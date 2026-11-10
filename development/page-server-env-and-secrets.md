# Pageのサーバー処理・環境変数・Secret参照

外部APIのトークンなどをPageで使う場合は、値をブラウザ用のJavaScript、HTML、初期データ、ログ、ソースマップへ入れず、**Pageのサーバー側だけ**で読むように設計します。Project Secretの実値をZIPへ含めたり、利用者のブラウザから直接外部サービスへ送ったりしてはいけません。

この記事は、Page/Appのリリース処理がルートの`.env`ファイルからProject Secret参照を検出し、サーバー用環境変数として解決する実装を対象にします。単にZIPをアップロードするPageで任意のNode.jsサーバー、SSR、APIルートが動くことまでは確認できません。サーバー実行環境を使えるPage/Appであることを、対象環境で確認してから採用してください。

## 関連記事

- [Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Pageのソース構成とランタイム](page-source-structure-and-runtime.md)

## 始める前に

- Secretの実値を、対象Projectの**「プロジェクトシークレット」**で登録します。作成・更新・削除、名前・値の入力規則、権限、ローテーションは[Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)を正本とします。
- Project Secret名は、先頭を英小文字とし、以後に英小文字・数字・アンダースコアだけを使う、**64文字以下**の名前です。空文字とUUID形式は使えません。例えば`external_api_token`は使用できます。値は空にできず、最大**8,192文字**です。
- サーバー処理が必要な理由を明確にします。外部API呼出、署名生成、トークンを伴うデータ取得など、秘密値を必要とする処理だけをサーバー側へ置きます。ブラウザは、必要最小限の入力をサーバー処理へ渡し、Secretを含まない結果だけを受け取ります。
- `NEXT_PUBLIC_`、`VITE_`、`REACT_APP_`、`PUBLIC_`で始まる環境変数名は、ブラウザ用にインライン化されるものとして扱われます。Project Secret参照には使いません。

> Secret名は用途を示す識別子であり、値ではありません。`external_api_token`のような名前は設定・診断で参照され得ますが、トークン本文、APIキー、Cookie、認証ヘッダーを名前、エラー表示、問い合わせへ書かないでください。

## 1. `.env` にSecret参照を宣言する

Page/Appのリリース処理は、バンドルのルートにある`.env`および`.env.<名称>`を読みます。`.env.local`、`.env.local.<名称>`、末尾が`.local`のファイルはリリース用の設定から除外されます。`.env.example`、`.env.sample`、`.env.template`もリリース用設定として扱われません。

Secretの実値ではなく、次の完全一致の参照形式を記述します。左辺は、サーバー側で読む環境変数名です。

```dotenv
EXTERNAL_SERVICE_TOKEN=${secret:external_api_token}
```

環境変数名は英字またはアンダースコアで始め、以後に英字・数字・アンダースコアを使います。値の中の`$VAR`や`${VAR}`による環境変数の展開はサポートされません。Secret参照と別の環境変数を組み合わせるのではなく、必要な値を1つのSecretとして登録してください。

複数の`.env`ファイルがある場合は、`.env`が先に処理され、`.env.<名称>`が後から同じキーを上書きします。ローカル専用の`.env.local`へ本番用Secretを置いても、リリース時の設定にはなりません。

### 参照の検出と対応付け

リリース処理は`${secret:...}`という値を検出すると、実値をバンドルへコピーせず、Secret参照として扱います。Secret名がProject Secretの命名規則に合わない場合、またはブラウザ公開用の環境変数名に書いた場合は、リリースできません。

Secret設定で不足項目の案内が出た場合は、表示された項目から対象のSecretを選択して紐づけます。参照名や環境変数名以外の識別子を手入力せず、通常は`${secret:external_api_token}`とサーバー用の環境変数名だけを管理します。

> `${secret:external_api_token}`の代わりに、発行元から受け取ったトークン文字列を`.env`へ書いてはいけません。リリース対象の設定に実値が残り、配布物・履歴・ログへ露出するおそれがあります。

## 2. サーバー処理だけで値を読む

サーバー環境で実行されるNode.jsコードでは、宣言した左辺の名前で環境変数を読みます。次は外部サービスへ問い合わせ、ブラウザには処理結果だけを返す形の例です。サーバー処理を置く入口やフレームワーク固有の書き方は確認範囲外のため、対象のPage/Appランタイムの方式に合わせてください。

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

次の実装は避けます。

```ts
// ブラウザへ渡るコード・設定にSecretを置く例。使用しない。
const token = process.env.NEXT_PUBLIC_EXTERNAL_SERVICE_TOKEN;
window.runtimeConfig = { token };
```

ブラウザ公開用の接頭辞を使うビルドツールでは、ビルド時に値がクライアントバンドルへ埋め込まれることがあります。Pageのリリース処理も、上記4種類の接頭辞をブラウザに公開されるキーとして判定し、Project Secret参照を拒否します。ただし、独自のビルド設定、生成済みJavaScript、HTML、JSON、ソースマップに実値を自分で書いた場合まで自動的に除去することは確認できません。**ビルド前後の成果物を確認し、実値を含めないことを開発者が保証してください。**

## 3. リリース時に満たす条件

Secret参照を含むPage/Appをリリース・有効化するには、少なくとも次を満たす必要があります。

| 条件 | 確認すること |
|---|---|
| 機能の利用条件 | 組織でProject Secret機能が利用可能であること。利用できない組織では、Secret参照を含むリリースは進められません。 |
| 正しいProject | Secretが、Page/Appを有効化する対象Projectに登録されていること。同名の値を別Projectへ置いても代用できません。 |
| 参照宣言 | ルートの対象`.env`に、サーバー用キーと`${secret:Secret名}`を正しい形式で記述していること。 |
| バインド | 対象のSecretが有効なProject共有のSecretとして対応付けられ、値が保存されていること。 |
| サーバー専用キー | Secret参照の左辺が`NEXT_PUBLIC_`、`VITE_`、`REACT_APP_`、`PUBLIC_`で始まらないこと。 |
| 実行確認 | 実際のPage/Appで、Secretを使う代表操作が成功し、画面・ネットワーク応答・ログに実値が出ないこと。 |

リリース処理では、Secret参照を含む環境変数を最大**32件**まで扱います。通常のサーバー環境変数は最大**128件**で、キーは最大**255文字**、1値は最大**4,096バイト**、全体は最大**32KiB**です。これらはProject Secretの値そのものの登録上限とは別です。

## Secretが見つからない・値がない場合

参照先のSecretがない、対応付けがない、対象Project・組織に一致しない、無効な共有Secretである、または保存済みの値が空の場合、Page/Appの有効化は失敗します。実行時の解決でも、対象Project、アクティブなリビジョン、対応付け、復号可能な値を検証するため、解決できない参照はエラーになります。空文字列を渡して続行する挙動を前提にしないでください。

対応は次の順で行います。

1. 対象組織・Project・Page/Appと、`.env`のSecret名を確認します。
2. **「プロジェクトシークレット」**で、同じ名前の値が登録され、マスク表示になっていることを確認します。実値を画面から復元しようとしません。
3. Secret設定で不足・未設定の案内がある場合は、対象のSecretを選択・紐づけます。前節の`page_secret_`形式を手入力して対応付けるのではなく、表示された設定項目を使います。
4. Secretを更新した場合は、利用経路で代表操作を再確認します。更新が実行中の処理や既存の版へいつ反映されるかは一律に確認できないため、即時反映を前提にしません。
5. 解決しない場合は、Secret値を共有せず、対象Project、Secret名、環境変数名、発生日時、エラーコードまたは表示メッセージ、リリース版、再現手順を記録して管理担当者へ連絡します。

## よくある誤解

### Secret名を公開用変数の値にすれば安全である

安全ではありません。`NEXT_PUBLIC_`などの公開用キーに`${secret:...}`を指定すると、参照として拒否されます。値を取得して別の公開変数へコピーする実装も同じく禁止です。

### `.env.local`に置けば本番のサーバーSecretになる

なりません。`.env.local`系はローカル開発用の上書きとしてリリース対象から除外されます。対象Projectで利用するSecretは[Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)の手順で登録し、リリース対象の`.env`には参照だけを記述します。

### Secretを使う処理なら、ブラウザから外部APIを呼んでもよい

よくありません。ブラウザからの呼出は認証ヘッダーや応答を利用者が観察でき、値の露出や権限の過剰付与につながります。Secretを必要とする外部呼出はサーバー側で行い、画面へ返すデータを最小化します。

## チェックリスト

- [ ] Secretの実値をProject Secretとして対象Projectに登録し、`.env`・ソース・ログへ書いていない
- [ ] Secret名が[Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)および[Pageの仕様・制限](../build/page-specs-and-limits.md)の命名規則（英小文字で開始、英小文字・数字・アンダースコア、64文字以下）に合っている
- [ ] ルートの`.env`に`${secret:Secret名}`を宣言し、`.env.local`へ依存していない
- [ ] Secret参照の環境変数名に公開用接頭辞を使っていない
- [ ] サーバー処理だけが`process.env`から読み、ブラウザへ値・認証ヘッダー・生のエラーを返していない
- [ ] ビルド後のJavaScript、HTML、JSON、ソースマップ、初期データに実値がないことを確認した
- [ ] Secret未設定時に、実値を表示せず安全に失敗することを確認した

## 次に読む記事

- [Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Pageのソース構成とランタイム](page-source-structure-and-runtime.md)
