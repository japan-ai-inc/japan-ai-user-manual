# JAPAN AI Studio ユーザーマニュアル

JAPAN AI Studio（以下「Studio」）の使い方を、**使う人・作る人・管理する人・開発する人・配布する人**のそれぞれの立場から説明するマニュアルです。

最初からすべてを読む必要はありません。次の表から、今の目的に近い記事を選んでください。

## 目的から読み始める

| やりたいこと | 最初に読む記事 |
|---|---|
| 案内されたAppを使いたい | [導入済みAppを開いて利用を始める](use/open-installed-app.md) |
| 共有されたProjectの機能を使いたい | [共有されたProjectで利用を始める](use/start-with-shared-project.md) |
| 自分の業務に合う仕組みを作りたい | [目的から機能を選ぶ](getting-started/choose-features-by-goal.md) |
| Appを選定し、組織に導入したい | [MarketplaceでAppを選ぶ](use/choose-marketplace-app.md) |
| メンバー・権限・接続を管理したい | [組織・Project・Appの権限を理解する](operations/understand-org-project-app-permissions.md) |
| コードやSDK・APIで開発したい | [Web UI・AI支援・SDK・CLI・APIを選ぶ](development/choose-dev-path.md) |
| 作ったAppを利用者に届けたい | [ProjectからAppへの配布設計](distribution/design-app-distribution-from-project.md) |
| 操作しながら学びたい | [最初のProjectとAgent](labs/first-project-and-agent.md) |
| うまく動かない・エラーが出る | [表示されない・参加できない](troubleshooting/not-visible-cannot-join.md) |
| 用語の意味を確認したい | [用語集](getting-started/studio-glossary.md) |

役割ごとの読み方は、[役割別の読み方](getting-started/reading-by-role.md)にまとめています。

## マニュアルの構成

| 分類 | 内容 | 記事数 |
|---|---|---|
| Studioを知る | 全体像、基本用語、役割別の読み方、利用開始時の基本画面 | 8 |
| 使う | App・Projectの機能を使って業務を進める | 12 |
| 作る | Project、Agent、Workflow、データ、Page、Skillの作成と、各機能の仕様・制限 | 29 |
| ハンズオン | 架空のデータで操作しながら学ぶ実習 | 9 |
| 管理・運用 | 権限、メンバー、接続、Secret、APIキー、定期実行、利用状況、削除と引き継ぎ | 18 |
| Appの配布 | ProjectからAppへの配布、組織内導入、Marketplace公開 | 5 |
| 開発・リファレンス | Page開発、SDK、CLI、公開API、Hosted Page API、Object SQL | 42 |
| トラブルシューティング・お知らせ | 症状別の切り分け、問い合わせ時の情報、リリースノート、非推奨・移行 | 11 |

## このマニュアルについて

- 記載内容は、2026年11月9日時点で確認したStudioの実装と、`@japan-ai-inc/studio-sdk` 0.11.0を基準にしています。11月10日までの本番更新（v1.22.20）との差分も確認し、本マニュアルに関わる変更を反映しています。
- 画面上の表示名は「」で囲んで記載しています。
- 上限・件数・時間などの数値は、確認時点の値です。契約プラン、組織の設定、権限、提供状況によって異なる場合があります。
- お使いの環境の表示や動作がこのマニュアルと異なる場合は、組織の管理者へ確認してください。
