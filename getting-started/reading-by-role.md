# 役割別の読み方

このマニュアルは、最初からすべて読む必要はありません。**今の自分が「使う」「作る」「管理する」「開発する」「配布する」のどれを担当するか**に合わせて、必要な記事から読み始めてください。

この記事では、役割ごとの入口と、次に読む記事の順序を案内します。機能の操作手順や権限の詳しい説明は、それぞれの専門記事で扱います。

> ここでの「役割」は、記事を選ぶための読者分類です。製品上のロール名や操作権限を表すものではありません。同じ人が複数の役割を担当する場合もあります。実際に操作できる範囲は、対象の組織・Project・Appの設定や権限によって異なります。

## 今やりたいことから入口を選ぶ

| 今やりたいこと | 読み始める記事 |
|---|---|
| 案内されたAppを使いたい | [導入済みAppを開いて利用を始める](../use/open-installed-app.md) |
| 共有されたProjectの機能を使いたい | [共有されたProjectで利用を始める](../use/start-with-shared-project.md) |
| 自分の業務に合う仕組みを作りたい | [目的から機能を選ぶ](choose-features-by-goal.md) |
| Appを選定し、組織に導入したい | [MarketplaceでAppを選ぶ](../use/choose-marketplace-app.md) |
| メンバー・権限・接続を管理したい | [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md) |
| コードやSDK・APIで開発したい | [Web UI・AI支援・SDK・CLI・APIを選ぶ](../development/choose-dev-path.md) |
| 作ったAppを利用者に届けたい | [ProjectからAppへの配布設計](../distribution/design-app-distribution-from-project.md) |
| 操作しながら学びたい | [最初のProjectとAgent](../labs/first-project-and-agent.md) |

## 利用者：用意された機能で業務を進める

AppやProjectを使う人は、構築方法やAPI仕様を先に学ぶ必要はありません。まず、自分に案内された対象から始めます。

1. **入口を確認する**：Appなら[導入済みAppを開いて利用を始める](../use/open-installed-app.md)、Projectなら[共有されたProjectで利用を始める](../use/start-with-shared-project.md)を読みます。
2. **使う機能の記事へ進む**：画面操作は[Pageから業務を進める](../use/work-with-pages.md)、会話は[Agentと会話して結果を確認する](../use/chat-with-agents.md)、Workflow実行は[公開済みWorkflowを実行する](../use/run-published-workflow.md)を選びます。
3. **本人の接続が必要な場合だけ設定する**：[自分の外部アカウントを接続する](../operations/connect-your-external-accounts.md)を確認します。

データの操作が担当業務に含まれる場合は、[Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md)や[Storageのファイルを利用する](../use/use-storage-files.md)へ進んでください。

**共有されたProjectを利用するだけなら、自分でProjectを新規作成する必要はありません。** 対象が見つからない場合は、新しく作る前に、案内された名前・参加状態・利用権限を管理者へ確認します。

## 作成者：業務に合う仕組みを組み立てる

Agent・Workflow・データ・Pageなどを組み合わせる人は、機能ごとの操作より先に、作る目的と最小構成を決めます。

1. [目的から機能を選ぶ](choose-features-by-goal.md)で、入力・出力と必要な機能を整理します。
2. 新しい作業場所が必要なら、[Projectを作成して基本設定する](../build/create-project-and-basic-settings.md)へ進みます。既存Projectを変更する場合は、変更対象と影響範囲を先に確認します。
3. 選んだ機能の構築記事へ進みます。例えばAgentなら[Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)、Workflowなら[Workflowのノードを構成する](../build/configure-workflow-nodes.md)です。
4. 利用者に渡す前にテストします。Agentは[Agentをテストして改善する](../build/test-and-improve-agents.md)、Workflowは[Workflowの下書きをテストする](../build/test-workflow-drafts.md)を参照します。

自動実行を組み込む場合は、[自動実行方式を選ぶ](choose-execution-method.md)で起動方法を整理します。機能の意味が曖昧な場合は、[構成機能の役割](studio-components.md)へ戻って確認してください。

## 導入・管理・運用担当者：使い続けられる状態を整える

管理を担当する人は、まず**どの組織・Project・Appを、どの範囲まで管理するのか**を確認します。管理者という呼び方だけで、すべての対象を操作できるとは判断しません。

| 担当する仕事 | おすすめの読む順序 |
|---|---|
| Marketplaceから新しいAppを導入する | [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)→ [MarketplaceでAppを選ぶ](../use/choose-marketplace-app.md)→ [MarketplaceからAppをインストールする](../use/install-marketplace-app.md)→ [Appの初期設定を完了する](../use/complete-app-setup.md) |
| メンバーや接続を管理する | [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md) → [Project・Appのメンバーを管理する](../operations/manage-project-and-app-members.md)→ [個人接続・共有接続・Secretを選ぶ](../operations/choose-personal-shared-connections-and-secrets.md) |
| 定期実行を引き継ぐ | [定期実行を変更・停止・引き継ぐ](../operations/change-stop-handover-scheduled-runs.md)→ [実行結果の通知を設定する](../operations/configure-run-result-notifications.md) |
| 導入済みAppの更新に対応する | [App更新の変更点と再設定を確認する](../use/check-app-updates.md)→ 必要に応じて[接続の失効・担当者変更に対応する](../operations/handle-expired-connections-and-owner-changes.md) |

インストールや更新の確認と、認証・メンバー設定は分けて読みます。共有接続を管理する作業と、利用者本人が自分の外部アカウントを接続する作業も、混同しないようにしてください。

## 開発者：コードで画面や処理を実装する

Pageのコード、SDK、CLI、APIを扱う人は、メソッド一覧から読み始めるのではなく、開発方法と実行環境の前提から確認します。

1. [Web UI・AI支援・SDK・CLI・APIを選ぶ](../development/choose-dev-path.md)で開発方法を選びます。
2. [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)で、実行場所と認証方法を確認します。
3. [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)で対象環境をそろえます。
4. Page開発、SDKガイド、CLI・APIリファレンスのうち、実装する機能へ進みます。

コードを書く担当者と、APIキー・権限・接続を管理する担当者が異なる場合は、必要な設定を管理担当者と確認します。開発者向け記事を読めることと、対象データや設定への操作権限を持つことは別です。

## 配布担当者：作ったAppを届ける

作成済みの構成をAppとして届ける人は、[ProjectからAppへの配布設計](../distribution/design-app-distribution-from-project.md)から始めます。その後、[配布対象・依存・認証設定の制約](../distribution/distribution-scope-dependencies-auth.md)で、何を配布し、導入先で何を設定するかを確認します。

次に、担当する配布方法へ進みます。

- **作成者が組織内に導入する**：[作成者が組織内self-installする](../distribution/self-install-in-organization.md)
- **Marketplace掲載を申請する**：[Marketplaceへ公開申請する](../distribution/submit-to-marketplace.md)

**導入先でAppを使い始める仕事と、配布元からAppを届ける仕事は別です。** 前者は「Studioを使う」「管理・運用」、後者は「Appの公開・配布」を中心に読みます。

## 学習したいとき・困ったとき

### 手を動かして学びたい

初めて構築する場合は、[最初のProjectとAgent](../labs/first-project-and-agent.md)から始めます。業務アプリ全体を試したい場合は[タスク管理アプリ](../labs/task-management-app.md)、コードでPageを開発したい場合は[SDK／CLIでPageを開発する](../labs/develop-page-with-sdk-cli.md)を選びます。

研修・ワークショップを運営する場合や、目的別のヒントを探す場合は、次の記事を参照してください。

- 研修・ワークショップを運営したい：[ワークショップの進行](../labs/run-workshop.md)
- 目的別のヒントを探したい：[目的別Tipsの索引](../labs/tips-index-by-goal.md)

ハンズオンは実習の順序を知るための教材です。実際の業務へ展開する際は、利用する機能の設定・権限・制限も確認してください。

### 動かない・表示されない

役割に関係なく、困っている症状から記事を探せます。

- 表示されない・参加できない：[表示されない・参加できない](../troubleshooting/not-visible-cannot-join.md)
- 権限や認証で止まる：[権限・認証エラー](../troubleshooting/permission-and-auth-errors.md)
- Appの導入や設定が完了しない：[App導入・初期設定・更新に失敗する](../troubleshooting/app-install-setup-update-failures.md)
- 問い合わせに必要な情報を整理したい：[問い合わせに必要な情報を集める](../troubleshooting/collect-info-for-support.md)

## 複数の役割を兼ねる場合

記事は肩書きではなく、**今行う作業**で選びます。例えば、業務担当者が自分でAgentを作る場合は「作成者」の順序で読み、完成後の日常利用では「利用者」の記事へ移ります。チームへ共有するときは、必要に応じて「管理・運用」を確認します。

まだ役割や対象が決まっていない場合は、[Studioとは](studio-overview.md)で全体像を確認し、[Studio Workspace・Project・App・Marketplaceの関係](studio-concepts.md)で使う対象を整理してください。

アカウントや基本画面の確認へ進む場合は、次の記事[利用開始の前提と基本画面](getting-started-and-navigation.md)を参照します。
