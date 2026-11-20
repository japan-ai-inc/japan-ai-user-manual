# SDK／APIのエラーを切り分ける

このページは、Studio SDKまたは公開APIの呼び出しが失敗する、想定外の応答になる、CLIが失敗終了する、429やタイムアウトになるときの切り分け手順です。対象は開発者です。認証情報を増やして試す前に、実行モード、Node.js・SDK・CLIの版、接続先、対象Project、HTTP応答またはCLI終了コードを分けて確認してください。

## 要点：実行場所ごとの認証情報

| 実行場所 | 認証情報 | 接続先 |
|---|---|---|
| ローカルNode.js（直接モード） | Project APIキー（サーバー側の環境変数などから渡す） | `baseUrl`にStudio APIのHTTPS接続先 |
| Pageのブラウザ（StudioでホストされたPage） | Pageセッション。APIキーは設定しない | `baseUrl: ""` |
| Pageのサーバー処理 | 開発者がルートの`.env`で決めた変数名から読むキー。Appとして公開すると、プラットフォームが管理するキーに置き換わる | 環境変数で渡した接続先 |
| CLI | APIキーと接続先URLを、それぞれ「コマンドフラグ ＞ 環境変数 ＞ プロファイル」の固定の順で解決 | `--env`、`JAI_STUDIO_API_URL`、プロファイル |

認証エラーの区別：無効なキーは403（`Invalid API key`）、期限切れのキーは403（`API key has expired`）です。401は認証情報がまったくない場合だけです。Studio APIのエラー応答にはすべて`requestId`が含まれます。

## 関連記事

- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [CLIの設定・プロファイル](../cli/cli-config-and-profiles.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
- [Objects API](../api/objects-api.md)
- [Storage API](../api/storage-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [Pages API](../api/pages-api.md)
- [Tool Proxy API](../api/tool-proxy-api.md)

## 始める前に

失敗した機能、操作種別（読取り・作成・更新・実行）、対象の識別情報、発生日時とタイムゾーン、SDK／CLIの版、Node.js版、実行モード、`statusCode`またはCLI終了コードを記録します。SDK／CLIは、対象機能に対応する完全な版番号とロックファイルをそろえます。現在のnpm版SDK／CLI 0.11.0では、Agent・Dataset・自動実行などの作成・更新や、レコードルール、比較、一括削除など、操作ごとに必要な入力・権限・状態を確認します。CLIは成功時にJSONを標準出力へ、失敗時にエラーを標準エラー出力へ出し、終了コード1で終了します。

> APIキー、`x-api-key`、Bearerトークン、Cookie、Secret、リクエスト本文に含まれる個人情報を、コンソール、CIログ、チケット、画面キャプチャ、問い合わせに出してはいけません。`jai-studio config show`のマスク値も認証には使えません。実値を貼って切り分ける代わりに、設定元と有効性を確認します。

## まず確認すること

- [ ] 実行場所をローカルNode.js、Pageのブラウザ、Pageのサーバー処理のどれかとして特定した
- [ ] SDK／CLI 0.11.0、Node.js 22以上、依存関係・ロックファイルの実際の版を確認した
- [ ] 対象Project用の有効なProject APIキーと、必要最小限のスコープを確認した
- [ ] `baseUrl`、CLIの`--env`、`JAI_STUDIO_API_URL`、プロファイルの接続先が意図した環境を指すことを確認した
- [ ] HTTP応答の有無、`statusCode`、`requestId`、安全に扱えるエラー要約、CLIの終了コードを記録した
- [ ] 書込み・実行・アップロードは、タイムアウトや失敗後に処理済みか確認せず再送していない

## 症状別の切り分け

| 症状・表示されるメッセージ | 主な原因 | 確認・対処 | 詳しい記事 |
|---|---|---|---|
| Node.jsで初期化時に`baseUrl is required`、または相対URLが失敗する | Node.jsでは空の`baseUrl`を使えない。Pageのブラウザ用の初期化を流用している | Node.jsの直接開発では、HTTPSのStudio API接続先と対象Projectのキーをサーバー側だけに設定します。空の`baseUrl`はPageのブラウザ用です。 | [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md) ／ [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) |
| Pageのブラウザで401・403・404、または期待するSDKモジュールが使えない | Pageセッションの経路とProject APIキーの公開API経路を混同している。アクセスモード・同一オリジン・ブラウザ対応範囲が不一致 | `baseUrl: ""`で初期化し、キーや`Authorization`をブラウザへ入れません。Pageを開き直し、アクセス設定とブラウザ対応モジュールを確認します。`members`と`workflows`はPage実行環境が中継するため技術的には呼び出せますが、現時点ではベータのサポート範囲外です（予告なく変更される可能性があります）。 | [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md) ／ [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md) |
| 403で`Invalid API key`または`API key has expired` | キーが無効（値の誤り・失効）、または有効期限切れ。マスク値を渡している | APIキーの実値を表示・共有せず、どの設定元のキーが使われたか（フラグ・環境変数・プロファイルの順）と、キーの失効・有効期限を確認します。マスク値をSDKへ渡しません。 | [Project APIキーを管理する](../operations/manage-project-api-keys.md) ／ [CLIの設定・プロファイル](../cli/cli-config-and-profiles.md) |
| 403（上記以外のメッセージ） | スコープ・操作権限の不足、キーの対象Projectの違い、キーの作成者がProjectの有効メンバーでなくなった（作成者が外れるとその人が作ったキーはすべて403になる） | 必要スコープ、対象Project、キー作成者のメンバー状態を確認します。 | [Project APIキーを管理する](../operations/manage-project-api-keys.md) |
| 401 | 認証情報がまったく送られていない（キーの環境変数が読めていない、プロファイル未設定など） | キーの設定元を確認します。CLIでは「コマンドフラグ ＞ 環境変数 ＞ プロファイル」の順で解決されます。 | [CLIの設定・プロファイル](../cli/cli-config-and-profiles.md) |
| `node --version` が22未満、SDKやCLIの挙動が環境ごとに異なる | Node.js・SDK・CLI・ロックファイルの版がそろっていない | `node --version`、`jai-studio --version`、`npm ls`、実行ディレクトリを確認し、SDK／CLI 0.11.0（または利用する機能に対応した固定版）とNode.js 22以上を満たす環境へそろえます。 | [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md) |
| 400台前半、404、409 | 入力・対象ID・対象Project・経路・現在状態が不一致 | 400台前半は入力と実行モード、404はID・Project・共有範囲・経路、409は現在状態と並行操作を確認します。同じ要求を自動再送しません。機能固有の入力は該当API記事で確認します。 | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) ／ [公開APIの各記事](../api/agents-conversations-artifacts-api.md) |
| 429、または待機を求める応答 | 利用制御または同時実行制御に到達した | `retryAfterSeconds`など応答の待機案内を確認し、並列数と再送頻度を下げます。CLIの通常の出力では`retryAfterSeconds`が除去されます。固定の待機秒数や連続再試行を実装しません。 | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) |
| 500台、ネットワークエラー、`statusCode`がない | サービス・依存先・通信・応答解析・クライアント中止（abort）・ストリームの読み取り中断・SDKタイムアウトの可能性。`SdkApiError`以外の例外のこともある | `cause`、開始・終了時刻、実行環境を記録します。`statusCode`がないことを未実行の証拠にせず、対象の状態を照会してから限定的な再試行または問い合わせを判断します。 | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) |
| `Request timed out`、操作後に結果が分からない | クライアント側の期限到達。サーバー側で開始・完了済みの可能性がある | `timeoutMs`、中止の有無、対象リソースや実行結果を確認します。クライアント全体の`timeoutMs`はagents、pages、`runAndWait`には効きません。作成・更新・実行は重複しないと確認できるまで同じ要求を送らず、読み取りと同じ再試行方針にしません。 | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) |
| CLIが終了コード1で終わる、CIで失敗する | CLI設定、接続先、キー、コマンド引数、API応答のいずれかで失敗 | 標準エラー出力の安全な要約と終了コードを保存します。`config show`でプロファイル名・接続先・マスク済みキーを確認し、`--debug`は共有しない一時環境だけで使います。通常の出力では`requestId`や`runId`が除去されるため、必要な場合は`--debug`で確認します。 | [CLIの設定・プロファイル](../cli/cli-config-and-profiles.md) |
| `workflows run-and-wait`がタイムアウトして終了コード1で終わる | CLIの待機時間が切れただけで、Workflowの実行は続いている。サーバーは`runId`つきの408を返すが、CLIの通常の出力では`runId`が除去される | 同じコマンドを再実行すると**重複実行**になります。`--debug`で`runId`を確認し、`workflows status`で状態・出力を取得します。 | [CLI：Workflows](../cli/cli-workflows.md) |
| `pages deploy`で更新したつもりが、新しいPageが作成された | `--page-id`なしで`./dist`などのビルド出力フォルダーをデプロイし、Page IDを保存した`.jai-studio/page.json`が再ビルドで消えていた | 更新用のスクリプトでは常に`--page-id`を指定します。 | [Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md) |

## 実行モード・接続先を先に固定する

1. **ローカルNode.js**では、Node.js 22以上とProject APIキーをサーバー側で使います。`baseUrl`にはStudio APIのHTTPS接続先を設定し、キーは環境変数など安全な経路から渡します。
2. **Pageのブラウザ**では、Pageセッションを使います。`baseUrl: ""`とし、APIキーは設定しません。利用可能なモジュール・経路はローカルと同一ではありません。
3. **Pageのサーバー処理**では、開発者がルートの`.env`で決めた変数名からキーを読みます（自動で注入されるキーはありません）。Appとして公開すると、プラットフォームが管理するキーに置き換わります。キーをレスポンス、画面初期値、ブラウザ公開用環境変数へ渡しません。
4. CLIは、接続先URLとAPIキーをそれぞれ「コマンドフラグ ＞ 環境変数 ＞ プロファイル」の順で解決します。この順序は固定です。`--env`、`JAI_STUDIO_API_URL`、プロファイルの値を確認し、意図した接続先と対象Project用キーの組合せかを確認します。

直接開発の最小例です。値は安全な環境変数から取得し、ログへ出力しません。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env["JAI_STUDIO_API_KEY"],
  timeoutMs: 30_000,
});
```

> `invokeUrl`をキーと併用する場合は、`baseUrl`と同じオリジンである必要があります。接続先を推測して別ドメインへ差し替えたり、ブラウザへキーを移したりして回避しないでください。

## HTTP応答・タイムアウト・再試行を分ける

SDKのモジュール固有エラーは`SdkApiError`を継承します。HTTP応答がある場合は`statusCode`、応答本文（`requestId`を含む）、原因例外を安全に扱える範囲で分類します。次は`SdkApiError`以外の形で届くため、別に扱います。

- ネットワークエラー
- `AbortSignal`による中断（abort）。`signal.aborted`で判定します。
- ストリームの読み取り中断
- `platform.*`が投げる通常の`Error`
- `openTask`が返す`{code}`オブジェクト（例外ではなく戻り値）

SDKの通常の既定タイムアウトは30秒です。クライアント全体の`timeoutMs`はagents、pages、`runAndWait`には効きません（`runAndWait`のtimeoutは第2引数で指定します）。直接モードのストリームはレスポンスヘッダーの受信後はタイムアウトの対象外のため、全体の時間は`AbortSignal`で制限します。

- **4xx**：入力、ID、Project、スコープ、実行モード、現在状態を修正してから試します。
- **429**：応答の待機案内を優先し、並列処理と再送を抑えます。
- **5xx・通信断・タイムアウト**：処理済みかを照会します。書込み・実行を無条件に再送しません。
- **CLI終了コード1**：成功JSONの不在だけで原因を決めず、標準エラー出力、安全な設定確認、API応答を合わせて確認します。`run-and-wait`のタイムアウトでは実行が続いているため、再実行しません。

## 管理者・サポートに相談する前に

秘密情報を除き、対象機能・API、組織・Project・対象ID、SDK／CLI・Node.jsの版、実行モード、`baseUrl`またはCLI環境名の**値そのものが機密でない範囲の識別**、発生日時とタイムゾーン、操作種別、`statusCode`、`requestId`（CLIでは`--debug`で確認）、CLI終了コード、エラー文の伏せた要約、再現手順、タイムアウト・再試行・状態照会の有無を集めます。形式は[問い合わせに必要な情報を集める](collect-info-for-support.md)を参照してください。

## 次に読む記事

- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [CLIの設定・プロファイル](../cli/cli-config-and-profiles.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [問い合わせに必要な情報を集める](collect-info-for-support.md)
