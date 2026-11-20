# CLI Pages

`jai-studio pages deploy`は、Pageのプロジェクトのソース（ZIPファイルまたはディレクトリ）をアップロードし、StudioのPageとしてデプロイするコマンドです。Pageの初回登録と、既存Pageへの新しい版の登録を同じコマンドで実行できます。この記事は、開発端末または管理された開発自動化で使う引数、メタデータ、出力を参照するためのものです。

CLI自体はビルドしませんが、**ビルドはStudio側で行われます**。アップロード後、Studioはロックファイルに応じてBun／Node／静的のいずれかを選び、依存関係のインストールと`package.json`の`build`スクリプトを実行し、Node／BunのPageはサーバーとして起動します。このため、アップロードするのはビルド済みの成果物ではなく、**プロジェクトのソース**です。

| アップロードする内容 | ルートに必要なもの |
|---|---|
| Node／BunのPage | `package.json`とロックファイル |
| 静的なPage | `index.html` |

ルートに`package.json`も`index.html`もない場合や、Next.jsのビルド成果物をアップロードした場合は拒否されます。初回デプロイの準備と利用者の入口での確認は[Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)、更新後の確認と切戻しは[Pageのビルド・起動・更新](../development/page-build-start-update.md)を参照してください。

## 関連記事

- [CLIの設定・プロファイル](cli-config-and-profiles.md)
- [Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)
- [Pageのビルド・起動・更新](../development/page-build-start-update.md)
- [SDK Pages・Platform](../development/sdk-pages-and-platform.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)

## 始める前に

- Node.js 22以上、`@japan-ai-inc/studio-sdk` 0.11.0、および対象Projectの`page:write`スコープを持つProject APIキーを用意します。導入、プロファイル、接続先とキーの優先順は[CLIの設定・プロファイル](cli-config-and-profiles.md)に従ってください。
- `--key`へキーを書かないでください。シェル履歴やプロセス一覧に残る可能性があります。プロファイル、`JAI_STUDIO_API_KEY`、または安全なCIの秘密情報設定を使います。
- ディレクトリ入力では、そのディレクトリの**中身**がZIPのルートになります。`package.json`（静的なPageでは`index.html`）があるプロジェクトのルートを指定します。`node_modules`は自動で除外されます。不要な秘密情報を含めません。
- ZIPの容量・構造・エントリ数・展開後サイズは、CLIの梱包処理とは別に登録先で検証されます。[Pageの仕様・制限](../build/page-specs-and-limits.md)の100MB、5,000件、200MBの制限を確認してください。

> Pageをデプロイできても、利用者が開けること、認証、データ処理、外部送信まで成功したことにはなりません。成功後は`studioUrl`または利用者の入口で、初期表示と代表操作を確認します。

## `pages deploy`

```text
jai-studio [--profile <name>] [--env <env>] pages deploy <zip-or-dir> [options]
```

以下の`<...>`を含む表記は**置換が必要な構文表記**です。角括弧を含めてそのままシェルへ貼り付けず、実際の値に置き換えてください。

| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `<zip-or-dir>` | はい | — | デプロイするZIPファイル、またはプロジェクトのルートディレクトリです。 |
| `--name <name>` | いいえ | — | **新規Page**の表示名です。更新先が決まる場合はデプロイ要求へ渡されません。 |
| `--page-id <pageId>` | いいえ | — | 更新する既存PageのIDです。指定すると、そのPageの新しい版としてデプロイします。 |
| `--timeout-ms <milliseconds>` | いいえ | SDKの既定値 | アップロード準備、ZIP送信、デプロイ要求に渡すタイムアウトです。正の有限数だけ指定できます。 |
| `--save-page-metadata <path>` | いいえ | — | ZIPファイル入力時に、Pageメタデータを書き込むパスです。 |
| `--json` | いいえ | なし | `pages deploy`で受け付ける出力指定です。成功結果はこの指定の有無にかかわらずJSONで標準出力へ出ます。 |

`--pretty`はグローバルフラグです。成功時のJSONを整形したい場合は、サブコマンドではなく先頭側に指定します。

```bash
jai-studio --profile my-project --pretty pages deploy ./my-page \
  --name "受注ダッシュボード" --json
```

既存Pageを明示して更新する例です。`PAGE_ID`には対象PageのIDを安全な環境変数などから渡し、実行前に対象を照合してください。

```bash
jai-studio --profile my-project pages deploy ./page-release.zip \
  --page-id "$PAGE_ID" --timeout-ms 600000 --json
```

### 初回デプロイと更新先の決まり方

CLIは、まず`--page-id`を確認します。ディレクトリ入力で`--page-id`がなければ、次にそのディレクトリ内の`.jai-studio/page.json`を読み、空でない文字列の`pageId`があれば更新先にします。いずれのIDもなければ初回デプロイです。

| 条件 | 実行されるデプロイ | `--name` |
|---|---|---|
| `--page-id`を指定した | 指定した既存Pageの新しい版 | 使用されません。 |
| ディレクトリ内のメタデータに`pageId`がある | その既存Pageの新しい版 | 使用されません。 |
| 上記以外 | 新規Page | 指定時に新規Pageの表示名として渡されます。 |

メタデータのファイルがない、読めない、JSONとして不正、または有効な`pageId`を持たない場合は、メタデータなしとして扱われ、**警告なしで新しいPageが作成されます**（Page IDとURLも新しくなります）。

{% hint style="warning" %}
メタデータは**入力ディレクトリの中**（`<入力ディレクトリ>/.jai-studio/page.json`）に書かれます。`./dist`のようなビルド出力先を指定すると、一般的なバンドラーは再ビルド時にこのフォルダーを空にするため、次回`--page-id`なしでデプロイすると新しいPageが黙って作成されます。

- 既存Pageを更新するスクリプトやCIでは、**常に`--page-id`を指定します**。
- ディレクトリ入力では、ビルド出力先ではなくプロジェクトのソースのフォルダーを指定します。
{% endhint %}

> `--page-id`はディレクトリの`.jai-studio/page.json`より優先されます。更新先が分からないときは推測で実行せず、対象PageとIDを確認してください。

### ディレクトリのZIP化と除外対象

ディレクトリを指定すると、CLIがZIPを作成して送信します。通常ファイルは再帰的に追加され、空のディレクトリとシンボリックリンクは追加されません。各階層で次の名前のファイルまたはディレクトリは除外されます。

`__MACOSX`、`node_modules`、`.git`、`.DS_Store`、`.jai-studio`、`__staging`、`__upload-lock.json`、`__backup`、`__versions`

`.jai-studio`が除外されるため、ディレクトリ更新で使うメタデータはアップロードするZIPには入りません。依存関係（`node_modules`）はStudio側でインストールされます。除外名のファイルやフォルダーに必要なソースを置かないでください。ZIPファイルを指定した場合、CLIは内容を再梱包せず、ファイル名とバイト列をそのままアップロードします。

## `.jai-studio/page.json`と`--save-page-metadata`

成功すると、CLIは次の情報をJSONとして保存します。`updatedAt`は保存時点のISO 8601日時です。

```json
{
  "pageId": "…",
  "studioUrl": "…",
  "runtimeUrl": null,
  "version": "…",
  "updatedAt": "…"
}
```

ディレクトリ入力では保存先が常に`<入力ディレクトリ>/.jai-studio/page.json`です。必要なディレクトリは作成されます。ZIP入力では既定の保存先はなく、保存する場合だけ`--save-page-metadata <path>`を指定します。反対に、ディレクトリ入力で`--save-page-metadata`を指定しても保存先は変更されません。

```bash
jai-studio --profile my-project pages deploy ./page-release.zip \
  --name "受注ダッシュボード" \
  --save-page-metadata ./page-metadata.json --json
```

このファイルにはPage IDとURLが含まれます。リポジトリへコミットせず、アクセスを必要な担当者に限定した場所で扱います。Project APIキー、Cookie、Secret値をメタデータへ追加してはいけません。

## 出力と確認

成功時は標準出力にJSONが1件出力されます。少なくとも次のフィールドを確認します。

| フィールド | 意味 |
|---|---|
| `pageId` | 作成または更新したPageのIDです。次回の明示的な更新先に使えます。 |
| `studioUrl` | Studioで対象Pageを確認するURLです。 |
| `runtimeUrl` | 実行用URLです。存在しない場合は`null`です。アクセス設定が既定の「サインイン必須」のPageでは常に`null`です。`null`をURLとして扱わず、`studioUrl`から確認します。 |
| `version` | デプロイ結果のバージョンです。 |

このほか`fileCount`、`createdAt`、`rebuildTriggered`も結果に含まれます。自動化では標準出力のJSONを解析し、標準エラー出力と終了コードを別に扱います。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 初回のつもりで既存Pageを更新しそう | `--page-id`、入力ディレクトリの`.jai-studio/page.json`、対象Project・Page IDを確認します。 |
| 更新のつもりが新しいPageが作成された | 入力ディレクトリの`.jai-studio/page.json`が消えていた可能性があります（ビルド出力先の再生成など）。以後は`--page-id`を必ず指定します。 |
| アップロードが拒否される | ルートに`package.json`とロックファイル、または`index.html`があるか確認します。Next.jsのビルド成果物ではなくソースを指定します。 |
| `--timeout-ms`の指定で失敗する | 単位をミリ秒として、0以下、無限大、数値以外を指定していないか確認します。必要以上に大きな値を推測で設定しません。 |
| デプロイ後に必要なファイルがない | プロジェクトのルートと、除外対象の名前を確認します。`node_modules`や`.jai-studio`はアップロードされません。 |
| URLが利用できない、または`runtimeUrl`が`null` | 「サインイン必須」のPageでは`runtimeUrl`は常に`null`です。`studioUrl`からPageとアクセス設定を確認し、利用者と同じ入口で検証します。 |
| CLIがエラーで終了する | 対象プロファイル、接続先、キー、ZIP、Page IDを確認します。APIの失敗時は標準エラー出力に`error`、`statusCode`、`details`のJSONが出て（使い方の誤りではテキスト）、終了コード1です。`--debug`は詳細に機密情報を含む可能性があるため、共有ログでは使いません。 |

問い合わせには、CLI版、キーを除いた実行コマンド、入力種別、Page ID、発生日時、標準エラー出力、再現手順を記録します。APIキー、Cookie、Secret値、公開前URLは添付しません。

## チェックリスト

- [ ] プロジェクトのソース（ルートに`package.json`とロックファイル、または`index.html`）をZIPまたはディレクトリで指定した
- [ ] 初回登録か更新かを決め、更新では`--page-id`を明示した
- [ ] `--name`が新規Pageだけに使われることを確認した
- [ ] `--timeout-ms`をミリ秒の正の有限数で指定した、または既定値を使った
- [ ] ディレクトリ入力時の除外対象に必要なファイルを置いていない
- [ ] `pageId`、`studioUrl`、`runtimeUrl`、`version`を安全な記録先で確認した
- [ ] 利用者と同じ入口でPageと代表操作を確認した

## 次に読む記事

- [CLIの設定・プロファイル](cli-config-and-profiles.md)
- [Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)
- [Pageのビルド・起動・更新](../development/page-build-start-update.md)
- [SDK Pages・Platform](../development/sdk-pages-and-platform.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
