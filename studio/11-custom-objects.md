# 11. カスタムオブジェクト

### 11.1 概要

カスタムオブジェクトは、業務データを格納するための **ユーザー定義のテーブル** です。サイドバーには「カスタムオブジェクト（OBJECT）」として表示されます（機能有効時のみ）。

カスタムオブジェクトは次の 3 階層で構成されます。

```
オブジェクト（スキーマ）         例：「顧客」「商談」「タスク」
  └── フィールド                 例：氏名・メール・ステータス
      └── レコード               例：実際の顧客データの 1 件
```

### 11.2 フィールドのデータ型

| データ型 | 用途 |
|---|---|
| `string` | テキスト（メール、URL、識別子など） |
| `number` | 数値（整数・小数） |
| `boolean` | true / false |
| `date` | 日時（RFC3339 形式・UTC） |
| `json` | 任意の構造化データ |
| `array` | 配列 |

各フィールドには次の **バリデーションルール** を設定できます。

| ルール | 用途 |
|---|---|
| 必須（Required） | 値が必須 |
| 文字列長（StringLength） | 最小〜最大文字数 |
| 数値範囲（NumericRange） | 最小〜最大値 |
| パターン（Pattern） | 正規表現でフォーマット指定 |
| 列挙値（AllowedValues） | 値リストから選択 |
| 配列長（ArrayLength） | 最小〜最大要素数 |
| 日付範囲（DateRange） | 期間 |
| メール形式（EmailFormat） | RFC 準拠の検証 |
| URL 形式（UrlFormat） | URL の検証 |
| 整数のみ（IntegerOnly） | |
| 偶数 / 奇数（EvenNumber / OddNumber） | |
| 倍数（MultipleOf） | 指定値の倍数 |
| 未来日付 / 過去日付 | |
| GCP 署名 URL / GCP ストレージ URL | |

### 11.3 デフォルト値の種類

| デフォルト種別 | 説明 |
|---|---|
| なし | 値が指定されない場合は `null` |
| 固定値（Static） | 指定した静的な値を設定 |
| 自動採番（AutoIntegerIncrement） | レコード作成ごとに 1, 2, 3...（`number` + `IntegerOnly` 必須） |
| 現在日時（AutoCurrentDateTime） | 作成時の UTC 日時（`date` 型のみ） |

### 11.4 システムフィールド

すべてのレコードには次の項目が自動付与されます。

- `id`（UUID、作成時に自動採番）
- `created_at`（作成日時、UTC）
- `updated_at`（更新日時、UTC）
- `created_by`（作成者のユーザー ID）

### 11.5 リレーション

オブジェクト間の参照を設定できます（1:1 / 1:N）。

- 参照先フィールドは **ユニーク制約付き** である必要があります
- 参照先のデータ型と一致している必要があります
- `json` / `array` 型はリレーションに使用不可

### 11.6 オブジェクト・フィールド・レコードの操作

#### 画面からの操作

- オブジェクトの作成・編集・削除
- フィールドの追加・編集・削除（フィールド名・データ型は作成後変更不可）
- レコードのフォーム入力による追加・編集・削除
- 一覧画面での検索・フィルター・ソート・ページネーション

> **画面プレースホルダー**：`screenshots/11-custom-objects.png`

#### REST API での操作

REST 経由でも CRUD が可能です（API キー認証）。代表的なリクエスト例:

```
POST /v1/organizations/{org}/projects/{proj}/objects/{object_id}/records
Content-Type: application/json

{
  "data": {
    "name": "山田 太郎",
    "email": "yamada@example.com",
    "status": "active"
  }
}
```

検索フィルターの演算子の例:

```
eq, ne, gt, gte, lt, lte, in, notin, between, notbetween,
contains, startswith, endswith, like, ilike, regex,
isnull, isnotnull, jsoncontains, exists
```

ページネーションは `limit`（既定 100、最大 1000）と `cursor` または `offset` を併用します。

### 11.7 SQL アクセス

カスタムオブジェクトは、画面上の検索 UI に加え、**制約付きの SQL** でも操作できます。SQL は LLM 経由の業務操作に最適化された限定的なサブセットで、`SELECT` のほか `INSERT` / `UPDATE` / `DELETE` / `UPSERT`、および `CREATE TABLE` / `ALTER TABLE` / `DROP TABLE` などの DDL に対応しています。

#### 例

```sql
-- 基本検索
SELECT name, email, status FROM customers
WHERE status = 'active'
ORDER BY name ASC
LIMIT 50;

-- 集計とグループ化
SELECT status, COUNT(*) AS cnt, SUM(amount) AS total
FROM orders
GROUP BY status
HAVING cnt > 10;

-- UPSERT
INSERT INTO products (id, name, price) VALUES ('p1', 'Widget', 19.99)
ON CONFLICT (id) DO UPDATE SET price = EXCLUDED.price;

-- ベクトル検索（埋め込み対象フィールドを設定した場合）
SELECT *, vector_score('パスワードリセット') AS score
FROM tickets
WHERE vector_score('パスワードリセット') >= 0.7
ORDER BY score DESC
LIMIT 10;
```

#### 主な制約（運用上の注意）

- 1 リクエストにつき 1 ステートメントのみ
- `UPDATE` / `DELETE` は `WHERE` または `LIMIT` を必須とする（無制限の更新・削除は不可）
- JOIN は単一の `INNER JOIN` / `LEFT JOIN` のみ（複数 JOIN・自己 JOIN・RIGHT/FULL は不可）
- サブクエリは `WHERE ... IN / NOT IN` 内に限定
- ベクトル検索は `LIMIT` 必須、`ORDER BY similarity DESC` のみ、`JOIN` / `GROUP BY` 等と併用不可
- `RETURNING` 句・CTE・UNION は非対応
- クエリタイムアウトや SQL 文字列の最大長、`SELECT` の最大行数、`OFFSET` 上限など、サーバー側のガードレールが適用されます

SQL の正確な構文・制約値・エラーコードについては、付属の **「STUDIO カスタムオブジェクト API ガイド」** を参照してください。

#### SQL のドライラン（Explain）

実行せずに構文・基本コストを確認できる Explain エンドポイントも提供されています。バリデーション結果と実行計画（の要約）が返ります。

### 11.8 アクセス制御（ACL）

カスタムオブジェクトには 2 段階の制御があります。

#### オブジェクト単位の権限（IAM）

| 権限 | 用途 |
|---|---|
| `studio.object.read` | オブジェクト・レコードの参照 |
| `studio.object.create` | オブジェクトの新規作成 |
| `studio.object.update` | スキーマ・レコードの更新 |
| `studio.object.delete` | オブジェクト・レコードの削除 |
| `studio.object.getPolicy` / `setPolicy` | リソースポリシーの参照・設定 |
| `studio.object.field.{read,create,update,delete}` | フィールド単位の操作 |
| `studio.object.record.{read,create,update,delete}` | レコード単位の操作 |

#### レコード単位の ACL（軽量条件 ACL）

レコードに対して、ロール・ユーザー・エージェント単位の **追加ルール** を設定できます。

- 効果：`ALLOW` または `DENY`
- 対象アクション：read / update / delete
- 優先順位（priority）：番号が小さいルールが先に評価され、最初に一致したルールが採用
- 条件 DSL（フェーズ 1 対応演算子）:
  - `eq`, `ne`, `gt`, `gte`, `lt`, `lte`
  - `in`, `notin`, `isnull`, `isnotnull`
  - `contains`, `startswith`, `endswith`
- フィールド参照：オブジェクトのフィールド、システムフィールド（`id`, `created_by` 等）、実行時変数（`$principal.id` 等）

ACL は SQL の `WHERE` 句にも自動的に組み込まれ、`UPDATE` / `DELETE` ではバルク操作前後の件数チェックで部分実行を防ぎます。

### 11.9 トリガー（カスタムオブジェクトイベント）

レコードの作成・更新・削除をトリガーとしてワークフローを起動できます。

- イベント種別：`recordCreated`（作成時）／ `recordUpdated`（更新時）／ `recordDeleted`（削除時）
- フィールドフィルター：特定フィールドが変更された場合のみ起動
- 条件フィルター：フィールド値が条件を満たす場合のみ起動

ワークフローには次の構造でデータが渡されます。

```json
{
  "record_id": "rec_abc123",
  "data": {
    "name": "山田 太郎",
    "email": "yamada@example.com",
    "status": "active"
  },
  "created_at": "2026-05-20T10:30:00Z",
  "updated_at": "2026-05-20T10:30:00Z",
  "created_by": "user_123",
  "changedFields": ["status", "email"]
}
```

### 11.10 ワークフローからのアクセス

ワークフローの「カスタムオブジェクト SQL」ノードでは、上記の SQL を任意に実行できます。上流ノードの変数をパラメーターとしてバインドできるため、エージェントの応答に基づくレコード操作を実装できます。

### 11.11 制限値

代表的な値：

- オブジェクト名・フィールド名・表示名：最大 **255 文字**
- 一覧取得（オブジェクト一覧、レコード一覧）の `limit`：既定 100、最大 1,000
- `offset` 上限：10,000
- フィールドの最大長制約（`max_length`）はフィールド定義時に指定（文字列型）
- ファイル / バイナリフィールドは現行未対応（必要に応じて JSON 内に Base64 等で格納）

オブジェクト数・フィールド数・SQL クエリの細かなガードレールは、**契約プラン・組織設定により異なります**。正確な値は付属の API ガイドおよび導入元へお問い合わせください。

### 11.12 ソフトデリートと復元

オブジェクト・フィールド・レコードの削除は **ソフトデリート** として記録されます。

- 削除されたリソースには `deleted_at` が記録され、即座に API からアクセス不可となります
- オブジェクトの復元 API では、`deleted_at` を解除し、付随するフィールド・レコードを非同期で復元する処理（タスク）が走ります
- 復元タスクの進行状況とエラー（失敗レコードの詳細）は、API から確認可能です
- 復元時はスキーマの現状に照らして再バリデーションされます（型・必須・ユニーク等を満たさないレコードは復元されません）

復元手続きの詳細は導入元・サポート窓口にご確認ください。

---

