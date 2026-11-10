# Object SQLのDDL

Object SQLは、Objectをテーブル、フィールドを列、レコードを行として扱うSQLです。DDLでは、Objectとフィールド、インデックス、説明を定義できます。変更の影響は、[Objectの構造を設計する](../build/design-object-structure.md)と[Objectの仕様・制限](../build/object-specs-and-limits.md)で確認してください。

## 関連記事

- [Objectの構造を設計する](../build/design-object-structure.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
- [SDK Objects](../development/sdk-objects.md)
- [CLI Objects](../cli/cli-objects.md)
- [Objects API](../api/objects-api.md)

## 始める前に

- 対象の組織・Project、変更対象のObject・フィールド・既存レコードを確認します。
- Objectまたはフィールドの作成・削除・変更には、対象ごとの対応する権限が必要です。閲覧権限だけではDDLを実行できません。
- 画面の「カスタムオブジェクトSQL」Workflowノードでは、「SQL」に1文だけ指定できます。「エージェントと会話してSQLを生成」はSQL作成の補助であり、実行前に生成内容を確認してください。AgentのObject SQLツールでもDDLを実行できます。

> DDLは1回の実行で1つのSQL文だけを受け付けます。`CREATE TABLE ...; CREATE INDEX ...`のような複数文は、`Only a single SQL statement is allowed`として拒否されます。変更は文ごとに確認し、必要なら個別に実行してください。

確認した範囲では、公開REST API、Studio SDK、Studio CLIにObject SQLの実行API／コマンドはありません。SDK・CLIではObject定義を、HTTP APIではObjects APIを利用します。WorkflowまたはAgent以外から直接利用する手順は、本記事では案内しません。

## 1. `CREATE TABLE`でObjectとフィールドを作成する

Object名とフィールド名は、Objectの命名規則に従います。DDLで指定したObject名とフィールド名は小文字化されるため、`CustomerCode`ではなく`customer_code`のように小文字で統一してください。表示用の日本語は後述の`SET DISPLAY_NAME`で設定します。

```sql
CREATE TABLE tasks (
  title VARCHAR(100) NOT NULL,
  priority INT DEFAULT 0,
  completed BOOLEAN DEFAULT false,
  due_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) COMMENT 'タスクを管理するObject'
```

`COMMENT`句はObjectの**説明**を設定します。表示名は変わりません。テーブルレベルの制約（例：`CONSTRAINT ...`やテーブル末尾の`UNIQUE (...)`）は使えず、制約は各列に記述します。

### SQL型とObjectフィールド型

| SQL型 | 作成されるフィールド型 | 補足 |
|---|---|---|
| `TEXT`、`VARCHAR(n)`、`CHAR(n)` | テキスト | `VARCHAR(n)`などの長さはフィールドの最大長になります。 |
| `INT`、`INTEGER`、`BIGINT`、`DECIMAL`、`NUMERIC`、`FLOAT`、`DOUBLE`、`REAL` | 数値 | 整数型は整数のみの数値として扱われます。 |
| `BOOLEAN`、`BOOL` | 真偽値 |  |
| `DATE`、`TIMESTAMP`、`DATETIME` | 日時 | `CURRENT_TIMESTAMP`／`NOW()`を既定値にできるのはこの型だけです。 |
| `JSON`、`JSONB` | JSON |  |
| `TEXT[]`などの配列型 | 配列 |  |
| `SERIAL`、`BIGSERIAL`、`AUTO_INCREMENT` | 自動採番の数値 | 自動採番として扱われます。 |

### 列で指定できる制約

| 構文 | 動作・条件 |
|---|---|
| `NOT NULL`／`NULL` | 必須／任意を設定します。 |
| `UNIQUE` | ユニーク制約とインデックスを設定します。`JSON`と配列には指定できません。 |
| `DEFAULT <リテラル>` | 数値、文字列、真偽値、`NULL`を既定値にできます。式は指定できません。 |
| `DEFAULT CURRENT_TIMESTAMP` | 日時型の作成時または列追加時だけ指定できます。 |
| `CHECK (列名 IN (...))` | 同じ列に対するリテラル値の許可リストになります。`NOT IN`、空のリスト、`NULL`を含むリスト、別列や式を使う条件は拒否されます。許可値は100件までです。 |
| `REFERENCES object_name(field_name)` | リレーションを設定します。参照先Objectとフィールドが存在し、参照先フィールドがユニークで、両フィールド型が一致する必要があります。 |

例えば、参照先を先に定義します。

```sql
CREATE TABLE customers (customer_key TEXT NOT NULL UNIQUE);
CREATE TABLE orders (
  title TEXT NOT NULL,
  customer_ref TEXT REFERENCES customers(customer_key)
);
```

`ON UPDATE`や複数列への`REFERENCES`は対応していません。参照先が存在しない、ユニークでない、または型が異なる場合は拒否されます。リレーションによって参照先の権限は付与されません。

## 2. `ALTER TABLE`で1項目だけ変更する

`ALTER TABLE`で指定できる操作は1文につき1つです。追加や変更をカンマで並べることはできません。

```sql
ALTER TABLE tasks ADD COLUMN assignee TEXT;
ALTER TABLE tasks ADD COLUMN IF NOT EXISTS estimate_hours INT DEFAULT 0;
ALTER TABLE tasks ALTER COLUMN title SET NOT NULL;
ALTER TABLE tasks ALTER COLUMN priority SET DEFAULT 0;
ALTER TABLE tasks ALTER COLUMN priority DROP DEFAULT;
ALTER TABLE tasks ALTER COLUMN title SET DATA TYPE VARCHAR(200);
ALTER TABLE tasks ALTER COLUMN title SET DISPLAY_NAME '件名';
ALTER TABLE tasks SET DISPLAY_NAME 'タスク';
```

対応する操作は、列の追加、`DROP COLUMN ... CASCADE`、`SET/DROP NOT NULL`、リテラル既定値の設定／解除、同じフィールド型内でのデータ型指定の変更、表示名の設定／解除、リレーションの設定／解除です。`ADD RELATIONSHIP field REFERENCES target(key)`も使えます。

型そのものの変更やテキストの最大長の縮小はできません。`SET DATA TYPE ... USING`、`GENERATED AS IDENTITY`、`ALTER COLUMN SET DEFAULT CURRENT_TIMESTAMP`は拒否されます。`DROP COLUMN`には`CASCADE`が必要です。`IF NOT EXISTS`付きの既存列の追加、または`IF EXISTS`付きの存在しない列の削除は、警告付きで変更しません。

`ALTER TABLE ... SET/DROP DISPLAY_NAME`は表示名を扱います。`DROP DISPLAY_NAME`は表示名を名前へ戻します。`COMMENT ON COLUMN`は使えません。

## 3. インデックスと削除を扱う

```sql
CREATE INDEX idx_tasks_title ON tasks(title);
DROP INDEX idx_tasks_title ON tasks(title);
DROP TABLE IF EXISTS obsolete_tasks CASCADE;
```

`CREATE INDEX`は1列の単純な列名だけに指定できます。`UNIQUE`は列の`UNIQUE`制約で指定し、`CREATE UNIQUE INDEX`は使いません。`CONCURRENTLY`、`USING`、`INCLUDE`、`NULLS DISTINCT`、`WITH`、`WHERE`、複数列、並び順は拒否されます。既にインデックス済みの列へ`IF NOT EXISTS`付きで作成した場合は、警告付きで変更しません。

`DROP INDEX`には`ON object(field)`が必要で、`CASCADE`は使えません。ユニーク制約によるインデックスは削除できません。

`DROP TABLE`は1つのObjectだけを対象にし、`CASCADE`を付けます。`IF EXISTS`を付ければ存在しないObjectは警告付きで変更しません。削除後、Objectを参照するSQLは失敗します。SQLによる復元構文は確認できないため、削除と復元の運用・影響は[Objectの仕様・制限](../build/object-specs-and-limits.md)を参照してください。

`COMMENT ON TABLE tasks IS '説明';`はObjectの説明を設定または更新します。`COMMENT ON COLUMN`など、テーブル以外への`COMMENT`は拒否されます。

## よくある誤解

### `COMMENT`で表示名を設定できる

できません。`COMMENT`はObjectの説明です。Objectは`ALTER TABLE tasks SET DISPLAY_NAME 'タスク'`、フィールドは`ALTER TABLE tasks ALTER COLUMN title SET DISPLAY_NAME '件名'`で設定します。

### `CREATE INDEX`は任意の検索条件を高速化できる

対応するのは1列への単純なインデックス作成だけです。部分・複数列インデックスは作成できません。検索設計とObjectの上限・制約は[Objectの仕様・制限](../build/object-specs-and-limits.md)を確認してください。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 400でDDLが拒否される | SQLを1文にし、Object／フィールド名、対応する型・構文、`CASCADE`、対象の存在を確認します。`not supported`なら、その構文はObject SQLの対象外です。 |
| 権限エラーになる | Objectまたはフィールドの作成・削除・変更のうち、実行したDDLに対応する対象ごとの権限を確認します。 |
| `REFERENCES`が失敗する | 参照先Object・フィールドの存在、参照先のユニーク制約、両者のフィールド型、既存レコードの値を確認します。 |
| WorkflowでDDLが失敗する | 「カスタムオブジェクトSQL」ノードの「SQL」、対象Project、Workflowの実行主体に必要な権限、実行履歴のエラーを確認します。構造変更を許可しない実行コンテキストでは、DDLは拒否されます。 |

変更前後にフィールド構成、既存レコード、リレーション、Page・Agent・Workflowの代表操作を確認してください。DDLの検証のみを行う機能の利用者向け入口は、この確認範囲で確定できません。

## 次に読む記事

- [Objectの構造を設計する](../build/design-object-structure.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
- [SDK Objects](../development/sdk-objects.md)
- [Objects API](../api/objects-api.md)
