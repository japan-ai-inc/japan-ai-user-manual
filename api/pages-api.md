# Pages API

Pages APIは、完成済みのPage用ZIPを一時アップロードし、新しいPageまたは既存Pageの新しいバージョンとして登録する開発者向けREST APIです。

このAPIはZIPのアップロード準備とデプロイだけを提供します。Page一覧、アクセスモード、公開URL、外部顧客認証などの**アクセス設定を取得・変更するエンドポイントはありません**。公開範囲はStudioの対象Pageで「アクセス設定」を開いて変更します。SDK・CLIの使い分けは[SDK Pages・Platform](../development/sdk-pages-and-platform.md)を参照してください。

## 関連記事

- [CLI Pages](../cli/cli-pages.md)
- [SDK Pages・Platform](../development/sdk-pages-and-platform.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)

**対象外：**Pageの一覧・アクセス設定はStudio UI、ローカルでの成果物確認は[Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)、新しい版の確認は[Pageのビルド・起動・更新](../development/page-build-start-update.md)を参照してください。

## 始める前に

本番ベースURLは`https://api.japan-ai.co.jp`、共通パスは`/api/v1/pages`です。各エンドポイントには、対象ProjectのProject APIキーと`page:write`スコープが必要です。キーは`x-api-key`ヘッダー、または`Authorization: Bearer <キー>`で渡せます。以下では`x-api-key`を使います。

```bash
export JAI_STUDIO_API_KEY='安全な保管場所から取得した値'
export PAGE_ZIP='./page-release.zip'
```

> APIキーをブラウザ用JavaScript、公開環境変数、ソース管理、ログ、問い合わせへ含めてはいけません。発行、最小スコープ、失効は[Project APIキーを管理する](../operations/manage-project-api-keys.md)に従ってください。送信するZIPは事前に完成させます。

## エンドポイント一覧

| 操作 | メソッド・パス | 必要スコープ |
|---|---|---|
| 新規Page用のZIPアップロード準備 | `POST /api/v1/pages/upload-url` | `page:write` |
| 新規Pageとしてデプロイ | `POST /api/v1/pages/deploy` | `page:write` |
| 既存Pageの版用ZIPアップロード準備 | `POST /api/v1/pages/{pageId}/upload-url` | `page:write` |
| 既存Pageの新しい版をデプロイ | `POST /api/v1/pages/{pageId}/deploy` | `page:write` |

`pageId`は更新対象のPage IDです。対象PageがProjectにない場合は404です。リリース管理されているPageは、この公開APIによる更新が拒否されます。新規・更新とも、**準備 → ZIP送信 → デプロイ**の順で実行します。

## 1. ZIPアップロードを準備する

新規Pageは`POST /upload-url`、既存Pageの更新は`POST /{pageId}/upload-url`を使います。本文は厳密に検証されるため、表にないフィールドを送らないでください。更新用の準備では、指定Pageを更新できることも確認されます。

| 名前 | 場所 | 必須 | 既定・制限 | 意味 |
|---|---|---:|---|---|
| `fileName` | body | はい | 前後空白を除き1〜255文字 | アップロードするZIPのファイル名です。 |
| `contentType` | body | いいえ | 指定なしは`application/zip`、1〜255文字 | ZIP送信時に使うContent-Typeです。 |
| `pageId` | path | 更新時のみ | 空でない文字列 | 更新対象のPage IDです。 |

```bash
curl -sS -X POST 'https://api.japan-ai.co.jp/api/v1/pages/upload-url' \
  -H "x-api-key: $JAI_STUDIO_API_KEY" \
  -H 'content-type: application/json' \
  --data '{"fileName":"page-release.zip","contentType":"application/zip"}'
```

成功時は200で`uploadUrl`、`uploadPath`、`expiresAt`、`requiredHeaders`を返します。`uploadPath`はデプロイ本文へそのまま渡す不透明な値、`requiredHeaders`はZIP送信時に使うヘッダーです。

`uploadUrl`へ`PUT`でZIP本体を送ります。`requiredHeaders`の内容を保持して送信します。

```bash
curl -sS -X PUT "$UPLOAD_URL" \
  -H 'Content-Type: application/zip' \
  --upload-file "$PAGE_ZIP"
```

ZIPの容量・構造の条件は[Pageの仕様・制限](../build/page-specs-and-limits.md)を参照してください。

> `uploadPath`は一度のデプロイで消費されます。同じ値を再送して別のデプロイを試みないでください。期限切れ、別Project・別キーでの利用、または既に消費済みの場合は400になります。通信失敗時も、同じ操作を無条件に再実行しません。

## 2. 新規・更新をデプロイする

ZIP送信後、新規作成は`POST /deploy`、更新は`POST /{pageId}/deploy`を呼びます。新規作成は`name`を省略できます。更新本文に`name`は含めません。

| 名前 | 場所 | 新規 | 更新 | 制限・意味 |
|---|---|---:|---:|---|
| `pageId` | path | — | 必須 | 更新対象のPage IDです。 |
| `uploadPath` | body | 必須 | 必須 | 前後空白を除き1文字以上。準備応答の値です。 |
| `zipFileName` | body | 必須 | 必須 | 前後空白を除き1〜255文字。ZIP名です。 |
| `name` | body | 任意 | — | 前後空白を除き1〜255文字。新規Pageの表示名です。 |

```bash
# 新規Page
curl -sS -X POST 'https://api.japan-ai.co.jp/api/v1/pages/deploy' \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'content-type: application/json' \
  --data '{"uploadPath":"<uploadPath>","zipFileName":"page-release.zip","name":"受注ダッシュボード"}'

# 既存Pageの新しい版
curl -sS -X POST "https://api.japan-ai.co.jp/api/v1/pages/$PAGE_ID/deploy" \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'content-type: application/json' \
  --data '{"uploadPath":"<uploadPath>","zipFileName":"page-release.zip"}'
```

ZIPの適用はアクセス設定を変更しません。公開範囲、公開URL、利用者の確認はStudio UIの「アクセス設定」で行います。

## デプロイ応答と確認

新規・更新のデプロイ成功時は200で同じ形式を返します。

| フィールド | 内容 |
|---|---|
| `pageId` | 作成または更新したPage IDです。 |
| `studioUrl` | Studioで対象Pageを確認するURLです。 |
| `runtimeUrl` | 実行用URLです。存在しない場合は`null`です。 |
| `version` | デプロイされたバージョンです。 |
| `fileCount` | 登録されたファイル数です。 |
| `createdAt` | デプロイ結果の作成日時を表す文字列です。 |
| `rebuildTriggered` | Pageの再ビルド要求が成功したかを示す真偽値です。 |

`runtimeUrl`が`null`、または`rebuildTriggered`が`false`でも利用可否を推測しないでください。`studioUrl`から対象Pageと適用中の版を確認し、設定済みのアクセス条件で初期表示と代表操作を検証します。CLIの流れは[CLI Pages](../cli/cli-pages.md)を参照してください。

## エラーと利用制御

入力検証、無効・期限切れ・消費済みの`uploadPath`、ZIPの形式・容量・構造の問題は400です。キー、`page:write`、Project上の操作許可、Page機能の提供条件、Page数上限などを満たさない場合は403、更新対象がない場合は404です。APIは、アップロード準備とデプロイに利用制御を適用し、デプロイの同時実行も制御します。制限に達すると429になります。

429時は連続再送せず、並列デプロイ数と要求頻度を下げます。Page APIの429応答に待機秒数が含まれることは確認できないため、待機値を決め打ちしません。一般的な扱いは[API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)を参照してください。

| 状況 | 確認・対応 |
|---|---|
| 400 | `fileName`、`contentType`、`uploadPath`、ZIP名、ZIPの容量・構造を確認します。アップロード準備からやり直す必要があるかを確認します。 |
| 403 | APIキー、`page:write`、対象Project、Page機能の提供条件、操作許可を確認します。スコープを推測で追加しません。 |
| 404 | `pageId`と対象Projectを確認します。別ProjectのIDを使いません。 |
| 429 | 同じZIPやデプロイを連続送信せず、並列数と頻度を下げてから再試行します。 |
| タイムアウト・通信失敗 | 新規Page、Pageのバージョン、`studioUrl`で現在状態を確認してから次の操作を判断します。 |

## チェックリスト

- [ ] 対象Projectに対する安全なProject APIキーと`page:write`を用意した
- [ ] 完成済みのZIPが[Pageの仕様・制限](../build/page-specs-and-limits.md)の容量・構造・秘密情報の条件を満たすことを確認した
- [ ] 準備、ZIPの`PUT`、デプロイを順に実行し、`uploadPath`を改変・再利用していない
- [ ] 新規作成と既存Page更新で、正しいパスと`pageId`を使い分けた
- [ ] `studioUrl`、`runtimeUrl`、バージョン、再ビルド結果を確認した
- [ ] アクセスモードと共有URLはAPIで変更できないことを理解し、Studio UIの「アクセス設定」で確認した
- [ ] 429、タイムアウト、通信失敗時に状態確認なしでデプロイを連続再送していない

## 次に読む記事

- [CLI Pages](../cli/cli-pages.md)
- [SDK Pages・Platform](../development/sdk-pages-and-platform.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
