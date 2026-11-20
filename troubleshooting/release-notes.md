# リリースノート

Studioの変更と、その確認方法を案内します。SDK／CLI、Studio UI、公開API、Page実行環境は別々に提供されます。SDKのnpm公開日と、本番環境への反映日は同じとは限りません。本記事の日付は、特記しない限りSDK同梱`CHANGELOG.md`のnpm公開日です。

## 関連記事

- [非推奨機能・移行のお知らせ](deprecations-and-migrations.md)
- [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)
- [SDK／APIのバージョン移行](../development/sdk-api-version-migration.md)

## この記事の範囲

掲載するSDK／CLI履歴は、npmパッケージ`@japan-ai-inc/studio-sdk`同梱の`CHANGELOG.md`にある0.2.0〜0.11.0です。SDKとCLIは同一パッケージで、CLI名は`jai-studio`です。SDKは1.0未満のためminor版にも破壊的変更があり得ます。範囲指定や`latest`ではなく、検証済みの完全な版を固定してください。

- **Studio UI**：画面操作はStudio Project・導入済みAppの各記事で確認してください。本記事では、Studio以外の組織全体の画面変更は扱いません。
- **公開API／Page実行環境**：SDK更新だけでは利用可否を判断しません。経路、認証モード、権限、組織設定を対象環境で確認します。

## リリースノートの読み方

対象をStudio UI、SDK／CLI、公開API、Page実行環境に分け、機能の提供条件と、利用する認証モードを確認します。

## 掲載エントリーの形式

以下は版・公開日・変更点・必要な対応をまとめた履歴です。利用経路の条件がある場合は各版に併記します。

## まず確認すること

1. 対象をStudio UI、SDK／CLI、公開API、Page実行環境に分けます。
2. 現在版から更新先までの全エントリーと、使用中のメソッド・型・CLIを照合します。
3. 検証環境で、接続先・認証モード・代表操作・失敗時の挙動を確認します。

```bash
node --version
npm ls @japan-ai-inc/studio-sdk
jai-studio --version
```

SDK 0.11.0とCLIのNode.js要件は22以上です。SDKとCLIを併用する場合は同じ完全な版にそろえます。

## 公開API・本番環境の変更

### 2026-11-13（本番反映日）：Objectレコードの一括削除API

公開APIに`DELETE /api/v1/objects/:objectId/records/bulk`が追加され、Project APIキーから一括削除できるようになりました。スコープは`object:delete`、1回あたり最大1,000件で、全件成功か全件失敗のどちらかになります（一部だけ削除されることはありません）。SDKでは0.10.0の`objects.define(id).bulkDelete(recordIds)`から使えます。

## SDK／CLIの変更

### 0.11.0（2026-11-06）

`compare()`の`models`は、モデル名に加えて`{ model, effort? }`（`low`／`mid`／`high`／`max`）を受け付けます。`primaryIndex`は`primary`より優先し、同じモデルも異なるeffortなら並べられます。同じモデル・同じeffortは「models must differ in model or effort」で拒否されます。`candidateEfforts`、`effortSupported`、候補の`effort`が追加されました。

回答比較は、StudioでホストされたPageのブラウザから使う機能です。Project APIキーによる直接呼出しは対象外です。同じモデル・同じeffortの重複は、送信前にSDK内で拒否され、このエラーにはエラーコードがありません。

### 0.10.0（2026-11-03）

Pageのブラウザでの`agents.listCompareModels(agentId)`、比較への画像・PDF・Storageファイル（`storageFileIds`は最大20件）、読取り専用ツール（最大10件）、候補のWeb参照元が追加されました。利用不可モデルは実行前に`model_not_allowed`で拒否されます。

`objects.define(id).bulkDelete(recordIds)`も追加されました。最大1,000件で、全件成功か全件失敗のどちらかです（削除できない対象が1件でもあれば1件も削除されません）。存在しないIDはスキップされます。`object:delete`が必要です。Pageのブラウザと、Project APIキーによる公開API（`DELETE /api/v1/objects/:objectId/records/bulk`、2026-11-13に本番提供）の両方で使えます。

### 0.9.0（2026-10-23）

Objectの`list()`と`search()`に`includeCount`が追加されました。`false`では全件カウントを省略し、`total`は0になります。`hasNextPage`とカーソルは変わりません。

### 0.8.0（2026-10-20）

- `scheduledTasks`とCLIでProject内Workflowの定期実行を管理できます。実行主体はキーの作成者で、`task:read`、`task:write`＋`workflow:execute`、`task:delete`と作成者の権限が必要です。これらのスコープはAppリリースマニフェストに宣言できません。
- `datasets`は読取り専用です。`project`はProject APIキーで使えますが、`organization`はPageのブラウザでサインインした利用者用で、Project APIキーでは403です。検索は取得のみです。
- Agent定義の作成・更新は`agent:write`と作成者の権限を要します。送信した項目だけを変更し、配列は置換、`null`はクリアです。
- レコードアクセスルールは一覧に`object:read`と`object:policy`、作成・削除に`object:write`と`object:policy`、さらに作成者のObjectポリシー権限を要します。

### 0.7.0（2026-10-05）

StudioでホストされたPageで、利用者自身のターンまたはroomの利用量取得と`platform.openAppSettings(tab?)`が追加されました。Page実行環境が対応していない場合、利用量取得は404、設定画面を開く呼出しは失敗します。Canvas公開は`agents.publishCanvasArtifact(roomId, canvasArtifactId, scope)`へ破壊的変更され、`"PUBLIC"`または`"ORGANIZATION"`のscopeが必要です。旧2引数の経路は使えません。

### 0.6.0（2026-10-01）

比較ターンのツールイベントと候補の`tools`、Pageのブラウザでの`storageFileIds`（最大20件）、PageからのCanvas書込み・公開が追加されました。Storage添付はPageのブラウザ専用で、対応していないPage実行環境ではファイルなしで回答します。Canvas書込み・公開もPageセッションと対応経路が前提です。同梱Pageデプロイ補助の`--no-inject-base`と`--allow-root-assets`は削除されました。

### 0.5.0（2026-09-28）

Pageのブラウザでの`compare()`、`adoptCandidate()`、`getTurns()`が追加されました。回答比較を使うには、組織で回答比較の機能が有効になっている必要があります。CLIには`jai-studio members list`が追加されました。skillのステージングからローカル生成物を除外し、`config show`のマスク済みキーをSDKが拒否するよう修正されました。

### 0.4.0（2026-09-15）

Canvas文書の一覧・取得、Workflowの作成・更新・下書き保存・公開・削除、定義の読出しと下書き競合検出が追加されました。対応する公開APIと必要スコープが前提です。対応経路では、非所有者は非公開roomのメッセージとCanvasを取得できず404になります。

### 0.3.0（2026-09-10）

Objectのフィールド検証ルールの型と、レコード更新・削除のrevisionを使う楽観的同時実行制御が追加されました。StudioでホストされたPageには、現在のPageセッションだけを終了する`platform.logout()`が追加されました。JAPAN AIアカウント全体からのサインアウトではありません。

### 0.2.1（2026-08-10）

PageのブラウザからのObject検索が、公開APIのフィルター・ソートの仕様に沿うよう修正されました。直接公開APIの動作変更としては記載されていません。

### 0.2.0（2026-08-10）

`jai-studio skill install`と同梱skillが追加されました。Object更新は完全な型付きペイロードが必要で、省略した通常フィールドは保持されません。Workflow待機入力は`timeout`から`timeoutMs`（ミリ秒）へ変更されました。Node SDKとCLIはNode.js 22以上です。

## よくある誤解

- **SDK更新だけで新機能が使える**：公開API、Page実行環境、認証モード、権限、組織設定を別に確認します。
- **npm公開日が本番提供日である**：異なります。公開日、マージ、デプロイ、各環境での有効化を混同しません。
- **CLIだけ別版でよい**：同一パッケージで提供されるため、使用する構成ではSDKとCLIの完全な版をそろえて検証します。

## 解決しない場合

SDK／CLI・Node.jsの版、実行環境、対象Project、接続先、メソッドまたはコマンド、発生日時、HTTP状態、更新前後の結果を記録します。APIキー、Cookie、認証ヘッダー、Secret値、トークンは記録・問い合わせに含めません。

## 次に読む記事

- [非推奨機能・移行のお知らせ](deprecations-and-migrations.md)
- [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)
- [SDK／APIのバージョン移行](../development/sdk-api-version-migration.md)
