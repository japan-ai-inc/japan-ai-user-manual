# Object SQLのDML

Object SQLは、ProjectのObjectをSQL風の構文で検索・追加・更新・削除するための、完全なPostgreSQLではないSQL方言です。Objectはテーブル、フィールドは列、レコードは行として指定します。Object・フィールドの作成や変更は[Object SQLのDDL](object-sql-ddl.md)、画面上でのレコード操作は[Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md)を参照してください。

## 関連記事

- [Object SQLのDDL](object-sql-ddl.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
- [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- [Objects API](../api/objects-api.md)

## 始める前に

- 対象Project、Object名、フィールド名、フィールド型、対象レコードを確認します。表示名ではなく、Object SQLで解決できるObject名・フィールド名を使います。
- 読み取りと書き込み・削除の可否は別です。SQLが正しくても、Object、フィールド、レコードのアクセス条件で結果が絞られたり拒否されたりします。役割・共有範囲・アクセス設定は[組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)で確認してください。
- `UPDATE`、`DELETE`、複数行の`INSERT`は実データを変えます。検証用Objectまたは絞り込んだ対象で確認し、書き込み前に条件・件数・後続のWorkflowへの影響をレビューします。

> Object SQLは1回の実行で**1文だけ**を受け付けます。`BEGIN`、`COMMIT`、`ROLLBACK`を記述して複数文をまとめることはできません。DMLはサーバー側の1実行単位で処理され、成功時に確定します。失敗・タイムアウト時に、同じ更新を確認なしで再実行しないでください。

## 実行できる場所

Workflowではノード一覧から「カスタムオブジェクトSQL」を選び、「SQL」に文を入力します。出力変数は、SELECTの「クエリ結果の配列」と「総件数（SELECTクエリ）」、DMLの「影響を受けた行数（DMLクエリ）」です。実行前にWorkflowのテスト・公開・実行条件を[Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)で確認します。

Agentでは、Object SQL用ツールが有効なAgentから実行できます。ツールは読み取り専用ではなく、`SELECT`だけでなくDMLも実行できます。Agentに更新・削除を許可する場合は、対象Object・フィールド・レコードの権限を最小限にし、実行者と結果を確認してください。

確認した範囲では、公開REST API、Studio SDK、CLIにObject SQLを直接実行する公開インターフェースはありません。HTTP API、SDK、CLIからレコードを扱う場合は[Objects API](../api/objects-api.md)を起点に、それぞれのObject操作を使います。

## SELECT：検索する

基本形は次のとおりです。`LIMIT`を省略した通常のSELECTは100行を既定値とし、指定しても最大1,000行です。`OFFSET`は10,000以下です。大量走査では、`_record_id`によるキーセット方式を使います。

```sql
SELECT name, status
FROM orders
WHERE status = 'open' AND amount >= 1000
ORDER BY name ASC
LIMIT 100 OFFSET 0
```

`WHERE`では`=`、`!=`／`<>`、`<`、`<=`、`>`、`>=`、`IN`／`NOT IN`、`BETWEEN`、`LIKE`／`ILIKE`、`IS NULL`／`IS NOT NULL`、`AND`を使えます。同一フィールドの等価比較をORで並べる代わりに`IN`を使います。異なるフィールドをまたぐORや、ORの内側にANDを置く形は扱えません。文字列フィールド以外への`LIKE`／`ILIKE`、`IN`内の`NULL`も使えません。

### 並び順・集計・結合

`ORDER BY`は複数列、`ASC`／`DESC`を指定できます。集計は`COUNT`、`SUM`、`AVG`、`MIN`、`MAX`で、`GROUP BY`と`HAVING`を併用できます。集計値を`HAVING`または`ORDER BY`で使うときは、明示的な別名を付けます。

```sql
SELECT status, COUNT(*) AS total
FROM orders
GROUP BY status
HAVING total > 1
ORDER BY total DESC
```

関連フィールドで定義済みの関係に限り、単一の`INNER JOIN`または`LEFT JOIN ... ON`を使えます。自己結合、複数JOIN、任意の列同士の結合、別名を使う結合は使えません。`WHERE ... IN (SELECT ...)`／`NOT IN (SELECT ...)`の副問合せは最大2個までで、関係で結ばれたObject間だけに使えます。副問合せは1列を選択し、`ORDER BY`、`LIMIT`、`GROUP BY`、`HAVING`、入れ子の副問合せを含められません。副問合せの結果は1,000行までです。DML内の副問合せは使えません。

通常のSELECTではフィールド投影、`*`、`DISTINCT`を使えます。関数は集計関数のほか、投影・集計引数で`COALESCE`を使える場合があります。任意の関数、ウィンドウ関数、`CASE`、型変換、`UNION`などを前提にしないでください。`WITH`（CTE）とFROM句の副問合せも使えません。

### `_record_id`によるページング

`_record_id`は唯一のシステム識別子です。結果ではレコードIDとして返りますが、`SELECT`の投影・`INSERT`・`UPDATE SET`・競合対象には使えません。`WHERE`では文字列リテラルとの`=`、`<`、`<=`、`>`、`>=`、`IN`、`ORDER BY`では`ASC`／`DESC`だけに使えます。作成日時・更新日時などのメタデータをSQLの列として検索・並べ替えできることは確認できません。

```sql
SELECT * FROM orders
WHERE _record_id > '前ページ末尾のレコードID'
ORDER BY _record_id ASC
LIMIT 100
```

この順序は作成時刻順ではありません。また、ページ間に追加・削除された行を含む可能性があるため、固定スナップショットとして扱わないでください。

## INSERT・UPDATE・DELETE：変更する

フィールド型と値は一致させます。Objectの型は文字列、数値、日時、真偽値、JSON、配列であり、日時には日時文字列、JSONにはオブジェクト、配列には配列を渡します。必須・ユニーク・数値範囲などのObject側制約はSQLでも検証されます。

```sql
INSERT INTO orders (name, amount, active)
VALUES ('見積A', 1200, true), ('見積B', 800, false)

UPDATE orders
SET status = 'closed'
WHERE _record_id = '対象レコードID'
LIMIT 1

DELETE FROM orders
WHERE status = 'obsolete'
LIMIT 100
```

`INSERT`では列名と`VALUES`が必要で、複数行を追加できます。`ON CONFLICT (...) DO UPDATE SET ...`によるUPSERTも使えます。`RETURNING`句は使わず、実行経路が返す件数または行の結果を確認します。INSERT／UPSERTは最大1,000行です。

`UPDATE`と`DELETE`には`WHERE`または`LIMIT`が必須です。`LIMIT`を指定した場合、対象が上限を超えると処理は拒否されます。指定しない場合も、更新・削除は最大1,000行に制限されます。削除は画面上の「ゴミ箱へ移動」や復元手順と同一であるとは断定できません。削除前の影響と復元可否は[Objectの仕様・制限](../build/object-specs-and-limits.md)で確認してください。

> 書き込みは、途中で失敗した場合やタイムアウトした場合に「全く反映されなかった」と決めつけないでください。対象をSELECTで再取得し、件数・値・実行履歴を確認してから対応を決めます。外部送信や通知を伴うWorkflowでは、再実行による重複も確認します。

## パラメーターを渡す場合

実行経路がパラメーター配列を受け取る場合、プレースホルダーは`$1`、`$2`のように1から連続して指定します。値は別配列で束縛し、文字列連結でSQLを作りません。同じ番号を複数回使えます。最大256値、全値の合計は8MiBまでです。オブジェクト・配列の値は、INSERTの完全なVALUES値またはUPDATEの完全なSET値にだけ渡します。

```text
SQL:        UPDATE orders SET memo = $1 WHERE _record_id = $2 LIMIT $3
parameters: ["確認済み", "対象レコードID", 1]
```

`$0`、欠番、余分な値、非数のLIMIT／OFFSET、検索条件や関数内に構造化値を置く指定は拒否されます。SQL本文も65,536文字までです。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 構文・意味エラー | Object名・フィールド名、型、対応する構文、1文だけになっているかを確認します。SQL全文を機密情報とともに共有しません。 |
| 結果が0件・一部だけ | WHERE、Object／レコードの閲覧条件、削除済みの状態、LIMIT／OFFSETを確認します。 |
| 更新・削除が拒否される | 読み取りではなく更新・削除の権限、フィールド単位・レコード単位のアクセス条件、WHEREまたはLIMIT、最大1,000行を確認します。 |
| タイムアウトする | 条件、投影列、LIMIT、結合・副問合せを小さくします。読み取りは30秒の文実行時間、書き込みは35秒の実行期限、要求全体は130秒の期限があります。 |
| 競合または再実行が必要 | 最新のレコードを取得して結果を確認します。更新済みか不明な書き込みを、同じ条件のまま自動再試行しません。 |

## チェックリスト

- [ ] 対象Project、Object、フィールド、レコードを確認した
- [ ] 読み取りと書き込み・削除の権限を分けて確認した
- [ ] SELECTに必要なWHERE、ORDER BY、LIMITを指定した
- [ ] UPDATE／DELETEにWHEREまたはLIMITを指定した
- [ ] 変更前後の件数・値を確認するSQLまたはWorkflow出力を用意した
- [ ] パラメーターを文字列連結せず`$1`形式で束縛した
- [ ] 削除・再実行・Workflow後続処理の影響を確認した

## 次に読む記事

- [Objectのベクトル検索](object-vector-search.md)
- [Object SQLのDDL](object-sql-ddl.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
- [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- [Objects API](../api/objects-api.md)
