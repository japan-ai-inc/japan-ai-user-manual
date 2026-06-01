# 2. 前提条件と利用環境

### 2.1 対応ブラウザ

- Google Chrome（最新版を推奨）
- Microsoft Edge（最新版）
- 上記以外でも JAPAN AI 本体のサポート範囲に準じます

### 2.2 ネットワーク

- HTTPS 経由でのアクセス
- 外部サービス連携（Gmail / Outlook / Google Drive 等）を利用する場合は、対応 SaaS への到達が必要

### 2.3 必要な権限

| 操作 | 必要なロール |
|---|---|
| プロジェクトの新規作成 | 組織編集者以上、または該当する組織側ポリシーの権限 |
| プロジェクト内リソースの作成・編集 | プロジェクトの編集者以上 |
| メンバー招待・ロール変更・削除 | プロジェクトの管理者、または組織管理者 |
| 監査ログ閲覧 | 組織管理者（`iam.audit.read` 権限を持つロール） |
| シークレット閲覧 | プロジェクトのメンバー以上（権限の詳細は [21 章](21-roles-permissions.md)） |

### 2.4 機能の提供条件

Studio の一部機能は、契約プラン・組織設定（機能トグル）により有効・無効が切り替わります。代表的な機能トグル:

- カスタムオブジェクト（`EnableStudioCustomObjects`）
- ページ（`EnableStudioPages`）
- IAM 強化モード（`EnableStudioIamEnforcement`）
- 組織横断リソースインポート（`EnableStudioGlobalResourceImport`）
- メール系トリガー（Gmail / Outlook / Email by JAI）

ご利用環境で表示されない機能がある場合は、組織管理者または導入元へお問い合わせください。

---

