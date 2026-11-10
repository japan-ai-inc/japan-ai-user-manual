# 最初のProjectとAgent

この実習では、検証用Projectに会議メモを整理するAgentを作り、架空の入力で会話を確認します。外部接続、Dataset、Workflow、Pageは追加しません。最後に会話とProjectを整理します。

## 関連記事

- [利用開始の前提と基本画面](../getting-started/getting-started-and-navigation.md)
- [Projectを作成して基本設定する](../build/create-project-and-basic-settings.md)
- [Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)
- [Agentと会話して結果を確認する](../use/chat-with-agents.md)
- [Agentをテストして改善する](../build/test-and-improve-agents.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)

## この実習で作るもの・完成イメージ

完成物は、貼り付けた会議メモを要点、決定事項、次の対応、未確認事項に分ける練習用Agentです。入力にない日付・担当者・決定を補わず、外部への送信や予定登録を行わないことを確認します。指示文だけで権限を制限できるわけではないため、ツールや接続を追加しない検証用Projectで行います。

## 始める前に

- ProjectとAgentを作成・編集できる組織を確認します。機能が見つからない場合は、組織の設定、契約、権限を[制限・提供条件の索引](../operations/limits-and-availability-index.md)で確認します。
- 実在の顧客名、個人情報、会議資料、認証情報は入力しません。
- Project作成とAgent設定の詳しい操作は、先に[Projectを作成して基本設定する](../build/create-project-and-basic-settings.md)と[Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)で確認します。

## この実習で使うサンプル

| 項目 | 実習で使う値 |
|---|---|
| Project名 | `練習_議事録要約` |
| Projectの説明 | 架空の会議メモを整理する練習用Project。外部送信、予定登録、業務データ更新は行わない。 |
| Agent名 | `議事録要約アシスタント（練習用）` |
| Agentの説明 | 貼り付けた会議メモを整理する。 |

Agentの指示には次を入力します。

> あなたは会議メモの要約を支援するAgentです。今回貼り付けられたメモだけを根拠に、要点、決定事項、次の対応、未確認事項の順で整理してください。記載のない担当者、期限、数値、決定を推測で補わず、未確認事項として示してください。外部への送信、予定登録、業務データの更新は行わず、会議メモの整理だけを返してください。

会話には次の架空メモを使います。

```text
新製品Aの紹介資料を見直す。営業チームから価格表の更新要望があった。
資料の初稿は田中さんが次回会議までに作る。次回会議の日付は未定。
```

## Step 1. 練習用Projectを作る

**目標：** 本番用と区別できる作業場所を用意します。

1. Studio Workspaceで対象組織を確認し、**「作成」**から**「プロジェクトを作成」**を開きます。
2. サンプル表のProject名と説明を入力し、今回参加しないメンバーは追加しません。
3. **「セットアップ」**を選び、構築を後にする場合は画面の**「スキップ」**でProjectを開きます。
4. 名前と説明が検証用の内容であることを確認します。

詳細は[Projectを作成して基本設定する](../build/create-project-and-basic-settings.md)を参照してください。**確認ポイント：** `練習_議事録要約`を開け、検証用だと判断できます。

## Step 2. Agentを設定する

**目標：** 会議メモの整理だけを担当するAgentを保存します。

1. ProjectのAgent一覧で**「エージェントを作成」**を選び、サンプルの名前と説明を入力します。
2. **「指示 (システムプロンプト)」**に、上記の指示を貼り付けます。
3. **「使用可能なモデル」**で表示される候補を確認し、必要に応じて検証する候補を追加して**「デフォルトに設定」**します。
4. ツール、Skill、Dataset、外部接続は追加せず、開き直して名前・説明・指示・モデルを確認します。

詳細は[Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)を参照してください。**確認ポイント：** Agentの設定が残り、検証対象のモデルを確認できます。

## Step 3. 会話結果を確認する

**目標：** 入力にない事実や外部操作を、実行済みと扱わないことを確かめます。

1. 作成したAgentとの会話を開き、名前を再確認します。
2. サンプルメモを送信し、要点、決定事項、次の対応、未確認事項が区別されるか確認します。
3. 日付が未定であることを、決定済みの日付として補っていないか確認します。
4. 次に、予定登録やメール送信を依頼する短い文を送信し、実行したと回答しないことを確認します。

会話の操作は[Agentと会話して結果を確認する](../use/chat-with-agents.md)、条件を増やすテストは[Agentをテストして改善する](../build/test-and-improve-agents.md)を参照してください。**確認ポイント：** 回答を原文と照合でき、不足情報と対象外の依頼を区別できます。

## よくある誤解

| 誤解 | 確認すること |
|---|---|
| 指示に書けば外部操作の権限もなくなる | 指示とツール・接続・権限は別です。この実習では対象を追加しません。 |
| 画面を閉じればAgentの変更は消える | Agent設定には自動保存があります。開き直して保存状態を確認します。 |
| 返答が自然なら内容も正しい | 入力原文と、未確認事項・次の対応を照合します。 |

## うまくいかない場合

| 状況 | 最初に確認すること |
|---|---|
| Workspace、Project、または作成操作が見つからない | 組織、ログイン先、権限、提供条件を[利用開始の前提と基本画面](../getting-started/getting-started-and-navigation.md)と[Projectを作成して基本設定する](../build/create-project-and-basic-settings.md)で確認します。 |
| Agentを作成・編集できない | 正しいProject、作成権限、画面の案内を[Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)で確認します。 |
| 会話を送れない、回答を確認できない | Agent名、保存・有効状態、利用権限、エラー表示を[Agentと会話して結果を確認する](../use/chat-with-agents.md)で確認します。 |
| 指示どおりに整理されない | 指示、選択中のAgent・モデル、入力原文を見比べ、変更は一つずつ[Agentをテストして改善する](../build/test-and-improve-agents.md)で検証します。 |

## 後片付け

1. 練習会話を残す場合は、会話一覧で対象を確認して表示される**「アーカイブに追加」**を使います。これは会話の整理であり、AgentやProjectの削除ではありません。
2. Projectを残す場合は、名前・説明が検証用であることを確認し、実在データを追加しません。
3. 不要な場合は、作成したAgent、会話、メンバー、外部接続、自動実行が他で使われていないことを確認してからProjectを削除します。削除前の確認と引き継ぎは[削除・引き継ぎと監査記録を確認する](../operations/deletion-handover-and-audit-logs.md)を参照してください。

この実習ではスケジュール、トリガー、APIキー、公開Pageを作成しません。途中で追加した場合は、それぞれを停止・失効・非公開にしてからProjectを整理します。

## 次に読む記事

- [Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)
- [Agentにツール・Skill・Datasetを設定する](../build/configure-agent-tools-skills-datasets.md)
- [Agentをテストして改善する](../build/test-and-improve-agents.md)
