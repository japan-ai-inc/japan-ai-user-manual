# API／SDK共通のエラー・タイムアウト・制限

APIまたはStudio SDKを組み込むときは、成功時のレスポンスだけではなく、認証、入力、通信、タイムアウト、利用制御で失敗した場合を設計します。この記事では、開発者が失敗を分類し、利用者に再実行を促すか、設定確認へ導くかを判断するための共通的な扱いを説明します。

Agentの実行・ストリーム固有の扱いは[SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)、機能別・組織別の提供条件や上限の正本は[制限・提供条件の索引](../operations/limits-and-availability-index.md)を参照してください。

## 関連記事

- [SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)
- [SDK／APIのエラーを切り分ける](../troubleshooting/isolate-sdk-api-errors.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)

## この記事の範囲

対象は、`@japan-ai-inc/studio-sdk`と公開APIを呼び出すアプリケーションです。HTTPの個別エンドポイント、機能固有の入力上限、契約・料金、組織別の提供可否は扱いません。機能記事と[制限・提供条件の索引](../operations/limits-and-availability-index.md)を確認してください。

SDK **0.11.0**は、モジュールごとに専用のエラー型を返します。いずれも`SdkApiError`を継承します。共通項目を記録し、必要な箇所だけモジュール固有の型で分岐します。`AgentsApiError`、`ScheduledTasksApiError`など、接続先が送る`code`を保持する型では、メッセージだけでなく`code`を安全な分類に使えます。

> Project APIキー、`x-api-key`ヘッダー、Bearerトークン、Cookie、Secret、利用者の入力に含まれる機密情報を、例外メッセージ・監視ログ・問い合わせにそのまま出力してはいけません。キーの発行・失効・スコープは[Project APIキーを管理する](../operations/manage-project-api-keys.md)で確認します。

## 1. まず失敗を四つに分類する

同じ「失敗」でも、再送すべきかは原因で異なります。対象機能、操作種別、識別値、時刻、SDKバージョン、実行モードを記録します。本文や添付内容は残しません。

| 分類 | SDKで確認するもの | 初動 |
|---|---|---|
| 呼出し前の設定・入力 | 初期化例外、入力検証エラー | `baseUrl`、実行モード、必須値、対象IDを修正してから再試行します。 |
| HTTP応答による拒否 | `statusCode`、`responseBody` | 認証、権限、入力、対象の存在、競合、利用制御を分けて確認します。 |
| 通信・タイムアウト | `statusCode`がないエラー、`cause`、中止状態 | サーバー側で処理済みか不明な可能性を考慮し、即時の自動再送を避けます。 |
| ストリーム内の失敗 | ストリームの`error`イベント | 既に受け取った断片を完了結果として確定せず、イベント内容を表示・記録します。 |

`SdkApiError`には、`message`、`statusCode`、`responseBody`、`cause`が入ります。`responseBody`は無加工で表示・保存しません。SDK 0.11.0のCLIは、APIが意図して返す`code`とオブジェクト型の`params`をメッセージとともに表示しますが、`--debug`なしではその他の生の応答本文を伏せます。`code`と必要最小限の`params`は分類に使い、値に個人情報・Secret・認証情報がないことを確認してから記録します。

## 2. SDKのエラーを安全に受け取る

共通型で最低限の診断情報を同じ形で扱えます。画面には固定の案内文を出します。

```ts
import {
  AgentsApiError,
  SdkApiError,
  createClient,
} from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env["JAI_STUDIO_API_KEY"],
});

try {
  await studio.agents.invoke("agent-id", { prompt: "要約してください" });
} catch (error) {
  if (error instanceof AgentsApiError) {
    recordFailure({ statusCode: error.statusCode, message: error.message, code: error.code });
    showRetryGuidance(error.statusCode);
  } else if (error instanceof SdkApiError) {
    recordFailure({ statusCode: error.statusCode, message: error.message });
  } else {
    recordFailure({ message: "SDK外で例外が発生しました" });
  }
}
```

`ObjectsApiError`、`StorageApiError`、`WorkflowsApiError`、`PagesApiError`、`PlatformApiError`も共通型を継承します。`TurnCandidatesApiError`は`AgentsApiError`を継承します。まず共通処理を置き、必要な箇所だけ専用型を判定します。

> HTTPの非成功応答はモジュール固有のエラーになります。ネットワーク障害、解析失敗、タイムアウトでは`statusCode`がない場合があります。これは成功や未実行の証拠ではありません。

## 3. HTTPステータスごとの判断を分ける

APIでは、入力不備、認証・権限不足、対象不在、競合、利用制御、サーバー側の失敗がHTTP応答として返り得ます。以下を一般的な切り分け順にします。

| 状態の例 | 判断 | 対応 |
|---|---|---|
| 400台前半 | 入力または要求条件が不足 | パラメーター、対象ID、実行モードを確認します。 |
| 401 | APIキーがない・無効、またはセッションが切れている | キーの値と有効期限、PRSのサインイン状態を確認します。 |
| 403 | キーまたは操作権限に問題がある | 対象Project、必要スコープ、権限を確認します。 |
| 404 | 対象がない、または参照できない | ID、Project、環境、共有範囲を確認します。 |
| 409 | 状態の競合または並行操作 | 現在の状態を再取得します。 |
| 408、タイムアウト | 開始・完了の状態が不明 | 実行結果や作成済みリソースを確認します。 |
| 429 | 利用制御または同時実行制御 | 応答の待機案内に従います。 |
| 500台 | サービス側または依存先の失敗 | 識別情報を記録して確認します。 |

公開APIの読み取り経路やPageのデプロイには利用制御があり、429になる場合があります。数値や適用範囲は固定値として埋め込みません。

## 4. タイムアウトとキャンセルを明示する

SDKの通常の既定タイムアウトは**30秒**です。`createClient()`または各呼出しの`timeoutMs`で変更でき、`AbortSignal`も指定できます。

Agentは例外で、`invoke()`は**5分**、`invokeStream()`は**10分**が既定値です。これは他モジュールやHTTP API全体の共通上限ではありません。

```ts
const controller = new AbortController();

try {
  await studio.objects.list({ limit: 20 }, {
    timeoutMs: 15_000,
    signal: controller.signal,
  });
} finally {
  // 画面を閉じるなど、呼出しをやめるときに実行します。
  controller.abort();
}
```

SDKはタイムアウト時に通信を中止し、該当モジュールのAPIエラーを送出します。中止・タイムアウトだけで、サーバー側の結果は判断できません。書込みは無条件で再送しません。

## 5. 再試行と利用制御を設計する

SDKの共通通信処理は、失敗を自動再試行しません。呼出し側が再試行の可否、待機、重複防止を決めます。読み取りと書込みを同じ方針にしません。

1. 操作を読み取り、作成、更新、実行、アップロードに分類します。
2. 429では待機案内を優先し、連続再送しません。
3. 接続断・タイムアウト・500台では、対象の状態または実行結果を照会します。
4. 未実行と確認できた読み取りだけを、回数を制限して再試行します。
5. 書込みは、状態確認後に再実行します。

> 再試行回数、間隔、レート制限値は一律に決めず、対象機能に応じて検証してください。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 常に403になる | APIキー、対象Project、必要スコープ、実行モードを確認します。 |
| 429が続く | 応答の案内を確認し、並列数・再送頻度を下げます。 |
| タイムアウト後に再実行してよいか分からない | 対象リソースや実行結果を照会します。 |
| `statusCode`がない | ネットワーク、クライアント中止、SDKのタイムアウトを確認します。 |
| SDK型が判定できない | `SdkApiError`で共通処理し、SDKバージョンと`message`を記録します。 |

問い合わせには、対象機能、SDKバージョン、実行モード、操作時刻、要求種別、`statusCode`、エラー、再現手順を含めます。APIキー、認証ヘッダー、Cookie、Secret、個人情報、入力データは含めません。

## チェックリスト

- [ ] 成功以外の`statusCode`と`message`を記録する
- [ ] `SdkApiError`を共通処理し、必要な箇所だけモジュール固有型を判定する
- [ ] 30秒の通常既定値と、Agentの5分・10分の既定値を区別した
- [ ] `timeoutMs`と`AbortSignal`を、画面・ジョブの終了方針に合わせて指定した
- [ ] タイムアウト後に状態確認なしで書込み・実行を自動再送しない
- [ ] 429では応答の待機案内を確認し、再送を抑制する
- [ ] エラー記録・問い合わせに認証情報や機密データを含めていない

## 次に読む記事

- [SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)
- [SDK／APIのエラーを切り分ける](../troubleshooting/isolate-sdk-api-errors.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
