# Project Members API

Project Members APIは、認証したProjectに属する有効なメンバーを読み取り専用で一覧取得する開発者向けREST APIです。宛先候補の表示、担当者の照合、利用者数の確認に使えます。表示名、メールアドレス、アバターURLは個人情報になり得るため、必要な項目・取得件数・表示先を用途に応じて絞ってください。

SDKでの実装は[SDK Members](../development/sdk-members.md)、CLIでの確認は[CLI Members](../cli/cli-members.md)、Projectの参加・権限の考え方は[組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)を参照してください。

## 関連記事

- [SDK Members](../development/sdk-members.md)
- [CLI Members](../cli/cli-members.md)
- [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)

## 始める前に

本APIの本番ベースURLは`https://api.japan-ai.co.jp`です。Project APIキーで直接呼び出す場合は、対象Projectのキーを安全なサーバー側設定から`x-api-key`ヘッダーへ渡します。`Authorization: Bearer <キー>`も受け付けます。以降の例では`x-api-key`を使います。

```bash
export JAI_STUDIO_API_KEY='安全な保管場所から取得した値'

curl -sS 'https://api.japan-ai.co.jp/api/v1/project-members?limit=50' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

> APIキーをブラウザ用JavaScript、公開環境変数、ソース管理、コマンド履歴、ログ、問い合わせへ含めてはいけません。発行、最小スコープ、期限、ローテーション、失効は[Project APIキーを管理する](../operations/manage-project-api-keys.md)に従ってください。

## 利用条件と取得範囲

`GET /api/v1/project-members`を実行するには、次のすべてを満たす必要があります。APIキーを作成できることや、Projectに参加していることだけでは、一覧取得を保証しません。

| 条件 | 確認すること |
|---|---|
| 組織での提供 | Members APIが対象組織で利用可能であること。利用できない場合は、別の認証情報・APIで代用せず、組織の管理担当者へ確認します。 |
| 認証 | Project APIキー、またはPage実行環境から引き継がれる認証コンテキストで呼び出すこと。 |
| APIキーのスコープ | Project APIキーを使う場合、`member:read`が必要です。不要な書込み・削除のスコープは追加しません。 |
| IAMによる認可 | 呼出し元が対象Projectのメンバー情報を読むことを許可されていること。キー作成者のProjectアクセスや権限状態が変わると、利用できなくなる場合があります。 |
| Projectの一致 | 認証に結び付いたProjectだけを対象にします。他のProject・組織のメンバーをIDや検索語で指定して取得するAPIではありません。 |

返るのは、対象Projectで有効かつ、対象組織で有効な利用者です。退会済みの利用者、無効なProjectメンバーは含まれません。メンバーのロールは応答に含まれないため、ロールの取得・判定をこのAPIの目的にしないでください。

## エンドポイント

| 操作 | メソッド・パス | 必要スコープ |
|---|---|---|
| Projectメンバー一覧の取得 | `GET /api/v1/project-members` | `member:read` |

### クエリパラメーター

| 名前 | 場所 | 必須 | 既定・制限 | 意味 |
|---|---|---:|---|---|
| `search` | query | いいえ | 最大255文字 | 表示名またはメールアドレスを大文字・小文字を区別せず部分一致で絞り込みます。前後の空白は除かれます。 |
| `limit` | query | いいえ | `50`、1〜100の整数 | 1ページで返す最大件数です。 |
| `cursor` | query | いいえ | 指定なし | 前回の応答の`nextCursor`をそのまま渡します。 |

検索語を省略すると、検索による絞り込みを行いません。空白だけの`search`は空文字として扱われるため、全件取得のために明示する必要はありません。`limit`は数字だけで指定します。範囲外、整数でない値、255文字を超える検索語は400です。

## 応答、並び順、ページング

成功時は200で、次の形式を返します。`requestId`は要求を識別するUUIDです。問い合わせ時は必要に応じて記録しますが、メンバー全件や認証情報と一緒に共有しません。

```json
{
  "success": true,
  "requestId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "members": [
    {
      "id": "project-member-id",
      "principalId": "user-id",
      "principalType": "user",
      "name": "表示名またはnull",
      "email": "mail@example.comまたはnull",
      "avatarUrl": "https://...またはnull",
      "createdAt": "2026-10-01T00:00:00.000Z"
    }
  ],
  "totalCount": 1,
  "nextCursor": null
}
```

| フィールド | 内容 |
|---|---|
| `success` | 成功時は`true`です。 |
| `requestId` | 要求を識別するUUIDです。 |
| `members` | 現在ページのメンバー配列です。 |
| `members[].id` | ProjectメンバーのIDです。 |
| `members[].principalId` | 利用者を表すIDです。 |
| `members[].principalType` | 現在は`"user"`です。 |
| `members[].name` / `email` / `avatarUrl` | 表示名、メールアドレス、アバターURLです。いずれも`null`の場合があります。 |
| `members[].createdAt` | メンバー情報の作成日時を表すISO 8601形式の文字列です。 |
| `totalCount` | 検索条件に一致する、対象Project全体の件数です。 |
| `nextCursor` | 次ページがあれば文字列、なければ`null`です。 |

一覧はメンバー情報の作成日時の降順で、同じ日時では利用者IDの昇順です。`totalCount`だけが必要な用途では、すべてのページを取得しません。次ページでは、返された`nextCursor`を改変せずに渡し、同じ`search`と`limit`を維持します。

```bash
curl -sS 'https://api.japan-ai.co.jp/api/v1/project-members?search=ada&limit=50' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"

curl -sS 'https://api.japan-ai.co.jp/api/v1/project-members?search=ada&limit=50&cursor=<nextCursor>' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

カーソルは不透明な値です。解析、生成、編集しないでください。認識できないカーソルは先頭ページとして扱われるため、重複を避ける処理では必ず直前の応答の値だけを使用します。取得中にメンバーが追加・無効化される可能性があるため、複数ページの結果を不変の名簿として扱う処理では、取得時刻と再確認方法を設計します。

> `email`、`avatarUrl`、一覧全体をログ、テストfixture、画面の永続ストレージ、チケットへ無条件に保存しないでください。`name`、`email`、`avatarUrl`が`null`でも処理できるように実装します。

## うまくいかない場合

| 状態 | 確認・対応 |
|---|---|
| 400 | `limit`が1〜100の整数か、`search`が255文字以下か、`cursor`を応答から未変更で渡したかを確認します。 |
| 401 | 認証情報がない、または認証コンテキストを確認できない可能性があります。サーバー側設定、対象Project、実行モードを確認します。 |
| 403 | 組織での提供、`member:read`、キーの有効期限・失効、キー作成者のProjectアクセス、IAMによる認可を順に確認します。スコープを推測で広げず、必要なら最小スコープの新しいキーへ安全に切り替えます。 |
| 503 | IAMの認可確認を完了できない場合があります。短時間に連続再送せず、対象Project、操作時刻、HTTP状態、`requestId`を記録して管理担当者へ確認します。 |
| 通信失敗・タイムアウト | 読み取りであっても、応答が届かなかった理由だけで結果を確定しません。必要なときは改めて一覧を取得します。共通の扱いは[API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)を参照してください。 |

429の有無や待機時間は環境・経路により異なることがあります。共通的な待機・再試行の方針は[API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)を参照し、固定の再試行回数や待機秒数を実装に埋め込まないでください。

## チェックリスト

- [ ] 対象組織でMembers APIを利用できることを確認した
- [ ] 対象Projectに対応する認証情報と、必要最小限の`member:read`を用意した
- [ ] IAMによる読み取り許可と、キー作成者のアクセス状態を確認した
- [ ] `limit`、`search`、未変更の`nextCursor`を正しく扱った
- [ ] `name`、`email`、`avatarUrl`が`null`の場合を処理した
- [ ] メンバー情報とAPIキーをログ・共有資料・クライアント側へ残していない

## 次に読む記事

- [SDK Members](../development/sdk-members.md)
- [CLI Members](../cli/cli-members.md)
- [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
