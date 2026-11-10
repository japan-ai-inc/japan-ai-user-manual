# ワークショップの進行

この実習は、JAPAN AI Studioの教材を講師として安全に進めるための進行例です。受講者が検証用Projectと架空データを使い、作成・実行・確認・後片付けまで自分で行えるようにします。講師が先に操作を終えたり、権限不足を広い権限や他人の認証情報で回避したりしません。

## 関連記事

- [最初のProjectとAgent](first-project-and-agent.md)
- [社内問い合わせAgent](internal-helpdesk-agent.md)
- [定型業務の自動化](automate-routine-work.md)
- [タスク管理アプリ](task-management-app.md)
- [外部サービス連携アプリ](external-service-integration-app.md)
- [SDK／CLIでPageを開発する](develop-page-with-sdk-cli.md)
- [Appの組織内導入](deploy-app-in-organization.md)
- [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)

## 始める前に

- 講師、補助者、受講者が開く組織・Projectを決め、本番用と検証用を名前と説明で区別します。
- 実在の顧客情報、個人情報、業務データ、パスワード、APIキー、Secretの値を、教材、投影、チャット、画面共有に入れません。
- 講師と補助者の組織・Projectの役割、受講者の参加状態を確認します。権限の層は[組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)、メンバー操作は[Project・Appのメンバーを管理する](../operations/manage-project-and-app-members.md)を参照してください。
- 利用できる機能、実行に伴う費用、組織ごとの条件は当日の画面と[制限・提供条件の索引](../operations/limits-and-availability-index.md)で確認します。別の教材や組織の条件を流用しません。
- APIキー、外部接続、Secret、自動実行、公開URLを使う教材では、所有者と停止・失効の担当を事前に決めます。

## この実習で使うサンプル

講師は、次のような架空の題材だけを配布します。受講者が業務の内容に置き換える場合も、実在データを使わないよう案内します。

| 教材 | 作成するもの | 使う架空データ | 到達点 |
|---|---|---|---|
| [最初のProjectとAgent](first-project-and-agent.md) | 会議メモを整理するAgent | 架空の会議メモ | 原文にない事項を補わない回答を確認する |
| [社内問い合わせAgent](internal-helpdesk-agent.md) | FAQを参照するAgent | 架空の経費精算FAQ | 資料内・資料外の質問を照合する |
| [定型業務の自動化](automate-routine-work.md) | 日報を整理するWorkflow | 架空の日報 | 下書きと公開版の結果を確認する |
| [タスク管理アプリ](task-management-app.md) | タスク管理のObject・Page・Agent | 架空のタスク | 更新値と回答をレコードに照合する |
| [外部サービス連携アプリ](external-service-integration-app.md) | 外部サービス連携のPage | 架空の日報要約 | 検証用送信先だけへ1件送る |
| [SDK／CLIでPageを開発する](develop-page-with-sdk-cli.md) | SDK／CLIのPage | 架空のお知らせ | 成果物とPage上の表示を確認する |
| [Appの組織内導入](deploy-app-in-organization.md) | 組織内導入するApp | [タスク管理アプリ](task-management-app.md)の実習データ | 導入先の利用者として確認する |

## Step 1. 主教材と到達点を決める

**目標：** 受講者の役割・権限・扱えるデータに合う教材を一つ選びます。

1. 初めての受講者には[最初のProjectとAgent](first-project-and-agent.md)、資料参照には[社内問い合わせAgent](internal-helpdesk-agent.md)、Workflowには[定型業務の自動化](automate-routine-work.md)、Object・Page・Agentの組合せには[タスク管理アプリ](task-management-app.md)を候補にします。
2. 外部サービス、SDK／CLI、App導入を扱う場合は、[外部サービス連携アプリ](external-service-integration-app.md)・[SDK／CLIでPageを開発する](develop-page-with-sdk-cli.md)・[Appの組織内導入](deploy-app-in-organization.md)の前提と後片付けを先に読みます。
3. 各受講者について、作成するもの、入力、確認ポイント、後片付けの担当を講師メモに記録します。
4. 複数教材を続ける場合も、前の教材で作った公開・自動実行・認証情報の状態を確認してから次へ進みます。

各教材の詳細と確認ポイントは[最初のProjectとAgent](first-project-and-agent.md)から[Appの組織内導入](deploy-app-in-organization.md)を参照してください。**確認ポイント：** 「面白そうな機能」ではなく、受講者が確認できる到達点と安全な検証範囲で教材を選びます。

## Step 2. 当日の環境を確認する

**目標：** 受講者が正しい検証環境で、必要最小限の範囲を操作できる状態にします。

1. 開始前に、全員が正しい組織・検証用Projectを開いていることを確認します。
2. 講師は必要な役割を確認しますが、受講者へ不要な管理権限を付与しません。
3. 画面に機能が表示されない場合は、対象、参加状態、役割、提供条件を確認します。表示させるために設定を自己判断で変更しません。
4. APIキーを使う教材だけ、用途、対象Project、保管者、失効担当を確認します。画面や配布資料へ実値を出しません。

権限は[組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)、参加状態は[Project・Appのメンバーを管理する](../operations/manage-project-and-app-members.md)、キーの扱いは[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照してください。**確認ポイント：** 全員が本番用ではなく、操作を許可された検証用Projectを開いています。

## Step 3. 受講者がStepと確認ポイントを実施する

**目標：** 講師の画面を模倣するだけでなく、受講者自身が入力・設定・結果を照合します。

1. 講師は今回の主教材のStep 1を説明し、受講者は自分の検証用Projectで実施します。
2. 各Stepの終わりに、対象名、入力値、保存・公開状態、結果を教材の確認ポイントと照合します。
3. 期待と異なる場合は、受講者が画面上の対象、入力、権限、表示メッセージを記録します。講師は同じ操作を連続実行させません。
4. Agent、Workflow、Pageなどの実行結果は、画面に成功と出たかだけでなく、架空入力と期待結果を照合します。

操作の詳細は主教材がリンクするBUILD／USE／OPS記事を正本とします。たとえば、会話の確認は[Agentと会話して結果を確認する](../use/chat-with-agents.md)、公開済みWorkflowの確認は[公開済みWorkflowを実行する](../use/run-published-workflow.md)、Page利用は[Pageから業務を進める](../use/work-with-pages.md)を参照してください。**確認ポイント：** 講師の結果と同じであることではなく、受講者が自分の対象と結果を説明できます。

## Step 4. 共有・自動実行・外部影響を確認する

**目標：** 実習で追加した影響範囲を、終了前に把握します。

1. Page、Agent、チャット、Appで共有・公開を行った場合は、対象、利用者、URL、公開範囲を確認します。
2. Workflowのトリガーやスケジュールを追加した場合は、対象、次回実行、通知、停止担当を確認します。
3. 外部接続、Secret、APIキーを使った場合は、どのProject・処理が使うかを確認し、値そのものを記録・共有しません。
4. 実習の目的に不要な公開・自動実行・接続を残さないよう、後片付けの担当と完了条件を決めます。

停止・引き継ぎは[定期実行を変更・停止・引き継ぐ](../operations/change-stop-handover-scheduled-runs.md)、接続の扱いは[接続の失効・担当者変更に対応する](../operations/handle-expired-connections-and-owner-changes.md)、削除判断は[削除・引き継ぎと監査記録を確認する](../operations/deletion-handover-and-audit-logs.md)を参照してください。**確認ポイント：** 誰が、何を、いつ停止・確認するかが決まっています。

## チェックリスト

- [ ] 主教材、対象者、到達点、後片付けの担当を決めた
- [ ] 組織・Project・受講者のログイン先を検証用として確認した
- [ ] 架空データだけを用意し、秘密値・実データを共有していない
- [ ] 各受講者が教材の確認ポイントを自分の画面で確認した
- [ ] 共有、公開、外部接続、APIキー、自動実行の有無と担当者を確認した
- [ ] 未完了の項目、対象、確認先を記録した

## うまくいかない場合

| 状況 | 講師の最初の対応 |
|---|---|
| 組織・Project・作成画面が見つからない | ログイン先、Project参加、役割、提供条件を確認します。[表示されない・参加できない](../troubleshooting/not-visible-cannot-join.md)を参照します。 |
| 受講者ごとに候補・結果が違う | Project、選択中の対象、共有範囲、役割、組織設定を分けて確認します。画面共有前に機密情報がないか確認します。 |
| Agentが資料を参照しない | Datasetの状態、選択、質問、指示を[社内問い合わせAgent](internal-helpdesk-agent.md)と[Agentがデータを参照しない](../troubleshooting/agent-not-using-data.md)で確認します。 |
| APIキー・外部接続が失敗する | 実値を共有せず、対象、期限、最小権限、表示メッセージを確認します。[SDK／APIのエラーを切り分ける](../troubleshooting/isolate-sdk-api-errors.md)を参照します。 |

## 後片付け

1. 受講者ごとに、作成したProject、Agent、Dataset、Object、Page、Workflow、Appを一覧にします。
2. スケジュール・トリガーは停止済みで、次回実行や公開中の自動起動が残っていないことを確認します。
3. 公開・共有した対象は、利用者と範囲を確認し、不要なら非公開または削除します。
4. APIキー、Secret、接続は、利用中の処理がないことと担当者を確認してから失効・削除・引き継ぎます。
5. 実習用Projectを残す場合は名前と説明に実習用と残し、不要な場合は他で参照されていないことを確認して整理します。

## 次に読む記事

- [最初のProjectとAgent](first-project-and-agent.md)
- [定型業務の自動化](automate-routine-work.md)
- [タスク管理アプリ](task-management-app.md)
- [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)
