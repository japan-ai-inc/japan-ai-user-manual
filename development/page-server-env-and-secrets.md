# Pageのサーバー処理・環境変数・Secret参照

外部APIのトークンなどをPageで使う場合は、値をブラウザ用のJavaScript、HTML、初期データ、ログ、ソースマップへ入れず、**Pageのサーバー側だけ**で読むように設計します。Project Secretの実値をZIPへ含めたり、利用者のブラウザから直接外部サービスへ送ったりしてはいけません。

StudioでホストされたPageは、アップロードされたソースからinstallと`build`を行い、Node／Bunのサーバーとして動きます（静的なPageを除く）。サーバー処理は、ルートの`.env`に書いた環境変数とProject Secret参照を`process.env`から読めます。

## 要点

| 項目 | Pageの直接アップロード（画面・CLI） | Appの公開・インストール |
|---|---|---|
| 読み込む`.env` | ルートの`.env`と`.env.production`のみ（ほかの`.env.*`は無視） | ルートの`.env`と`.env.<名称>`（`.env.local`系などは除外） |
| `${secret:名前}`の解決 | 対象ProjectのProject Secretを**名前でそのまま**解決。紐づけの画面はない | 導入先で「シークレットを選択」から紐づける |
| 公開用接頭辞（`NEXT_PUBLIC_`など）のキーへのSecret参照 | 拒否されない。開発者が書かないようにする | 拒否される |
| 予約キー | `PORT`、`NODE_ENV`、`PRS_*`などは除外される | 同左 |
| 値の上限 | Project Secretは最大8,192文字だが、環境変数として渡せるのは1値4,096バイトまで | 同左 |

サーバーは`process.env.PORT`で待ち受けます。`/api/v1/*`や`/api/storage`などのパスはプラットフォームが使用するため、自分のAPIルートに使いません。SSRの最初の応答には約60秒の制限があります。

## 関連記事

- [Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Pageのソース構成とランタイム](page-source-structure-and-runtime.md)

## 始める前に

- Secretの実値を、対象Projectの**「プロジェクトシークレット」**で登録します。作成・更新・削除、名前・値の入力規則、権限、ローテーションは[Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)を正本とします。
- Project Secret名は、先頭を英小文字とし、以後に英小文字・数字・アンダースコアだけを使う、**64文字以下**の名前です。空文字とUUID形式は使えません。例えば`external_api_token`は使用できます。値は空にできず、最大**8,192文字**です。ただし、Pageの環境変数として渡せる値は**1値4,096バイト**までです。これより長い値は保存できても、Pageの起動時に失敗します。
- サーバー処理が必要な理由を明確にします。外部API呼出、署名生成、トークンを伴うデータ取得など、秘密値を必要とする処理だけをサーバー側へ置きます。ブラウザは、必要最小限の入力をサーバー処理へ渡し、Secretを含まない結果だけを受け取ります。
- `NEXT_PUBLIC_`、`VITE_`、`REACT_APP_`、`PUBLIC_`で始まる環境変数名は、ビルド時にブラウザ用のコードへ埋め込まれ得ます。Project Secret参照には使いません。

> Secret名は用途を示す識別子であり、値ではありません。`external_api_token`のような名前は設定・診断で参照され得ますが、トークン本文、APIキー、Cookie、認証ヘッダーを名前、エラー表示、問い合わせへ書かないでください。

## 1. `.env` にSecret参照を宣言する

Secretの実値ではなく、次の完全一致の参照形式を記述します。左辺は、サーバー側で読む環境変数名です。

```dotenv
EXTERNAL_SERVICE_TOKEN=${secret:external_api_token}
```

環境変数名は英字またはアンダースコアで始め、以後に英字・数字・アンダースコアを使います。値の中の`$VAR`や`${VAR}`による環境変数の展開はサポートされません。Secret参照と別の環境変数を組み合わせるのではなく、必要な値を1つのSecretとして登録してください。

`PORT`、`NODE_ENV`、`PRS_*`などの予約キーは、`.env`に書いても除外されます。

### Pageを直接アップロードする場合

画面またはCLIでPageを直接アップロードする場合、読み込まれるのはルートの`.env`と`.env.production`だけです。ほかの`.env.*`（`.env.local`、`.env.staging`など）は無視されます。

`${secret:名前}`は、対象ProjectのProject Secretを名前でそのまま解決します。紐づけの画面はありません。同じ名前のSecretを対象Projectに登録しておいてください。

直接アップロードでは、公開用接頭辞のキーにSecret参照を書いても拒否されません。`NEXT_PUBLIC_`などのキーにSecret参照を書かないことを、開発者自身が守ってください。

### Appとして公開・インストールする場合

Appの公開・インストールでは、ルートの`.env`および`.env.<名称>`を読みます。`.env.local`、`.env.local.<名称>`、末尾が`.local`のファイルは除外されます。`.env.example`、`.env.sample`、`.env.template`も扱われません。複数のファイルがある場合は、`.env`が先に処理され、`.env.<名称>`が後から同じキーを上書きします。

Appの流れでは、Secret名がProject Secretの命名規則に合わない場合、または公開用接頭辞の環境変数名にSecret参照を書いた場合は、リリースできません。導入先で必要なSecretの案内が出た場合は、「シークレットを選択」から対象のSecretを紐づけます。詳しくは[配布可能なPageの設定設計](design-distributable-page-config.md)を参照してください。

> `${secret:external_api_token}`の代わりに、発行元から受け取ったトークン文字列を`.env`へ書いてはいけません。アップロードするソースに実値が残り、配布物・履歴・ログへ露出するおそれがあります。

## 2. サーバー処理だけで値を読む

サーバーで実行されるNode.jsコードでは、宣言した左辺の名前で環境変数を読みます。次は外部サービスへ問い合わせ、ブラウザには処理結果だけを返す形の例です。サーバー処理を置く入口は、使用するフレームワークの方式に合わせてください。

```ts
export async function loadItems() {
  const token = process.env["EXTERNAL_SERVICE_TOKEN"];
  if (!token) throw new Error("外部サービス用の設定がありません");

  const response = await fetch("https://example.invalid/v1/items", {
    headers: { Authorization: `Bearer ${token}` },
  });
  if (!response.ok) throw new Error("外部サービスへの接続に失敗しました");

  const data = await response.json();
  return data.items;
}
```

ブラウザ側は`token`、`process.env`の値、外部サービスの認証ヘッダーを受け取りません。サーバー処理の返却値は、画面に必要な項目だけに絞ります。例外をブラウザへ返す場合も、元のAPIエラーやリクエスト設定をそのまま返さず、Secret値・Authorizationヘッダー・接続文字列を含まないメッセージにします。

`JAI_PROJECT_API_KEY`のようなProject APIキー用の環境変数は自動では注入されません。サーバー処理でStudio APIを使う場合は、変数名を開発者がルートの`.env`で決めます。Appの公開時には、その値がプラットフォーム管理のキーに置き換わります。

次の実装は避けます。

```ts
// ブラウザへ渡るコード・設定にSecretを置く例。使用しない。
const token = process.env.NEXT_PUBLIC_EXTERNAL_SERVICE_TOKEN;
window.runtimeConfig = { token };
```

ブラウザ公開用の接頭辞を使うビルドツールでは、ビルド時に値がクライアントバンドルへ埋め込まれます。独自のビルド設定、生成されるJavaScript、HTML、JSON、ソースマップにも実値が入らないよう、**ビルド前後の成果物を確認し、実値を含めないことを開発者が保証してください。**

## 3. 公開時に満たす条件

Secret参照を含むPageを公開・有効化するには、少なくとも次を満たす必要があります。

| 条件 | 確認すること |
|---|---|
| 機能の利用条件 | 組織でProject Secret機能が利用可能であること。利用できない組織では、Secret参照を含むリリースは進められません。 |
| 正しいProject | Secretが、Pageを有効化する対象Projectに登録されていること。同名の値を別Projectへ置いても代用できません。 |
| 参照宣言 | 読み込まれる`.env`（直接アップロードでは`.env`または`.env.production`）に、サーバー用キーと`${secret:Secret名}`を正しい形式で記述していること。 |
| 紐づけ（Appのみ） | Appの導入先で、対象のSecretが「シークレットを選択」から紐づけられ、値が保存されていること。 |
| サーバー専用キー | Secret参照の左辺が`NEXT_PUBLIC_`、`VITE_`、`REACT_APP_`、`PUBLIC_`で始まらず、予約キー（`PORT`、`NODE_ENV`、`PRS_*`など）でないこと。 |
| 値の長さ | 環境変数として渡す値が1値4,096バイト以下であること。 |
| 実行確認 | 実際のPageで、Secretを使う代表操作が成功し、画面・ネットワーク応答・ログに実値が出ないこと。 |

Secret参照を含む環境変数は最大**32件**まで扱います。通常のサーバー環境変数は最大**128件**で、キーは最大**255文字**、1値は最大**4,096バイト**、全体は最大**32KiB**です。Project Secretの値そのものの登録上限（8,192文字）より小さいため、長い値は保存できてもPageの起動時に失敗します。

## Secretが見つからない・値がない場合

参照先のSecretがない、対象Project・組織に一致しない、無効な共有Secretである、または保存済みの値が空の場合、Pageの有効化は失敗します。実行時の解決でも、対象Project、アクティブなリビジョン、復号可能な値を検証するため、解決できない参照はエラーになります。空文字列を渡して続行する挙動を前提にしないでください。

対応は次の順で行います。

1. 対象組織・Project・Pageと、`.env`のSecret名を確認します。直接アップロードでは、`.env`または`.env.production`に書いているかも確認します。
2. **「プロジェクトシークレット」**で、同じ名前の値が登録され、マスク表示になっていることを確認します。実値を画面から復元しようとしません。
3. Appとしてインストールした場合は、導入先で「シークレットを選択」から対象のSecretを紐づけます。直接アップロードしたPageには紐づけの画面はなく、名前の一致で解決されます。
4. 値が4,096バイトを超えていないかを確認します。
5. Secretを更新した場合は、利用経路で代表操作を再確認します。即時反映を前提にしません。
6. 解決しない場合は、Secret値を共有せず、対象Project、Secret名、環境変数名、発生日時、エラーコードまたは表示メッセージ、リリース版、再現手順を記録して管理担当者へ連絡します。

## よくある誤解

### Secret名を公開用変数の値にすれば安全である

安全ではありません。`NEXT_PUBLIC_`などの公開用キーの値はブラウザ用のコードへ埋め込まれ得ます。Appの流れでは参照として拒否されますが、直接アップロードでは拒否されません。値を取得して別の公開変数へコピーする実装も同じく禁止です。

### `.env.local`や`.env.staging`に置けば本番のサーバーSecretになる

なりません。直接アップロードで読むのは`.env`と`.env.production`だけです。Appの流れでも`.env.local`系は除外されます。対象Projectで利用するSecretは[Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)の手順で登録し、読み込まれる`.env`には参照だけを記述します。

### Secretを使う処理なら、ブラウザから外部APIを呼んでもよい

よくありません。ブラウザからの呼出は認証ヘッダーや応答を利用者が観察でき、値の露出や権限の過剰付与につながります。Secretを必要とする外部呼出はサーバー側で行い、画面へ返すデータを最小化します。

## チェックリスト

- [ ] Secretの実値をProject Secretとして対象Projectに登録し、`.env`・ソース・ログへ書いていない
- [ ] Secret名が[Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)および[Pageの仕様・制限](../build/page-specs-and-limits.md)の命名規則（英小文字で開始、英小文字・数字・アンダースコア、64文字以下）に合っている
- [ ] 直接アップロードでは`.env`または`.env.production`に`${secret:Secret名}`を宣言した
- [ ] Secret参照の環境変数名に公開用接頭辞や予約キーを使っていない
- [ ] 環境変数として渡す値が4,096バイト以下である
- [ ] サーバーは`process.env.PORT`で待ち受け、`/api/v1/*`・`/api/storage`などのパスを自分のルートに使っていない
- [ ] サーバー処理だけが`process.env`から読み、ブラウザへ値・認証ヘッダー・生のエラーを返していない
- [ ] ビルド後のJavaScript、HTML、JSON、ソースマップ、初期データに実値がないことを確認した
- [ ] Secret未設定時に、実値を表示せず安全に失敗することを確認した

## 次に読む記事

- [Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Pageのソース構成とランタイム](page-source-structure-and-runtime.md)
