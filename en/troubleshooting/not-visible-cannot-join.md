# Something Is Not Visible or You Cannot Join

Use this procedure when a Project, App, or Page is not listed, you cannot open a shared link, or you cannot complete "Join." It is for users. Do not conclude from an item not being visible alone that it was deleted, is not installed for the organization, or that there is an outage. Check the **entry point, membership status, sharing scope, Page access mode, and organization-specific availability conditions** in that order.

## Key points: entry points and joining rules

| Item | Rule |
|---|---|
| Entry point | In the left navigation, "STUDIO" > "Workspace" (Projects) or "Apps". The Apps tabs are "My Apps", "Marketplace", and "Organization Apps"; "Organization Apps" is shown only to administrators. |
| STUDIO is not shown | STUDIO is hidden when the screen width is under 600px. In organizations with app-only seats, "Workspace" is not shown to members who do not have a chat/speech seat. |
| Who can join an App by themselves | Organization administrators only. Joining by yourself always makes you an App administrator (a paid App uses one seat). |
| How regular members start using an App | Ask an App administrator to add you as a member, or open for the first time an App (free App) with publishing to all organization members (「組織の全メンバーに公開」) enabled; you then join as a member. |
| Pre-installed App waiting for installation | It is installed the first time someone selects "Open". It can also be deleted. |

## Related articles

- [Open an installed App and get started](../use/open-installed-app.md)
- [Get started with a shared Project](../use/start-with-shared-project.md)
- [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)

## Scope of this article

This article covers cases where you cannot find or open an item before or during use. Page source, build, and registration failures are covered in [Page Build, Start, or Display Fails](page-build-start-display-failures.md). If a menu is visible but an action is denied, see [Permission and Authentication Errors](permission-and-auth-errors.md).

## Before you begin

- [ ] I verified the organization, Project/App name, Page name, URL, and account specified in the guidance.
- [ ] If I searched the list, I cleared the search terms and checked whether there is an "All Projects" tab in addition to "Joined Projects".
- [ ] I have not treated a shared link and a grant of access to the item as the same thing.
- [ ] I recorded displayed text such as "Join," "Not assigned," or "Access denied," and the time it occurred.
- [ ] If the network I use has organization connection requirements, I checked those requirements.

> Do not send passwords, API keys, tokens, or Secret values to an administrator or support. When sharing a screen image, limit personal information and business data to what is necessary.

## Isolate the issue by symptom

| Symptom or displayed message | Likely cause | What to check or do | Detailed article |
|---|---|---|---|
| A Project, App, or Page is not in the list or cannot be found by searching | You are using another organization or account; search criteria; you have not joined the Project or App; or the resource sharing scope does not include you. | Check the organization and account, clear the search terms, and review the list. Ask the source of the guidance for the official Project/App/Page name and link, and ask to be added as a member. For an App, ask an App administrator to add you or to enable publishing to all organization members (「組織の全メンバーに公開」, free Apps). A pre-installed App waiting for installation is installed the first time you select "Open". | [Open an installed App and get started](../use/open-installed-app.md), [Get started with a shared Project](../use/start-with-shared-project.md), [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| The name is visible but is not a link, "Not assigned" is shown, or "Join" is displayed | You must join. Only organization administrators can join an App by themselves, and they always join as administrators. | If "Would you like to join as an administrator?" appears, confirming makes you an administrator (a paid App uses a seat). Unless you have been instructed to administer it, do not confirm; ask an App or Project administrator to add you as a member. If there is no join action or it fails, also ask an administrator. | [Get started with a shared Project](../use/start-with-shared-project.md) |
| Opening a URL displays "Access denied" or returns you to the list | A link is an entry point, and Project membership, resource sharing, or App availability conditions may be missing. | Before forwarding the URL again, ask an administrator to check the organization, Project membership, target resource sharing scope, and user registration. Creating another Project yourself does not inherit the source's permissions. | [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| "STUDIO" or "Workspace" is not shown in the left navigation | The screen width is under 600px, or your organization uses app-only seats and you do not have a chat/speech seat. | Widen the browser to 600px or more and reopen. If you need Workspace, ask an organization administrator to check your seat assignment. | [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| Opening an App shows a message containing "not published to everyone", "not available on your plan", "member limit", or "has been uninstalled" (quoted from the Japanese UI: 「全員には公開されていません」「プランでは利用できません」「メンバー数が上限」「アンインストールされています」) | Respectively: the App is not published to all organization members and you are not a member; the organization's plan does not include it; the App's member count has reached its limit; the App has been uninstalled. | Record the exact text and ask an App or organization administrator to check membership, plan, member count, and installation status. If a message says the first launch takes about 30 seconds, wait for the initial preparation to finish. | [Open an installed App and get started](../use/open-installed-app.md) |
| Opening a Page URL asks you to sign in, or you still cannot open it after signing in | The Page is "Sign-in required," and Page access permission is also required. | Sign in with the correct account and check the Page name, Project name, and membership status. Knowing the URL alone does not allow access. Ask the Page administrator to check the user's Page access and sharing settings. | [Get started with a shared Project](../use/start-with-shared-project.md), [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| The Page sign-in method differs from what you expected, or an external URL cannot be used | The access mode is "Sign-in optional," "Anonymous only," or "External customer auth," or a URL and authentication for that mode are required. | Users must not change the access mode based on their own judgment; check the guided URL and authentication method. Administrators should check the target Page's "Access Settings" and, if external customer authentication is selected, its connection status. | [Register and publish Pages from the screen](../build/register-and-publish-pages.md) |
| A menu, Agent, Page, or Workflow that others have is not available to you | The home Page configuration, Project role, individual resource publishing/sharing scope, or App-specific conditions differ. | Ask the source of the guidance which feature name is needed. Organization membership or Project membership alone does not necessarily make every feature visible or runnable. Do not work around this by making the sharing scope public; ask an administrator for only the required users and actions. | [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| A feature name or setting such as "API Key Management" is not displayed | Organization availability, contract or usage conditions, permissions, or target Project/App configuration may be involved. | Do not conclude based on whether another screen exists or what another organization displays. Record the target organization, Project, and feature name and ask an administrator. The authoritative availability conditions differ by feature. | [Index of limits and availability conditions](../operations/limits-and-availability-index.md) |
| "Access restricted" is displayed | IP address restrictions are enabled for the organization and the current network is not allowed. | Reconnect to the internal network or VPN specified by the organization. If this does not resolve the issue, provide the "Current IP address" shown on the screen to the organization administrator and check the allow conditions. | [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |

## Order of checks

1. **Confirm the entry point.** Compare the guided URL, item name, account, and organization. Also check that you have not opened an old bookmark or a screen for another organization.
2. **Confirm membership status.** Check whether "Join" is required, whether the status is "Not assigned," and whether the button can be selected. Only organization administrators can join an App by themselves, and they always join as administrators. If you do not administer it, ask an administrator to add you as a member.
3. **Check the sharing scope.** Even after you join a Project, Agents, Pages, Workflows, Datasets, and other items may not be visible. Specify the required item name and ask the owner to check its sharing and publishing settings.
4. **For a Page, check the access mode.** "Sign-in required" requires sign-in and Page access permission. "Sign-in optional" and "Anonymous only" use the guided public URL; users must not change the publishing scope. "External customer auth" can be used only when it is configured.
5. **Check availability conditions.** When the feature itself is not visible, organization configuration, contracts, or the target App's state may be involved in addition to permissions. Do not create an alternative Project or publishing setting based on your own judgment.

## Common misunderstandings

- **Receiving a link means you have already joined**: Not necessarily. A link is an entry point; it does not replace access permission, membership status, or sharing scope.
- **Organization members can use every Project and App**: Not necessarily. Project membership, App membership (added by an App administrator, or via publishing to all organization members), individual resource sharing scope, and connection settings are separately required.
- **Anonymous access allows any action**: No. The conditions for opening a Page and the ability to display data, process it, or connect externally in the Page must be checked separately.
- **A missing item always means insufficient permissions**: Not necessarily. Check organization-specific availability and feature settings as well.

## Before contacting an administrator or support

Collect the following so membership status, sharing scope, and availability conditions can be investigated separately.

- Organization name, Project name, App name, Page or feature name, and the URL you tried to open
- Account used and network used (the IP address shown on screen when an IP restriction is displayed)
- Time of occurrence, steps taken, displayed text, and what was or was not visible in the list
- Whether you attempted to join, the role requested, and what you have already checked

For detailed collection instructions, see [Collect the Information Needed for an Inquiry](collect-info-for-support.md). Do not attach authentication information or actual Secret values.

## Next articles

- [Open an installed App and get started](../use/open-installed-app.md)
- [Get started with a shared Project](../use/start-with-shared-project.md)
- [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)
- [Permission and Authentication Errors](permission-and-auth-errors.md)
