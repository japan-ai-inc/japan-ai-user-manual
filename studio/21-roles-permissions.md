# 21. ロールと権限リファレンス

Studio の権限文字列は `{サービス}.{リソース}.{アクション}` の形式です。

### 21.1 代表的な権限名

| サービス | 例 |
|---|---|
| `jai` | `jai.dataset.read`, `jai.agent.invoke`, `jai.workflow.publish`, `jai.secret.read`, `jai.model.invoke` |
| `studio` | `studio.storage.read`, `studio.page.update`, `studio.object.record.create`, `studio.object.getPolicy` |
| `iam` | `iam.role.create`, `iam.binding.update`, `iam.audit.read` |

### 21.2 ロール別 権限サマリ

| ロール | 主要権限 |
|---|---|
| 組織管理者（`jai:global:admin`） | `jai.*` + `iam.*` |
| 組織編集者（`jai:global:editor`） | `jai.*.{read,create,update,delete,invoke}` + `iam.*` |
| 組織メンバー（`jai:global:member`） | 主要リソースの参照と実行 |
| 組織 API キー（`jai:global:api_key`） | `jai.model.invoke`, `jai.agent.invoke`, `jai.workflow.invoke`, `jai.dataset.read` 等 |
| プロジェクト管理者（`jai:project:admin`） | `jai.*` + `studio.*` + `iam.*`（プロジェクトスコープ） |
| プロジェクト編集者（`jai:project:editor`） | `jai.*.{read,create,update,delete,invoke}` + `studio.*.{read,create,update,delete}` |
| プロジェクトメンバー（`jai:project:member`） | 参照・実行・限定的な作成（オブジェクト・レコード等） |

> Studio プロジェクトのメンバー招待時に選択可能なロールは 管理者・編集者・メンバー の 3 種類です。

### 21.3 リソースポリシー（個別設定）

ロールに加え、リソース個別にポリシー（[14.3](14-members-iam.md)）で許可・拒否を上書きできます。

---

