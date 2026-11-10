# CLI Objects

`jai-studio objects`は、Object、フィールド、レコードをCLIから操作します。成功時の標準出力はJSONです。制約は[Objectの仕様・制限](../build/object-specs-and-limits.md)、SDK操作は[SDK Objects](../development/sdk-objects.md)を確認してください。

## 関連記事

- [SDK Objects](../development/sdk-objects.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [CLIの設定・プロファイル](cli-config-and-profiles.md)

## 始める前に

SDK **0.11.0** のCLIを対象にします。Node.jsは22以上を使い、接続情報はフラグ、環境変数、選択したプロファイルの順に解決されます。

```text
jai-studio --profile <name> objects list --limit 50 --pretty
```
以下の`<...>`を含む表記は**置換が必要な構文表記**です。角括弧を含めてそのままシェルへ貼り付けず、実際の値に置き換えてください。


共通フラグは`--profile <name>`、`--env <env>`、`--key <key>`、`--pretty`、`--debug`です。`--env`には`production`またはHTTP/HTTPS URLを指定できます。`--pretty`はJSONを整形し、`--debug`は詳細な応答をエラー出力へ表示します。`--json`フラグはなく、成功時の出力はJSONです。

> Project APIキーは秘密情報です。`--key`の実値をシェル履歴、CIログ、共有記録に残さないでください。

## 1. Object定義を操作する

| コマンド | 位置引数 | フラグ（必須／既定値） | 内容 |
|---|---|---|---|
| `list` | なし | `--cursor <cursor>`、`--limit <n>`（任意） | Objectをページ取得します。 |
| `get` | `<objectId>`（必須） | なし | 1件の定義を取得します。 |
| `create` | なし | `--name <name>`（必須）、`--display-name <name>`、`--description <desc>` | Objectを作成します。 |
| `update` | `<objectId>`（必須） | `--name <name>`、`--display-name <name>`、`--description <desc>`（すべて任意） | 指定した定義項目を更新します。 |
| `delete` | `<objectId>`（必須） | なし | Objectを削除します。 |

```text
jai-studio objects list [--cursor <cursor>] [--limit <n>]
```
```text
jai-studio objects get <objectId>
jai-studio objects create --name <name> [--display-name <name>] [--description <desc>]
jai-studio objects update <objectId> [--name <name>] [--display-name <name>] [--description <desc>]
jai-studio objects delete <objectId>
```

```bash
jai-studio objects create --name contacts --display-name '取引先担当者'
jai-studio objects update object-id --description '営業連絡先'
```

`--limit`は安全な整数として解析されます。取得結果の`cursor`と`hasNextPage`を確認し、次ページには返されたカーソルを渡します。削除前には、影響を[Objectの仕様・制限](../build/object-specs-and-limits.md)で確認してください。

## 2. フィールド定義を操作する

| コマンド | 位置引数 | フラグ（必須／既定値） | 内容 |
|---|---|---|---|
| `fields list` | `<objectId>`（必須） | なし | フィールド一覧を取得します。 |
| `fields create` | `<objectId>`（必須） | `--data <json>`（必須） | JSONでフィールドを作成します。 |
| `fields update` | `<objectId> <fieldId>`（必須） | `--data <json>`（必須） | JSONで指定項目を更新します。 |
| `fields delete` | `<objectId> <fieldId>`（必須） | なし | フィールドを削除します。 |

```text
jai-studio objects fields list <objectId>
jai-studio objects fields create <objectId> --data <json>
jai-studio objects fields update <objectId> <fieldId> --data <json>
jai-studio objects fields delete <objectId> <fieldId>
```

`fields create`のJSONには`fieldName`、`displayName`、`dataType`、`fieldNum`が必要です。JSONの外側はシェルで引用し、内部のキーと文字列は二重引用符にします。

```bash
jai-studio objects fields create object-id --data '{"fieldName":"email","displayName":"メール","dataType":"email","fieldNum":1}'
```

> フィールドの型・制約と既存値の整合性を確認します。削除前に[Objectの仕様・制限](../build/object-specs-and-limits.md)を確認してください。

## 3. レコードを作成・取得・更新・削除する

| コマンド | 位置引数 | フラグ（必須／既定値） | 内容 |
|---|---|---|---|
| `records list` | `<objectId>`（必須） | `--cursor <cursor>`、`--limit <n>`（任意） | レコードをページ取得します。 |
| `records get` | `<objectId> <recordId>`（必須） | なし | 1件を取得します。 |
| `records create` | `<objectId>`（必須） | `--data <json>`（必須） | JSONレコードを作成します。 |
| `records update` | `<objectId> <recordId>`（必須） | `--data <json>`（必須） | レコードのデータ全体を置換します。 |
| `records delete` | `<objectId> <recordId>`（必須） | なし | レコードを削除します。 |

```text
jai-studio objects records list <objectId> [--cursor <cursor>] [--limit <n>]
```
```text
jai-studio objects records get <objectId> <recordId>
jai-studio objects records create <objectId> --data <json>
jai-studio objects records update <objectId> <recordId> --data <json>
jai-studio objects records delete <objectId> <recordId>
```

```bash
jai-studio objects records create object-id --data '{"name":"山田花子","email":"hanako@example.example"}'
jai-studio objects records update object-id record-id --data '{"name":"山田花子","email":"hanako@example.example","company":"JAPAN AI"}'
```

`update`は部分更新ではなく完全置換です。残す通常フィールドも含めた完全なJSONを渡してください。`--data`はJSONとして解釈され、定義との整合性はサービスで判定されます。

## 4. 検索・並び順・ページングを指定する

| コマンド | 位置引数 | フラグ（必須／既定値） | 内容 |
|---|---|---|---|
| `records search` | `<objectId>`（必須） | `--filter <filter>`、`--sort <sort>`、`--cursor <cursor>`、`--limit <n>`（すべて任意） | 条件、並び順、カーソルで検索します。 |

```text
jai-studio objects records search <objectId> [--filter <field:operator:value>] [--sort <field:direction>] [--cursor <cursor>] [--limit <n>]
```

`--filter`はJSONではなく`field:operator:value`形式で、繰返し指定できます。値にコロンがあっても2つ目以降は値です。`isnull`、`isnotnull`、`exists`だけは値を省略できます。演算子は`eq`、`ne`、`gt`、`gte`、`lt`、`lte`、`contains`、`startswith`、`endswith`、`in`、`notin`、`isnull`、`isnotnull`、`exists`、`regex`、`between`、`jsoncontains`です。`--sort`は`field:direction`形式で繰返し指定でき、方向は`asc`、`desc`、`ascending`、`descending`です。方向を省略できます。

```bash
jai-studio objects records search object-id --filter 'company:contains:AI' --filter 'email:isnotnull' --sort 'name:asc' --limit 50
```

> 一覧・検索の次ページでは、同じ条件と並び順を保ち、直前の応答の`cursor`を渡します。CLIには`--offset`、`--include-deleted`は定義されていません。

## 5. レコードアクセスルールを操作する

SDK 0.11.0では、Studioのアクセスルールに対応する`objects rules`を使えます。`object:policy`スコープと、キー作成者の対象Objectポリシー権限が必要です。作成・削除はObjectへの書込み権限も必要です。

```text
jai-studio objects rules list <objectId>
jai-studio objects rules create <objectId> --file ./rule.json
jai-studio objects rules delete <objectId> <ruleGroupId>
```

`create`の`--file`にはルールJSONを渡します。既存ルールを先に一覧し、優先順位と削除時のアクセス影響を確認します。失敗時に表示される`code`、`params`を記録し、部分的な変更の可能性がある場合は推測で再実行せず一覧を取り直します。CLIには`bulkDelete`、`includeCount`、比較更新用のフラグはありません。大量削除、件数集計の省略、`revision`を使う競合保護には[SDK Objects](../development/sdk-objects.md)のSDKを使います。

## revisionと競合の扱い

SDKのレコード応答には`revision`が含まれる場合があり、SDKでは`expectedRevision`による比較更新・削除を指定できます。このCLIの`records update`と`records delete`には対応フラグがないため、比較更新や競合時の自動再試行はできません。同時更新が問題になる処理は、最新値を再取得するか、[SDK Objects](../development/sdk-objects.md)のSDK手順で`revision`と`expectedRevision`を扱ってください。

## うまくいかない場合

| 状況 | 対応 |
|---|---|
| JSONとして読めない | シェルの引用符、二重引用符、カンマを確認します。 |
| フィールド作成で必須キーのエラー | 必須の4キーと`fieldNum`を確認します。 |
| 検索条件が無効 | `field:operator:value`、演算子、並び順を確認します。 |
| 更新後に値が消えた | 完全なレコードを渡したか確認し、最新値を取得します。 |
| エラー詳細が必要 | 検証環境でのみ`--debug`を使い、キーは記録しません。 |

## チェックリスト

- [ ] 接続先と対象Projectを確認した
- [ ] APIキーを履歴・ログ・ソース管理に残していない
- [ ] `--data`を有効なJSONとして引用した
- [ ] 更新には完全なレコードを渡した
- [ ] 検索の次ページでは同じ条件・並び順・カーソルを使った
- [ ] 競合保護が必要なら[SDK Objects](../development/sdk-objects.md)のSDK方式を検討した
- [ ] 削除前に[Objectの仕様・制限](../build/object-specs-and-limits.md)の影響を確認した

## 次に読む記事

- [SDK Objects](../development/sdk-objects.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [CLIの設定・プロファイル](cli-config-and-profiles.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
