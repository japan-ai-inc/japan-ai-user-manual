# Pageのビルド・起動・表示に失敗する

PageのZIPが登録できない、登録後に起動しない・白い画面になる、深いURLで404になる、認証を繰り返す、またはSecretを使う処理が失敗する場合は、失敗した段階を分けて確認します。StudioへのZIP登録は任意の依存関係をインストールしたり任意のビルドコマンドを実行したりする機能としては確認できません。ローカルで完成したブラウザ向け成果物を作り、登録、アクセス、画面表示、業務操作を別々に確認してください。

## 関連記事

- [Pageのビルド・起動・更新](../development/page-build-start-update.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [Pageのソース構成とランタイム](../development/page-source-structure-and-runtime.md)
- [Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)
- [Pageの画面遷移・ディープリンク](../development/page-navigation-and-deep-links.md)
- [Pageのサーバー処理・環境変数・Secret参照](../development/page-server-env-and-secrets.md)
- [画面からPageを登録・公開する](../build/register-and-publish-pages.md)
- [Pageのバージョンを管理する](../build/manage-page-versions.md)

## まず確認すること

- 対象の組織・Project・Page、操作したZIP名、現在「適用中」のバージョン、発生日時を記録します。最新番号の版と適用中の版は一致するとは限りません。
- ローカルでビルド済み成果物を開き、初期表示、アセット読み込み、今回変更した代表操作と結果を確認します。開発サーバーで動くことだけでは不十分です。
- ZIPを展開して、最初に表示するHTMLと必要なJavaScript、CSS、画像が登録対象として残る構成か確認します。単一の親フォルダーは取り除かれますが、`dist`や`build`などのビルド出力フォルダーをZIPの最上位にすると除外されます。
- Pageを開けること、認証できること、データ操作・外部連携が成功することを別の確認項目にします。原因未確定のまま、アップロード・復元・業務処理を連続して実行しません。

> ブラウザへ配信されるHTML、JavaScript、設定、ソースマップ、ログには、APIキー、Cookie、トークン、Project Secretの実値を含めないでください。値を隠した変数名や難読化は保護になりません。

## 症状別の切り分け

| 症状・表示されるメッセージ | 主な原因 | 確認・対処 | 詳しい記事 |
|---|---|---|---|
| 「ページをアップロードする権限がありません」 | PageまたはProjectを更新する権限がない | 正しい組織・Project・対象Pageを確認し、管理担当者へ必要な権限を確認します。操作を表示させる目的で公開設定を変更しません。 | [画面からPageを登録・公開する](../build/register-and-publish-pages.md)、[Pageのビルド・起動・更新](../development/page-build-start-update.md) |
| 「ZIPファイルに有効なファイルが含まれていません。」、構造エラー、またはZIPが受け付けられない | 成果物がない、パスに現在・親ディレクトリ要素がある、除外対象の下だけにファイルがある、上限超過 | ZIPを展開し、通常ファイルが残るか、HTML・アセットがルートから参照できるかを確認します。ZIPの構造と画面で示される上限は[Pageの仕様・制限](../build/page-specs-and-limits.md)で確認します。 | [Pageの仕様・制限](../build/page-specs-and-limits.md)、[Pageのソース構成とランタイム](../development/page-source-structure-and-runtime.md) |
| 「アップロードに失敗しました。再度お試しください。」または「別のアップロードが進行中です。しばらくしてから再度お試しください。」 | 通信・登録処理の失敗、同じPageの処理中、対象やZIPの問題 | 直ちに再試行せず、対象Page、ZIP名、表示メッセージを記録します。時間を置いて「バージョン履歴」の「適用中」と実際のPageを確認してから、必要な1回だけ再試行します。 | [画面からPageを登録・公開する](../build/register-and-publish-pages.md)、[Pageのバージョンを管理する](../build/manage-page-versions.md) |
| アップロード完了後に「バージョンの切り替えに失敗しました」、または想定した版にならない | 適用・切替の未完了、対象Pageの取り違え、処理中 | 「バージョン履歴」で意図した版が「適用中」か、日時・名前・アップロードした人を確認します。問題の原因が不明な間は、さらにアップロードや復元を重ねません。 | [Pageのビルド・起動・更新](../development/page-build-start-update.md)、[Pageのバージョンを管理する](../build/manage-page-versions.md) |
| 登録は成功したが白い画面、アセット読込失敗、更新後に古い表示 | 配布物のパス、必要ファイルの欠落、開発サーバー固有の変換・プロキシ・環境変数への依存 | ブラウザのネットワーク表示で失敗したURLとContent-Typeを確認します。新しいZIPにHTML、JavaScript、CSS、画像を一式入れ、相対パスで解決できるか確認します。新しい成果物に含まれない旧ファイルは残らない前提で見直します。 | [Pageのソース構成とランタイム](../development/page-source-structure-and-runtime.md)、[Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md) |
| 深いURLの直接アクセス・再読み込みで404または白い画面 | Page用ホストのSPAフォールバック、ビルドツールのベースパス、ルーティング設定に依存 | Page起点からの遷移だけでなく、深いURLを別タブで開く、再読み込みする、サインイン後に戻る試験を行います。SPAフォールバックの提供可否は一律に確認できないため、推測で公開しません。 | [Pageの画面遷移・ディープリンク](../development/page-navigation-and-deep-links.md) |
| Pageを開くと認証画面に戻る／401を繰り返す | アクセスモード、Pageセッション、利用者の権限、外部顧客認証の設定が一致しない | 「アクセス設定」と想定利用者を確認し、正しい入口から開き直します。ブラウザのPage相対APIでProject APIキー、`Authorization`、Cookieを足して回避せず、401を無条件に再送しません。 | [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)、[Pageの画面遷移・ディープリンク](../development/page-navigation-and-deep-links.md) |
| Secretを使う処理だけ失敗する、または必要な値がない | 対象ProjectにSecretがない、参照形式・環境変数名が不正、ブラウザ公開用の変数名を使っている、サーバー実行面が未確認。App導入・更新後にProject Secretの設定が必要な場合もある | 実値をZIPや`.env`へ書かず、「プロジェクトシークレット」に対象Project用の値があることを確認します。ルートの`.env`では`${secret:Secret名}`を参照し、`NEXT_PUBLIC_`、`VITE_`、`REACT_APP_`、`PUBLIC_`で始まる左辺には使いません。Appの場合は、導入先Projectの設定担当・必要なSecret名を確認します。 | [Pageのサーバー処理・環境変数・Secret参照](../development/page-server-env-and-secrets.md) |

## 段階別フロー

1. **ローカルのビルド段階**：プロジェクトで定義されたビルド後、開発サーバーではなくビルド済み成果物で初期表示・画面遷移・代表操作を確認します。任意のフレームワーク、サーバーランタイム、ビルドコマンドがStudioで実行される前提にしません。
2. **ZIPの登録段階**：ZIPを展開し、成果物が登録対象として残ることを確認します。単一の親フォルダーは取り除かれますが、`node_modules`、`.git`などや最上位の`dist`、`build`などは登録対象から除外されます。画面の上限・構造エラーを先に解消します。
3. **版の適用段階**：「アップロードが完了しました。」だけでは稼働確認になりません。「バージョン履歴」の「適用中」と、実際に開いたPageを照合します。
4. **起動・表示段階**：利用者と同じ入口・アクセスモードで開き、HTML、JavaScript、CSS、画像を読み込めることを確認します。失敗した要求とURLを記録します。
5. **認証・業務処理段階**：サインイン状態と未サインイン状態、必要なら外部顧客認証後の状態で試験します。データ更新・送信は、処理済みか不明なときに再実行しません。

## よくある誤解

- **ZIPを登録すればStudioがソースをビルドする**：確認できる登録処理はZIPの検査・配置です。依存関係の導入や`package.json`のスクリプト実行を前提にせず、完成済み成果物を登録します。
- **画面が一度開けば公開完了である**：利用者のアクセス条件、深いURL、認証復帰、データ取得・更新・外部送信は別途確認が必要です。
- **公開URLを共有すれば認証・権限エラーを解消できる**：公開URLは入口であり、Page内のデータ操作やAPIの権限を増やしません。アクセス設定を緩める前に、表示情報と操作を確認します。
- **Secretをクライアント環境変数に入れればよい**：ブラウザへ届く値はSecretではありません。外部APIなど秘密値を使う処理は、利用可能なサーバー実行面だけで扱います。

## 管理者・サポートに相談する前に

[問い合わせに必要な情報を集める](collect-info-for-support.md)を参照し、組織・Project・Page名、Page ID、適用中の版、ZIPのファイル一覧とサイズ、アクセスモード、失敗段階、発生日時、表示メッセージ、失敗した要求のURLとHTTP状態、再現手順、試した対処をまとめます。Pageの詳細なビルド・デプロイ状態や利用者向けビルドログは、確認した画面では表示されません。APIキー、Cookie、認証ヘッダー、Secret値、トークン、個人情報をZIP、ログ、スクリーンショット、問い合わせに含めません。

## 次に読む記事

- [Pageのビルド・起動・更新](../development/page-build-start-update.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [Pageのソース構成とランタイム](../development/page-source-structure-and-runtime.md)
- [Pageの画面遷移・ディープリンク](../development/page-navigation-and-deep-links.md)
- [Pageのサーバー処理・環境変数・Secret参照](../development/page-server-env-and-secrets.md)
