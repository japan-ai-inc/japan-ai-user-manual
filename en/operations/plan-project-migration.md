# Plan a Migration Between Projects

When switching Projects, do not assume a bulk migration. For each resource, decide whether to **link it, recreate it in the destination, or publish it as an App and install it**. Creating an Agent or Workflow with the same name does not provide dependencies such as Datasets, Skills, connections, Secrets, Storage, Pages, and Objects.

This article explains the inventory and confirmation order before migration. For linking and unlinking, see [Link and Unlink Existing Resources](../build/link-and-unlink-resources.md); for Page design, see [Design Configuration for Distributable Pages](../development/design-distributable-page-config.md); and for distribution constraints, see [Distribution Scope, Dependencies, and Authentication Constraints](../distribution/distribution-scope-dependencies-auth.md).

## Key points

| Item | Rule |
|---|---|
| Copying between Projects | **There is no such feature.** |
| What can be linked | **Only organization-level and official resources** (Agents, Workflows, Knowledge (including Datasets), and Skills). Resources created inside another Project cannot be linked. |
| Moving resources created in a Project | Recreate them in the destination, or publish them as an App and then install it. |
| Nature of links | A link references the same resource. Changes to the source are reflected immediately in every linked destination. Unlinking an Agent also removes that Agent's IAM assignments. |

## Scope of this article

Migration here means planning to move configuration and data within the same organization. It does not cover deleting Projects, moving data in external services, or transferring authentication values. For publishing and installing Apps, start from [Distribution Scope, Dependencies, and Authentication Constraints](../distribution/distribution-scope-dependencies-auth.md).

In "Add Resources," you can link organization-level or official **Agents, Workflows, Knowledge (including Datasets), and Skills**. Objects, Storage, Pages, Connectors, and Project Secrets cannot be linked.

> A link is a reference to the source resource itself. If you want to change only the destination independently, a link cannot do that.

## Choose the migration method first

For each target, record which of the following methods to use.

| Method | Applicable targets | Relationship to the source | What to check after migration |
|---|---|---|---|
| Link | Organization-level and official Agents, Workflows, Knowledge, and Skills | References the same resource. Source changes are reflected immediately in all linked destinations. | Clarify the source administrator and update procedure. |
| Recreate | Everything (including resources created in a Project) | Prepare new settings or data in the destination. Independent of the source. | Compare the source and destination, and confirm run results after cutover. |
| Publish and install as an App | Agents, Workflows, Pages, and other items created in the source Project that can be included in an App | Introduced as an App. What is included follows the distribution specifications. | Check for configurations that cannot be published and the initial setup after installation (shared connections, Secrets). |

## Migration approach by target

| Target | Approach | Main checks |
|---|---|---|
| Agent | Link organization-level or official Agents. Recreate Agents created in a Project, or move them via an App. | Instructions, model, Datasets, Skills, connections, and default Agent. |
| Workflow | Link organization-level or official Workflows. Recreate Workflows created in a Project, or move them via an App. | Testing, publication, start conditions, acting identity, and destinations. |
| Dataset | Link organization-level or official items as Knowledge. Re-import others. | Sharing scope, processing status, and reference settings. |
| Skill | Link organization-level or official Skills. Recreate Skills created in a Project. | Attached files, publication scope, and addition to Agents. |
| Object | Recreate the structure in the destination and move required records. | Fields, access conditions, relations, and import results. Prepare referenced items first. |
| Storage | Prepare folders and files in the destination. | Structure, permissions, and paths used by Agents, Workflows, and Pages. |
| Page | Upload the Page source again in the destination. | Access conditions, Storage references, and Secret names. |
| Connector | Select, create, and authenticate it in the destination. | Authentication account and personal/shared status. Workflows use only shared connections. |
| Project Secret | Register it anew in the destination. | Do not record actual values; confirm a representative operation. |
| Project API key | Issue a new key in the destination. | A key runs as its creator. Switch the external system's configuration. |

> Authentication information and Secrets are not items to migrate merely by moving their setting names. Treat cutover as complete only after confirming external-service permissions, authentication accounts, sharing scope, and run results in the destination. Do not enter the values themselves in migration tables or inquiries.

## 1. Create a migration inventory

List targets before changing the source Project. Check not only screen lists but also what Agents, Workflows, and Pages reference.

| Item to record in the inventory | Example record |
|---|---|
| Target | Agent, Workflow, Dataset, Skill, Object, Storage, Page, Connector, Project Secret, API key |
| Name and purpose | A name distinguishable in a list and the business purpose |
| Migration method | Link, recreate, via App, or no migration needed |
| Source and destination | Source Project, destination Project, and source administrator |
| Dependencies | Agent→Skill/Dataset, Workflow→Agent/Skill, Page→Storage/connection, etc. |
| Owner and confirmation method | Content reviewer, authentication owner, representative operation, and cutover approver |
| Cutover and rollback | Cutover time, how long to retain the old configuration, and contact |

Record Workflows that send to or update external services, scheduled runs, webhooks, published Pages, shared connections, and Secrets as targets with potential impact.

## 2. Follow dependencies outward from the source

Determine migration targets as units.

1. For each Agent, record "Plugins," "Data Integration," model, instructions, and intended users.
2. For each Workflow, record its Agents, Workflows, Skills, Datasets, start conditions, inputs, publication status, and destinations.
3. When linking a Workflow, check **"Auto-add Dependencies"** and **"Skipped Dependencies"** on the confirmation screen. Required Agents, Workflows, and Skills can be added automatically.
4. For Objects, Storage, and Pages, record not only names but also referenced Objects, file paths, and connection and Secret names.
5. For Connectors and Project Secrets, do not record actual values. Record their purpose, authentication owner, destination, features that reference them, and contact for updates.

"Skipped Dependencies" shows reasons such as insufficient access permissions, not found, disabled, deleted, Project-specific, or out of scope. Project-specific items cannot be linked, so recreate them in the destination.

## 3. Migrate dependencies first

Before the definitions that use them, prepare the data, authentication, and settings those definitions reference.

1. Decide the members, roles, migration owners, test data, and external-service test destinations for the destination Project.
2. Prepare Object structures and required records, and Storage folders and files, in the destination.
3. Configure Connectors and Project Secrets in the destination and confirm the smallest representative external-service operation.
4. Link or recreate Datasets and Skills, and confirm sharing scope and availability.
5. Link or recreate Agents and confirm the destination's Dataset, Skill, and connection prerequisites are satisfied.
6. Link or recreate Workflows. Check automatic addition and skipping of dependencies, and publish recreated items after testing.
7. Upload Pages in the destination and confirm Storage references, authentication, Page display, and representative operations.
8. Switch settings that could duplicate runs, such as scheduled runs, webhooks, and notifications, last.

> If automatic execution is enabled in both the source and destination Projects, the same notification, send, or update is duplicated. Decide the cutover time and who stops the old configuration before enabling it.

## 4. Compare before and after cutover

Separately confirm that settings are visible in the destination and that user operations succeed.

### Pre-migration checklist

- [ ] Recorded the source and destination Projects, administrators, cutover time, and rollback method
- [ ] Inventoried targets as Agents, Workflows, Datasets, Skills, Objects, Storage, Pages, Connectors, Project Secrets, and API keys
- [ ] Classified each target as link, recreate, via App, or no migration needed (only organization-level and official resources can be linked)
- [ ] Checked Workflow dependencies, automatic additions, and reasons for skipping
- [ ] Checked external sends, data updates, scheduled runs, webhooks, and published Pages as potentially affected
- [ ] Did not record Secret values or tokens in the inventory

### Post-cutover checklist

- [ ] Confirmed the update owner (source administrator) of linked resources
- [ ] Confirmed that recreated resources can be changed as needed in the destination
- [ ] Confirmed Dataset processing status, Skill publication scope, Object structures and records, and Storage files
- [ ] Confirmed representative operations for Agents, Workflows, and Pages with permitted test data
- [ ] Confirmed that external-service destinations, update destinations, and execution accounts are correct
- [ ] Organized automatic execution, webhooks, and usage guidance in the old Project, and confirmed that runs are not duplicated
- [ ] Before deleting the old configuration, confirmed that the destination operations owner completed review

## Troubleshooting

Resources that do not appear as link candidates (resources created in a Project, Objects, Storage, Pages, Connectors, and Secrets) must be recreated or moved via an App.

When consulting an administrator or responsible person, record the following.

1. The source and destination organizations and Projects, target type and name, and purpose of use.
2. The migration method selected, candidates and skip reasons displayed on screen, and error messages.
3. Dependent Agents, Workflows, Datasets, Skills, Objects, Storage, Pages, connections, and Secret names.
4. Whether external sends or updates occur, the desired cutover time, and test results already performed.

Do not include Secret values, API keys, tokens, passwords, confidential data, or unnecessary file contents in an inquiry.

## Related and next articles

- [Link and Unlink Existing Resources](../build/link-and-unlink-resources.md)
- [Workflow and Automatic Execution Specifications and Limits](../build/workflow-and-automation-specs.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Configure Shared Connections for Projects and Apps](configure-shared-connections.md)
- [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
- [Manage Project API Keys](manage-project-api-keys.md)
- [Design Configuration for Distributable Pages](../development/design-distributable-page-config.md)
- [Distribution Scope, Dependencies, and Authentication Constraints](../distribution/distribution-scope-dependencies-auth.md)
