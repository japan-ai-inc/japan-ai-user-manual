# Datasets API

Datasets APIは、Datasetの一覧取得と、検索（該当する文書断片の取得のみ）をHTTPで行う公開APIです。SDKでは`datasets.list()`と`datasets.search()`が同じ経路を呼びます。画面上の準備手順は[Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)、仕様は[Datasetの仕様・制限](../build/dataset-specs-and-limits.md)を参照してください。

## 関連記事

- [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)
- [Datasetの仕様・制限](../build/dataset-specs-and-limits.md)
- [実行モード・認証](../development/execution-modes-and-auth.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)

## この記事の範囲

この記事は、読取り用の2経路だけを扱います。同じ`/api/v1/datasets`には、Datasetの作成・更新・削除やドキュメントの追加・削除の経路もありますが、これらを公開して利用してよいかは確認できていないため、ここでは案内しません。SDKの`datasets`モジュールも、読取り専用（`list()`と`search()`）です。

## 始める前に

ベースURLは`https://api.japan-ai.co.jp`、経路は`/api/v1/datasets`です。Project APIキーは`x-api-key`または`Authorization: Bearer <キー>`で渡し、必要スコープは`dataset:read`です。

```bash
export JAI_STUDIO_API_KEY='安全な保管場所から渡す値'
curl -H "x-api-key: $JAI_STUDIO_API_KEY" \
  'https://api.japan-ai.co.jp/api/v1/datasets?limit=50'
```

> APIキーをブラウザ、ソース管理、ログ、チケットへ残してはいけません。

## 1. Datasetを一覧する

| メソッド・パス | 必要スコープ | パラメーター | 主な成功応答 |
|---|---|---|---|
| `GET /api/v1/datasets` | `dataset:read` | `limit`：任意／`cursor`：任意／`search`：任意、Dataset名の部分一致（大文字小文字を区別しない）／`scope`：任意、`project`（既定）または`organization` | `datasets`、`totalCount`、`nextCursor` |

- `scope=project`は、Projectにリンクされたデータセットを返します。
- `scope=organization`は、Projectにリンクされていない組織データセットのうち、サインイン済みの利用者が読めるものを返します。利用者のセッションが必要で、Project APIキーでは使えません。
- 各Datasetは`id`、`name`、`description`、`status`、`version`、`updateInterval`、`itemCount`、`createdAt`、`updatedAt`、`lastUpdateAt`を含みます。
- 利用者のセッションでは、権限で読めないDatasetが除かれるため、1ページの件数が`limit`より少ない場合があります。また、`totalCount`にはその権限の絞り込みが反映されません。続きがある間は`nextCursor`で取得を続けます。

## 2. Datasetを検索する

| メソッド・パス | 必要スコープ | パラメーター | 主な成功応答 |
|---|---|---|---|
| `POST /api/v1/datasets/{datasetId}/search` | `dataset:read` | `datasetId`：パス、必須／本文：`query`（必須）、`topN`（任意、1〜50、既定10） | `datasetId`、`results` |

この経路は検索のみで、回答は生成しません。検索できるのは、Projectにリンクされたデータセットと、サインイン済みの利用者が読める組織データセットです。

各結果は`rank`、`chunkId`、`score`、`text`を含み、`filename`、`title`、`pageNumber`、`sheetName`、`sourceUrl`が含まれる場合があります。

```bash
curl -X POST \
  -H "x-api-key: $JAI_STUDIO_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{"query":"返品の条件","topN":5}' \
  'https://api.japan-ai.co.jp/api/v1/datasets/<datasetId>/search'
```

`<datasetId>`は、一覧応答の`id`に置き換えます。

## よくある誤解

### 検索結果がそのまま回答になる

返るのは、質問に近い文書断片です。回答の生成や、出典の妥当性の判断は、呼び出し側で行います。

### `scope=organization`はProject APIキーでも使える

使えません。利用者のサインインセッションを前提とします。

### すべての文書の漏れを確認できる

検索は近い断片を返す仕組みのため、契約書の論点を漏れなく挙げるような全文確認には向きません。

## うまくいかない場合

| 状態 | 確認すること |
|---|---|
| 401 | キーの値と有効期限を確認します。 |
| 403 | 対象Project、`dataset:read`、`scope=organization`を利用者セッションで呼んでいるかを確認します。 |
| 404 | `datasetId`と対象Projectを確認します。別ProjectのIDを探索しません。 |
| 429 | 応答の`Retry-After`と`retryAfterSeconds`に従い、連続再送を避けます。 |

問い合わせには、対象Project、API経路、`requestId`、`datasetId`、実行時刻、HTTP状態を記録します。APIキーやAuthorizationヘッダーは記載しません。

## チェックリスト

- [ ] Project APIキーをサーバー側の安全な設定から渡した
- [ ] `dataset:read`のスコープを付けた
- [ ] 組織Datasetを読む場合は、利用者のセッションで呼んだ
- [ ] `nextCursor`がある間は取得を続け、`totalCount`だけで件数を判断していない
- [ ] 検索結果を、回答ではなく文書断片として扱った

## 次に読む記事

- [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)
- [実行モード・認証](../development/execution-modes-and-auth.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
