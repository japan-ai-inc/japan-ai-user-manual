# Register, Update, and Delete Project Secrets

Project Secrets are settings for managing confidential values such as API keys and tokens for each Project. This article explains **how administrators register confidential information, update it regularly, and safely delete values that are no longer needed**. Values are stored encrypted and are masked rather than displayed as actual values in lists and edit screens. Therefore, manage the original value in secure storage when registering it; do not assume it can be retrieved later from this screen.

For related content, see [Complete the Initial Setup of an App](../use/complete-app-setup.md), [Page Specifications and Limits](../build/page-specs-and-limits.md), [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md), and [Manage Web Login Secrets](manage-web-login-secrets.md). Project Secrets and web login Secrets are different types.

## Before you begin

- Confirm the target organization, Project, and App in use. You can use the settings screen **only when the Project Secret feature is enabled in the organization**. If you cannot find the item, do not substitute another screen or URL; ask the organization administrator.
- Manage Project Secrets by Project. Because the same name cannot be registered more than once in a Project, check existing names and purposes.
- Permissions are required. "View" permission is required to view a Secret; "Create" to register one; "Update" to update its value; and "Delete" to delete it. In addition to these, registration, updates, and deletion require a role that can edit the Project. Even if you can open the screen, you may not be able to perform an operation without its permission.
- Record the external service that supplies the value, its purpose, owner, switch date and time, and rollback method. Do not put actual values in tickets, chat, description fields, Page distribution files, browser settings, or logs.

> In Page/App release settings, you can declare a reference in the root target `.env` in the form `server_env_variable_name=${secret:secret_name}`. Rather than writing the actual value in distribution files, select and bind the Secret in the target Project. You cannot specify this reference for browser-public keys that begin with `NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`, or `PUBLIC_`. For Page/App execution methods and server-side implementation, see [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md).

## Input rules and display

| Item | Rule | Example and notes |
|---|---|---|
| Name | Begins with a lowercase English letter. Thereafter, use only lowercase English letters, numbers, and underscores. Maximum 64 characters. | `sales_api_token`. Uppercase letters, hyphens, and spaces cannot be used. Empty names and UUID-format names also cannot be registered. |
| Value | Cannot be empty; maximum 8,192 characters | Enter the value obtained from the issuer as is. Before and after pasting, check the issuer's instructions to see whether whitespace or line breaks affect authentication. |
| Uniqueness | Names cannot be duplicated in the same Project. | You cannot change the name in an update. To change a name, register it under the new name after checking the impact, switch references, then delete the old value. |
| List and details | Values are masked. | The list displays "Name," "Value," "Updated at," and "Actions." The current value remains masked when editing, so re-enter a new value if needed. |

> A masked display does not guarantee that you will not lose the value or that processing using it works. Do not copy or reregister the displayed string as the actual value.

## 1. Open "Project Secrets"

1. In JAPAN AI, confirm the target organization and Project.
2. From Project settings, open **"Project Secrets."**
3. In the list, check the name, masked value, and **"Updated at."** If needed, narrow the target with **"Search by secret name."**
4. If "You do not have permission to view project secrets." is displayed, check View permission and access to the target Project.

Entry points, displayed content, and available operations vary according to organization settings and permissions. If required Secrets are shown during initial App setup, check required items and their current configuration under the App's **"Required App Secrets."**

## 2. Register a new value

1. Select **"Create Secret"** and open **"Create Project Secret."**
2. Enter a name under **"Name"** that follows a convention that identifies the purpose. You cannot change the name from this screen after registration.
3. Under **"Value,"** enter the actual value obtained from the external service. The input is displayed in password format.
4. Check the input rules, target Project, issuer, and owner again, then select **"Create."**
5. When "Project secret created." appears, confirm that the name and update date and time appear in the list and that the value is masked.

If it is shown as an item required by an App, creating it alone does not necessarily complete configuration. Use **"Select secret"** on the screen to select the target, perform **"Bind,"** and check the status. Do not generalize the binding method or Page implementation; check App guidance and [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md).

## 3. Update and rotate a value

When a value may have leaked, its issuer plans to revoke it, or ownership changes, issue and switch to a new value. You can update the value, not the name.

1. From **"Actions"** in the target row, select **"Edit"** and open **"Edit Project Secret."**
2. Delete the masked existing value and enter the new actual value in **"Value."** If you will not change the value, do not save it while it remains masked.
3. Recheck the target name, issuer, switch time, and affected Pages, Apps, and Workflows, then select **"Save."**
4. When "Project secret updated." appears, check the update date and time.
5. Test a representative operation requiring authentication through the actual usage route. If Pages or Workflows may reference it, check success or failure for each target before informing users that switching is complete.

This scope of confirmation cannot determine when existing Pages or Workflows reflect an update, whether republication or rerunning is required, or how it affects an in-progress run. Do not conclude that it takes effect immediately or switches without interruption.

### Rotation checklist

- [ ] I confirmed that the new value is valid at the issuer.
- [ ] I confirmed the target, Secret name, and owner.
- [ ] I registered or updated the new value.
- [ ] I tested representative affected operations.
- [ ] I confirmed switching was complete before revoking the old value.
- [ ] I confirmed that I have not recorded or shared the actual value.

## 4. Delete a value that is no longer needed

Deletion cannot be undone. Even when changing a name or rotating, first finish registering, binding, and testing the new value, then delete the old value only after it is no longer used.

1. In the list, check the target name and update date and time.
2. From **"Actions,"** select **"Delete."**
3. Read the confirmation in **"Delete Project Secret."** If it is referenced by an App, also check the referenced items.
4. Contact the people responsible for dependent Pages, Apps, and Workflows, and confirm that alternate values are configured and testing is complete.
5. Confirm **"Delete."** When "Project secret deleted." appears, confirm that the target disappeared from the list.

> Even if you delete a Secret referenced by an App, the App itself may remain usable. However, some dependent features may produce runtime errors. Check reference items displayed before deletion and test Pages, Apps, and Workflows in use. The specific Page reference method has not been confirmed, so this article does not explain it.

## If it does not work

| Situation | Check and response |
|---|---|
| "Project Secrets" cannot be found | Ask the administrator whether the feature is enabled in the organization, whether this is the target organization and Project, and whether you have permission. Do not substitute another Secret type. |
| You cannot select create, edit, or delete | Check the Secret permission for that operation and a role that can edit the Project. Do not grant yourself unnecessary permissions; ask the permission administrator. |
| You cannot save the name or value | Check that the name is no more than 64 characters and follows the rule, the value is non-empty and no more than 8,192 characters, and the same name does not already exist. Do not paste actual values into an inquiry. |
| You cannot check the value in a list or details | This is the masked-display specification. Check the actual value at the issuer or secure storage, and update it as a new value if needed. |
| A Page or Workflow fails after an update or deletion | Record the change target, run time, displayed error, and affected operation. Do not repeatedly guess at republication, rerunning, or permission changes; check the immediately preceding switch status and reference configuration with the responsible person. |

For an inquiry, include the organization name, Project name, Secret name, operation type, date and time, displayed message, affected Page, App, or Workflow, and checks already performed. Do not attach values, API keys, tokens, passwords, or masked strings from the screen.

## Next articles

- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md)
- [Manage Web Login Secrets](manage-web-login-secrets.md)
