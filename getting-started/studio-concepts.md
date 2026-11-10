# Studio Workspace・Project・App・Marketplaceの関係

Studioでは、業務に合わせて機能を作ることも、すでに用意されたものを利用することもできます。そのときに登場するのが、Studio Workspace、Project、Studio App、Marketplaceです。

**Studio Workspaceは構築・管理の領域、Projectはリソースとメンバーをまとめる単位、Appは利用者に提供するアプリ、MarketplaceはAppを探して導入する入口**と考えると、違いを整理できます。

この記事では、4つの用語の関係と、目的に合った利用の入口を説明します。作成・インストールの操作手順や、詳しい権限設定は別の記事で扱います。

## 4つの用語を区別する

| 用語 | 役割 | 例えばこんなときに使います |
|---|---|---|
| **Studio Workspace** | Project単位でAgent・Workflow・Pageなどを作成・管理する領域 | 業務に合う機能を作る、既存の構成を見直す |
| **Project（プロジェクト）** | 業務に必要なリソースとメンバーをまとめる単位 | 営業支援のAgent、資料、画面などをまとめてチームで使う |
| **Studio App（アプリ）** | 利用者向けに提供する業務アプリ。配布されたAppをインストールして利用する方法もあります | 用意された画面や処理で、日常の業務を進める |
| **Marketplace** | 提供されているAppを探し、内容や導入条件を確認する入口 | 自分の業務に合うAppを探して、導入を検討する |

本マニュアルでは、構築・管理の領域を指すときは「Studio Workspace」と表記します。Projectや組織と同じ意味で「Workspace」を使いません。

## Studio WorkspaceとProjectの関係

Studio Workspaceでは、Projectを単位として構築・管理を進めます。

例えば「営業支援」というProjectに、商談情報を扱うObject、質問に答えるAgent、入力画面となるPageなどを組み合わせる構成が考えられます。こうした構成要素を、このマニュアルでは**リソース**と呼びます。

```text
Studio Workspaceで作成・管理する
  └─ Project：営業支援
       ├─ 利用するメンバー
       └─ 業務に必要なリソース
            ├─ Agent・Workflow
            ├─ Object・Dataset・Storage
            └─ Page など
```

この図は、Projectで扱う内容を整理した概念図です。画面のメニュー配置や、リソースがすべてProject専用であることを示すものではありません。既存リソースを追加・再利用する場合の扱いは、対象機能や設定によって異なります。

**Projectは作成者だけが使う場所ではありません。** 作成を担当しない人も、共有されたProjectで、許可されたAgentやPage、Workflowなどを利用できます。Projectに参加していることと、すべての機能を編集できることは別です。

## ProjectとAppの関係

ProjectとAppは、完全に別々の仕組みではありません。Projectで構成した画面や処理を、利用者向けのAppとして提供・配布する使い方があります。

- **Projectに注目するとき**：どのリソースを使い、誰が参加し、どの範囲を管理するかを考えます。
- **Appに注目するとき**：利用者が何の業務を行え、どの画面や処理を使うかを考えます。

例えば、作成者が営業支援のProjectで画面と処理を整え、利用者には営業支援Appとして案内する、という関係です。

ただし、Projectを作成しただけでMarketplaceへ掲載されるわけではありません。Appとして配布する場合は、配布する内容や必要な接続を確認し、所定の公開・導入の手順を進めます。**Projectで使うこと、Appとして配布すること、Marketplaceへ掲載することは、それぞれ別の段階です。**

利用者が共有されたProjectをそのまま使う場合、必ずApp化やMarketplaceからのインストールを行う必要がある、という意味でもありません。

## AppとMarketplaceの関係

Marketplaceは、Appを探して導入を検討する場所です。導入済みAppを開いて日常業務を行う場面とは、目的が異なります。

```text
MarketplaceでAppを探す
  → 内容・導入条件を確認する
  → 必要な権限を持つ担当者がインストールする
  → 必要に応じて初期設定・外部接続・利用者の設定を行う
  → 導入済みAppを利用する
```

この流れは、Marketplaceから導入する場合の概要です。組織内で提供されたAppを利用するなど、Marketplaceを経由しない場合もあります。

また、**インストール済みでも、必要な設定がすべて完了しているとは限りません。** 外部サービスへの接続や利用者本人の認証が必要なAppでは、その設定が済むまで一部の機能を利用できない場合があります。

Appの一覧では、導入済みのものを利用する表示と、Marketplaceで探す表示を区別して確認します。管理権限に応じて、組織内のAppを管理する表示が加わる場合もあります。表示される項目や操作範囲は、組織の設定・提供条件・権限によって異なります。

## 自分の目的に合った入口を選ぶ

| 今の状況 | 最初に確認すること | 詳しい記事 |
|---|---|---|
| 管理者からAppを使うよう案内された | 対象のAppと、必要な初期設定・個人認証 | [導入済みAppを開いて利用を始める](../use/open-installed-app.md) |
| Projectを共有された | 参加先のProjectと、自分が利用する機能 | [共有されたProjectで利用を始める](../use/start-with-shared-project.md) |
| 業務に合う既存のAppを探したい | MarketplaceのAppの内容・導入条件 | [MarketplaceでAppを選ぶ](../use/choose-marketplace-app.md) |
| 自分で業務に合う機能を作りたい | Studio Workspaceで構築するProject | [Projectを作成して基本設定する](../build/create-project-and-basic-settings.md) |
| 作ったものをAppとして届けたい | 配布対象・導入先・必要な設定 | [ProjectからAppへの配布設計](../distribution/design-app-distribution-from-project.md) |

AppやProjectが見つからないときは、別の場所に新しく作る前に、管理者へ対象名・参加状態・利用権限を確認してください。

## 次に読む記事

- 各構成要素の役割を知る：[構成機能の役割](studio-components.md)
- 権限の違いを知る：[組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- Studio全体の概要に戻る：[Studioとは](studio-overview.md)
