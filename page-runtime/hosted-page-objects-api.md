# Hosted PageのObjects API

Hosted Pageのブラウザコードでは、Studio SDKの`objects`モジュールを使って、Pageが属するProjectのObject定義、フィールド、レコードを操作できます。これはPage実行環境の相対パスを通る連携であり、公開REST APIへProject APIキーを付けて呼び出す方法とは異なります。Objectの構造・制約・削除の影響は[Objectの仕様・制限](../build/object-specs-and-limits.md)、通常のHTTP APIは[Objects API](../api/objects-api.md)、SDKメソッドと型は[SDK Objects](../development/sdk-objects.md)を参照してください。

## 関連記事

- [Objects API](../api/objects-api.md)
- [SDK Objects](../development/sdk-objects.md)
- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)

## 始める前に

- この方式は、Page実行環境で動く**ブラウザ用コード**だけを対象にします。SDKの現行公開版0.11.0で、Pageブラウザ向けのモジュールに`objects`が含まれます。
- PageのブラウザではProject APIキーを設定・読取り・送信しません。`baseUrl: ""`でSDKを初期化すると、ブラウザはPageと同じオリジンの相対パスを呼び出します。
- 対象ObjectのID、フィールド名、型、レコードのアクセス設定を確認します。`define<T>()`の`T`はTypeScriptの型であり、Objectの入力制約や権限を置き換えません。

> APIキー、Cookie、認証ヘッダーをPageのJavaScript、公開環境変数、ログ、問い合わせに含めないでください。外部サーバーからの連携やキーを使う直接APIは、本記事ではなく[Objects API](../api/objects-api.md)と[実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)の実行モードに従います。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

type Contact = { name: string; email: string; company?: string };

const studio = createClient({ baseUrl: "" });
const contacts = studio.objects.define<Contact>("OBJECT_ID");
```

## Page実行環境で使う経路

SDKのObjects APIは、Pageから`/api/v1/objects`を基点とする相対パスへ送られます。ブラウザコードで組織ID、Project ID、利用者IDをパス・本文・ヘッダーへ指定して切り替えることはできません。Pageに結び付いた有効なセッションのProjectと実効主体で処理されます。

|対象|SDKメソッド|Page相対パスとメソッド|
|---|---|---|
|Object定義|`list()`、`get()`、`create()`、`update()`、`delete()`|`GET`・`POST /api/v1/objects`、`GET`・`PATCH`・`DELETE /api/v1/objects/{objectId}`|
|レコード|`define<T>()`、`list()`、`get()`、`create()`、`update()`、`delete()`、`bulkCreate()`、`bulkDelete()`|`GET`・`POST /api/v1/objects/{objectId}/records`、`GET`・`PATCH`・`DELETE /api/v1/objects/{objectId}/records/{recordId}`、`POST`・`DELETE /records/bulk`|
|検索|`search()`|SDKのPRSブラウザでは`GET /api/v1/objects/{objectId}/records`へ検索条件をクエリ文字列として送ります。|
|フィールド|`listFields()`、`createField()`、`updateField()`、`deleteField()`|`GET`・`POST /api/v1/objects/{objectId}/fields`、`PATCH`・`DELETE /api/v1/objects/{objectId}/fields/{fieldId}`|

`/api/records`はObjects APIとして登録されていません。Page自身が提供する別のAPIと競合・混同しないでください。`/api/custom-objects/...`は別の互換経路で、組織・Projectを含む形式を検証する処理がありますが、SDKの`objects`が使う経路ではありません。新しいPageのブラウザ実装でObjectを扱う場合は、`/api/v1/objects...`または上記SDKを使います。

> 直接`fetch()`でSDKと同じ経路を呼ぶ場合も、APIキーや主体を示すヘッダーを付けず、相対パスだけを使います。ただし、SDKはPRS検索の変換、応答キーの正規化、エラー型を扱うため、通常はSDKを優先してください。

## 1. レコードを読む・書く

レコードを作成、取得、完全更新、削除する例です。`update()`は部分更新ではありません。残す通常フィールドをすべて含む`data`を渡してください。削除と構造変更の影響は、実装前に[Objectの仕様・制限](../build/object-specs-and-limits.md)で確認します。

```ts
const created = await contacts.create({
  name: "山田 花子", email: "hanako@example.example",
});
const current = await contacts.get(created.recordId);

const updated = await contacts.update(current.recordId, {
  ...current.data,
  company: "JAPAN AI",
});

await contacts.delete(updated.recordId);
```

同時更新を検出する必要がある場合は、取得した`revision`を`expectedRevision`として渡します。不一致では409になります。`expectedRevision`を省略した更新はlast-write-wins、削除は無条件です。`bulkCreate()`は1回に1,000レコードまでです。`bulkDelete(recordIds)`も最大1,000件で、削除できないレコードが1件でもあれば全件を削除せず失敗します。存在しないIDは結果の`skippedIds`で確認します。削除結果が不明な場合は、先に対象の状態を確認します。

## 2. 検索とページングを実装する

`search()`には`filters`、`sort`、`limit`、`offset`、`cursor`、`includeDeleted`、`includeCount`を渡せます。`list()`にも`includeCount`を渡せます。件数が不要な画面では`includeCount: false`を指定すると一致レコードの総数計算を省略でき、`total`は0になります。次ページの`hasNextPage`と`cursor`は利用できるため、次ページは同じ条件・並び順で返された`cursor`を渡します。

```ts
const page = await contacts.search({
  filters: [{ field: "company", operator: "contains", value: "AI" }],
  sort: [{ field: "name", direction: "asc" }],
  limit: 50,
});

const next = page.hasNextPage && page.cursor
  ? await contacts.search({
      filters: [{ field: "company", operator: "contains", value: "AI" }],
      sort: [{ field: "name", direction: "asc" }],
      limit: 50, cursor: page.cursor,
    })
  : undefined;
```

PRSブラウザの検索では、SDKが作るクエリ文字列は**8,000文字以下**でなければなりません。超えると要求を送る前に`ObjectsApiError`（ステータス400）になります。これは検索結果件数やObjectの文字数上限ではありません。フィルター・並び順を減らす、検索を段階に分けるなどして短くしてください。

## アクセスモードと権限

Objects APIには有効なPageセッションが必要です。Pageを開けることと、Objectのすべてのデータを操作できることは別です。実効主体に対するProject内のObject・レコードの権限、Object定義、アクセスルールが最終的な可否を決めます。PageのAPIアクセスをブラウザから許可しない設定では、ブラウザのObjects APIは利用できません。

|「アクセス設定」のモード|ブラウザからのObjects API|
|---|---|
|「サインイン必須」|サインイン済みでPageへアクセスできる利用者のセッションが必要です。|
|「サインイン任意」|サインイン済みの利用者はそのセッションを使います。未サインイン利用者については、Pageの匿名アクセス用セッションが成立した場合に呼び出されます。|
|「匿名アクセス」|匿名アクセス用のPageセッションが成立した場合に呼び出されます。公開URLだけで任意のObject操作を許可するものではありません。|
|「外部顧客認証」|外部顧客認証を完了したPageセッションが必要です。通常のPageセッションを代用したり、ブラウザから認証情報を偽装したりできません。|

匿名アクセス、サインイン任意の未サインイン利用者、外部顧客認証で表示するPageでは、Objectのレコード権限を最小限にし、画面でObject IDやレコードIDを変えただけで他のデータへ到達できないことをテストしてください。Pageのアクセスモード、公開URL、アクセス設定の確認は[Pageの仕様・制限](../build/page-specs-and-limits.md)を参照します。

## うまくいかない場合

|状況|確認・対応|
|---|---|
|401|Pageセッションがない、期限切れ、またはObject要求に必要な実効主体を確立できません。Pageを開き直し、アクセスモードと認証完了状態を確認します。|
|403|Page・組織・Projectの対応、Objectまたはレコードの権限、アクセスモード、書込み時のOrigin/Refererを確認します。Pageを公開しただけで権限は増えません。|
|404|`/api/v1/objects...`、Object ID、レコードID、フィールドIDを確認します。`/api/records`や`/api/custom-objects`へ置き換えません。|
|409|`expectedRevision`と最新のレコードを確認します。同じ値で再送せず、最新値と業務上正しい変更内容を確認します。|
|400|Object・フィールドの入力条件、検索条件、またはPRS検索の8,000文字制限を確認します。|
|429・5xx・通信失敗|短時間の連続呼出を抑え、読み取り後に必要最小限で再試行します。書込み失敗時は無条件に再送せず、現在の状態を取得して重複・競合を確認します。|

SDKのHTTP失敗、通信失敗、JSON解析失敗は`ObjectsApiError`として扱えます。`statusCode`と`responseBody`を使って画面用メッセージを分けられますが、応答本文に個人情報、Cookie、認証情報が含まれ得る前提で、そのまま表示・記録しないでください。

## チェックリスト

- [ ] Pageブラウザで`createClient({ baseUrl: "" })`を使い、APIキーを含めていない
- [ ] Object操作に`/api/v1/objects...`またはSDKの`objects`を使い、`/api/records`へ依存していない
- [ ] `update()`に残すフィールドを含む完全な`data`を渡している
- [ ] 検索クエリを8,000文字以下にし、件数が不要な画面では`includeCount: false`を検討し、同じ条件・並び順でcursorを引き継いでいる
- [ ] 一括削除では、対象ID、最大1,000件、失敗時に全件削除されないこと、削除後の状態を確認している
- [ ] アクセスモードごとの利用者で、許可される読取り・書込み・削除だけを確認した
- [ ] Object・レコードの権限と、匿名・外部顧客向け画面の表示データを最小化した

## 次に読む記事

- [Objects API](../api/objects-api.md)
- [SDK Objects](../development/sdk-objects.md)
- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
