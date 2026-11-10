# Objects API

Objects APIは、ProjectのObject定義、フィールド、レコードをHTTPで操作する公開REST APIです。構造・削除は[Objectの仕様・制限](../build/object-specs-and-limits.md)、SDKは[SDK Objects](../development/sdk-objects.md)を参照してください。

## 関連記事

- [SDK Objects](../development/sdk-objects.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)

## 始める前に

ベースURLは`https://api.japan-ai.co.jp`、共通パスは`/api/v1/objects`です。操作に応じて`object:read`、`object:write`、`object:delete`を付与します。

認証には`x-api-key`ヘッダーへ実値を入れます。`Authorization: Bearer <キー>`も受け付けます。キーをブラウザ、コード、ログ、問い合わせに含めません。

```bash
export JAI_STUDIO_API_KEY='（Project APIキー）'
export JAI_OBJECT_ID='（Object ID）'
curl -sS "https://api.japan-ai.co.jp/api/v1/objects" \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

> Object名・フィールド名は先頭を英字とし、英数字とアンダースコアだけで指定します。構造・削除は[Objectの仕様・制限](../build/object-specs-and-limits.md)で確認してください。

## この記事の範囲

本記事はProject APIキーで呼び出す定義、フィールド、レコード、検索、一括作成を扱います。CSV、SQL、権限設定、構造設計は対象外です。App提供のObjectは構造変更が403になることがあります。

## 共通の応答・ページング

一覧・検索の主な応答は`data`、`total`、`limit`、`cursor`、`hasNextPage`、`queryTimestamp`です。次ページは同じ条件・並び順で`cursor`を渡します。`limit`・`offset`は数値文字列、`include_deleted`は`true`または`false`です。

成功時は`GET`・`PATCH`・検索は200、`POST`は201、削除は204です。入力不正は400、認証・スコープ・権限不足は403、対象不在は404、`expectedRevision`不一致は409です。読み取りが429になった場合は`retryAfterSeconds`と`Retry-After`に従います。再試行・エラーは[API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)を参照してください。

## 1. Object定義を操作する

| 操作 | メソッド・パス | 必要スコープ | 主なパラメーター／応答 |
|---|---|---|---|
| 一覧 | `GET /api/v1/objects` | `object:read` | query: `limit`、`offset`、`cursor`、`include_deleted`（すべて任意）。ページ応答。 |
| 作成 | `POST /api/v1/objects` | `object:write` | body: 必須`objectName`、任意`displayName`、`description`。Object定義を返します。 |
| 取得 | `GET /api/v1/objects/{objectId}` | `object:read` | path: 必須`objectId`。Object定義を返します。 |
| 更新 | `PATCH /api/v1/objects/{objectId}` | `object:write` | body: 任意`objectName`、`displayName`、`description`。後二者は`null`で解除できます。Object定義を返します。 |
| 削除 | `DELETE /api/v1/objects/{objectId}` | `object:delete` | path: 必須`objectId`。成功時は204、本文はありません。 |

応答は`objectId`、`objectName`、`displayName`、`description`、`createdAt`、`updatedAt`です。

## 2. フィールドを操作する

| 操作 | メソッド・パス | 必要スコープ | 主なパラメーター／応答 |
|---|---|---|---|
| 一覧 | `GET /api/v1/objects/{objectId}/fields` | `object:read` | queryはObject一覧と同じ。配列またはページ応答のフィールド定義を返します。 |
| 作成 | `POST /api/v1/objects/{objectId}/fields` | `object:write` | body: 必須`fieldName`、`displayName`、`dataType`、`fieldNum`。`fieldNum`は1以上の整数です。 |
| 更新 | `PATCH /api/v1/objects/{objectId}/fields/{fieldId}` | `object:write` | body: 表示名、フラグ、参照先、既定値、範囲、検証ルールなど変更項目。 |
| 削除 | `DELETE /api/v1/objects/{objectId}/fields/{fieldId}` | `object:delete` | 成功時は204、本文はありません。 |

`dataType`は`text`、`string`、`number`、`boolean`、`datetime`、`date`、`json`、`array`、`relationship`、`file`、`select`、`multiselect`、`url`、`email`、`phone`、`auto_increment`です。`maxLength`は1〜65,535の整数です。応答は`fieldId`、`fieldName`、`displayName`、`dataType`、`fieldNum`、各`is...`フラグ、`refObjectId`、`refFieldId`、`defaultValue`、`validationRules`です。

`validationRules`は配列で全置換、空配列で解除、未指定なら保持です。

## 3. レコードを操作する

レコード本文は`{"data": {"フィールド名": 値}}`です。`data`内のキーは変換されません。応答は`recordId`、`data`、`revision`、`createdAt`、`updatedAt`です。

| 操作 | メソッド・パス | 必要スコープ | パラメーター／成功時 |
|---|---|---|---|
| 一覧 | `GET /api/v1/objects/{objectId}/records` | `object:read` | queryは共通一覧と同じ。ページ応答。 |
| 作成 | `POST /api/v1/objects/{objectId}/records` | `object:write` | body: 必須`data`。作成したレコード。 |
| 取得 | `GET /api/v1/objects/{objectId}/records/{recordId}` | `object:read` | path: 必須`recordId`。レコード。 |
| 更新 | `PATCH /api/v1/objects/{objectId}/records/{recordId}` | `object:write` | body: 必須`data`、任意`expectedRevision`。レコード。 |
| 削除 | `DELETE /api/v1/objects/{objectId}/records/{recordId}` | `object:delete` | query: 任意`expectedRevision`。成功時204、本文なし。 |

`PATCH`の`data`は部分更新ではなく**完全置換**です。保持するフィールドも含めます。`expectedRevision`は1〜2,147,483,647の整数です。取得した`revision`を更新本文または削除queryへ渡し、不一致なら409です。省略時、更新はlast-write-wins、削除は無条件です。競合時は最新レコードを取得します。

> SDK 0.11.0には`objects.define(objectId).bulkDelete(recordIds)`がありますが、この公開REST APIの確認対象には対応する一括削除経路がありません。公開REST APIでは、対象ごとの`DELETE /api/v1/objects/{objectId}/records/{recordId}`を使います。PRSのObjects経路とは区別してください。

```bash
curl -sS -X PATCH \
  "https://api.japan-ai.co.jp/api/v1/objects/$JAI_OBJECT_ID/records/RECORD_ID" \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'Content-Type: application/json' \
  -d '{"data":{"name":"山田 花子","email":"hanako@example.example"},"expectedRevision":7}'
```

## 4. 検索・一括作成

| 操作 | メソッド・パス | 必要スコープ | 本文・既定値 |
|---|---|---|---|
| 検索 | `POST /api/v1/objects/{objectId}/records/search` | `object:read` | `filters`、`sort`、`limit`、`offset`、`cursor`、`includeDeleted`は任意。`limit`既定50（1〜1,000）、`offset`既定0、`includeDeleted`既定`false`。ページ応答。 |
| 一括作成 | `POST /api/v1/objects/{objectId}/records/bulk` | `object:write` | body: `[{"data": {...}}, ...]`。1件以上、**1,000件以下**。作成レコードの配列またはその`data`配列を返します。 |

フィルターは`field`、`operator`、任意の`value`です。`operator`は`eq`、`ne`、`gt`、`gte`、`lt`、`lte`、`contains`、`startswith`、`endswith`、`in`、`notin`、`isnull`、`isnotnull`、`exists`、`regex`、`between`、`jsoncontains`です。`sort`は`field`と任意`direction`で、既定`asc`です。`cursor`は`offset`より優先されます。

## 5. レコードアクセスルールを管理する

レコードアクセスルールは、Objectごとの閲覧・編集範囲を管理するためのAPIです。SDKでは`objects.recordRules.list|create|delete`を使えます。

| 操作 | メソッド・パス | 必要スコープ |
|---|---|---|
| 一覧 | `GET /api/v1/objects/{objectId}/record-rules` | `object:read`、`object:policy` |
| 作成 | `POST /api/v1/objects/{objectId}/record-rules` | `object:write`、`object:policy` |
| 削除 | `DELETE /api/v1/objects/{objectId}/record-rules/{ruleGroupId}` | `object:write`、`object:policy` |

キーの作成者にも対象Objectのポリシーを読む・設定する権限が必要です。作成時は`name`、`status`（`hidden`、`read`、`edit`）、`conditions`、`principals`を指定し、必要に応じて`conditionLogic`（`AND`または`OR`）と`priority`を指定します。`principals`は`role`、`user`、`agent`を指定できます。既存ルールと優先順位、削除時の利用者への影響を確認してから変更してください。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 400 | JSON、必須項目、名前、`fieldNum`、検索条件、1,000件以下の一括作成を確認します。 |
| 403 | キー形式・有効性、対象Project、必要な`object:*`スコープ、権限を確認します。 |
| 404／409 | IDまたは`expectedRevision`と最新レコードを確認してから操作します。 |
| 422 | 一覧・検索結果が大きすぎる場合があります。`limit`を下げるか条件を絞ります。1件だけが大きすぎる場合は、Project管理者に内容の見直しを依頼します。 |
| レコード削除またはアクセスルール変更が失敗する | 対象ID、`object:delete`または`object:policy`、キー作成者のObject権限、既存ルール・リレーション・対象への影響を確認します。状態不明の書込みをそのまま再送しません。 |
| 429 | `retryAfterSeconds`と`Retry-After`に従い、待機後に回数を抑えて再試行します。 |

> 書込みがタイムアウトした場合は無条件で再送せず、現在状態を確認します。

## チェックリスト

- [ ] APIキーを安全なサーバー側設定に置き、最小の`object:*`スコープを付与した
- [ ] Object名・フィールド名・型・リレーションの条件を[Objectの仕様・制限](../build/object-specs-and-limits.md)で確認した
- [ ] レコード更新では残す全フィールドを含む`data`を渡す
- [ ] 同時更新がある処理では`revision`と`expectedRevision`を扱う
- [ ] 一括作成は1回1,000件以下に分割した。公開REST APIでレコードを削除する場合は対象ごとの削除経路を使う
- [ ] レコードアクセスルールを変更する場合は、既存ルール、優先順位、利用者への影響を確認した
- [ ] 検索では同じ条件と並び順でcursorを引き継ぐ

## 次に読む記事

- [SDK Objects](../development/sdk-objects.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
