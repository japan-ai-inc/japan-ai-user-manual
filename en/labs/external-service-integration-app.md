# An App That Integrates an External Service

In this exercise, you will prepare a test Page that sends only one fictional daily-report summary to an external service approved for use by your organization. Check the connection, Secret, Page runtime environment, and send result separately. Do not use real customer names, revenue, personal information, credentials, or business destinations.

Do not put Secret values in screens, source code, deliverables, public environment variables, or support inquiries. Conditions for server processing and Secret references available to a Page vary by environment, so you must check them before beginning this exercise.

## Related articles

- [Choose personal connections, shared connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md)
- [Configure shared connections for Projects and Apps](../operations/configure-shared-connections.md)
- [Create, update, and delete Project Secrets](../operations/manage-project-secrets.md)
- [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md)
- [Register and publish Pages from the screen](../build/register-and-publish-pages.md)
- [Handle expired connections and owner changes](../operations/handle-expired-connections-and-owner-changes.md)

## Before you begin

- Choose a person who can edit the Project and manage connections and **"Project Secrets."** If permissions or the target service are not displayed, do not choose an alternative service on your own; check the [Index of limits and availability conditions](../operations/limits-and-availability-index.md).
- Prepare a test destination and an account with minimum permissions on the external service. Also choose who will stop or revoke sending.
- Confirm in [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md) that the target Page runtime environment can use server processing and Project Secret references. Being able to register a ZIP does not necessarily mean arbitrary server processing is available.
- Add only a test connection, test Secret, and test Page in this exercise. Do not add schedules, triggers, automatic retries, or public links.

## Sample used in this exercise

| Item | Value used in this exercise |
|---|---|
| Page name | Daily report sending (test) |
| Connection name | Daily report sending_test |
| Secret name | `lab005_service_token` |
| Destination | Test destination in the external service |
| Content to send | A fictional daily-report summary containing only date, assignee, activity, and next action |

Use the following input.

```text
Date: 2026-10-03
Assignee: Training coordinator
Activity: Reviewed the proposal material structure
Next action: Record the verification procedure
```

For Secret name and value constraints and for creating, updating, and deleting Secrets, use [Create, update, and delete Project Secrets](../operations/manage-project-secrets.md) as the source of truth. Do not record the actual token value in this material, screen shares, chats, or source code.

## Step 1. Decide the connection owner and test destination

**Goal:** Define whose credentials send to which destination and who is responsible.

1. Determine whether this send is personal or shared by multiple users or automatic processing.
2. For this exercise, choose who manages `Daily report sending_test` as a shared connection that uses a test account and destination.
3. Record the sender, connection owner, person responsible for stopping or revoking, and destination. Do not record the values themselves.

For choosing connections and Secrets, see [Choose personal connections, shared connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md). **Checkpoint:** You can explain the practice destination and person responsible for stopping it without repurposing a personal account as a team connection.

## Step 2. Create and check a shared connection

**Goal:** Check external-service authentication and the connection used by the Project.

1. Open **"Connector Settings"** from Project settings and check **"Project connectors."**
2. From **"New Connector,"** select the displayed target service and **"Project connector."** If shared settings are displayed, confirm that it is a **"Shared connection."**
3. Enter `Daily report sending_test` as the connection name. On the external-service authentication screen, select the test account. Check the requested permissions and destination.
4. After creation, compare the connection name, target service, and sharing scope in the list. Do not post anything here.

For details, see [Configure shared connections for Projects and Apps](../operations/configure-shared-connections.md). **Checkpoint:** Not only the name, but also the authentication account and destination are for testing.

## Step 3. Register a test Secret

**Goal:** Manage the external-service secret value separately from the Page deliverable.

1. Open **"Project Secrets"** in Project settings.
2. Select **"Create Secret,"** enter `lab005_service_token` as the name, and enter a test value obtained securely as the value.
3. After creation, check the name and update state in the list. Do not copy, display, or share the value to check it.
4. If an existing Secret has the same name, do not overwrite it; check the target and owner.

For Secret operations, see [Create, update, and delete Project Secrets](../operations/manage-project-secrets.md). **Checkpoint:** A Secret is not a replacement for a connection. Check the connection, Secret, and Page-side reference separately.

## Step 4. Implement and register the Page sending process

**Goal:** Create a process that passes only input values to the test destination.

1. Create a screen in `Daily report sending (test)` that accepts the sample four fields and starts a test post.
2. Use server processing and Secret references only by the method for the applicable environment in [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md).
3. Do not return Secret values or authorization headers to the browser, and do not output them in screens, errors, or logs. Do not create responses or screen displays that contain values.
4. On the server side, check required input fields and the destination, and limit the process to sending one summary by the approved method.
5. Before registering the Page, confirm that the deliverable, configuration files, source maps, and screen display contain no actual Secret value.

For Page registration and access settings, see [Register and publish Pages from the screen](../build/register-and-publish-pages.md). **Checkpoint:** Do not use a configuration that sends directly from the browser to an external API, contains an actual Secret value, or automates retries. Because conditions for implementations that do not expose Secrets depend on the environment, this exercise alone does not guarantee anything outside the scope of [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md).

## Step 5. Send one item and compare the result

**Goal:** Separately check sent content, destination, and that values are not displayed.

1. Open the Page as a user authorized for the exercise and confirm the target name.
2. Submit the test post only once with the sample input. If processing or the result is unclear, do not run it repeatedly.
3. Confirm that exactly one post containing only the four fields exists at the external service's test destination.
4. Confirm that the Page screen, authorized run records, and browser-visible responses do not have a design that displays Secret values or authorization headers.

For precautions when using a Page, see [Work with Pages](../use/work-with-pages.md). **Checkpoint:** Check not only connection success but also the destination, content, and count. If an actual Secret value may have been exposed, prioritize the response in the following table.

## Common misunderstandings

| Misunderstanding | What to check |
|---|---|
| Creating a connection makes a Secret unnecessary | Whether a connection and Secret are needed varies by service and implementation. |
| Saving a Secret makes it automatically available from a Page | Check the Page-side reference setting and runtime environment. |
| A success display means it reached the correct destination | Check the destination, content, and count in the external service. |
| It is safe as long as the Secret is not displayed on screen | Do not put it in source, deliverables, public settings, logs, or responses either. |

## If something does not work

| Situation | First thing to check |
|---|---|
| Connector Settings or the target service is not displayed | Check the Project, permissions, organization availability conditions, and approval status. See [Configure shared connections for Projects and Apps](../operations/configure-shared-connections.md). |
| You cannot create or reference a Secret | Check the correct Project, name, permissions, and Page runtime-environment support. See [Create, update, and delete Project Secrets](../operations/manage-project-secrets.md) and [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md). |
| Sending fails | Check the connection authentication state, external permissions, destination, input, and Page processing. Do not attach values to an error report. |
| A value appears in a screen, deliverable, or log | Stop sending, do not share the URL, and revoke and reissue the value on the external-service side. Notify the responsible person of the impact scope and do not resume until you confirm a safe state. |

## Cleanup

1. Unpublish or delete the Page, and if you shared a URL, notify users that it is no longer available.
2. After confirming that no Page, App, or Workflow uses the connection, revoke the test account or authentication on the external-service side, then clean up the Project connection. For details, see [Handle expired connections and owner changes](../operations/handle-expired-connections-and-owner-changes.md).
3. After confirming dependent processing has stopped, delete `lab005_service_token`. Deleting a Secret first may cause dependent functions to fail at runtime.
4. Delete or retain posts at the practice destination according to organization rules.

## Read next

- [Configure shared connections for Projects and Apps](../operations/configure-shared-connections.md)
- [Create, update, and delete Project Secrets](../operations/manage-project-secrets.md)
- [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md)
- [Handle expired connections and owner changes](../operations/handle-expired-connections-and-owner-changes.md)
