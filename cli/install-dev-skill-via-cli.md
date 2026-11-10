# CLIによる開発支援Skillの導入

`jai-studio skill install`は、`@japan-ai-inc/studio-sdk`に同梱された`studio_sdk` Skillを、CodexまたはClaude Codeが発見できるローカルのSkillディレクトリへコピーするコマンドです。Studio SDK／CLIを使った開発時に、対応するSDK・CLIの使い方をコーディングエージェントへ参照させたい開発者が対象です。

これはStudioのProjectを操作するコマンドではありません。Project APIキー、CLIプロファイル、`gh`、GitHubアカウント、ネットワーク接続は不要です。SDK、CLI、Skillを同じ検証済みバージョンでそろえる考え方は[開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)、開発経路の選択は[Web UI・AI支援・SDK・CLI・APIを選ぶ](../development/choose-dev-path.md)を参照してください。

## 関連記事

- [Web UI・AI支援・SDK・CLI・APIを選ぶ](../development/choose-dev-path.md)
- [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)
- [CLIの設定・プロファイル](cli-config-and-profiles.md)

## 始める前に

対象パッケージは`@japan-ai-inc/studio-sdk`、実行名は`jai-studio`です。Node.js 22以上と、利用したいSkillに対応するCLIを導入します。この記事はSDK **0.11.0** を対象にしています。

```bash
npm install --global @japan-ai-inc/studio-sdk@0.11.0
jai-studio --version
```

Skillの導入は、APIキーを必要とする`pages deploy`、`workflows`などのProject操作とは独立しています。このため、`--profile`、`--env`、`--key`、`JAI_STUDIO_API_KEY`を付ける必要はありません。認証が必要なCLI操作の設定は[CLIの設定・プロファイル](cli-config-and-profiles.md)に従ってください。

> このSkillはコーディングエージェントへの補助情報です。エージェントの提案やコマンド実行結果だけで、Studioの設定変更、デプロイ、公開、外部送信が正しいとは判断しません。変更を伴う操作では、対象Project、ID、入力、実行結果を個別に確認してください。

## Skillに含まれるもの

導入されるSkill名は`studio_sdk`です。SDK／CLIの公開された利用方法をコーディングエージェントが参照するための`SKILL.md`を必須として、参照用Markdownと補助スクリプトを含むスナップショットがパッケージに同梱されます。内容は、Studio SDK／CLIの認証境界、Agent、Members、Object、Storage、Workflow、Pageのパッケージ化・デプロイ、およびPRS内での公開SDK利用を扱う範囲です。

導入時に提供元を取得し直すのではなく、公開パッケージ内の同梱Skillをコピーします。

SDK 0.11.0の同梱Skillでは、Pageデプロイ補助はHTMLへ`<base>`を追加しません。旧版の`--no-inject-base`と`--allow-root-assets`は廃止されたため、既存スクリプトから取り除きます。`deploy.yaml`はホスト環境用のメタデータであり、同梱Skillおよび導入先には含まれません。Agent定義、レコードアクセスルール、予定実行タスク、静的PageのWorkflow紐付けに関するガイダンスも含まれます。

> SkillへAPIキー、トークン、実在するリソースID、顧客データ、署名付きURL、内部専用の手順を追加しないでください。既存の`studio_sdk`ディレクトリを独自に編集すると、次の更新時に既存内容が同梱Skillと異なるものとして扱われます。

## 1. 対象エージェントと導入範囲を選ぶ

対話可能な端末では、引数なしで実行すると、対象エージェントと範囲を選択できます。

```bash
jai-studio skill install
```

CIなどの非対話実行では、`--agent`と`--scope`の**両方**が必要です。

```text
jai-studio skill install --agent <codex|claude-code|all> --scope <project|user> [--force]
```
以下の`<...>`を含む表記は**置換が必要な構文表記**です。角括弧を含めてそのままシェルへ貼り付けず、実際の値に置き換えてください。


| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `--agent <agent>` | 非対話時ははい | 対話選択 | `codex`、`claude-code`、または両方へ入れる`all`を指定します。 |
| `--scope <scope>` | 非対話時ははい | 対話選択 | 現在のProjectだけの`project`、または現在のOSユーザー用の`user`を指定します。 |
| `--force` | いいえ | なし | 内容が異なる既存Skillを置き換えます。 |

ProjectだけでCodexを使う場合の例です。Gitリポジトリ配下から実行すると、現在のサブディレクトリではなく最も近いGitルートが対象になります。

```bash
jai-studio skill install --agent codex --scope project
```

個人の端末で、対応する両方のエージェントに導入する場合の例です。

```bash
jai-studio skill install --agent all --scope user
```

## 導入先

`project`は最も近いGitルートを探します。Gitルートが見つからない場合は、コマンドを実行したカレントディレクトリをProjectルートとして使います。`user`は現在のOSユーザーのホームディレクトリを基準にします。

| エージェント | `project`の導入先 | `user`の導入先 |
|---|---|---|
| Codex | `<Gitルート>/.agents/skills/studio_sdk` | `~/.agents/skills/studio_sdk` |
| Claude Code | `<Gitルート>/.claude/skills/studio_sdk` | `~/.claude/skills/studio_sdk` |

`--agent all`では両方の導入先を事前に確認してからコピーします。片方に置換が必要な異なるSkillがあり、`--force`を指定していない場合、もう片方だけを先に導入しません。Project範囲では、導入先にシンボリックリンクが含まれる場合も中止します。

## 2. 結果を確認してエージェントを開始する

成功時は標準出力へJSONが出力されます。`skill`は`studio_sdk`、`scope`は指定範囲、`installs`にはエージェント、導入先の`path`、状態が含まれます。`--pretty`はCLI全体のフラグであり、整形したい場合は先頭側に指定します。

```bash
jai-studio --pretty skill install --agent claude-code --scope project
```

状態は次のように確認します。

| 状態 | 意味 | 対応 |
|---|---|---|
| `installed` | 導入先がなく、新しくコピーしました。 | 新しいエージェントセッションを開始します。 |
| `unchanged` | 導入先のファイル構成・内容が同梱Skillと同一でした。 | 変更は不要です。 |
| `replaced` | `--force`を指定し、異なる既存Skillを同梱Skillへ置換しました。 | 独自変更が必要だったかを確認し、新しいセッションを開始します。 |

導入済みのエージェントが起動中の場合、Skillを再検出しないことがあります。導入または更新後は、対象のCodexまたはClaude Codeの**新しいセッション**を開始してください。以後は通常どおり、SDK・CLI・Pageの実装や確認を依頼します。エージェントに秘密情報を会話やコードとして渡さず、生成されたコマンドは実行前に確認します。

## 更新、上書き、アンインストール

SkillはCLIを提供するSDKパッケージの版に対応します。更新前にはパッケージ内の`CHANGELOG.md`を確認し、SDKとCLIを同じ完全なバージョンへ更新して、検証環境で必要な代表操作を確認してください。0.x系ではminor更新にも破壊的変更が含まれ得ます。更新判断の詳細は[開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)を参照します。

```text
npm install --global @japan-ai-inc/studio-sdk@<検証済みバージョン>
jai-studio --version
jai-studio skill install --agent codex --scope project
```

同じ内容なら再実行は`unchanged`となり、ファイルを変更しません。パッケージ更新後に同じ導入先の内容が異なる場合は、既存内容を保護するためエラーになります。内容を比較・退避し、同梱版で置き換えると判断したときだけ`--force`を使います。

```bash
jai-studio skill install --agent codex --scope project --force
```

このCLIには`skill uninstall`サブコマンドはありません。不要になった場合は、対象表で確認した`studio_sdk`ディレクトリだけを、OSまたはリポジトリの通常のファイル操作で削除します。`user`範囲を削除すると、そのOSユーザーの該当エージェントでSkillを利用できなくなります。削除前に範囲、パス、独自の追加ファイルがないことを確認してください。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 非対話実行で選択が必要と表示される | `--agent`と`--scope`の両方を指定します。値は`codex`、`claude-code`、`all`、`project`、`user`だけです。 |
| 異なるSkillが存在すると表示される | 導入先の独自変更を確認します。残す場合は中止し、置換が承認済みの場合だけ`--force`を使います。 |
| Project範囲の場所が想定と違う | カレントディレクトリから最も近いGitルートを確認します。Gitルートがない場合はカレントディレクトリが基準です。 |
| 導入後もエージェントがSkillを使わない | 導入結果の`path`と`SKILL.md`を確認し、対象エージェントの新しいセッションを開始します。対応外のエージェント用の場所を推測してコピーしません。 |
| CLI自体が見つからない、または版が違う | `command -v jai-studio`と`jai-studio --version`で実行実体と版を確認し、[開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)に従って再導入します。 |

CLIの失敗時は標準エラーへJSON形式のエラーが出力され、終了コードは1です。`--debug`はサーバー応答の詳細表示用であり、このローカルSkill導入の切り分けには通常不要です。問い合わせにはCLI版、キーを含まない実行コマンド、`--agent`、`--scope`、導入先、エラー内容を記録します。APIキー、トークン、個人用ホームディレクトリの不要な詳細は添付しません。

## チェックリスト

- [ ] Node.js 22以上と、検証済みの`jai-studio`版を確認した
- [ ] CodexまたはClaude Codeのどちらへ、Projectまたはユーザーのどちらへ導入するか決めた
- [ ] 非対話実行では`--agent`と`--scope`の両方を指定した
- [ ] 出力の導入先と`installed`、`unchanged`、`replaced`の状態を確認した
- [ ] 異なる既存Skillを確認せずに`--force`を使っていない
- [ ] 導入・更新後に新しいコーディングエージェントセッションを開始した
- [ ] APIキー、トークン、実在するリソースIDをSkillやエージェント会話へ含めていない

## 次に読む記事

- [Web UI・AI支援・SDK・CLI・APIを選ぶ](../development/choose-dev-path.md)
- [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)
- [CLIの設定・プロファイル](cli-config-and-profiles.md)
