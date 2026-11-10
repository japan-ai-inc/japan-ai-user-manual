# CLI Storage

`jai-studio storage`は、Project Storage内のファイルとフォルダについて、一覧、メタデータ、署名付きダウンロードURL、削除、フォルダ操作をJSONで実行するCLIコマンド群です。対象は、Project APIキーを安全に管理でき、CLIを利用する開発者です。

Storage SDK全体の操作・実行モードは[SDK Storage](../development/sdk-storage.md)、Storageの保存条件・画面上の制限は[Storageの仕様・制限](../build/storage-specs-and-limits.md)を参照してください。プロファイル、認証情報の解決順、共通フラグは[CLIの設定・プロファイル](cli-config-and-profiles.md)で扱います。

## 関連記事

- [SDK Storage](../development/sdk-storage.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [CLIの設定・プロファイル](cli-config-and-profiles.md)

## 始める前に

- Node.js 22以降で`@japan-ai-inc/studio-sdk` 0.11.0のCLIを実行します。
- 対象ProjectのAPIキーと接続先を、プロファイル、環境変数、またはグローバルフラグで設定します。キーをコマンド履歴、ソース管理、ログ、問い合わせに残さないでください。
- ファイル操作には`fileId`、フォルダ操作には`folderId`を使います。まず一覧JSONから対象ID、パス、件数を確認します。

> `jai-studio storage`には、`upload`および`download`というサブコマンドはありません。ファイル内容を送受信するCLI構文を推測して実行しないでください。SDKの`upload()`・`download()`を使う方法は[SDK Storage](../development/sdk-storage.md)を参照します。

成功時の標準出力はJSONです。`--pretty`を付けると整形表示します。失敗時は標準エラーへJSONが出力され、終了コードは1です。`--debug`はサーバー応答の詳細を表示し得るため、機密情報を含む出力を共有しないでください。

## コマンド一覧

| コマンド | 用途 | 破壊的操作 |
|---|---|---|
| `storage list` | ファイル・フォルダを一覧する | いいえ |
| `storage metadata` | ファイルのメタデータを取得する | いいえ |
| `storage download-url` | 期限付きダウンロードURLを取得する | いいえ |
| `storage delete` | ファイルを削除する | はい |
| `storage folders list` | 指定フォルダの内容を一覧する | いいえ |
| `storage folders create` | フォルダを作成する | いいえ |
| `storage folders delete` | フォルダと配下を削除する | はい |

## 1. ファイルとフォルダを一覧する

```text
jai-studio storage list [--path <path>] [--recursive] [--cursor <cursor>] [--limit <n>] [--order-by <field>] [--order-direction <dir>]
```

以下の`<...>`を含む表記は**置換が必要な構文表記**です。角括弧を含めてそのままシェルへ貼り付けず、実際の値に置き換えてください。

| フラグ | 必須 | 既定値 | 意味 |
|---|---|---|---|
| `--path <path>` | いいえ | 指定なし | 対象パスで絞り込みます。 |
| `--recursive` | いいえ | 指定なし | 再帰的な一覧を要求します。 |
| `--cursor <cursor>` | いいえ | 指定なし | 次ページ取得に使うカーソルです。 |
| `--limit <n>` | いいえ | 指定なし | 1ページの最大項目数です。整数を指定します。 |
| `--order-by <field>` | いいえ | 指定なし | `name`、`size`、`createdAt`、`updatedAt`で並び順の対象を指定します。 |
| `--order-direction <dir>` | いいえ | 指定なし | `asc`または`desc`を指定します。 |

```bash
jai-studio storage list --path /reports --recursive --limit 50 --order-by updatedAt --order-direction desc --pretty
```

応答の`nextCursor`がnull以外なら、その値を次の`--cursor`へ渡します。

```text
jai-studio storage list --cursor '<nextCursor>'
```

## 2. ファイルのメタデータと署名付きURLを取得する

```text
jai-studio storage metadata <fileId>
```

| 位置引数・フラグ | 必須 | 既定値 | 意味 |
|---|---|---|---|
| `<fileId>` | はい | — | 対象ファイルのIDです。 |

メタデータにはファイルID、名前、パス、MIMEタイプ、サイズが含まれます。

```bash
jai-studio storage metadata file-id
```

```text
jai-studio storage download-url <fileId> [--expires-in-minutes <n>]
```

| 位置引数・フラグ | 必須 | 既定値 | 意味 |
|---|---|---|---|
| `<fileId>` | はい | — | 対象ファイルのIDです。 |
| `--expires-in-minutes <n>` | いいえ | 指定なし | URLの有効時間を分単位の整数で要求します。 |

```bash
jai-studio storage download-url file-id --expires-in-minutes 30
```

返却される`downloadUrl`は恒久URLではありません。`expiresAt`と`expiresInMinutes`を確認し、必要な時点で取得します。指定できる時間の下限・上限はCLIでは検証していないため、未確認の値を前提にしません。

> 署名付きURLは認証情報に準じて扱います。チケット、公開ドキュメント、ソースコードへ貼り付けず、期限切れURLを再利用しないでください。

## 3. フォルダを操作する

```text
jai-studio storage folders list <folderId> [--recursive] [--cursor <cursor>] [--limit <n>]
```

| 位置引数・フラグ | 必須 | 既定値 | 意味 |
|---|---|---|---|
| `<folderId>` | はい | — | 対象フォルダのIDです。 |
| `--recursive` | いいえ | 指定なし | 再帰的な一覧を要求します。 |
| `--cursor <cursor>` | いいえ | 指定なし | ページ取得用カーソルです。 |
| `--limit <n>` | いいえ | 指定なし | 1ページの最大項目数です。整数を指定します。 |

```bash
jai-studio storage folders list folder-id --recursive --limit 100
```

```text
jai-studio storage folders create --name <name> [--path <path>]
```

| フラグ | 必須 | 既定値 | 意味 |
|---|---|---|---|
| `--name <name>` | はい | — | 作成するフォルダ名です。 |
| `--path <path>` | いいえ | 指定なし | 親パスです。 |

```bash
jai-studio storage folders create --name archive --path /reports
```

フォルダ名は、Storageの共通制限に従います。名前は前後の空白を除き、空文字、`.`、`..`、`/`・`\`を含む名前は使えません。制御文字は除去され、255文字を超える名前も使えません。

## 4. ファイルまたはフォルダを削除する

```text
jai-studio storage delete <fileId>
```

| 位置引数 | 必須 | 既定値 | 意味 |
|---|---|---|---|
| `<fileId>` | はい | — | 削除するファイルのIDです。 |

```bash
jai-studio storage delete file-id
```

```text
jai-studio storage folders delete <folderId>
```

| 位置引数 | 必須 | 既定値 | 意味 |
|---|---|---|---|
| `<folderId>` | はい | — | 削除するフォルダのIDです。 |

```bash
jai-studio storage folders delete folder-id
```

フォルダ削除は配下の項目も対象です。削除前に一覧・メタデータでID、パス、参照元を確認してください。削除結果の`deletedCount`は削除された項目数です。

## よくある誤解

### `storage download-url`はファイルをローカルへ保存する

いいえ。このコマンドは期限付きURLをJSONで返すだけです。内容を直接取得する`storage download`はCLIに実装されていません。取得処理が必要なら、SDKの`download()`または利用環境に適した方法を選びます。

### `--limit`に任意の文字列や小数を指定できる

できません。`--limit`と`--expires-in-minutes`は安全な整数として解析されます。ただしCLIはページサイズやURL有効時間の範囲を独自に制限しません。サーバー側の条件に従い、エラー時は値と対象を確認します。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 認証・接続エラーになる | 対象Project、プロファイル、接続先、APIキーの有効期限・スコープ・失効を確認します。 |
| 一覧が空、または対象が見つからない | `--path`、`--recursive`、カーソル、対象Projectを確認します。空の結果だけで不存在と断定しません。 |
| 署名付きURLで取得できない | `fileId`、URLの期限、利用者の権限を確認し、新しいURLを取得します。URL自体は共有しません。 |
| 削除を誤った可能性がある | 同じIDやパスへ推測で書き戻さず、対象、実行時刻、出力を記録して管理担当者へ連絡します。 |

## チェックリスト

- [ ] 対象Projectと実行プロファイルを確認した
- [ ] 一覧JSONから対象の`fileId`または`folderId`を確認した
- [ ] 署名付きURLの期限を確認し、不要な共有をしていない
- [ ] CLIに`upload`・`download`がないことを確認し、SDK操作と混同していない
- [ ] 削除前に対象パス、配下、参照元を確認した

## 次に読む記事

- [SDK Storage](../development/sdk-storage.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [CLIの設定・プロファイル](cli-config-and-profiles.md)
