# Storage API

Storage APIは、Project StorageのファイルとフォルダをHTTPで一覧、登録、取得、削除する公開APIです。SDKでの実装は[SDK Storage](../development/sdk-storage.md)、画面上の保存条件は[Storageの仕様・制限](../build/storage-specs-and-limits.md)を参照してください。

## 関連記事

- [SDK Storage](../development/sdk-storage.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [CLI Storage](../cli/cli-storage.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md)

## 始める前に

ベースURLは`https://api.japan-ai.co.jp`、主経路は`/api/v1/storage`です。以下はこの主経路です。Project APIキーは`x-api-key`または`Authorization: Bearer <キー>`で渡し、操作に必要なスコープだけを付与します。

```bash
export JAI_STUDIO_API_KEY='安全な保管場所から渡す値'
curl -H "x-api-key: $JAI_STUDIO_API_KEY" \
  'https://api.japan-ai.co.jp/api/v1/storage/files?path=/reports&limit=50'
```

> APIキー、Bearer値、署名付きURLをブラウザ、ソース管理、ログ、チケットへ残してはいけません。Hosted Pageのブラウザからの利用は別の認証・経路になります。[Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md)を確認してください。

## 応答とエラーの共通事項

JSON応答には原則`success`と`requestId`が含まれます。IDは作成・一覧応答から取得します。認証不備は401または403、入力不備は400、対象不在は404です。429の場合は応答の`retryAfterSeconds`を待機の目安にします。詳細は[API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)を参照してください。

パスは省略時に`/`です。`..`を含むパスは使えず、連続する`/`は正規化されます。ファイル名・フォルダ名は前後の空白を除いた1〜255文字で、パス区切り、制御文字、`.`、`..`などは受け付けられません。

## 1. ファイルとフォルダを一覧する

`GET /api/v1/storage/files`には`storage:read`が必要です。完了済みのファイルとフォルダを返します。

| パラメーター | 場所 | 必須 | 既定値・範囲 | 意味 |
|---|---|---:|---|---|
| `path` | query | いいえ | `/` | 一覧対象のパスです。 |
| `recursive` | query | いいえ | `false` | `true`ならサブフォルダも含めます。 |
| `limit` | query | いいえ | 50、1〜100 | 1ページの最大件数です。 |
| `cursor` | query | いいえ | — | 前応答の`nextCursor`です。 |
| `orderBy` | query | いいえ | `updatedAt` | `name`、`size`、`createdAt`、`updatedAt`です。 |
| `orderDirection` | query | いいえ | `desc` | `asc`または`desc`です。 |

応答は`files`（ID、名前、パス、サイズ、MIMEタイプ、日時）、`folders`（ID、名前、パス）、このページの`totalCount`、`nextCursor`です。`nextCursor`が`null`でなければ、その値を次の要求へ渡します。

## 2. ファイルをアップロードする

### multipartで直接送る

`POST /api/v1/storage/files`には`storage:write`が必要です。`multipart/form-data`の`files`へ1件以上を渡します。1回は最大20ファイル、各ファイルと合計サイズはいずれも3GBまでです。`path`はqueryで指定します。本文の`path`も互換用に受け付けますが、queryを使います。`conflictResolution`は未指定または`error`では同名をエラーにし、`replace`では置換を要求します。

```bash
curl -X POST -H "x-api-key: $JAI_STUDIO_API_KEY" \
  -F 'files=@./report.pdf' \
  'https://api.japan-ai.co.jp/api/v1/storage/files?path=/reports&conflictResolution=replace'
```

成功時の`files`にはID、名前、パス、サイズ、MIMEタイプ、状態が含まれます。

### 署名付きURLで送る

準備、返却URLへの送信、確定の順で実行します。`POST /api/v1/storage/files/prepare-upload`と`POST /api/v1/storage/files/finalize-upload`はいずれも`storage:write`が必要です。

| API | 必須本文 | 任意本文 | 主な応答／結果 |
|---|---|---|---|
| `prepare-upload` | `fileName`、正の整数の`fileSize` | `mimeType`、`path`、`metadata`、`md5`、`conflictResolution` | `storageId`、`uploadUrl`、`requiredHeaders`、`expiresAt`、`logicalPath`。`replace`指定時だけ`replaceIntent`が返る場合があります。 |
| `finalize-upload` | `storageId`、`fileName`、正の整数の`fileSize` | `mimeType`、`path`、`metadata`、`md5`、`replaceIntent` | `file`（`fileId`、名前、パス、サイズ、MIMEタイプ、状態）。 |

`prepare-upload`の`fileSize`は2GB以下です。`uploadUrl`へ`requiredHeaders`をそのまま付けて送信し、同じファイル情報と`storageId`を確定APIへ渡します。置換時の`replaceIntent`も変更せず渡します。確定前は完了済みファイルとして扱いません。確定時はサイズまたはMD5不一致で400、準備済みアップロードまたは保存先が見つからないと404です。

## 3. 取得、メタデータ、期限付きURL

いずれも`storage:read`が必要で、`fileId`は必須です。

| メソッド・パス | 用途 | パラメーター | 主な成功応答 | 状態 |
|---|---|---|---|---|
| `GET /api/v1/storage/files/:fileId` | 内容をダウンロード | `fileId` | ファイル本体。`Content-Type`、添付ファイル名、長さを返します。 | 200、404 |
| `GET /api/v1/storage/files/:fileId/metadata` | 内容を送らずに確認 | `fileId` | `file.fileId`、`fileName`、`path`、`mimeType`、`sizeBytes` | 200、404 |
| `GET /api/v1/storage/files/:fileId/download-url` | 期限付きURLを発行 | 任意の`expiresInMinutes` | `downloadUrl`、`expiresAt`、`expiresInMinutes` | 200、400、404 |

`expiresInMinutes`は省略すると60分です。正の整数を指定し、最大1440分に丸められます。`downloadUrl`は恒久URLではないため、`expiresAt`を確認し、必要な時点で取り直してください。

## 4. フォルダを操作し、ZIPを取得する

| メソッド・パス | スコープ | 入力 | 主な応答・制約 |
|---|---|---|---|
| `POST /api/v1/storage/folders` | `storage:write` | 本文`folderName`必須、`path`・`metadata`任意 | `folder`のID、名前、パス。親パスがなければ作成されます。 |
| `GET /api/v1/storage/folders/:folderId/entries` | `storage:read` | `folderId`、任意の`recursive`、`limit`、`cursor` | ファイル・フォルダ一覧、`nextCursor`。`limit`は50、1〜100です。 |
| `GET /api/v1/storage/folders/:folderId` | `storage:read` | `folderId`、任意の`maxSizeMb` | ZIP本体。既定100MB、最大500MB。空・上限超過は400です。 |
| `DELETE /api/v1/storage/folders/:folderId` | `storage:delete` | `folderId` | `folderId`、`deletedCount`。配下を含めて削除します。 |

ZIPの`maxSizeMb`は1以上の整数です。上限超過値は500MBとして扱われ、空・上限超過フォルダは取得できません。削除はファイルでも`DELETE /api/v1/storage/files/:fileId`を使い、`storage:delete`が必要です。成功時は`fileId`とメッセージを返し、存在しない対象は404です。

> 削除は影響範囲を確認してから実行してください。フォルダ削除は配下も対象です。通常画面での復元操作は確認できません。

## よくある誤解

### `downloadUrl`を保存して恒久リンクとして使える

できません。URLには期限があります。利用直前に取得し、返却された有効期限を確認します。

### prepareだけ成功すればアップロードは完了している

違います。返却URLへの送信後に`finalize-upload`が成功して初めて、APIで完了済みファイルとして扱います。

### `storage:write`があれば削除もできる

違います。ファイル・フォルダの削除には`storage:delete`が必要です。読み取りには`storage:read`を使います。

## うまくいかない場合

400では入力、401/403ではProject・キー・スコープ、404ではIDを確認します。429では`retryAfterSeconds`を確認し、連続再送を避けます。通信断後の書込みは、一覧・メタデータ・確定結果を確認してから扱います。

問い合わせには、対象Project、API経路、`requestId`、対象ID、実行時刻、HTTP状態を記録します。APIキー、Authorizationヘッダー、署名付きURL、ファイル内容は添付しません。

## チェックリスト

- [ ] Project APIキーをサーバー側の安全な設定から渡した
- [ ] 操作ごとに`storage:read`、`storage:write`、`storage:delete`の最小スコープを選んだ
- [ ] 一覧・作成応答から`fileId`または`folderId`を取得した
- [ ] multipartは20ファイル以下、各ファイルと合計を3GB以下として送った
- [ ] 署名付きアップロードは確定まで完了した
- [ ] 期限付きURLの`expiresAt`を確認した
- [ ] 削除前に対象と配下を確認した

## 次に読む記事

- [SDK Storage](../development/sdk-storage.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [CLI Storage](../cli/cli-storage.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md)
