# SDK Storage

Studio SDKの`storage`は、ProjectのStorageでファイルとフォルダを操作するモジュールです。実行場所により認証と一部の操作が異なります。

実行モードは[実行モード・認証・対応範囲](execution-modes-and-auth.md)、保存条件は[Storageの仕様・制限](../build/storage-specs-and-limits.md)を参照してください。[Storage API](../api/storage-api.md)と[Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md)で詳細を扱います。

## 関連記事

- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [Storage API](../api/storage-api.md)
- [Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md)

## 始める前に

- SDKはNode.js 22以降を使います。
- ローカルとPRSサーバーではAPIキーをサーバー側だけから渡します。PRSブラウザでは`createClient({ baseUrl: "" })`を使います。
- ファイルIDは一覧またはアップロードの戻り値から受け取ります。

> APIキー、署名付きURLをブラウザ、ログ、ソース管理、問い合わせへ残してはいけません。

## 実行モード別の対応範囲

`downloadFolder()`はPRSブラウザでは使えません。

| 操作 | ローカル | PRSブラウザ | PRSサーバー | 補足 |
|---|---|---|---|---|
| 一覧・メタデータ | 可 | 可 | 可 | `list()`、`listFolderContents()`、`getMetadata()`を使います。 |
| ファイルのアップロード | 可 | 可 | 可 | `upload()`はPRSブラウザでは署名付きURLを使う手順へSDK内部で切り替わります。 |
| ファイルのダウンロード | 可 | 可 | 可 | `download()`は`Response`を返します。 |
| 署名付きダウンロードURL | 可 | 可 | 可 | `getDownloadUrl()`でURLと有効期限情報を取得します。 |
| ファイル・フォルダの削除 | 可 | 可 | 可 | `deleteFile()`、`deleteFolder()`は復元の代わりになりません。 |
| フォルダZIPダウンロード | 可 | 不可 | 可 | PRSブラウザでは`downloadFolder()`がエラーになります。 |

## 1. 一覧を取得する

`list()`では`path`、`recursive`、`limit`、`cursor`、`orderBy`、`orderDirection`を指定できます。

```ts
const result = await studio.storage.list({
  path: "/reports",
  recursive: true,
  limit: 50,
  orderBy: "updatedAt",
  orderDirection: "desc",
});

for (const file of result.files) {
  console.log(file.fileId, file.path, file.sizeBytes);
}

if (result.nextCursor) {
  const next = await studio.storage.list({ cursor: result.nextCursor });
}
```

PRSブラウザでは、一覧のページサイズは1〜100、未指定時は50です。フォルダ内だけを調べる場合は`listFolderContents()`を使います。

## 2. ファイルをアップロードする

`upload()`には`File`、`Blob`、または配列を渡します。`path`は保存先、`conflictResolution: "replace"`は同名ファイルの置換です。

```ts
const input = document.querySelector<HTMLInputElement>("#file");
const file = input?.files?.[0];
if (!file) throw new Error("ファイルを選択してください");

const uploaded = await studio.storage.upload(file, {
  path: "/reports",
  conflictResolution: "replace",
});
console.log(uploaded.files[0].fileId);
```

1回の`upload()`に渡せるファイルは最大20件です。1ファイルは2GBまでで、複数ファイル要求も合計2GBを超えないように分割します。

大きなファイルは、`prepareUpload()`、`uploadUrl`への`PUT`、`finalizeUpload()`の順に実行します。`requiredHeaders`、`storageId`、置換時の`replaceIntent`は戻り値のまま渡します。

```ts
const prepared = await studio.storage.prepareUpload({
  fileName: file.name,
  fileSize: file.size,
  mimeType: file.type,
  path: "/reports",
  conflictResolution: "replace",
});

await fetch(prepared.uploadUrl, {
  method: "PUT",
  headers: prepared.requiredHeaders,
  body: file,
});

const saved = await studio.storage.finalizeUpload({
  storageId: prepared.storageId,
  replaceIntent: prepared.replaceIntent,
  fileName: file.name,
  fileSize: file.size,
  mimeType: file.type,
  path: "/reports",
});
```

> `finalizeUpload()`が成功するまで、アップロードしたデータをStorageファイルとして扱いません。通信失敗時は、URLや`replaceIntent`を推測して再利用しません。

## 3. ダウンロードと署名付きURLを使い分ける

内容を処理する場合は`download()`を使い、`Response`から`blob()`、`text()`などを取得します。一時的な取得先には`getDownloadUrl()`で署名付きURLと`expiresAt`を取得します。PRSブラウザで`expiresInMinutes`を省略した場合は60分です。

```ts
const response = await studio.storage.download("file-id");
const blob = await response.blob();

const { downloadUrl, expiresAt } = await studio.storage.getDownloadUrl(
  "file-id",
  { expiresInMinutes: 30 }
);
console.log(downloadUrl, expiresAt);
```

`downloadFolder()`はローカルとPRSサーバーだけで利用できます。フォルダZIPの制約は[Storageの仕様・制限](../build/storage-specs-and-limits.md)を確認してください。

## 4. 削除とフォルダ操作を行う

フォルダは`createFolder()`・`deleteFolder()`、ファイルは`deleteFile()`で扱います。削除前に対象IDとパスを確認してください。

```ts
const folder = await studio.storage.createFolder({
  folderName: "archive",
  path: "/reports",
});

await studio.storage.deleteFile("file-id");
const deleted = await studio.storage.deleteFolder(folder.folderId);
console.log(deleted.deletedCount);
```

> 削除前に対象と参照元を確認してください。

## よくある誤解

### PRSブラウザならAPIキーを追加してフォルダZIPを取得できる

できません。PRSブラウザでは`downloadFolder()`は非対応です。必要な処理はPRSサーバーへ置くか、個別ファイルの操作へ変更します。

### 署名付きURLは恒久的なファイルURLである

違います。アップロード用URLとダウンロード用URLには有効期限があります。必要な時点で再取得します。

### `upload()`が成功すれば、ファイル数・容量・保存容量を確認しなくてよい

違います。`upload()`は最大20ファイル、1ファイルは2GBまでです。保存容量や権限も結果に影響します。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 認証エラーになる | 対象Project、APIキー、実行場所を確認します。 |
| PRSブラウザのStorage操作が失敗する | PRS内で実行し、`baseUrl: ""`で初期化しているか確認します。 |
| アップロードが失敗する | ファイル数、サイズ、保存容量、権限、`path`、競合指定を確認します。 |
| `finalizeUpload()`が失敗する | `storageId`、ファイル情報、`replaceIntent`、PUTの結果を確認します。 |
| PRSブラウザで`downloadFolder()`が失敗する | 非対応です。個別ダウンロードまたはPRSサーバー処理へ変更します。 |
| 削除対象を誤った可能性がある | 対象パスと実行記録を確認して管理担当者へ連絡します。 |

問い合わせには、実行モード、SDKバージョン、対象ID、操作日時、エラー、再現手順を記録します。APIキー、Cookie、署名付きURL、ファイル内容は添付しません。

## チェックリスト

- [ ] ローカル、PRSブラウザ、PRSサーバーのいずれで動かすかを決めた
- [ ] PRSブラウザを`createClient({ baseUrl: "" })`で初期化し、APIキーを含めていない
- [ ] 一覧の戻り値から対象の`fileId`または`folderId`を確認した
- [ ] アップロードは20件以下、1ファイル・合計要求サイズを2GB以下として扱った
- [ ] 署名付きURLの有効期限と、`finalizeUpload()`の完了を確認した
- [ ] PRSブラウザでは`downloadFolder()`を使っていない
- [ ] 削除前に対象パス、参照元、影響範囲を確認した

## 次に読む記事

- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [Storage API](../api/storage-api.md)
- [Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
