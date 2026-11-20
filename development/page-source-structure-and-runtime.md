# Pageのソース構成とランタイム

Pageを配信する開発者は、**プロジェクトのソース一式**をZIPにまとめてアップロードします。StudioでホストされたPageの実行環境（Page実行環境）は、アップロードされたソースのロックファイルに応じてBun／Node／静的のいずれかを選び、依存関係のinstallと`build`を実行したうえで、Node／Bunのサーバーとして動かします（静的の場合はファイルを配信します）。

登録・公開の画面操作は[画面からPageを登録・公開する](../build/register-and-publish-pages.md)、容量・ZIP構造などの制限は[Pageの仕様・制限](../build/page-specs-and-limits.md)を正本として確認します。

## 要点

| 項目 | 内容 |
|---|---|
| アップロードするもの | プロジェクトのソース。ルートに`package.json`とロックファイル、または静的なPageなら`index.html` |
| Studio側で行う処理 | ロックファイルに応じてBun／Node／静的を選択し、installと`build`を実行。Node／Bunはサーバーとして起動 |
| 拒否されるZIP | Next.jsのビルド成果物をアップロードしたもの。ルートに`package.json`も`index.html`もないもの |
| サーバーの待ち受け | `process.env.PORT`で指定されたポートで待ち受ける |
| 配信パス | サブパス配下で配信されるため、フレームワークの`basePath`の設定を配信URLに合わせる |
| 時間 | 初回表示までビルドに数分（最大15分）かかることがある。SSRの最初の応答は約60秒以内に返す必要がある |

## 関連記事

- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [Pageをローカル確認してデプロイする](local-preview-and-deploy-page.md)
- [画面からPageを登録・公開する](../build/register-and-publish-pages.md)

## この記事の範囲

この記事では、ZIPに入れるソースの構成、登録時に除外される内容、ランタイムでの扱いを説明します。サーバー処理で使う環境変数とSecretは[Pageのサーバー処理・環境変数・Secret参照](page-server-env-and-secrets.md)、URLとルーティングは[Pageの画面遷移・ディープリンク](page-navigation-and-deep-links.md)を参照してください。

Pageは、Studio本体とは別のPage用ホストで配信され、Studioではiframeで表示されます。

## 始める前に

- 対象Project、Page名、利用者が最初に開く画面、想定するブラウザを確認します。
- ルートに`package.json`とロックファイルがあること（静的なPageでは`index.html`があること）を確認します。ロックファイルの種類でBun／Nodeのどちらで動かすかが決まるため、ローカルで使っているパッケージマネージャーのロックファイルを含めます。
- `package.json`の`build`スクリプトが、ローカルでエラーなく完了することを確認します。
- サーバーを持つPageでは、`process.env.PORT`で待ち受けるように実装します。ポート番号を固定しません。
- ブラウザへ届くファイルには、パスワード、APIキー、トークン、Project Secretの値、個人情報を含めないようにします。Secretの管理は[Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)を参照してください。

> `node_modules`やローカルのビルド出力（`dist`、`build`など）は入れません。依存関係とビルド成果物はStudio側のinstallと`build`で作られます。Next.jsのビルド成果物をアップロードすると拒否されます。

## アップロードするソースの構成

ZIPのルートに、`package.json`、ロックファイル、ソースを置きます。ファイル名は例です。

```text
page-source.zip
├── package.json
├── package-lock.json   （使っているパッケージマネージャーのロックファイル）
├── src/
│   └── ...
└── public/
    └── ...
```

静的なPage（ビルド処理を持たないHTML・JavaScript・CSS）では、ルートに`index.html`を置きます。

```text
page-static.zip
├── index.html
├── assets/
│   ├── app.js
│   └── app.css
└── images/
    └── logo.svg
```

ルートに`package.json`も`index.html`もないZIPは拒否されます。

### `package.json` とビルドスクリプトの扱い

アップロード後、Page実行環境はロックファイルに応じてBun／Node／静的を選び、依存関係をinstallしてから`build`スクリプトを実行します。ローカルで次を確認してからアップロードしてください。

- ロックファイルが`package.json`と一致しており、installが成功する
- `build`スクリプトがローカルで成功する
- サーバーを起動する場合、`process.env.PORT`で待ち受ける
- フレームワークの`basePath`（配信パスの接頭辞）の設定が、Pageの配信URLと合っている

## 登録時に除外されるフォルダーとファイル

`__MACOSX`、`node_modules`、`.git`、`.DS_Store`、`.jai-studio`、`__staging`、`__upload-lock.json`、`__backup`、`__versions`を含むパスと、ルートの一般的なビルド出力用フォルダーは除外されます。詳細は[Pageの仕様・制限](../build/page-specs-and-limits.md)を正本として確認してください。

単一の親フォルダーだけで全ファイルを包んだZIPでは、その親フォルダーは取り除かれます。ZIP内に現在ディレクトリまたは親ディレクトリを表すパス要素がある場合は、構造エラーになります。除外後にファイルが残らないZIPも登録できません。

## ランタイムでの表示

アップロード後、Page実行環境がinstallと`build`を行ってから配信を始めます。ビルド中・ビルド失敗のときは、それぞれ専用の画面が表示されます。初回表示まで数分（最大15分）かかることがあります。Pageを更新すると、新しいソースから改めてビルドされます。バージョン操作は[Pageのバージョンを管理する](../build/manage-page-versions.md)を参照してください。

- Node／Bunのサーバーとして動くPageは、`process.env.PORT`で待ち受けます。`PORT`、`NODE_ENV`、`PRS_*`などの予約キーは、開発者が`.env`で指定しても除外されます。
- `/api/v1/*`や`/api/storage`などのパスはプラットフォームが使用します。自分のサーバーのAPIルートにこれらのパスを使わないでください。
- SSRの最初の応答には約60秒の制限があります。初回応答の前に時間のかかる処理を置かないでください。
- 静的なPageには、SPA向けのフォールバックがあり、`<base href>`も自動で挿入されます。

Studio内でPageを開く場合、StudioはPage用ホストのURLを作成し、認証が必要なPageでは短期の認証情報を付与したURLをiframeの表示先に設定します。どのアクセス設定でも、ブラウザへ届くファイルに秘密値を埋め込まないでください。秘密値はサーバー処理で扱います。詳しくは[Pageのサーバー処理・環境変数・Secret参照](page-server-env-and-secrets.md)を参照してください。

## よくある誤解

### ローカルでビルドした `dist` をZIPにすればよい

いいえ。Studioがソースからinstallと`build`を行います。ルートの一般的なビルド出力用フォルダーは除外され、Next.jsのビルド成果物は拒否されます。`package.json`とロックファイルを含むソースをアップロードします。

### ポートは固定してよい

いいえ。サーバーは`process.env.PORT`で待ち受けます。`PORT`は予約キーのため、`.env`で指定しても使われません。

### クライアント側の設定ならSecretを入れてもよい

ブラウザへ配信された値は、利用者やブラウザの開発者ツールから取得され得ます。難読化、変数名の変更、非表示の画面要素は保護になりません。秘密値を配布ファイル、ソースマップ、初期データ、ログへ入れないでください。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| ZIPが拒否される | ルートに`package.json`または`index.html`があるか、Next.jsのビルド成果物をアップロードしていないかを確認します。 |
| ビルド失敗の画面が表示される | ロックファイルと`package.json`の整合、`build`スクリプトがローカルで成功するかを確認します。 |
| ビルド中の画面が続く | 初回表示まで数分（最大15分）かかることがあります。時間をおいて開き直します。 |
| 起動しない、応答しない | `process.env.PORT`で待ち受けているか、SSRの最初の応答を約60秒以内に返しているかを確認します。 |
| アセットやAPIが404になる | `basePath`の設定と、`/api/v1/*`・`/api/storage`などプラットフォームが使用するパスと衝突していないかを確認します。 |
| ZIPの構造エラーになる | パスに現在・親ディレクトリを表す要素がないか、除外後にファイルが残るかを確認します。上限・構造条件は[Pageの仕様・制限](../build/page-specs-and-limits.md)を参照します。 |

問い合わせるときは、対象ProjectとPage名、ZIP内のファイル一覧、発生日時、表示されたメッセージ、ブラウザの失敗したリクエスト、再現手順を共有します。ZIP、ログ、スクリーンショットにはSecret、APIキー、トークン、個人情報を含めないでください。

## チェックリスト

- [ ] ルートに`package.json`とロックファイル（静的なPageでは`index.html`）がある
- [ ] ローカルでinstallと`build`が成功する
- [ ] サーバーは`process.env.PORT`で待ち受け、`basePath`を配信URLに合わせた
- [ ] `node_modules`、ローカルのビルド出力、Next.jsのビルド成果物を入れていない
- [ ] ZIPを展開して、不要な親フォルダーや親ディレクトリへのパスがないことを確認した
- [ ] ブラウザへ届くファイルに秘密値や個人情報が含まれていない
- [ ] 登録後、ビルド完了を待って実際のPageを開き、代表的な表示と操作を確認する準備ができている

## 次に読む記事

- [Pageをローカル確認してデプロイする](local-preview-and-deploy-page.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [Pageのサーバー処理・環境変数・Secret参照](page-server-env-and-secrets.md)
