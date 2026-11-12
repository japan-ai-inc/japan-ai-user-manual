# Plan a Migration Between Projects

When switching Projects, do not assume a bulk migration. For each resource, decide whether to **link it, copy it, or recreate it in the destination**. Creating an Agent or Workflow with the same name does not provide dependencies such as Datasets, Skills, connections, Secrets, Storage, Pages, and Objects.

This article explains the inventory and confirmation order before migration. For linking and unlinking, see [Link and Unlink Existing Resources](../build/link-and-unlink-resources.md); for Page design, see [Design Configuration for Distributable Pages](../development/design-distributable-page-config.md); and for distribution constraints, see [Distribution Scope, Dependencies, and Authentication Constraints](../distribution/distribution-scope-dependencies-auth.md).

## Scope of this article

Migration here means planning to move configuration and data within the same organization. It does not cover deleting Projects, distributing or installing Apps, moving data in external services, or transferring authentication values.

The resources currently confirmed as link targets in "Add Resources" are **Agents, Workflows, Knowledge (including Datasets), and Skills**. It could not be confirmed that Objects, Storage, Pages, Connectors, and Project Secrets can be linked on the same screen. If candidates or actions are not displayed, check the target Project, permissions, and the organization's availability conditions.

> Being able to reference something across Projects and being able to independently edit and operate it are different. A link is a reference to the source; if you want to change only the destination, do not treat it as a permanently completed migration.

## Choose the migration method first

For each target, record which of the following three methods to use.

| Method | Suitable purpose | Relationship to the source Project | What to check after migration |
|---|---|---|---|
| Link | Use the same definition in multiple Projects | References the source definition. Changes in the source may affect linked destinations. | Clarify the source administrator and update procedure. |
| Copy | Changes must be made independently in the destination | Creates a new resource. Do not assume later source changes are reflected automatically. | Check the copy scope, dependencies, and publication and active status. |
| Recreate | It has Project-specific settings, data, or authentication information | Prepare new settings or data in the destination. | Compare the source and destination, and confirm run results after cutover. |

Copying does not necessarily duplicate dependent settings, data, and authentication information.

## Migration approach by target

Use the following table as an initial inventory. Confirm the final decision against candidates actually displayed, permissions, and dependencies in use.

| Target | Approach | Main checks |
|---|---|---|
| Agent | Organization-wide and official Agents are link candidates. Copy for independent editing. | Instructions, model, Datasets, Skills, connections, and default Agent. |
| Workflow | Organization-wide and official Workflows are link candidates. Copy for specific changes. | Testing, publication, start conditions, and destinations. |
| Dataset | Organization-wide and official items are link candidates as Knowledge. | Sharing scope, processing status, and reference settings. Re-import independent materials. |
| Skill | Organization-wide and official Skills are link candidates. Check whether copying is possible for specific changes. | Attached files, publication scope, and addition to Agents. |
| Object | Recreate the structure in the destination and move required records. | Fields, access conditions, relations, and import results. Prepare referenced items first. |
| Storage | Prepare folders and files in the destination. | Structure, versions, permissions, and paths used by Agents, Workflows, and Pages. |
| Page | Prepare the Page, artifacts, and settings in the destination. | Applied version, access conditions, Storage references, and Secret names. |
| Connector | Select, create, and authenticate it in the destination. | Authentication account, personal/shared status, and common App connectors. |
| Project Secret | Register it anew in the destination and configure references. | Do not record actual values; confirm a representative operation. |

> Authentication information and Secrets are not items to migrate merely by moving their setting names. Treat cutover as complete only after confirming external-service permissions, authentication accounts, sharing scope, and run results in the destination. Do not enter the values themselves in migration tables or inquiries.

## 1. Create a migration inventory

List targets before changing the source Project. Check not only screen lists but also what Agents, Workflows, and Pages reference.

| Item to record in the inventory | Example record |
|---|---|
| Target | Agent, Workflow, Dataset, Skill, Object, Storage, Page, Connector, Project Secret |
| Name and purpose | A name distinguishable in a list and the business purpose |
| Migration method | Link, copy, recreate, or no migration needed |
| Source and destination | Source Project, destination Project, and source administrator |
| Dependencies | Agent→Skill/Dataset, Workflow→Agent/Skill, Page→Storage/connection, etc. |
| Owner and confirmation method | Content reviewer, authentication owner, representative operation, and cutover approver |
| Cutover and rollback | Cutover time, how long to retain the old configuration, and contact |

Record Workflows that send to or update external services, scheduled runs, webhooks, published Pages, shared connections, and Secrets as targets with potential impact. Ask the responsible person about items whose impact is unknown.

## 2. Follow dependencies outward from the source

Determine migration targets as units.

1. For each Agent, record "Plugins," "Data Integration," model, instructions, and intended users.
2. For each Workflow, record its Agents, Workflows, Skills, Datasets, start conditions, inputs, publication status, and destinations.
3. When linking a Workflow, check **"Auto-add Dependencies"** and **"Skipped Dependencies"** on the confirmation screen. Required Agents, Workflows, and Skills may be added automatically.
4. For Objects, Storage, and Pages, record not only names but also referenced Objects, file paths, registered versions, and connection and Secret names.
5. For Connectors and Project Secrets, do not record actual values. Record their purpose, authentication owner, destination, features that reference them, and contact for updates.

"Skipped Dependencies" may display insufficient access permissions, not found, disabled, deleted, Project-specific, or out of scope. Do not treat migration as complete while items are skipped; recreate the dependency or check availability with the administrator.

## 3. Migrate dependencies first

Before the definitions that use them, prepare the data, authentication, and settings those definitions reference.

1. Decide the members, roles, migration owners, test data, and external-service test destinations for the destination Project.
2. Prepare Object structures and required records, and Storage folders and files, in the destination.
3. Configure Connectors and Project Secrets in the destination and confirm the smallest representative external-service operation.
4. Link, copy, or recreate Datasets and Skills, and confirm sharing scope and availability.
5. Link or copy Agents and confirm the destination's Dataset, Skill, and connection prerequisites are satisfied.
6. Link or copy Workflows. Check automatic addition and skipping of dependencies, and publish copied items after testing.
7. Register Pages in the destination and confirm Storage references, authentication, Page display, and representative operations.
8. Switch settings that could duplicate runs, such as scheduled runs, webhooks, and notifications, last.

> If automatic execution is enabled in both the source and destination Projects, the same notification, send, or update may be duplicated. Decide the cutover time and who stops the old configuration before enabling it.

## 4. Compare before and after cutover

Separately confirm that settings are visible in the destination and that user operations succeed.

### Pre-migration checklist

- [ ] Recorded the source and destination Projects, administrators, cutover time, and rollback method
- [ ] Inventoried targets as Agents, Workflows, Datasets, Skills, Objects, Storage, Pages, Connectors, and Project Secrets
- [ ] Classified each target as link, copy, recreate, or no migration needed
- [ ] Checked Workflow dependencies, automatic additions, and reasons for skipping
- [ ] Checked external sends, data updates, scheduled runs, webhooks, and published Pages as potentially affected
- [ ] Did not record Secret values or tokens in the inventory

### Post-cutover checklist

- [ ] Confirmed the update owner if a linked target is "Read-only" in the destination
- [ ] Confirmed that copied targets were created as resources separate from the source and can be changed as needed in the destination
- [ ] Confirmed Dataset processing status, Skill publication scope, Object structures and records, and Storage files
- [ ] Confirmed representative operations for Agents, Workflows, and Pages with permitted test data
- [ ] Confirmed that external-service destinations, update destinations, and execution accounts are correct
- [ ] Organized automatic execution, webhooks, and usage guidance in the old Project, and confirmed that runs are not duplicated
- [ ] Before deleting the old configuration, confirmed that the destination operations owner completed review

## When you cannot confirm

Do not force-link a type that is not a candidate, or assume that a copy action also moves data, authentication, and execution settings.

Record the following and check with an administrator or responsible person.

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
- [Design Configuration for Distributable Pages](../development/design-distributable-page-config.md)
- [Distribution Scope, Dependencies, and Authentication Constraints](../distribution/distribution-scope-dependencies-auth.md)
