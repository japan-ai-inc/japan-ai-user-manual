# Objectのベクトル検索

Object SQLでは、テキストの完全一致や部分一致ではなく、検索語と意味が近いレコードを探すベクトル検索を利用できます。問い合わせ文と表現が異なるFAQ、説明文、問い合わせ履歴などを候補順に取得したい場合に向きます。検索対象にするフィールドを先に設定し、レコードの検索用データが生成された後に、`vector_score()` を含む`SELECT`を実行します。

Object SQLの基本的な読取り・更新・削除は[Object SQLのDML](object-sql-dml.md)、Objectの構造と一般的な制約は[Objectの仕様・制限](../build/object-specs-and-limits.md)を参照してください。DatasetのRAG参照との使い分けは[Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)も参照してください。

## 関連記事

- [Object SQLのDML](object-sql-dml.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)
- [Object SQLのDDL](object-sql-ddl.md)

## この記事の範囲

この記事では、Objectをテーブル、フィールドを列、レコードを行として扱うObject SQLのベクトル検索を説明します。検索対象フィールドの定義、`SELECT`の構文、通常の条件との組合せ、制約、反映待ちの考え方を対象にします。

利用できる入口として、Workflowの「カスタムオブジェクトSQL」ノードと、AgentのObject SQLツールを確認しています。Workflowノードでは「SQL」に文を入力し、`SELECT`の結果は「クエリ結果の配列」と「総件数（SELECTクエリ）」へ出力できます。SDK、CLI、公開REST APIには、Object SQLを直接実行する公開インターフェースは確認できていません。外部アプリケーションから実行する前提にはしないでください。

> ベクトル検索は、任意のObject・任意の文字列列で自動的に使える機能ではありません。少なくとも1つの検索対象フィールドがあり、利用環境で検索用データの生成・処理が利用可能であることが必要です。画面や実行入口が表示されない場合は、権限、Project、組織の提供条件を管理者に確認してください。

## 始める前に

- 対象Object、検索したい文章が入るフィールド、検索結果を利用するWorkflowまたはAgentを確認します。
- 検索対象にするのは文字列のフィールドだけにします。数値、真偽値、日時、配列、JSONを検索対象として指定することはできません。
- 新規作成・更新直後のレコードを検索する検証では、検索結果が反映されるまで待つ前提でテストを設計します。
- 検索語は固定の文字列リテラルとして用意します。列名、変数、式を`vector_score()`の引数に渡すことはできません。

## 1. 検索対象フィールドを定義する

Object SQLのDDLでは、文字列列の型の直後に`EMBEDDING`を付けると、そのフィールドをベクトル検索の対象として定義できます。新しいObjectで定義する例です。

```sql
CREATE TABLE tickets (
  subject TEXT EMBEDDING,
  status TEXT,
  priority INTEGER
);
```

既存Objectに検索対象列を追加する場合は、次のようにします。

```sql
ALTER TABLE tickets
  ADD COLUMN details TEXT EMBEDDING;
```

`EMBEDDING`は`TEXT`など文字列型の列だけに指定できます。対象を持たないObjectに`vector_score()`を使うと、検索は検証で拒否されます。フィールド設定を変更して既存データを対象にした場合も、後述の非同期処理が完了するまでは、既存レコードが検索結果に現れないことがあります。

> `EMBEDDING`は通常の`INDEX`、`UNIQUE`、または`LIKE`検索の指定ではありません。「意味の近さ」で候補を得るための検索対象指定です。フィールドの型、必須、ユニーク制約などの設計は[Objectの仕様・制限](../build/object-specs-and-limits.md)に従ってください。

## 2. 意味の近いレコードを取得する

関数名は`vector_score()`です。引数は空白だけではない**1つの文字列リテラル**にします。通常は関数に別名を付け、その別名で降順に並べ、取得件数を`LIMIT`で指定します。

```sql
SELECT
  subject,
  status,
  vector_score('パスワードを再設定したい') AS similarity
FROM tickets
ORDER BY similarity DESC
LIMIT 10;
```

`vector_score()`を含む検索結果の各行には、選択列とは別に`similarity_score`が返ります。大きい値ほど近い候補です。別名はSQL中の並べ替えに使うためのものであり、返却値の名称が別名へ置き換わるとは限りません。

類似度の下限を設けるには、同じ検索語の`vector_score()`を`WHERE`で比較します。しきい値は0.0から1.0までの数値リテラルです。

```sql
SELECT subject, status, vector_score('請求書の発行について') AS similarity
FROM tickets
WHERE vector_score('請求書の発行について') >= 0.70
ORDER BY similarity DESC
LIMIT 10;
```

検索語を複数箇所に書くときは、すべて完全に同じ文字列にします。検索語は前後の空白を除いた後で最大8,000文字です。空文字、空白だけの文字列、列参照、プレースホルダーを引数にはできません。

## 3. 通常の条件と組み合わせる

`WHERE`では、ベクトル条件と通常の条件を`AND`で組み合わせられます。これは、例えば「未対応のレコードだけから、質問に近いものを上位に出す」ための絞込みです。

```sql
SELECT subject, priority, vector_score('ログインできない') AS similarity
FROM tickets
WHERE status = 'open'
  AND priority <= 2
  AND vector_score('ログインできない') >= 0.50
ORDER BY similarity DESC
LIMIT 20;
```

`LIKE`または`ILIKE`も通常条件として併用できます。

```sql
SELECT subject, vector_score('認証に失敗する') AS similarity
FROM tickets
WHERE subject ILIKE '%認証%'
ORDER BY similarity DESC
LIMIT 10;
```

ただし、`LIKE`は文字列のパターン一致、`vector_score()`は意味の近さによる順位付けです。前者だけでは言い換えは見つかりませんが、後者だけでは必ずしも特定の語を含むとは限りません。業務上必須の状態・担当・日付・文字列条件は`WHERE`で明示し、意味の近さを候補順位に使ってください。`OR`でベクトル条件を分岐させることはできません。

## 制約と結果の扱い

| 項目 | 条件・制約 |
|---|---|
| 件数 | `LIMIT`は必須で、1〜50です。`OFFSET`は使えません。ページを順にたどる検索には向きません。 |
| 並び順 | 類似度だけを`DESC`で指定します。`ASC`、第2の並べ替え列、別の列による並べ替えは使えません。`ORDER BY vector_score('…') DESC`と直接書くこともできます。 |
| SQLの形 | `JOIN`、副問合せ、`DISTINCT`、集計関数、`GROUP BY`とは組み合わせられません。集計が必要なら、候補を取得してから別の処理として行います。 |
| しきい値 | 省略すると、上位`LIMIT`件を返します。指定時は0.0〜1.0の数値リテラルで比較します。 |
| 総件数 | ベクトル検索は通常のページング検索とは異なり、次ページの有無は返りません。`LIMIT`内の候補を1回で扱う設計にします。 |

## 反映タイミングと検索品質の注意

検索対象フィールドを持つObjectでは、レコード作成後、または検索対象の値が変わった後に、検索用データの生成要求がトランザクションの確定後に発行され、別の処理で生成・保存されます。したがって、保存や更新の成功と、その内容が直ちにベクトル検索で見つかることは同義ではありません。検索対象外のフィールドだけを変更した場合は、同じ検索用データを再利用できるよう変更判定されます。

また、検索は候補の意味的な近さを示すもので、正しさ・完全性・最新性を保証しません。短い語だけで判断せず、返ったレコードの本文、状態、更新時刻、アクセスできる範囲を後続処理で確認してください。検索用データの生成や検索実行が失敗・遅延した場合は、直前の更新内容が反映されない、または検索自体が失敗する可能性があります。

## DatasetのRAG参照との使い分け

| 目的 | 選択する機能 | 理由 |
|---|---|---|
| Object内の構造化されたレコードを、状態・担当・数値などで絞って意味検索する | Object SQLのベクトル検索 | `WHERE`による業務条件と類似度順を同じ読取りで扱えるためです。 |
| PDF、Web、外部データなどの資料を取り込み、AgentやWorkflowが回答の参考情報を探す | DatasetのRAG参照 | 資料の取り込み、同期、RAG参照のための機能です。 |
| 特定の表記を含むレコードを厳密に探す | `WHERE ... LIKE` / `ILIKE` | 文字列パターンへの一致を条件にでき、意味的な類似判定は行いません。 |

DatasetのRAG参照は、資料を取り込んで処理完了を確認してからAgentまたはWorkflowに設定します。一方、Object SQLはObjectのレコードを直接読むSQLです。長い資料の全内容を漏れなく確認する用途を、検索結果だけで完結させないでください。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 「検索対象フィールドがない」趣旨のエラーになる | 対象Objectに`TEXT EMBEDDING`など、文字列の検索対象フィールドが少なくとも1つあるか確認します。 |
| 保存した直後のレコードが出ない | 非同期の検索用データ生成が完了していない可能性があります。少し待って同じ固定検索語で再確認し、対象フィールドの値が保存されているかも確認します。 |
| SQLが検証エラーになる | `LIMIT 1`〜`LIMIT 50`、単一の文字列リテラル、`DESC`、単一の類似度順に直します。`OFFSET`、`JOIN`、副問合せ、`OR`、集計、`DISTINCT`を外します。 |
| 必須の語を含まない候補が返る | 意味検索だけに依存せず、`AND subject ILIKE '%語%'`などの通常条件、状態、担当、日付の条件を追加します。 |
| 入口が表示されない、または実行できない | 対象Project、WorkflowまたはAgentの設定、権限、組織の提供条件を確認します。公開API・SDK・CLIで同等のSQL実行を代用する前に、管理者へ確認してください。 |

## チェックリスト

- [ ] 検索対象にする文字列フィールドを`EMBEDDING`付きで定義した
- [ ] 新規・更新レコードの検索用データが反映されるまで待つ設計にした
- [ ] `vector_score()`へ空でない固定文字列を1つだけ渡した
- [ ] `LIMIT`を1〜50で指定し、類似度だけを`DESC`で並べた
- [ ] 必須の業務条件を`AND`の通常条件で絞った
- [ ] `similarity_score`を候補順位として扱い、レコード本文と業務条件を確認した
- [ ] 資料のRAG参照が必要な用途と、Objectレコード検索が必要な用途を分けた

## 次に読む記事

- [Object SQLのDML](object-sql-dml.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)
- [Object SQLのDDL](object-sql-ddl.md)
