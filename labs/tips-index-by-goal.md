# 目的別Tipsの索引

この実習は、「何をしたいか」から読む記事を選ぶための索引です。目的、対象の組織・Project、扱えるデータ、利用者、期待する結果を小さく整理し、適切な記事を一つ選びます。ここは機能の仕様や操作を再掲する記事ではありません。リンク先の手順・権限・制限を正本として確認してください。

## 関連記事

- [目的から機能を選ぶ](../getting-started/choose-features-by-goal.md)
- [自動実行方式を選ぶ](../getting-started/choose-execution-method.md)
- [役割別の読み方](../getting-started/reading-by-role.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)
- [問い合わせに必要な情報を集める](../troubleshooting/collect-info-for-support.md)

## 始める前に

- 実在の顧客情報、個人情報、Secret、APIキーを、検討メモや問い合わせに書きません。
- 対象組織・Projectの条件を確認し、最初は一つの目的・利用者・確認結果に絞ります。

## この実習で使うサンプル

架空の相談「人事規程への質問に、検証用資料を根拠として答えさせ、根拠がなければ担当者確認を案内したい」を例にします。「社内資料で答えさせたい」から始め、資料準備、Agent設定、回答テストを順に確認します。全社公開、外部接続、自動実行は追加しません。

## Step 1. 目的を一文にして、最初の記事を選ぶ

**目標：** 手段ではなく、利用者が達成したい結果から読み始めます。

1. 「誰が」「何を入力し」「どの結果を確認したいか」を一文にします。
2. 次表から近い目的を一つ選び、最初のリンクを読みます。
3. 記事を読んで前提が合わなければ、別の目的へ戻ります。権限や機能を推測して先へ進みません。

| 目的 | 読む記事 |
|---|---|
| 回答の形式や判断を整える | [Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md) → [Agentをテストして改善する](../build/test-and-improve-agents.md) |
| 社内資料を根拠に回答する | [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md) → [Agentにツール・Skill・Datasetを設定する](../build/configure-agent-tools-skills-datasets.md) |
| 定型作業を実行する | [Workflowのノードを構成する](../build/configure-workflow-nodes.md) → [Workflowの下書きをテストする](../build/test-workflow-drafts.md) |
| 日時・イベントで起動する | [自動実行方式を選ぶ](../getting-started/choose-execution-method.md) → [Workflowトリガーを設定する](../build/configure-workflow-triggers.md)または[タスクスケジューラを設定する](../build/configure-task-scheduler.md) |
| Pageを作る | [AIにPageの作成・修正を依頼する](../build/ask-ai-to-build-pages.md) → [画面からPageを登録・公開する](../build/register-and-publish-pages.md) |
| Objectを保存・更新する | [Objectの構造を設計する](../build/design-object-structure.md) → [Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md) |
| コード・外部システムと連携する | [Web UI・AI支援・SDK・CLI・APIを選ぶ](../development/choose-dev-path.md) → [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md) |
| Appを利用・配布する | [MarketplaceでAppを選ぶ](../use/choose-marketplace-app.md) → [Appの初期設定を完了する](../use/complete-app-setup.md) |

目的と機能の選び方は[目的から機能を選ぶ](../getting-started/choose-features-by-goal.md)を参照してください。**確認ポイント：** 最初に選んだ記事が、利用者・入力・結果の一文と一致しています。

## Step 2. 必要な条件を分けて確認する

**目標：** 機能、データ、アクセス、運用を混ぜずに確認します。

1. 対象の組織・Projectと、作成者・利用者・管理者を決めます。
2. 入力に使うデータ、参照するデータ、出力や更新先を分けて書きます。
3. 共有、外部接続、Secret、APIキー、公開URL、自動実行が必要かを個別に判断します。
4. 利用できない・表示されない場合は、権限、提供条件、設定状態を確認します。別の人の認証情報や広い権限で回避しません。

役割ごとの読む順序は[役割別の読み方](../getting-started/reading-by-role.md)、権限は[組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)、条件と上限の確認先は[制限・提供条件の索引](../operations/limits-and-availability-index.md)を参照してください。**確認ポイント：** 「作りたいこと」と「必要な権限・共有・認証・自動実行」を別々に説明できます。

## Step 3. 小さく試し、結果から次の記事を選ぶ

**目標：** 最初の代表操作を確認してから、範囲を広げます。

1. 検証用Projectと架空データで、目的に対応する記事の最小手順を一つ実施します。
2. 期待する結果、実際の結果、未確認事項を記録します。成功表示だけで、業務上の正しさや他の利用者の利用可否まで判断しません。
3. 次表を使い、結果に応じた詳細記事を選びます。

| 確認したいこと | 読む記事 |
|---|---|
| Agentが資料を参照しない | [Agentがデータを参照しない](../troubleshooting/agent-not-using-data.md) |
| Workflowの入力・失敗箇所 | [Workflowの実行履歴でデバッグする](../build/debug-workflow-run-history.md) |
| Pageの表示・操作 | [Pageから業務を進める](../use/work-with-pages.md) |
| 接続・担当者変更 | [接続の失効・担当者変更に対応する](../operations/handle-expired-connections-and-owner-changes.md) |
| APIやSDKのエラー | [SDK／APIのエラーを切り分ける](../troubleshooting/isolate-sdk-api-errors.md) |
| App導入後の設定 | [Appの初期設定を完了する](../use/complete-app-setup.md) |

小さく試す設計は[目的から機能を選ぶ](../getting-started/choose-features-by-goal.md)、問い合わせ前の記録は[問い合わせに必要な情報を集める](../troubleshooting/collect-info-for-support.md)を参照してください。**確認ポイント：** 次に読む記事を、目的ではなく実際に確認した結果と未確認事項から選びます。

## チェックリスト

- [ ] 利用者、入力、期待する結果を一文にした
- [ ] 目的に合う最初の記事を一つ選んだ
- [ ] 対象組織・Project、データ、共有範囲、必要な役割を確認した
- [ ] 外部接続、Secret、APIキー、公開、自動実行の必要性を分けて確認した
- [ ] 検証用の小さな操作と結果を確認した
- [ ] 未確認事項と次に読む記事を記録した

## よくある誤解

| 誤解 | 確認すること |
|---|---|
| 検索・参照結果だけで、全資料を確認できる | 資料検索と全文確認は別の目的です。必要な方法を[目的から機能を選ぶ](../getting-started/choose-features-by-goal.md)で選びます。 |
| 記事を読めば、その機能を使える | 組織、Project、役割、提供条件、設定によって利用可否は変わります。 |
| 公開・自動実行を最初に設定した方が早い | まず手動の代表操作を検証し、影響範囲と停止方法を確認してから追加します。 |
| 管理者が成功すれば全利用者も使える | 利用者のアクセス、個人設定、対象データへの権限を別に確認します。 |

## うまくいかない場合

| 状況 | 最初に確認すること |
|---|---|
| どの記事から始めるか決められない | 利用者、入力、結果を一文に戻し、[目的から機能を選ぶ](../getting-started/choose-features-by-goal.md)から選びます。 |
| 機能や作成画面が表示されない | 対象組織・Project、参加状態、役割、提供条件を[表示されない・参加できない](../troubleshooting/not-visible-cannot-join.md)で確認します。 |
| 権限・認証エラーが出る | 他人の認証情報を使わず、対象、必要な操作、表示内容を[権限・認証エラー](../troubleshooting/permission-and-auth-errors.md)で確認します。 |
| 問い合わせが必要になった | 対象、日時、再現手順、表示内容、確認済みの操作を[問い合わせに必要な情報を集める](../troubleshooting/collect-info-for-support.md)に従って整理します。Secret値は送りません。 |

## 後片付け

1. 検討メモに含めた架空データ、URL、ID、画面共有用の記録を確認し、不要な共有を停止します。
2. Step 3で実際に作成したAgent、Dataset、Object、Page、Workflow、Appがあれば、各詳細記事の後片付けに従います。
3. 接続、Secret、APIキー、トリガー、スケジュール、公開URLを追加した場合は、利用中の処理と担当者を確認してから停止・失効・非公開にします。
4. 実習対象を残す場合は、実習用であること、所有者、次の確認先を記録します。

削除・引き継ぎの判断は[削除・引き継ぎと監査記録を確認する](../operations/deletion-handover-and-audit-logs.md)を参照してください。

## 次に読む記事

- [目的から機能を選ぶ](../getting-started/choose-features-by-goal.md)
- [自動実行方式を選ぶ](../getting-started/choose-execution-method.md)
- [役割別の読み方](../getting-started/reading-by-role.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)
- [問い合わせに必要な情報を集める](../troubleshooting/collect-info-for-support.md)
