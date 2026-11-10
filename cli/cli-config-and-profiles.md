# CLIの設定・プロファイル

`jai-studio` CLIは、開発端末または管理された開発自動化でStudioのProjectを操作するコマンドです。導入、プロファイル、実行時の設定の優先順、確認・切替方法を説明します。キーの発行、スコープ、有効期限、失効は[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照してください。

## 関連記事

- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)

## 始める前に

対象パッケージは`@japan-ai-inc/studio-sdk`、CLIの実行名は`jai-studio`です。Node.js 22以上を用意し、操作対象のProjectで必要最小限のスコープを持つProject APIキーを発行してください。CLIプロファイルはCLIだけが読む設定です。SDKの`createClient()`はプロファイルを読みません。

> Project APIキーは秘密情報です。ブラウザ用コード、公開環境変数、ソース管理、ログ、チャットに保存してはいけません。キーをコマンド引数に書くと、シェル履歴やプロセス一覧に残る可能性があります。

## 1. CLIを導入して版を確認する

グローバルに導入します。

```bash
npm install --global @japan-ai-inc/studio-sdk@0.11.0
jai-studio --version
command -v jai-studio
```

`jai-studio --version`はCLIのバージョンを表示します。期待するコマンドが見つからない、または版が異なる場合は、`command -v`で実行される実体とグローバル導入版を確認します。

## 2. プロファイルを安全に作成する

プロファイルには、環境名または接続先URLとAPIキーを保存します。最初に作成したプロファイルは自動的にアクティブになります。`production`は`https://api.japan-ai.co.jp`に解決されます。`http`または`https`の完全なURLも接続先として指定できます。

### `config set`

```text
jai-studio config set <name> [apiKey] [--key-stdin] [-e|--env <env>]
```

以下の`<...>`を含む表記は**置換が必要な構文表記**です。角括弧を含めてそのままシェルへ貼り付けず、実際の値に置き換えてください。

| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `<name>` | はい | — | 作成または更新するプロファイル名です。 |
| `[apiKey]` | いいえ | — | Project APIキーです。互換用の入力方法ですが、使用を避けます。 |
| `--key-stdin` | いいえ | なし | 標準入力からキーを読みます。CIや秘密情報管理ツールとの連携用です。 |
| `-e, --env <env>` | いいえ | `production` | `production`、または完全なHTTP/HTTPS URLを指定します。 |

通常は対話入力を使います。入力中は端末にキーを表示しません。

```bash
jai-studio config set development
```

CIなどで、秘密情報管理ツールの出力を標準入力へ渡す場合は、`--key-stdin`を明示します。

```bash
vault read -field=key secret/jai | jai-studio config set ci --key-stdin
```

`JAI_STUDIO_API_KEY`が設定されている場合、`config set`はその値を使います。

```bash
JAI_STUDIO_API_KEY=pak_... jai-studio config set development --env production
```

互換用の`[apiKey]`へ実値を渡さないでください。

### 保存場所と表示上の注意

設定ファイルは、`XDG_CONFIG_HOME`がある場合は`$XDG_CONFIG_HOME/jai-studio/config.json`、ない場合は`~/.config/jai-studio/config.json`に保存されます。所有者だけが読書きできる権限（`0600`）が設定されるため、共有ディレクトリやソース管理配下へコピーしないでください。

`config show`は実値を表示せず、先頭8文字と末尾4文字以外をマスクします。表示値は再利用できません。実値を失った場合は、[Project APIキーを管理する](../operations/manage-project-api-keys.md)に従って新しいキーへ切り替えます。

## 3. 一覧、確認、切替、削除を行う

### `config list`

```bash
jai-studio config list
```

| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| なし | — | — | プロファイル名の一覧とアクティブな名前をJSONで出力します。 |

### `config show`

```bash
jai-studio config show [name]
```

| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `[name]` | いいえ | アクティブなプロファイル | プロファイル名、環境、解決後の接続先URL、マスク済みキーを表示します。 |

```bash
jai-studio config show development
```

### `config use`

```text
jai-studio config use <name>
```

| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `<name>` | はい | — | 存在するプロファイルをアクティブにします。 |

```bash
jai-studio config use project-b
```

### `config delete`

```text
jai-studio config delete <name>
```

| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `<name>` | はい | — | プロファイルを設定ファイルから削除します。 |

アクティブなプロファイルを削除すると、残っている名前のうち先頭のものが次のアクティブプロファイルになります。残っていなければ、アクティブなプロファイルはありません。削除はProject APIキー自体を失効させません。不要・漏えいしたキーの失効は[Project APIキーを管理する](../operations/manage-project-api-keys.md)で実施します。

## 4. 実行時の接続先とキーの優先順を確認する

認証が必要なCLIコマンドでは、接続先URLとAPIキーを**別々に**解決します。いずれも、コマンドフラグ、環境変数、プロファイルの順で優先されます。

| 設定値 | 1. 最優先 | 2. 次点 | 3. 最後 | 補足 |
|---|---|---|---|---|
| 接続先URL | `--env <env>` | `JAI_STUDIO_API_URL` | `--profile <name>`で選んだプロファイル、またはアクティブなプロファイルの接続先 | `--env`には既知の環境名または完全なURLを指定します。 |
| APIキー | `--key <key>` | `JAI_STUDIO_API_KEY` | 選択されたプロファイルのキー | `--key`は履歴・プロセス一覧に残り得るため、通常は使いません。 |
| Project | Projectを指定するグローバルフラグ・環境変数・プロファイル項目はありません | — | APIキーが対象とするProject | 対象Projectを変えるときは、そのProject用のキーを持つプロファイルまたは安全な環境変数を使います。 |

`--profile <name>`はアクティブ設定を書き換えず、その実行だけ指定名のプロファイルを選びます。たとえば、次の実行では`project-b`のプロファイルを選びますが、`JAI_STUDIO_API_KEY`があればキーだけは環境変数が優先されます。

```bash
jai-studio --profile project-b workflows list
```

`--env`または`JAI_STUDIO_API_URL`だけを上書きしても、キーは別に解決されます。異なる接続先とキーを組み合わせないよう、実行前に`config show`で接続先を確認し、CIでは必要な変数を明示してください。

## グローバルフラグと出力

| フラグ | 意味 |
|---|---|
| `--profile <name>` | この実行で使うプロファイルを選びます。 |
| `--env <env>` | 接続先を上書きします。 |
| `--key <key>` | APIキーを上書きします。通常は使用しません。 |
| `--pretty` | 成功時のJSON出力を整形します。 |
| `--debug` | サーバーの詳細なエラー応答を表示します。機密情報を含む可能性があるため、共有ログでは使いません。 |
| `--version` | CLIのバージョンを表示します。 |

CLIの成功結果は標準出力へJSONで出力され、`--pretty`で整形できます。グローバルな`--json`はありません。`--json`があるのは`pages deploy`固有のフラグであり、他のコマンドへ共通指定しません。

## エラー出力の扱い

SDK 0.11.0のCLIは、失敗時に標準エラーへ`error`、`statusCode`、`details`を持つJSONを出します。APIが`message`形式のエラー本文を返した場合、意図して返す`code`とオブジェクト型の`params`は`details`内に保持され、ルールの未完了IDなどの切り分けに使えます。通常はその他の生の応答本文を伏せます。`--debug`は詳細を出すため、Secret、認証情報、個人情報が出力されない隔離済みの調査環境でだけ使います。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 接続先が未設定というエラーになる | `--env`、`JAI_STUDIO_API_URL`、選択中プロファイルのいずれかを設定します。`config show`で接続先を確認します。 |
| キーが未設定・認証エラーになる | `--key`を追加して回避せず、`JAI_STUDIO_API_KEY`または対象プロファイルの実値、対象Project、期限、スコープを確認します。マスク表示は使いません。 |
| プロファイルが見つからない | `jai-studio config list`で名前を確認し、必要なら`config set`で作成します。 |
| CIで対話入力待ちになる | `--key-stdin`または`JAI_STUDIO_API_KEY`を使います。標準出力・ログに実値を出さないよう設定します。 |
| エラーの詳細を確認したい | 通常のエラーは標準エラー出力にJSONで表示されます。`--debug`は応答詳細を出すため、安全な一時環境だけで使い、結果を共有しません。 |

エラー・対話入力中止では終了コード1です。通常のエラー出力は`error`、`statusCode`、`details`のJSONで、応答詳細は`--debug`時だけ出ます。自動化では標準出力と標準エラー出力を分け、終了コードが0以外なら成功にしません。

## チェックリスト

- [ ] Node.js 22以上と、検証済みのCLI版を確認した
- [ ] 対象Projectに必要最小限のスコープを持つキーを用意した
- [ ] キーを引数へ書かず、対話入力、`--key-stdin`、または環境変数を使った
- [ ] `config show`で対象プロファイルと接続先を確認した
- [ ] CIではプロファイル、接続先、キーの優先順を確認した
- [ ] マスク済みキー、詳細なデバッグ出力、キーの実値を共有していない

## 次に読む記事

- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)
- [CLI Agents](cli-agents.md)
- [CLI Objects](cli-objects.md)
- [CLI Storage](cli-storage.md)
- [CLI Workflows](cli-workflows.md)
- [CLI Pages](cli-pages.md)
- [CLI Members](cli-members.md)
- [CLIによる開発支援Skillの導入](install-dev-skill-via-cli.md)
