# 社内問い合わせAgent

この実習では、架空の経費精算FAQをDatasetに取り込み、それを参照するAgentを作ります。資料に答えがある質問と、ない質問を比べ、回答を原文と照合します。実在の社員名、申請内容、領収書、認証情報は使いません。

Datasetは関連する情報を探して回答に使うためのものです。契約書などを漏れなく点検する用途とは分けてください。

## 関連記事

- [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)
- [Agentにツール・Skill・Datasetを設定する](../build/configure-agent-tools-skills-datasets.md)
- [Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)
- [Agentと会話して結果を確認する](../use/chat-with-agents.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)

## 始める前に

- DatasetとAgentを作成・編集できる検証用の組織またはProjectを用意します。利用できない場合は[制限・提供条件の索引](../operations/limits-and-availability-index.md)を確認します。
- 検証用資料だけを使い、共有範囲は必要最小限にします。
- Dataset作成、Agent設定、会話の詳細は上記の関連記事で確認します。指示に資料名を書くだけではDatasetは接続されません。

## この実習で使うサンプル

ローカルで`経費精算FAQ.md`を作り、次を保存します。

```md
# 経費精算FAQ

## Q. 電車で訪問先へ行った場合、何を申請しますか？
A. 経路、利用日、訪問目的を記載して交通費として申請します。

## Q. 領収書はいつまでに提出しますか？
A. 経費を利用した月の翌月5日までに提出します。

## Q. 会食費を申請するときの注意は？
A. 参加者、目的、店舗名を記載し、領収書を添付します。
```

| 項目 | 実習で使う値 |
|---|---|
| Dataset名 | 経費精算FAQ（検証用） |
| Agent名 | 社内問い合わせAgent（検証用） |
| 使わないもの | 実在の経費情報、外部接続、送信・更新ツール |

Agentの指示には次を使います。

> 接続されたDatasetの経費精算FAQを根拠に回答してください。回答と根拠を分け、資料に根拠がない質問には推測せず、資料に記載がないため確認が必要である旨を伝えてください。この検証では外部への送信、申請の作成、業務データの更新は行いません。

## Step 1. FAQをDatasetに取り込む

**目標：** Agentが参照する資料を準備します。

1. 対象の組織またはProjectで**「データ連携」**を開き、Datasetを作成します。
2. 名前に`経費精算FAQ（検証用）`を入力し、共有設定が表示される場合は検証に必要な範囲を選びます。
3. **「データアップロード」**から`経費精算FAQ.md`を追加します。
4. 一覧で対象データの状態が**「完了」**になったことを確認します。

詳細は[Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)を参照してください。**確認ポイント：** 正しいファイルが対象Datasetにあり、処理中や失敗の表示ではありません。

## Step 2. Agentを作り、Datasetを選ぶ

**目標：** FAQを根拠に回答するAgentを準備します。

1. **「エージェントを作成」**で`社内問い合わせAgent（検証用）`を作り、上記の指示を入力します。
2. 利用できるモデルを選び、保存状態を確認します。
3. Agentの**「データ連携」**を開き、**「データセットを選択」**で`経費精算FAQ（検証用）`だけにチェックを付けます。
4. **「適用」**後、Agentを開き直してDataset名と保存状態を確認します。

Agent作成は[Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)、Dataset選択は[Agentにツール・Skill・Datasetを設定する](../build/configure-agent-tools-skills-datasets.md)を参照してください。**確認ポイント：** 指示だけで済ませず、正しいDatasetが選択されています。

## Step 3. 資料内の質問を照合する

**目標：** FAQ原文と対応する回答か確認します。

1. Agentとの会話を開き、次を送信します。

   > 電車で訪問先へ行った場合、申請に何を記載しますか？

2. 回答が経路、利用日、訪問目的と交通費申請に触れているかを原文と照合します。
3. 回答が示す根拠や参照先があれば、実際のFAQ内容を裏付けているか確認します。

会話の詳細は[Agentと会話して結果を確認する](../use/chat-with-agents.md)を参照してください。**確認ポイント：** FAQにない条件を付け足していません。

## Step 4. 資料外の質問を確認する

**目標：** 根拠がない制度を作り出さないことを確かめます。

1. 同じAgentへ「海外出張の宿泊費の上限はいくらですか？」と送信します。
2. 資料に記載がないため確認が必要である旨を回答できるか確認します。
3. 金額、上限、承認者など、FAQにない制度を補っていないか確認します。

テストの観点は[Agentをテストして改善する](../build/test-and-improve-agents.md)を参照してください。**確認ポイント：** 資料内の質問と資料外の質問を、どちらもFAQ原文と照合しました。

## よくある誤解

| 誤解 | この実習で確認すること |
|---|---|
| Datasetを接続すればFAQ全文を毎回読む | 関連情報を探す用途です。全文確認は別の手順にします。 |
| 根拠らしい表示があれば正しい | 回答と参照先を原文で照合します。 |
| 指示だけで資料参照を制御できる | Datasetの選択、取り込み状態、共有範囲も確認します。 |

## うまくいかない場合

| 状況 | 確認すること |
|---|---|
| Datasetを作成・追加できない | 対象、権限、画面の案内を[Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)で確認します。 |
| AgentがFAQを参照しない | データの状態、選択済みDataset、**「適用」**、保存状態を確認し、[Agentがデータを参照しない](../troubleshooting/agent-not-using-data.md)を参照します。 |
| 回答と原文が異なる | 回答を業務判断に使わず、FAQ、質問、指示、選択Datasetを見直します。 |
| Agentが見つからない・会話できない | Agent名、Project、利用権限、有効状態を[Agentと会話して結果を確認する](../use/chat-with-agents.md)で確認します。 |

## 後片付け

1. Agentの**「データ連携」**でDatasetの選択を外し、**「適用」**と保存状態を確認します。
2. Agent、Dataset、元ファイルを削除する場合は、他のAgentや利用者が使っていないことを確認します。選択解除とDataset・元ファイルの削除は別の操作です。
3. この実習ではスケジュール、トリガー、APIキー、公開Pageを作成しません。追加した場合は停止・失効・非公開を確認します。

## 次に読む記事

- [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md)
- [Agentにツール・Skill・Datasetを設定する](../build/configure-agent-tools-skills-datasets.md)
- [Agentをテストして改善する](../build/test-and-improve-agents.md)
- [Agentがデータを参照しない](../troubleshooting/agent-not-using-data.md)
