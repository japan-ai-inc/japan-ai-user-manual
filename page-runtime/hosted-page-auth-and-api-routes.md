# Hosted Pageの認証・API経路

Hosted Pageのブラウザコードは、Project APIキー付きの外部公開APIではなく、**Page実行環境**の同一オリジン相対パスを呼びます。Pageを開くと作られるPage単位のセッションが、組織・Project・Pageと利用者（または訪問者）の認証コンテキストを結び付けます。ブラウザのJavaScript、公開用環境変数、初期表示データ、ログへProject APIキーを置きません。

## 関連記事

- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [Pageの画面遷移・ディープリンク](../development/page-navigation-and-deep-links.md)
- [Pageのサーバー処理・環境変数・Secret参照](../development/page-server-env-and-secrets.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
- [Objects API](../api/objects-api.md)
- [Storage API](../api/storage-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Hosted PageのObjects API](hosted-page-objects-api.md)

## 始める前に

この記事の対象は、Hosted Pageの**ブラウザ**から呼ぶAPIです。Pageのサーバー処理は別の実行面であり、ブラウザへキーやSecretを返しません。詳細は[Pageのサーバー処理・環境変数・Secret参照](../development/page-server-env-and-secrets.md)と[実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)を確認してください。

> Pageの公開URLを知っていることと、任意のProjectリソースを操作できることは同じではありません。表示データ、許可する操作、アクセスモードを組み合わせて受入試験を行ってください。

## 1. ブラウザでは相対パスとPageセッションを使う

Page内ではSDKを空の`baseUrl`で初期化します。これにより`fetch`はPageの同一オリジンに対する相対パスになり、ブラウザが保持するPageセッションで処理されます。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const files = await studio.storage.list({ path: "/" });
```

SDK 0.11.0のブラウザ対応範囲は`agents`、`datasets`、`objects`、`storage`、`platform`です。`agents.invoke()`はPage実行環境を検出すると`POST /api/agent-chat`を使います。Objects、Storage、Dataset読取りは、例えば`/api/v1/objects/...`、`/api/v1/storage/...`、`/api/v1/datasets...`を使います。

`/api/v1/*`は任意の公開APIを中継する汎用入口ではありません。Objects、Storage、Dataset読取りはブラウザ向けSDKの対象です。`workflows`と`scheduledTasks`は対象外です。Projectメンバーの取得は、Page実行環境での許可と読取り権限がそろう場合だけ検討し、[Project Members API](../api/project-members-api.md)を確認します。ほかの機能はサーバー処理へ分離します。

| 用途 | Pageブラウザでの経路 | 使い分け |
|---|---|---|
| Agent実行 | `/api/agent-chat` | `agents.invoke()`を使います。`/api/v1/agents`の一覧・詳細取得をブラウザ対応と決めつけません。 |
| Objects | `/api/v1/objects/...` | `objects`のSDK/API仕様に従います。詳細は[Objects API](../api/objects-api.md)と[Hosted PageのObjects API](hosted-page-objects-api.md)を参照します。 |
| Storage | `/api/v1/storage/...` | `storage`を使います。ブラウザでは`downloadFolder()`を利用できません。詳細は[SDK Storage](../development/sdk-storage.md)を参照します。 |
| Dataset読取り | `/api/v1/datasets...` | `datasets.list()`と`datasets.search()`だけを使います。対応するPage実行環境がなければ404です。組織Datasetは、署名済み利用者のセッションでだけ扱えます。 |
| Tool Proxy | `/api/tools/...` | Page実行環境専用です。[Tool Proxy API](../api/tool-proxy-api.md)の経路だけを使います。 |

> `x-api-key`、`Authorization: Bearer`、`pak_...`形式のProject APIキーを、Pageブラウザのリクエストへ追加してはいけません。コードを難読化しても、ブラウザへ配布した値は利用者から取得できます。

## 2. アクセスモードごとのセッションを確認する

| 「アクセス設定」のモード | Pageを開くときの認証コンテキスト | 実装・試験の要点 |
|---|---|---|
| 「サインイン必須」 | サインイン済みでPageへのアクセス権を持つ利用者のPageセッションです。 | 未サインインなら認証後に元のPage URLへ戻ることを確認します。 |
| 「サインイン任意」 | サインイン済みなら利用者のセッション、未サインインなら匿名のPageセッションです。 | 同じ画面・APIで、サインイン状態と未サインイン状態を個別に試験します。 |
| 「匿名アクセス」 | サインイン済みかどうかにかかわらず、匿名のPageセッションです。 | 利用者固有の情報や権限を前提にせず、匿名訪問者として読取り・書込みの可否を確認します。 |
| 「外部顧客認証」 | 設定済みの顧客IDプロバイダーで認証された訪問者のPageセッションです。 | 外部認証が有効でセッションがない場合は401となり、現在のPage文書を再読み込みして認証をやり直します。 |

「サインイン任意」と「匿名アクセス」はどちらも未サインインで開けますが、前者は既存の利用者セッションを使え、後者は常に匿名コンテキストになります。「外部顧客認証」は設定済みの接続を前提に確認してください。画面への到達とAPI操作の両方で、組織・Project・Pageがセッションと一致することが検証されます。

## 3. 書込みはOrigin検証を通す

`POST`、`PUT`、`PATCH`、`DELETE`、およびWebSocketの接続要求では、PageセッションだけでなくOrigin検証が行われます。`Origin`がない場合は`Referer`のオリジンが確認され、信頼できない値または両方がない書込みは403です。通常の同一オリジン`fetch`とSDK利用では、このための独自ヘッダーを追加する必要はありません。

他サイトからCookieを利用して書込む設計、またはOriginなしで状態変更する設計は採りません。Page内のクライアント操作として実行するか、Pageサーバー処理へ分けて認可を実装してください。

## 4. 401を一律に再試行しない

セッション不足・無効の401は、ページ側のランタイムが認証更新の対象として扱います。通常のPageセッションでは親画面へ`prs:retry-requested`を通知し、親画面が新しい認証情報でPageを読み直します。外部顧客認証ではPageを再読み込みして認証を再開します。再読込みも抑制します。

一方、アプリ自身や転送先が返す401は認証更新の合図とは限りません。レスポンスがPage実行環境のセッション認証エラーとして印付けられた場合だけ更新に委ね、業務上の401・403は利用者に必要な案内を表示します。独自実装で401を無条件に再送したり、Cookie・トークンを画面へ表示したりしないでください。画面復帰の検証は[Pageの画面遷移・ディープリンク](../development/page-navigation-and-deep-links.md)も参照します。

## Pageセッション経路とPublic APIの違い

| 観点 | Pageセッション経路 | Public API（Project APIキー） |
|---|---|---|
| 呼出元 | Hosted Page内のブラウザ | ローカル開発、管理された自動化、またはサーバー側処理 |
| URL | Pageと同じオリジンの`/api/...` | `https://api.japan-ai.co.jp`のAPI経路 |
| 認証情報 | Pageを開いた結果のセッション | Project APIキーを`x-api-key`またはBearerで渡します |
| ブラウザへのキー | 不要、かつ禁止 | 使用しません。キーはサーバー側だけに保管します |
| 利用範囲 | Page実行環境が提供する経路とブラウザ対応SDKに限定 | キーのスコープと各Public API仕様に従います |
| 運用 | セッション切れ時はPageの再認証経路を使います | 発行・最小スコープ・期限・ローテーション・失効を管理します |

Public APIの詳細は[Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)から[Project Members API](../api/project-members-api.md)まで、キーの管理は[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照してください。

## チェックリスト

- [ ] ブラウザSDKを`createClient({ baseUrl: "" })`で初期化し、Project APIキーを読んでいない
- [ ] Page相対の`/api/...`を使い、公開APIのホストをブラウザコードへ固定していない
- [ ] アクセスモードごとに、画面表示とAPI操作を未認証・認証済みの両方で確認した
- [ ] 書込みを同一オリジンのクライアント操作として実装し、401の無条件再試行をしていない
- [ ] 401、403、429、一時的な5xxをUIで区別し、Cookie・トークン・APIキーをログや問い合わせに残していない

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| `/api/v1/...`が404になる | 先頭パスがPage実行環境で提供されるものか、ブラウザ対応のSDKモジュールかを確認します。Public API用の任意の経路を中継するものではありません。 |
| 401になる | Pageを開き直し、アクセスモード、対象Pageへの到達、認証更新が行われる表示経路を確認します。401をキー追加で回避しません。 |
| 403になる | Origin/Refererを偽装・固定していないか、同一オリジンのPage操作か、Page・Projectの組合せが正しいかを確認します。 |
| StorageやObjectsの操作が期待と違う | SDKのブラウザ対応範囲と、各APIのPublic API仕様を分けて確認します。ブラウザで非対応の操作はサーバー処理へ分離します。 |

問い合わせには、Page名、アクセスモード、SDKバージョン、相対パス、HTTP状態、発生日時、再現手順を記録します。Project APIキー、Cookie、認証ヘッダー、外部認証のトークンは添付しません。

## 次に読む記事

- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [Pageの画面遷移・ディープリンク](../development/page-navigation-and-deep-links.md)
- [Pageのサーバー処理・環境変数・Secret参照](../development/page-server-env-and-secrets.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)〜[Project Members API](../api/project-members-api.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Hosted PageのObjects API](hosted-page-objects-api.md)
- [Hosted PageのStorage API](hosted-page-storage-api.md)
- [Pageとチャット・コンテキストの連携](page-chat-and-context.md)
- [Page補助APIの対応範囲](page-auxiliary-api-scope.md)
