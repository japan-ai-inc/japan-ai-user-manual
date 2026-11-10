# CLI Agents

`jai-studio agents`は、Agentの一覧・詳細、単発実行、会話（room）の作成と読取、Canvas文書の読取を行うCLIです。利用できるコマンドを説明します。

## 関連記事

- [CLIの設定・プロファイル](cli-config-and-profiles.md)
- [SDK Agents：実行・ストリーム](../development/sdk-agents-invoke-and-stream.md)
- [SDK Agents：会話履歴・Canvas](../development/sdk-agents-history-and-canvas.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)

## 始める前に

Node.js 22以降と`@japan-ai-inc/studio-sdk` 0.11.0を導入し、接続用のCLIプロファイルまたはAPIキーを用意します。プロファイル、接続先、キーの優先順は[CLIの設定・プロファイル](cli-config-and-profiles.md)を参照してください。

成功時は標準出力へJSONを出力します。`--pretty`を付けると整形します。失敗時は標準エラー出力へ`error`、`statusCode`、`details`を持つJSONを出し、終了コード1で終了します。`--debug`は安全な調査環境だけで使います。

> Agentへのプロンプト、会話履歴、Canvasには機密情報が含まれ得ます。出力を共有する前に内容を確認してください。APIキーを`--key`へ直接書くことは避けます。

## 共通フラグと対象範囲

| フラグ | 既定値 | 意味 |
|---|---|---|
| `--profile <name>` | なし | この実行で使うプロファイルを選択します。 |
| `--env <env>` | なし | 接続先を上書きします。 |
| `--key <key>` | なし | APIキーを上書きします。履歴に残るため通常は使いません。 |
| `--pretty` | 無効 | 成功時のJSONを整形します。 |
| `--debug` | 無効 | 詳細なエラー応答を表示します。 |

グローバルな`--json`はありません。`pages deploy`だけの固有フラグを`agents`へ渡してはいけません。また、CLIにストリーム出力、ファイル添付、Storageファイル添付、タイムアウト指定のフラグはありません。これらが必要な場合は[SDK Agents：実行・ストリーム](../development/sdk-agents-invoke-and-stream.md)のSDKを使います。

## 1. Agentを探して詳細を確認する

### 一覧：`agents list`

```text
jai-studio [共通フラグ] agents list [--cursor <cursor>] [--limit <n>] [--search <query>]
```
以下の`<...>`を含む表記は**置換が必要な構文表記**です。角括弧を含めてそのままシェルへ貼り付けず、実際の値に置き換えてください。


| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `--cursor <cursor>` | いいえ | なし | 前の`nextCursor`を渡して次ページを取得します。 |
| `--limit <n>` | いいえ | CLIでの指定なし | 1ページの最大件数を数値で指定します。 |
| `--search <query>` | いいえ | なし | Agent名で検索します。 |

```bash
jai-studio agents list --search "要約" --limit 20 --pretty
```

結果には`agents`、`totalCount`、`nextCursor`が含まれます。各Agentの`id`などを確認し、次ページが必要な場合だけ返却された`nextCursor`をそのまま渡します。`--limit`は安全な整数形式でなければ拒否されます。利用可能な上限は接続先の応答に従います。

### 詳細：`agents get`

```text
jai-studio [共通フラグ] agents get <agentId>
```

| 引数 | 必須 | 意味 |
|---|---:|---|
| `<agentId>` | はい | 取得するAgentの識別子です。 |

```bash
jai-studio agents get 550e8400-e29b-41d4-a716-446655440000 --pretty
```

## 2. Agent定義を作成・取得・更新する

SDK 0.11.0では、Project APIキーの`agent:write`スコープでAgent定義をCLIから管理できます。キーの作成者にも対象操作の権限が必要です。作成は冪等ではないため、同名を作る前に`agents list --search`で確認します。

```text
jai-studio agents create --file ./agent.json
jai-studio agents definition <agentId> > ./agent.json
jai-studio agents update <agentId> --file ./agent.json
```

作成用JSONでは`name`、`prompt`、`model`が必須です。`description`、`openingStatement`、`modelMode`、`datasets`、`tools`、`maxOutputTokens`、`reasoningEffort`、`temperature`は必要に応じて指定します。`definition`は更新に渡せる定義だけをJSONで出力します。更新では、存在しない項目は変更されず、配列は置換され、`null`はその項目をクリアします。キーのProject外のデータセット、または作成者に提示されないツール・Skill・モデルは指定できません。

## 3. Agentを実行し、会話を継続する

### 実行：`agents invoke`

```text
jai-studio [共通フラグ] agents invoke <agentId> --prompt <text> [--session-id <id>] [--model <model>] [--temperature <temp>] [--system-prompt <text>]
```

| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `<agentId>` | はい | — | 実行するAgentの識別子です。 |
| `--prompt <text>` | はい | — | Agentへ渡すプロンプトです。 |
| `--session-id <id>` | いいえ | なし | 会話を継続するroom IDです。 |
| `--model <model>` | いいえ | なし | モデル指定を上書きします。 |
| `--temperature <temp>` | いいえ | なし | 温度を数値で上書きします。 |
| `--system-prompt <text>` | いいえ | なし | システムプロンプトを上書きします。 |

```bash
jai-studio agents invoke 550e8400-e29b-41d4-a716-446655440000 \
  --prompt "この文章を3点で要約してください" --pretty
```

完了後にJSONを出力する非ストリーム実行です。結果の`status`、`sessionId`、`text`を確認し、`status`が`failed`なら`errorMessage`も確認します。画像、ファイル、参照も確認します。URLには期限がある場合があるため、出力をそのまま永続化・公開しません。

`sessionId`を次回の`--session-id`へ渡すと会話を継続できます。

```bash
jai-studio agents invoke 550e8400-e29b-41d4-a716-446655440000 \
  --session-id "前回のsessionId" --prompt "2点目を詳しくしてください"
```

`--temperature`は数値へ変換されます。数値でない値の詳細な妥当性確認は接続先に委ねられます。CLIには逐次表示、キャンセル、添付の指定はありません。必要な場合はSDKを使います。

## 4. roomの作成とメッセージ履歴の取得

### roomを作成する：`agents rooms`

```text
jai-studio [共通フラグ] agents rooms <agentId>
```

| 引数 | 必須 | 意味 |
|---|---:|---|
| `<agentId>` | はい | roomを作成するAgentの識別子です。 |

```bash
jai-studio agents rooms 550e8400-e29b-41d4-a716-446655440000
```

成功結果の`roomId`を後続の実行・読取に使います。作成済みroomの一覧を取得するCLIコマンドはありません。

### メッセージを読む：`agents messages`

```text
jai-studio [共通フラグ] agents messages <roomId> [--cursor <cursor>] [--limit <n>]
```

| 引数・フラグ | 必須 | 既定値 | 意味 |
|---|---:|---|---|
| `<roomId>` | はい | — | 読み取るroomの識別子です。 |
| `--cursor <cursor>` | いいえ | なし | 前の結果の`nextCursor`を渡します。 |
| `--limit <n>` | いいえ | CLIでの指定なし | 1ページの最大件数を数値で指定します。 |

結果は`roomId`、`messages`、`totalCount`、`nextCursor`です。`nextCursor`が`null`になるまで繰り返し、取得済みIDで重複表示を防ぎます。

## 5. Canvas文書を一覧・取得する

### 一覧：`agents canvas list`

```text
jai-studio [共通フラグ] agents canvas list <roomId> [--cursor <cursor>] [--limit <n>]
```

`<roomId>`は必須です。`--cursor`と`--limit`の意味は`agents messages`と同じです。結果の`canvasArtifacts`はメタデータだけで、本文の`content`は含みません。`id`、`name`、`documentType`、`contentSizeBytes`、`createdAt`、`updatedAt`から対象を選びます。

### 本文：`agents canvas get`

```text
jai-studio [共通フラグ] agents canvas get <roomId> <canvasArtifactId>
```

| 引数 | 必須 | 意味 |
|---|---:|---|
| `<roomId>` | はい | Canvasのroom IDです。 |
| `<canvasArtifactId>` | はい | 一覧で得たCanvas IDです。 |

`canvas get`は保存済みの`content`と`contentHash`を含む1件を返します。別roomに属するIDは取得できません。CanvasのHTMLやJSONを検証せずブラウザへ表示・実行してはいけません。詳細は[SDK Agents：会話履歴・Canvas](../development/sdk-agents-history-and-canvas.md)を参照してください。

## よくある誤解

- **CLIでストリーミング実行できる**：できません。`agents invoke`は完了後に結果を出力します。逐次表示はSDKの`invokeStream()`を使います。
- **CLIで任意ファイルを添付できる**：できません。添付関連のフラグは登録されていません。SDKの`files`または、該当する実行環境での`storageFileIds`を検討します。
- **`sessionId`とroom IDは別物である**：継続実行で使う`sessionId`はroom IDとして扱えます。成功結果から取得した値を使います。
- **Canvas一覧には本文が含まれる**：含まれません。必ず`canvas list`でIDを選び、`canvas get`で個別取得します。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 接続・認証に失敗する | プロファイル、接続先、対象Project、キーの有効期限とスコープを[CLIの設定・プロファイル](cli-config-and-profiles.md)、[Project APIキーを管理する](../operations/manage-project-api-keys.md)に沿って確認します。実値をログへ出しません。 |
| `--limit`で失敗する | 整数だけを指定し、先頭・末尾の不要な文字を除きます。前の`nextCursor`以外を`--cursor`へ組み立てません。 |
| 会話が継続されない | 前回の成功結果の`sessionId`を、同じAgentの`--session-id`へ渡しているか確認します。 |
| 履歴・Canvasを取得できない | room ID、Canvas ID、対象Project、認証経路を確認します。別のroom IDを試して内容を探索しません。 |
| 詳細なエラーを見たい | 共有しない安全な環境でのみ`--debug`を付けます。出力に秘密情報がないことを確認してから記録します。 |

## チェックリスト

- [ ] `agents invoke`の`status`、`sessionId`、`text`、必要時は`errorMessage`を確認した
- [ ] 継続会話では成功結果の`sessionId`を保存して`--session-id`へ渡した
- [ ] ページングでは返却された`nextCursor`を利用した
- [ ] Canvasの内容を未検証のまま表示・実行していない

## 次に読む記事

- [CLIの設定・プロファイル](cli-config-and-profiles.md)
- [SDK Agents：実行・ストリーム](../development/sdk-agents-invoke-and-stream.md)
- [SDK Agents：会話履歴・Canvas](../development/sdk-agents-history-and-canvas.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
