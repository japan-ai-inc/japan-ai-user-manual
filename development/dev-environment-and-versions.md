# 開発環境・バージョン・互換性

`@japan-ai-inc/studio-sdk` を使う開発では、SDK、Node.js、CLI、実行先の組合せを固定します。この記事は公開済みのSDK **0.11.0** を基準に、環境確認、導入、版確認、更新判断を説明します。SDKの導入だけでは、任意の外部本番ホスティングやすべての実行環境での利用は保証されません。

## 関連記事

- [SDK／APIのバージョン移行](sdk-api-version-migration.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)

## 始める前に

対象パッケージは `@japan-ai-inc/studio-sdk` です。SDKとCLIを含みます。確認対象は **0.11.0** です。

Node SDKとCLIには、**Node.js 22以上**が必要です。Node.js 20以前や、ブラウザへ直接APIキーを渡す構成は対象外です。ブラウザでのSDK利用は、PRS（Pages Rendering Service）内で動くPage向けに限定されています。ローカル開発と明示的に管理した開発自動化では、Node.js側でProject APIキーを扱います。

> Project APIキーは秘密情報です。ブラウザ用JavaScript、ブラウザに公開される環境変数、ログ、ソース管理へ入れてはいけません。キーの発行・保管・失効は[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照してください。

使用するシェルでNode.jsとnpmの版を確認します。

```bash
node --version
npm --version
```

`node --version` の結果が `v22` 以上であることを確認します。開発端末、CI、コンテナで別のNode.jsを使わないようにします。

## 対象のSDKとモジュール形式

### バージョンをそろえる

SDK 0.11.0は、パッケージ定義と`SDK_VERSION`で確認できます。依存関係、ロックファイル、CIを同じ版にそろえます。

| 確認項目 | 確認する内容 |
|---|---|
| パッケージ名 | `@japan-ai-inc/studio-sdk` |
| SDK版 | `0.11.0`を依存関係とロックファイルで固定します。 |
| Node.js | 開発、CI、サーバー側を`>=22`にそろえます。 |
| 読込み | CommonJSとしてコンパイルされます。ESM専用とは扱いません。 |

SDKのTypeScript設定は `commonjs` を指定しています。Node.js側で明示的にCommonJSとして読み込む場合は、次の形式を使えます。

```js
const { createClient, SDK_VERSION } = require("@japan-ai-inc/studio-sdk");

console.log(SDK_VERSION); // 0.11.0
```

TypeScriptのプロジェクトで既存のコンパイル設定がimport構文を扱う場合は、READMEにある次の書き方を使用できます。

```ts
import { createClient, SDK_VERSION } from "@japan-ai-inc/studio-sdk";

console.log(SDK_VERSION); // 0.11.0
const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env.JAI_STUDIO_API_KEY,
});
```

この例はNode.js側の設定例です。値を画面へ返しません。ブラウザで動くPRS Pageの認証・初期化は[実行モード・認証・対応範囲](execution-modes-and-auth.md)を確認します。

## 1. SDKを導入する

プロジェクトのルートで、対象バージョンを明示して導入します。

```bash
npm install @japan-ai-inc/studio-sdk@0.11.0
```

使用するパッケージマネージャーにかかわらず、SDKを `0.11.0` に固定し、ロックファイルを共有します。

導入後は、インストール済みの依存関係と、SDKが公開する版を確認します。

```bash
npm ls @japan-ai-inc/studio-sdk
node -e "console.log(require('@japan-ai-inc/studio-sdk').SDK_VERSION)"
```

両方が `0.11.0` でない場合は、`package.json`、ロックファイル、実行ディレクトリを確認します。重複した依存関係や別パッケージを読んでいる場合があります。

## 2. CLIを導入して確認する

CLIコマンド名は `jai-studio` です。グローバルに利用する場合は、SDKと同じ版を指定して導入します。

```bash
npm install --global @japan-ai-inc/studio-sdk@0.11.0
jai-studio --version
```

`jai-studio --version` はCLIのバージョンを表示します。SDKとCLIを併用する場合、両方を0.11.0にそろえます。実体が期待と異なるときは、場所と版を確認します。

```bash
command -v jai-studio
jai-studio --version
```

Windows環境では、シェルに対応するコマンドで実行パスを確認してください。未確認の別版CLIでPageの操作や自動化を実行しないでください。

CLIには、同梱skillをローカルへ配置する `skill install` もあります。

```bash
jai-studio skill install --agent codex --scope project
```

GitHubアカウントやネットワーク接続は不要です。同じ内容での再実行は変更せず、別のコピーを置き換えるときだけ `--force` を使います。配置後は新しいコーディングエージェントのセッションで確認します。

## 3. 互換性を確認して固定する

SDKの利用可否はnpmでの導入だけでは決まりません。実行モード、Node.js、SDK、接続先を一緒に確認します。

| 利用場所 | 必要な前提 |
|---|---|
| ローカルNode.js | Node.js 22以上と開発者管理のProject APIキー。任意の外部本番ホスティングをサポートするものではありません。 |
| PRSブラウザ | PRSセッション。Project APIキーを設定・参照しません。 |
| PRSサーバー | Node.js 22以上とPRS管理のサーバー側設定。キーをブラウザへ返しません。 |

> SDKを更新しても接続先の機能は自動的にそろいません。CHANGELOGで前提を確認し、代表操作を検証してください。

0.x系のSDKは、minor（例：`0.6.0`から`0.11.0`）で破壊的変更を含む場合があります。範囲指定ではなく完全な版を固定します。

```json
{
  "dependencies": {
    "@japan-ai-inc/studio-sdk": "0.11.0"
  }
}
```

`^0.11.0`、`~0.11.0`、`latest` は後日の再インストールで別版を解決し得ます。検証済みの完全な番号とロックファイルを記録します。

## CHANGELOGを使った更新判断

更新前には、パッケージに含まれる `CHANGELOG.md` を確認します。CHANGELOGは変更内容と互換性上の注意を確認する一次情報です。特に次を確認します。

1. 現在の版から更新先までのリリース項目を読みます。
2. `Changed`、`Fixed`と、追加機能の実行環境・接続先の前提を確認します。
3. SDK・CLI・ロックファイルを更新候補にそろえ、代表操作と失敗時の処理を検証します。

0.6.0から0.11.0では、Canvas公開の`publishCanvasArtifact()`に公開範囲の第3引数が必要になりました。0.11.0のクレジット確認と`platform.openAppSettings()`は、対応するPRS実行環境で利用します。移行の詳細は[SDK／APIのバージョン移行](sdk-api-version-migration.md)で扱います。

## よくある誤解

### `npm install`が成功したので、Node.jsの条件も満たしている

依存関係の取得成功は、実行時にNode.js 22以上で動く保証ではありません。Node.jsコードが動く環境を分けて確認します。

### SDKとCLIは別々に最新版へ上げてよい

CLIは同じパッケージから提供されます。SDKを0.11.0に固定する場合、CLIも `jai-studio --version` で確認します。異なる版を使う場合は、理由と検証結果を記録します。

### CommonJS設定なら、どのブラウザでもSDKを直接使える

CommonJSかESMかは、主にNode.js側のモジュール読込方式の問題です。ブラウザでのSDK利用の対応範囲は別であり、PRS内のPageかどうか、認証方式、利用するモジュールを確認する必要があります。

### 0.x系のminor更新は常に安全である

このSDKでは、pre-1.0のminor更新に破壊的変更を含む場合があります。版を固定し、CHANGELOGを読んだうえで、更新候補を別環境で検証します。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| `node --version` が22未満 | Node.js 22以上を使う環境へ切り替えます。SDKだけを旧Node.jsへ合わせて下げる判断は、対応範囲とCHANGELOGを確認してから行います。 |
| `jai-studio` が見つからない、または期待と違う版を表示する | `command -v jai-studio` と `jai-studio --version` を確認し、PATH上の実体とグローバル導入版を見直します。 |
| `npm ls` と `SDK_VERSION` が一致しない | 実行ディレクトリ、ロックファイル、ワークスペースの依存関係を確認します。複数版を解決している場合は、使用される版を一つに固定します。 |
| ブラウザで認証・APIキー関連の問題が起きる | APIキーをブラウザへ追加せず、実行場所がPRS内か、Node.js側かを確認します。[実行モード・認証・対応範囲](execution-modes-and-auth.md)と[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照します。 |
| SDK更新後に一部の呼び出しが失敗する | CHANGELOGで更新先までの変更と追加前提を確認し、SDK／CLI／Node.js／接続先の組合せを記録して切り分けます。必要に応じて[SDK／APIのバージョン移行](sdk-api-version-migration.md)の移行手順を参照します。 |

## チェックリスト

- [ ] SDKが `@japan-ai-inc/studio-sdk` の0.11.0で固定されている
- [ ] 開発端末とCIでNode.js 22以上を使っている
- [ ] ロックファイルを含め、インストール結果を再現できる
- [ ] `npm ls` と `SDK_VERSION` で導入版を確認した
- [ ] CLIを使う場合、`jai-studio --version` がSDKと同じ0.11.0である
- [ ] APIキーをブラウザ、公開環境変数、ログ、ソース管理へ含めていない
- [ ] 実行モードと利用モジュールが対応範囲にあることを確認した
- [ ] 更新前にCHANGELOGを読み、検証環境で代表操作を確認した

## 次に読む記事

- [SDK／CLIでPageを開発する](../labs/develop-page-with-sdk-cli.md)
- [SDK／APIのバージョン移行](sdk-api-version-migration.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [CLIによる開発支援Skillの導入](../cli/install-dev-skill-via-cli.md)
