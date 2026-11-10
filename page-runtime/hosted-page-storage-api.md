# Hosted PageのStorage API

Hosted Pageのブラウザでは、Page実行環境のセッションでProject Storageを扱います。Project APIキーをコードへ置きません。

## 関連記事

- [SDK Storage](../development/sdk-storage.md)
- [Storage API](../api/storage-api.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [CLI Storage](../cli/cli-storage.md)

## この記事の範囲

この記事は、Hosted Pageの**ブラウザ内**で使うPage相対のStorage APIとStudio SDKの`storage`を扱います。通常画面、ローカルNode.js、Pageのサーバー処理、任意の外部サイトでのブラウザ利用は対象外です。

ブラウザ用コードはPage実行環境でのみ`createClient({ baseUrl: "" })`を使います。空の`baseUrl`では同一オリジンの相対パスを呼び、Pageセッションで認証されます。Project APIキーや認証ヘッダーを追加してはいけません。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const { files, nextCursor } = await studio.storage.list({
  path: "/reports",
  limit: 50,
  orderBy: "updatedAt",
  orderDirection: "desc",
});
```

> この初期化はHosted Pageのブラウザ専用です。ローカルとPageのサーバー処理は[SDK Storage](../development/sdk-storage.md)を参照してください。

## Page相対の経路を選ぶ

ブラウザからSDKが使う入口は`/api/v1/storage`です。`storage`はPage実行環境の応答を`fileId`、`folderId`、`path`などのSDK公開形へ変換します。アプリはSDKメソッドを使います。

| 目的 | SDKメソッド | Page相対の経路 | 備考 |
|---|---|---|---|
| ファイル・フォルダ一覧 | `list()` | `GET /api/v1/storage/files` | `path`、`recursive`、`limit`、`cursor`、並び順を指定します。ブラウザでは1〜100、未指定は50です。 |
| ファイル登録 | `upload()`、`prepareUpload()`、`finalizeUpload()` | `POST /api/v1/storage/files/prepare-upload`、返却URLへの`PUT`、`POST /api/v1/storage/files/finalize-upload` | `upload()`も準備・送信・確定の順です。 |
| 内容の取得 | `download()` | `GET /api/v1/storage/files/{fileId}/content-stream` | 戻り値は`Response`です。 |
| メタデータ | `getMetadata()` | `GET /api/v1/storage/files/{fileId}` | ファイル内容を取得せずに確認します。 |
| 期限付きURL | `getDownloadUrl()` | `GET /api/v1/storage/files/{fileId}/download-url` | `expiresAt`を確認し、恒久URLとして保存・共有しません。 |
| フォルダ | `createFolder()`、`listFolderContents()`、`deleteFolder()` | `POST /api/v1/storage/folders`、`GET /api/v1/storage/folders/{folderId}/entries`、`DELETE /api/v1/storage/folders/{folderId}` | ファイル削除は`deleteFile()`を使います。 |

`/api/storage/...`もPage相対のセッション経路ですが、SDK公開APIの契約ではありません。新規実装は`/api/v1/storage`を使うSDKへ統一し、`/api/v1/studio-storage/...`は指定しません。

## 1. アップロードを確定まで完了する

`upload()`には`File`、`Blob`、またはその配列を渡します。1回は最大20件です。同名を置換する場合だけ`conflictResolution: "replace"`を指定します。未指定時の競合はエラーです。

```ts
const input = document.querySelector<HTMLInputElement>("#report");
const file = input?.files?.[0];
if (!file) throw new Error("ファイルを選択してください");

const result = await studio.storage.upload(file, {
  path: "/reports",
  conflictResolution: "replace",
});
console.log(result.files[0].fileId);
```

大きなファイルや進行状況を制御する実装では、`prepareUpload()`、返却`uploadUrl`への`PUT`、`finalizeUpload()`を順に呼びます。`requiredHeaders`、`storageId`、置換時の`replaceIntent`は返却値のまま使います。確定前は完了済みではありません。失敗時は確認してから再試行してください。

既定の1ファイル上限は2GBです。`/api/storage`経路の本文は32MiBまでです。Page実行環境のSDK `upload()`は署名付きURLを使う準備・確定フローへ切り替わります。保存可否は保存容量、権限、環境設定にも依存します。

## 2. ダウンロードと画面内表示を分ける

内容を処理する場合は`download()`を使い、`Response`から`blob()`、`text()`などを取得します。期限付き取得先には`getDownloadUrl()`を使い、直前に取得します。`expiresInMinutes`省略時の戻り値は60分です。

```ts
const response = await studio.storage.download("file-id");
const blob = await response.blob();

const { downloadUrl, expiresAt } = await studio.storage.getDownloadUrl("file-id", {
  expiresInMinutes: 30,
});
console.log(expiresAt);
```

同一オリジンで安全な形式を表示する専用経路は、`GET /api/v1/storage/files/{fileId}/view`です。PDF、画像、動画、音声、`text/plain`が対象で、SVGは対象外です。50MiB超は表示できません。動画・音声はRange要求によるシークに対応します。`fileId`をURLエンコードし、対象形式・サイズ・権限エラーを処理してください。

```ts
const viewUrl = `/api/v1/storage/files/${encodeURIComponent(fileId)}/view`;
preview.src = viewUrl;
```

`downloadFolder()`によるフォルダZIP取得はブラウザでは非対応です。キーを渡して回避せず、個別ファイルのUIかサーバー処理を検討します。上限は[Storage API](../api/storage-api.md)、画面操作は[Storageの仕様・制限](../build/storage-specs-and-limits.md)を参照してください。

## アクセスモードごとの確認

Storage経路はPageセッションがある要求だけを受け付けます。「サインイン必須」「サインイン任意」「匿名アクセス」「外部顧客認証」では認証・セッションの経路が異なります。経路自体はアクセスモード名で分岐していないため、特定モードで全Storage操作が必ず成功するとは断定できません。

| 「アクセス設定」 | Page相対のStorage要求 | 実装時の確認 |
|---|---|---|
| 「サインイン必須」 | サインイン済みのPageセッションで要求します。 | 対象利用者で一覧、登録、取得、削除の必要範囲だけを確認します。 |
| 「サインイン任意」 | Pageセッションを使う要求です。 | サインイン状態と未サインイン状態の表示・更新可否を検証します。 |
| 「匿名アクセス」 | 匿名のPageセッションで要求します。 | 見せてよいパスと操作だけに絞り、拒否・成功を確認します。 |
| 「外部顧客認証」 | 認証後のPageセッションで要求します。 | 表示範囲・書込み・削除を想定ロールで確認します。 |

> URLや`fileId`を知っていても許可されません。403・404では認証情報を足さず、アクセス設定、対象Project、利用者、対象ファイルを確認してください。

## よくある誤解

### `/api/v1/studio-storage`をブラウザで呼べばよい

いいえ。これはブラウザから指定する公開のPage相対経路ではありません。SDKは`/api/v1/storage`を呼び、Page実行環境が現在の組織・Project・セッションの文脈で接続します。識別子を手入力して他のProjectへアクセスする実装にはしません。

### `uploadUrl`や`downloadUrl`を恒久URLとして保存できる

できません。期限付きURLです。`expiresAt`を確認し、必要な都度取り直します。ソース管理、公開URL、長期保存データ、問い合わせへ入れないでください。

### フォルダZIPだけはAPIキーを追加すればブラウザから使える

できません。`downloadFolder()`はPage実行環境のブラウザで非対応です。キーの追加は秘密情報を露出させるだけです。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 401、403、404になる | アクセスモード、対象Project、Pageセッション、対象ID、利用者を確認します。キーは追加しません。 |
| アップロードが失敗する | 20件、2GB、`/api/storage`の32MiB、保存容量、競合指定、確定結果を確認します。 |
| インライン表示できない | `/view`の対象MIMEタイプか、SVGではないか、50MiB以下か、対象IDを確認します。 |
| フォルダZIP取得が失敗する | ブラウザでは非対応です。個別ファイルのUIかサーバー処理を検討します。 |
| Storage APIが503になる | Storage連携が構成されていない可能性があります。URLや認証を変えず、対象Pageの設定と発生時刻を添えて管理担当者へ確認します。 |

問い合わせには対象Project、Page名、アクセスモード、SDKバージョン、メソッド、対象ID、日時、HTTP状態、再現手順を記録します。APIキー、Cookie、認証ヘッダー、署名付きURL、ファイル内容は共有しません。

## チェックリスト

- [ ] Hosted Pageのブラウザで`createClient({ baseUrl: "" })`を使い、APIキーを含めていない
- [ ] 新規実装を`studio.storage`へ統一し、`/api/storage`や`/api/v1/studio-storage`を直接組み立てていない
- [ ] アップロードは20件以下とし、準備・送信・確定の完了を確認した
- [ ] 保存容量、既定の1ファイル2GB、32MiBの通常プロキシ制限を区別した
- [ ] 期限付きURLの`expiresAt`を確認し、不要に共有していない
- [ ] `/view`のMIMEタイプと50MiBの制限を確認した
- [ ] Page実行環境のブラウザで`downloadFolder()`を使っていない
- [ ] 想定するアクセスモードと利用者で、必要最小限のStorage操作をテストした

## 次に読む記事

- [SDK Storage](../development/sdk-storage.md)
- [Storage API](../api/storage-api.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [CLI Storage](../cli/cli-storage.md)
