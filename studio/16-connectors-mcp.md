# 16. 外部接続（コネクタ）と MCP

### 16.1 概要

Google Workspace、Microsoft 365、その他 SaaS との認証情報を一元管理します。設定画面 →「外部接続」配下に以下が並びます。

- **データ連携**：データソースの認証設定（Google Drive・OneDrive 等）
- **データ連携 API**：API ベースのコネクタ設定
- **ツール設定**：ツール（スキル）側の認証設定

> **画面プレースホルダー**：`screenshots/16-connectors.png`

### 16.2 利用シーン

- Gmail / Outlook の新着メールを起点にワークフロー起動
- Google Drive / OneDrive 上のファイルを RAG 用に取り込み
- 業務 SaaS の API を呼び出すスキルでの利用
- Slack / Chatwork のチャンネル履歴の取り込み

### 16.3 MCP サーバー連携

設定画面 →「デベロッパー」→「MCP サーバー設定」から、Model Context Protocol（MCP）対応の外部サーバーを Studio に登録できます。

| 項目 | 設定内容 |
|---|---|
| サーバー種別 | JAI クラスタ提供のサービス／任意の URL（Static） |
| クライアント種別 | streamable_http / sse |
| 認証 | OAuth / Bearer / カスタムマッピング |
| 公開ツール | サーバーが提供するツール群を Studio に取り込み |

JAI クラスタ提供型では、組織が登録済みのクレデンシャル（Gmail 等）を自動マッピングできます。Static 型では URL と認証情報をユーザーが指定します。

### 16.4 認証情報の管理

外部 SaaS への接続情報は、組織・ユーザー・コネクタの組み合わせで安全に保管され、ステータス（ACTIVE / INACTIVE / REVOKED）で管理されます。失効した場合は再認証が必要です。

---

