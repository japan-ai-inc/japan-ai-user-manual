# SDK Members

Studio SDKの`members`モジュールでは、認証済みのProjectに属する有効なメンバーを一覧取得できます。宛先候補の表示、担当者の照合、Project内の利用者数の確認など、読み取り用途で使います。メンバー情報には個人情報が含まれるため、取得範囲、表示先、ログ出力を最小限にしてください。

公開APIのHTTP仕様は[Project Members API](../api/project-members-api.md)、Project APIキーの発行・失効・スコープの管理は[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照してください。共通のタイムアウトとエラー処理は[API／SDK共通のエラー・タイムアウト・制限](api-sdk-errors-timeouts-limits.md)で扱います。

## 関連記事

- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Project Members API](../api/project-members-api.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [API／SDK共通のエラー・タイムアウト・制限](api-sdk-errors-timeouts-limits.md)

## 始める前に

- `@japan-ai-inc/studio-sdk` **0.11.0**とNode.js **22以降**を使います。
- この機能はローカルの直接開発、またはPRSサーバーランタイム向けです。ブラウザへAPIキーを渡しません。
- 対象の組織・Project・実行モードを確認します。
- Project APIキーにはメンバー読み取り用の`member:read`スコープが必要です。

> APIキーはブラウザ、公開環境変数、ソース管理、ログ、問い合わせに含めません。実値は安全なサーバー側設定から渡します。

## 利用条件と返る範囲

一覧取得には、認証に加えて、次の条件をすべて満たす必要があります。

| 条件 | 確認すること |
|---|---|
| 組織での提供 | Members APIは組織によって利用できない場合があります。管理担当者へ確認します。 |
| APIキーのスコープ | 直接開発でProject APIキーを使う場合、`member:read`が必要です。 |
| 呼出し元の権限 | 対象Projectのメンバー情報を読む権限が必要です。 |
| Projectの一致 | 認証したProjectを取り違えないでください。 |

返るのは有効なProjectメンバーです。`name`、`email`、`avatarUrl`は`null`の場合があります。

| 項目 | 内容 |
|---|---|
| `id` | ProjectメンバーのID |
| `principalId` | 利用者を表すID |
| `principalType` | `"user"` |
| `name` | 表示名。`null`の場合があります。 |
| `email` | メールアドレス。`null`の場合があります。 |
| `avatarUrl` | アバター画像のURL。`null`の場合があります。 |
| `createdAt` | 作成日時を表すISO 8601形式の文字列 |

> メンバー一覧、メールアドレス、アバターURLをログやクライアント側の永続ストレージへ無条件に保存しません。

## 1. SDKから一覧を取得する

`createClient()`で作成したクライアントの`studio.members.list()`を呼びます。引数は省略でき、`search`、`limit`、`cursor`を指定できます。直接開発では、APIキーをサーバー側の環境変数から渡します。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env["JAI_STUDIO_API_KEY"],
});

const page = await studio.members.list({
  search: "ada",
  limit: 50,
});

for (const member of page.members) {
  console.log(member.id, member.name);
}
console.log(page.totalCount, page.nextCursor);
```

`search`は名前またはメールアドレスを対象に絞り込みます。全件が必要なら`search`を指定しません。`limit`は**1〜100**、未指定時は**50**です。検索語は最大**255**文字です。

## 2. ページングと件数を扱う

`members`は現在ページ、`totalCount`は該当Project全体の件数、`nextCursor`は次ページ用です。`nextCursor`が`null`なら次ページはありません。

```ts
let cursor: string | undefined;
const members = [];

do {
  const page = await studio.members.list({ limit: 100, cursor });
  members.push(...page.members);
  cursor = page.nextCursor ?? undefined;
} while (cursor);
```

検索条件や対象Projectを変えるときは、以前の`cursor`を再利用しません。カーソルはSDKが返した値をそのまま次の同じ検索条件の呼出しへ渡します。

## 3. CLIで確認する

`jai-studio members list`でも一覧を取得できます。CLIプロファイルはSDKの`createClient()`からは読み込まれません。

```bash
jai-studio members list
jai-studio members list --search ada --limit 50
jai-studio members list --cursor '<next-cursor>'
```

`--search`は名前またはメールアドレスで絞り込みます。全件取得ではオプションを省略します。`--limit`と`--cursor`はSDKの`limit`と`cursor`に対応します。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| `403`でスコープ不足と表示される | 対象Projectのキーに`member:read`があるか確認します。 |
| `403`で利用できない、または権限不足になる | 組織での提供、対象Project、呼出し元の権限を確認します。 |
| APIキーが無効と表示される | キーの失効・削除・対象Projectを確認します。 |
| `400`で取得できない | `limit`、検索語、`cursor`を確認します。 |
| タイムアウトまたは通信エラーになる | `MembersApiError`の`statusCode`、`message`、実行モードを記録します。 |

SDKはHTTPの非成功応答を`MembersApiError`として返します。この型は`SdkApiError`を継承し、`statusCode`と`responseBody`を持ちます。`responseBody`は無加工で出力しません。既定タイムアウトは**30秒**で、`timeoutMs`、`AbortSignal`を指定できます。詳細は[API／SDK共通のエラー・タイムアウト・制限](api-sdk-errors-timeouts-limits.md)を参照してください。

問い合わせには、SDKの版、実行モード、操作時刻、HTTP状態、エラー、再現手順を記録します。APIキー、認証ヘッダー、個人情報は添付しません。

## チェックリスト

- [ ] SDK 0.11.0、Node.js 22以降、対象Project、実行モードを確認した
- [ ] 組織で利用できること、呼出し元に読み取り権限があることを確認した
- [ ] APIキーを使う場合、`member:read`だけを必要最小限として付与した
- [ ] APIキーをブラウザ、公開環境変数、ログ、ソース管理へ含めていない
- [ ] `name`、`email`、`avatarUrl`が`null`になり得ることを処理した
- [ ] `totalCount`と`nextCursor`を用途に応じて使い、カーソルを改変していない
- [ ] 取得した個人情報をログ・fixture・問い合わせへ残していない

## 次に読む記事

- [Project Members API](../api/project-members-api.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [API／SDK共通のエラー・タイムアウト・制限](api-sdk-errors-timeouts-limits.md)
