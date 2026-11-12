# Manage Web Login Secrets

Web login secrets store the URL, login ID, and password used to sign in to websites. This article explains **how administrators register, update, and delete web login secrets** and what to check to manage them safely.

They are not for saving API keys or tokens in a Project. For that purpose, see [Register, Update, and Delete Project Secrets](manage-project-secrets.md). For guidance on choosing a credential type, see [Choose Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md).

## Related articles

- [Choose Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md)
- [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
- [Configure Shared Connections for Projects and Apps](configure-shared-connections.md)

## Before you begin

Web login secrets manage information used to sign in to websites. They are not connection settings or a place to store passwords that people share directly.

- Confirm the correct organization, Project, and target Agent or App. What is displayed varies by organization settings, target Project, App configuration, and the user's role and permissions.
- Decide on the target site, purpose of use, business account to sign in with, administrator, and contact for password changes. Do not use a personal-only account for shared business work.
- Check the website terms and your organization's operations to confirm that automated actions and use by multiple people are allowed. If MFA, IP restrictions, device restrictions, image verification, or similar controls apply, registration does not necessarily mean sign-in will succeed.
- Confirm that you have permission to create, update, and delete Secrets and permission to operate the target Project. Even if you can open the screen, you may be unable to register or change items without permission for the individual action.

> Do not write passwords, one-time codes, recovery codes, cookies, or access tokens in Secret names, descriptions, Agent instructions, chats, tickets, or screenshots. Do not send actual values or masked strings when making inquiries.

## Differences from Project Secrets

Both are handled in "Secret Management," but their purpose and use differ. Do not use one in place of the other even if their names are similar.

| Comparison | Web login secrets | Project Secrets |
|---|---|---|
| Primary purpose | Information for signing in to websites | Secret values such as API keys and tokens used in a Project |
| Information registered | URL of the target website, login ID, and password | Name and value |
| Used by | Configured uses such as tasks of Agents that perform web login | Features and Apps in the Project configured to reference the Secret |
| Where configured | "Web login secrets" in "Secret Management" | "Project secrets" in "Secret Management" |
| App configuration | Confirm it is for signing in to websites. Also check the guidance for the target feature to confirm availability. | If required, select and link it in the App's "Required App Secrets." |

> Do not register an API key as a web login secret merely because you use an API key. Conversely, avoid deciding that a website ID and password registered as a Project Secret can be used for browser login. If the purpose is unclear, check the guidance for the target App or feature and the administrator before registering it.

## 1. Open "Web login secrets"

1. In JAPAN AI, confirm the target organization and Project.
2. Open **"Secret Management"** in Settings. While configuring an App, you may open it from the target App's settings.
3. Select **"Web login secrets."** If the Project Secrets screen opens, check that you have not selected the wrong tab or type.
4. In the list, check **"Secret Name,"** **"Created by,"** and **"Created at."** If necessary, narrow the list with **"Search by secret name."**

The list displays registration information; it is not a screen for reading passwords. If there are no web login secrets, "No web login secrets" is displayed.

## 2. Register a new web login secret

1. Select **"Create Secret."**
2. In **"Secret Name,"** enter a name that identifies the target site and business purpose. Do not include the login ID or password in the name.
3. Check the displayed sharing scope. When registering within a Project, make sure you do not select the wrong Project or the wrong Agent or App that uses this information. On screens where you can choose personal or organization-wide use, choose after checking whose work needs the information and whether the external site's account may be shared.
4. Enter the login URL in **"WEB URL."** If you use multiple URLs, select **"Add"** and register only the URLs you need. Specify an appropriate URL that identifies the target site, rather than an arbitrary post-login page.
5. In **"Login ID"** and **"Password,"** enter information for an approved business account. Even if you can use a description field, do not enter actual password values; enter only the purpose, administrator, and contact for changes.
6. Recheck the target organization and Project, site, sharing scope, entered URLs, and purpose of use, then select **"Create."**
7. Confirm that the Secret name appears in the list. If possible, run a representative task for the target Agent using permitted test data and scope.

> An Agent being able to sign in to a website does not mean actions on that site are approved. For tasks that include sending, updating, downloading, deleting, or similar actions, first confirm the target and outcome with a small amount of approved data before expanding use.

## 3. Update URLs, login IDs, and passwords

Update promptly when a password changes, the target site URL changes, an account is handed over, or a compromise is suspected. Before updating, confirm that the new information is valid on the external site.

1. From **"Actions"** in the target row, select **"Edit."**
2. Check or change **"WEB URL,"** **"Login ID,"** **"Password,"** and, as needed, **"Secret Name"** and the description.
3. The password on the edit screen may be masked instead of displaying its actual value. If you are not changing the password, do not copy or re-enter that display as the actual value. Enter the new actual value only when changing it.
4. Confirm the target site, Agents and Apps in use, cutover time, and contact for failures, then select **"Update."**
5. After updating, run a representative task within the permitted scope and confirm the sign-in destination and processing result. Because the time when changes take effect and the effect on running tasks need to be confirmed depending on the situation, do not assume they are immediately reflected in all runs.

If you invalidate the password first on the external site, dependent tasks may fail. For ordinary changes that do not require emergency invalidation, register and test the new information before invalidating the old information.

## 4. Delete a secret that is no longer needed

Deletion cannot be undone. Before retiring an account, switching to another Secret, or stopping use, first check dependent Agents, Apps, and tasks.

1. In the list, check the target **"Secret Name,"** creator, and creation date.
2. From **"Actions,"** select **"Delete."**
3. On the confirmation screen, read the target name and check the notice that the secret cannot be restored.
4. Confirm that replacement settings, stopping or switching dependent tasks, and communication to stakeholders are complete.
5. Confirm **"Delete"** and check that the target has disappeared from the list.

> Even a Secret that appears unnecessary may be referenced by scheduled runs or infrequently used Agents. If sign-in failures occur after deletion, do not assume that you can restore the deleted actual value. Instead, issue new information on the external site and configure what is needed.

## Management checklist

- [ ] Confirmed that automated website actions and account sharing are allowed
- [ ] Selected an approved business account, not a personal account
- [ ] Distinguished web login secrets from Project Secrets by purpose
- [ ] Confirmed the target organization, Project, Agent, App, URL, and sharing scope
- [ ] Did not write actual values in Secret names, descriptions, or inquiries
- [ ] Confirmed the effects of MFA, IP restrictions, device restrictions, and password changes
- [ ] Confirmed the sign-in destination and result with a permitted representative task before and after updating
- [ ] Recorded only the administrator, purpose, dependent features, and date of change

## If something does not work

| Situation | What to check and do |
|---|---|
| Cannot find "Secret Management" or "Web login secrets" | Check that you opened the correct organization, Project, and App, then ask the administrator about feature display conditions and permissions. Do not substitute a Project Secret. |
| Cannot select create, edit, or delete | Check permissions for the individual action and a role that can operate the target Project. Do not grant yourself broader permissions than needed; ask the permission administrator. |
| Cannot save | Check required fields for Secret name, WEB URL, login ID, and password. Also check for unsupported characters or duplicate names in the Secret name. Do not paste actual values into an inquiry. |
| Sign-in still fails after registration | Check the URL, login ID, password, external-site account status, MFA, IP restrictions, device restrictions, and the target Agent's use settings. Avoid repeated attempts that could lock the account. |
| Tasks fail after updating or deleting | Record the operation time, target Agent, App, and task, displayed message, changes, and external-site status. Do not recreate a Secret based on guesswork; check cutover status and dependent settings with the responsible person. |

For inquiries, include the organization name, Project and App names, Secret name, target site, action type, date and time, displayed message, affected task, and what you have already checked. Do not include login IDs, passwords, MFA codes, recovery codes, cookies, tokens, or masked strings.

## Next articles

- Choose a credential type: [Choose Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md)
- Manage API keys and tokens in a Project: [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
- Configure shared connections: [Configure Shared Connections for Projects and Apps](configure-shared-connections.md)
