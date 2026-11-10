# SDK／APIのバージョン移行

`@japan-ai-inc/studio-sdk` と公開APIを使うアプリケーションでは、SDKだけを更新して終わりにはできません。SDKの公開型、CLI、呼び出すAPI、Page内で利用する実行環境は組合せで動作します。特にSDKは1.0未満であり、minor版の更新にも破壊的変更が含まれ得ます。この記事では、利用中の実装を安全に更新するために、影響範囲の確認、段階的な移行、反映後の確認を説明します。

## 関連記事

- [開発環境・バージョン・互換性](dev-environment-and-versions.md)
- [非推奨機能・移行のお知らせ](../troubleshooting/deprecations-and-migrations.md)
- [SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)

## この記事の範囲

対象は、Node.jsアプリケーション、CI、`jai-studio` CLI、またはStudio PageからSDK・公開APIを利用する開発者です。確認対象のSDK版は **0.11.0** です。

ここでいうSDKの版はnpmパッケージ `@japan-ai-inc/studio-sdk` の版、公開APIの版はURLに含まれるAPI世代を指します。たとえばSDKのObject、Storage、Page、Agent、Workflow、Memberの各クライアントは、`/api/v1/` 配下の公開APIを呼び出します。一方、Workflowの公開済み定義に付く `v1` は、公開APIの世代ではなくWorkflowの公開スナップショットを表します。名称が同じでも、更新手順を混同しないでください。

この記事は、任意の外部本番ホスティング環境への対応を保証するものではありません。実行場所と認証の選択は[開発環境・バージョン・互換性](dev-environment-and-versions.md)および[実行モード・認証・対応範囲](execution-modes-and-auth.md)を確認してください。

> Project APIキーは移行用の設定値ではなく秘密情報です。ソースコード、ブラウザへ配信するJavaScript、公開環境変数、ログに含めません。発行・保管・失効は[Project APIキーを管理する](../operations/manage-project-api-keys.md)に従ってください。

## まず区別する3つのバージョン

| 対象 | 何を表すか | 移行時に確認すること |
|---|---|---|
| SDK／CLI | `@japan-ai-inc/studio-sdk` と `jai-studio` の機能・型・コマンド | 依存関係、ロックファイル、CLIの実体を同じ確認済みの版にそろえます。 |
| 公開API | `/api/v1/` などのURLで識別されるAPI世代 | SDKが利用するエンドポイントと、接続先が提供する機能・応答項目を確認します。 |
| 実行環境 | ローカルNode.js、Page内ブラウザ、Page内サーバー側 | 同じSDKメソッドでも、実行場所によって使える機能や認証が異なることを確認します。 |

SDK 0.11.0はNode.js **22以上**を要求します。SDKとCLIは同一パッケージから提供されるため、CIでは同じ確認済みの版にそろえます。

```bash
node --version
npm ls @japan-ai-inc/studio-sdk
jai-studio --version
```

上記の結果、`package.json`とロックファイルを記録します。グローバル導入のCLIでは、`command -v jai-studio`も確認します。

## 更新前に読むべき変更

`CHANGELOG.md`を読み、使用中のモジュール、型、CLI、実行環境に関係する変更を抜き出します。SDKは1.0未満のため、minor更新に破壊的変更を含む場合があります。完全な版を固定します。

```json
{
  "dependencies": {
    "@japan-ai-inc/studio-sdk": "0.11.0"
  }
}
```

### 代表的な移行対象

| 変更の種類 | 影響を受ける実装 | 移行時の確認 |
|---|---|---|
| 0.2.0のObject更新 | レコードを更新する処理 | 完全な型付きペイロードを送る前提で見直します。 |
| 0.2.0のWorkflow待機設定 | 実行完了を待つ処理 | `timeout`を`timeoutMs`へ置き換え、単位をミリ秒として確認します。 |
| 0.3.0のObject同時更新 | 同じレコードを更新する処理 | `revision`を使う更新・削除方式と、競合時の処理を確認します。 |
| 0.4.0のCanvas／Workflow機能 | Canvas取得、Workflowの作成・公開を行う処理 | 接続先の公開APIが必要な経路と応答を提供するか確認します。 |
| 0.5.0〜0.7.0のPRS Agent機能 | 候補比較、Storage添付、Canvas公開、クレジット、App設定 | PRSブラウザ専用の機能・接続先対応を確認し、Canvas公開は第3引数の範囲を指定します。 |
| 0.8.0のデータセット・予定実行 | PageのRAG参照、Workflowの予定実行 | `datasets`のスコープとPRS経路、`scheduledTasks`の`task:*`スコープ・実行者を確認します。 |
| 0.8.0のAgent定義・Objectルール | Agent構築、Objectアクセス設定 | `agent:write`、`object:policy`、キー作成者の権限と、配列置換・`null`クリアの更新意味を確認します。 |
| 0.9.0のObject検索 | 件数表示を伴う一覧・検索 | `includeCount: false`時は`total`が0となるため、ページングと件数表示を分けて扱います。 |
| 0.10.0の一括削除・候補比較 | 大量削除、PRS内の比較UI | `bulkDelete()`の全件成功・全件失敗と削除経路の提供状況、比較モデル、添付、ツール、参照の対応状況を確認します。 |
| 0.11.0の候補別effort | 比較UIと候補の選択 | `candidateEfforts`と`effortSupported`を確認し、両方の条件を満たす候補だけにeffortを提示します。接続先の応答が`candidateEfforts: false`の場合は、同じモデルの重複やeffortを選択肢として提示しません。 |

追加されたAPIや型は、SDK更新だけで既存環境に必ず現れるとは限りません。接続先が古い場合、未提供の経路は404、追加の入力は無視、応答項目は欠けることがあります。新機能は対応する接続先で確認します。

### 0.6.0から0.7.0へCanvas公開を移行する

0.6.0のCanvas公開呼出しは、0.7.0以降では使えません。利用箇所を検索し、公開範囲を第3引数に明示します。`"PUBLIC"`はリンクを知る利用者向け、`"ORGANIZATION"`はサインイン済みの組織メンバー向けです。更新後は対応する接続先で公開・解除の双方を確認します。

```ts
// 0.6.0
await studio.agents.publishCanvasArtifact(roomId, canvasArtifactId);

// 0.7.0以降
await studio.agents.publishCanvasArtifact(
  roomId,
  canvasArtifactId,
  "ORGANIZATION"
);
```

## 移行手順

### 1. 現状を固定して影響箇所を洗い出す

現在のSDK、Node.js、CLI、ロックファイルを保存します。更新先までのCHANGELOGを古い順に読み、SDKのimport、`createClient()`、モジュール呼出し、CLI、独自型・モックを対応付けます。

特に、Workflowの待機時間、Objectの更新、Page内専用API、追加された応答フィールドを確認します。

### 2. 更新候補を完全な版で導入する

検証用環境で、SDKとCLIを同じ完全な版へそろえます。0.11.0を採用する場合の例は次のとおりです。

```bash
npm install @japan-ai-inc/studio-sdk@0.11.0
npm install --global @japan-ai-inc/studio-sdk@0.11.0
npm ls @japan-ai-inc/studio-sdk
jai-studio --version
```

CLIをグローバル導入しない構成では、プロジェクトで固定したCLIを使います。SDKとCLIの版を別々に「最新」へ解決しません。

### 3. 接続先と実行モードを確認する

SDKの多くのモジュールは`/api/v1/`配下を使います。独自のHTTPリクエストは、公開APIのリファレンスで確認します。

Page内ブラウザへNode.js用のProject APIキー設定をコピーしません。実行場所と対応モジュールを確認します。

### 4. 代表操作と失敗経路をテストする

更新後は、利用中の機能ごとに読み取り、書き込み、権限不足、接続先未対応を確認します。Workflowでは一覧・取得、下書き保存、公開、実行結果を対象にします。Agentのストリームではエラーイベントと中断時も確認します。

応答、ページネーション、エラー処理の変更をテストし、未対応時に成功したように見せません。

### 5. 段階的に反映し、記録する

検証結果を確認してから、依存関係、ロックファイル、CLI利用環境を同時に更新します。反映後も代表操作を実行し、SDK版、CLI版、Node.js版、確認日時を記録します。問題時は直前の確認済み版へ戻せるようにします。

## よくある誤解

### SDKが0.11.0なら、すべての新機能が使える

SDKの版だけで利用可否は決まりません。対応する公開APIやPageの実行環境も確認します。

### `/api/v1/` のv1とWorkflowのv1は同じ版である

前者は公開APIのURL上の世代、後者はWorkflowを公開したスナップショットです。Workflowの定義を公開しても、公開API世代を変更する操作にはなりません。

### 1.0未満でもminor更新は互換である

minor更新に破壊的変更を含み得ます。完全な版を固定し、CHANGELOGを確認してから反映します。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 更新後に型エラーが出る | CHANGELOG、変更された引数、必須フィールド、モックを確認します。 |
| 新しいSDKメソッドが404になる | 接続先の公開APIと実行環境がその機能を提供するか確認します。 |
| Page内でだけ呼び出しが失敗する | APIキーをブラウザへ追加せず、実行モードとモジュールを確認します。 |
| CLIとアプリで結果が異なる | `jai-studio --version`、`npm ls`、実行パス、ロックファイルを確認します。 |
| 更新後に書き込みが変わった | 更新ペイロード、同時更新、待機時間、応答を確認します。 |

## チェックリスト

- [ ] 現在のSDK、CLI、Node.js、ロックファイルを記録した
- [ ] 更新元から更新先までのCHANGELOGを確認した
- [ ] SDK 0.7.0から0.11.0までの利用箇所を洗い出した
- [ ] SDKとCLIを同じ完全な版に固定した
- [ ] 接続先の公開APIと実行環境を確認した
- [ ] 読み取り・書き込み・権限不足・未対応時を検証した
- [ ] APIキーをブラウザ、公開環境変数、ログへ含めていない
- [ ] 反映後に版と代表操作の結果を記録した

## 次に読む記事

- [開発環境・バージョン・互換性](dev-environment-and-versions.md)
- [SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)
- [非推奨機能・移行のお知らせ](../troubleshooting/deprecations-and-migrations.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
