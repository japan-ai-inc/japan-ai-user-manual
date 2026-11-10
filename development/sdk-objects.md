# SDK Objects

Studio SDKの`objects`モジュールでは、Object定義、フィールド、レコードをTypeScriptから操作できます。`define<T>()`でレコードのデータ形を型に結び付け、作成・取得・更新・検索の入出力を扱えます。型は実データの検証・権限・Object制約に代わりません。

制約は[Objectの仕様・制限](../build/object-specs-and-limits.md)を正本として参照してください。SQL・HTTP API直接呼出しは対象外です。

## 関連記事

- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Objects API](../api/objects-api.md)
- [Object SQLのDDL](../object-sql/object-sql-ddl.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)

## 始める前に

- Node.jsでのSDK／CLI利用は22以降です。
- APIキーは安全な環境変数から渡し、ブラウザ、公開環境変数、ログには置きません。PRSブラウザではAPIキーを設定せず、`baseUrl: ""`で初期化します。
- Object ID、フィールド名、型を確認し、構造変更前に影響するレコード・連携先を確認します。制約は[Objectの仕様・制限](../build/object-specs-and-limits.md)に従います。

```bash
npm install @japan-ai-inc/studio-sdk
```

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const apiKey = process.env["JAI_STUDIO_API_KEY"];
if (!apiKey) throw new Error("Project APIキーが設定されていません");
const studio = createClient({ baseUrl: "https://api.japan-ai.co.jp", apiKey });
```

> APIキーは秘密情報です。実値をサンプル、例外メッセージ、問い合わせ、テスト記録に含めないでください。CLIのプロファイル設定はSDKの`createClient()`からは読み込まれません。

## 1. Objectを確認し、型付きハンドルを作る

Object定義の一覧、取得、作成、更新、削除には順に`list()`、`get()`、`create()`、`update()`、`delete()`を使います。

レコード操作には`studio.objects.define<T>(objectId)`を作ります。`T`は`RecordItem<T>`の`data`と、`create()`・`update()`のデータ部分に適用されます。`recordId`と`revision`は`data`の外側です。

```ts
type Contact = { name: string; email: string; company?: string };

const definition = await studio.objects.get("contacts");
const contacts = studio.objects.define<Contact>(definition.objectId);

const added = await studio.objects.create({
  objectName: "contacts",
  displayName: "取引先担当者",
});
await studio.objects.update(added.objectId, { description: "営業連絡先" });
```

`list()`は`limit`、`offset`、`cursor`、`includeDeleted`、`includeCount`を指定できます。レコード一覧と`search()`で`includeCount: false`を指定すると一致件数の集計を省略し、応答の`total`は0になります。ページングの`hasNextPage`と`cursor`はそのまま扱えます。未集計のカーソルを使う全ページで同じ指定を続けます。

## 2. レコードを作成・取得・更新・削除する

`create(data)`は作成、`get(recordId)`は1件取得を行います。`update(recordId, data)`は部分更新ではなく、**完全な`data`の置換**です。通常フィールドを省くとサーバー側で削除されるため、残すフィールドをすべて渡して更新してください。

```ts
const created = await contacts.create({
  name: "山田 花子", email: "hanako@example.example",
});
const current = await contacts.get(created.recordId);
const updated = await contacts.update(current.recordId, {
  ...current.data, company: "JAPAN AI",
});
await contacts.delete(updated.recordId);
```

複数レコードには`bulkCreate(records)`を使え、1回は**1,000レコードまで**です。[Objectの仕様・制限](../build/object-specs-and-limits.md)のCSV取込・内部一括処理単位と同じです。大量投入は重複を考慮して分割してください。

```ts
await contacts.bulkCreate([
  { name: "佐藤 一郎", email: "ichiro@example.example" },
  { name: "鈴木 次郎", email: "jiro@example.example" },
]);
```

> `delete()`は復元手順を提供するメソッドではありません。Object、フィールド、レコードの削除影響と画面上の条件は、実行前に[Objectの仕様・制限](../build/object-specs-and-limits.md)で確認してください。

### 複数レコードを削除する

`bulkDelete(recordIds)`（SDK 0.10.0以降）は、対応する公開APIがある接続先で、最大1,000件のレコードを1回で削除します。キーには`object:delete`スコープが必要です。削除不能なレコードが1件でもあれば全件を削除せずに失敗します。すでに存在しないIDはスキップされ、結果の`skippedIds`と`skippedCount`で確認できます。

SDK 0.11.0は`DELETE /api/v1/objects/:objectId/records/bulk`を送ります。確認できた公開REST APIには、同じパスの一括**作成**（`POST`）はありますが、削除の実装は確認できていません。利用前に、接続先がこの削除経路を提供することを確認してください。

```ts
const result = await contacts.bulkDelete(["record-id-1", "record-id-2"]);
console.log(result.deletedIds, result.deletedCount);
console.log(result.skippedIds, result.skippedCount);
```

結果が不明な通信失敗では、同じ削除を直ちに送信せず、対象レコードを再確認します。

### 同時更新を検出する

`revision`がある場合、その値を`expectedRevision`として更新・削除に渡せます。更新済みならHTTP 409のRevision conflictで失敗します。`revision`がない場合は比較更新を要求しません。

```ts
const record = await contacts.get("record-id");
if (record.revision === undefined) throw new Error("revisionを取得できません");

await contacts.update(
  record.recordId,
  { ...record.data, company: "更新後の会社名" },
  undefined,
  { expectedRevision: record.revision },
);
```

`expectedRevision`を省略した更新はlast-write-wins、削除は無条件です。競合時は最新レコードと業務上の正しい値を確認します。

## 3. 条件・並び順・ページングで検索する

`search()`には`filters`、`sort`、`limit`、`offset`、`cursor`、`includeDeleted`を渡せます。フィルターは`field`、`operator`、必要に応じて`value`を持ちます。

```ts
const page = await contacts.search({
  filters: [
    { field: "company", operator: "contains", value: "AI" },
    { field: "email", operator: "isnotnull" },
  ],
  sort: [{ field: "name", direction: "asc" }],
  limit: 50,
});

if (page.hasNextPage && page.cursor) {
  await contacts.search({
    filters: [{ field: "company", operator: "contains", value: "AI" }],
    sort: [{ field: "name", direction: "asc" }],
    limit: 50,
    cursor: page.cursor,
  });
}
```

`sort.direction`には`asc`、`desc`、`ascending`、`descending`を指定できます。

## レコードアクセスルールを扱う

`objects.recordRules.list|create|delete`は、Studioの「アクセスルール」と同じレコードアクセスルールを扱います。キーには`object:policy`が必要で、キー作成者にも対象Objectのポリシーを読む・設定する権限が必要です。作成・削除はObject書込み権限も必要です。ルールの優先順位と削除の影響を、画面上の既存ルールを含めて確認します。

```ts
const { roles, items } = await studio.objects.recordRules.list("contacts");
const staff = roles.find(role => role.name === "Staff");
if (!staff) throw new Error("対象ロールがありません");

const created = await studio.objects.recordRules.create("contacts", {
  name: "本人作成レコード",
  status: "edit",
  conditions: [{ field: "created_by", operator: "eq", value: "$principal.id", valueType: "variable" }],
  principals: [{ principalType: "role", principalId: staff.id }],
});
await studio.objects.recordRules.delete("contacts", created.ruleGroupId);
```

ルールは優先順に評価されます。部分的な書込み・削除のエラーでは`code`と`params`を記録し、推測で再実行せず、`list()`で現在のルールを確認します。

## 4. フィールド定義を扱う

フィールドは`listFields()`、`createField()`、`updateField()`、`deleteField()`で扱います。`createField()`には`fieldName`、`displayName`、`dataType`、1から始まる正の整数の`fieldNum`が必要です。

```ts
const fields = await contacts.listFields();
const phone = await contacts.createField({
  fieldName: "phone",
  displayName: "電話番号",
  dataType: "phone",
  fieldNum: fields.length + 1,
});
await contacts.updateField(phone.fieldId, { displayName: "連絡先電話番号" });
```

型や制約の変更は既存レコードへ影響します。許容値・上限・変更条件は、[Objectの仕様・制限](../build/object-specs-and-limits.md)および[Objects API](../api/objects-api.md)で確認してください。フィールド削除は保存済みの値にも影響します。

## よくある誤解

### `define<T>()`が実行時の入力を検証する

`T`はSDKの入出力を型付けするためのものです。必須、重複、アクセス権、フィールド制約を保証しません。作成・更新結果とエラーを処理します。

### `update()`は指定フィールドだけを変更する

`update()`は完全な`data`を置き換えます。残す通常フィールドを省略すると削除されます。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| Objectまたはレコードを取得できない | Object ID、対象Project、実行モード、認証、閲覧権限を確認します。 |
| 作成・更新でエラーになる | フィールド名、型、必須・一意性・リレーション、既存データとの整合を確認します。 |
| 更新後に値が消えた | `update()`へ渡した`data`が完全か確認します。 |
| Revision conflictになる | 最新レコードを再取得し、競合した変更を確認してから操作します。 |
| 検索が失敗する、結果が少ない | フィールド名・演算子・値、並び順、ページング、削除済みを含める指定、権限を確認します。 |
| 一括作成が失敗する | 1回が1,000レコード以下か、各値が定義に合うか確認します。 |

問い合わせ時は対象Object、SDKバージョン、実行モード、メソッド、エラーを記録し、APIキーや個人情報は送らないでください。

## チェックリスト

- [ ] フィールド名と`T`のプロパティ名が一致している
- [ ] APIキーを安全なサーバー側設定に置き、ブラウザやログへ出していない
- [ ] 更新時には残すフィールドを含む完全な`data`を渡している
- [ ] 同時更新が問題になる処理では`revision`と`expectedRevision`を確認している
- [ ] 検索ではフィルター・並び順・カーソルを一貫して扱っている
- [ ] 一括作成は1回あたり1,000レコード以下に分割している
- [ ] 構造変更・削除前に[Objectの仕様・制限](../build/object-specs-and-limits.md)の制約と影響を確認している

## 次に読む記事

- [Objects API](../api/objects-api.md)
- [Object SQLのDDL](../object-sql/object-sql-ddl.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
