# Something Is Not Visible or You Cannot Join

Use this procedure when a Project, App, or Page is not listed, you cannot open a shared link, or you cannot complete "Join." It is for users. Do not conclude from an item not being visible alone that it was deleted, is not installed for the organization, or that there is an outage. Check the **entry point, membership status, sharing scope, Page access mode, and organization-specific availability conditions** in that order.

## Related articles

- [Open an installed App and get started](../use/open-installed-app.md)
- [Get started with a shared Project](../use/start-with-shared-project.md)
- [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)

## Scope of this article

This article covers cases where you cannot find or open an item before or during use. Page source, build, and registration failures are covered in [Page Build, Start, or Display Fails](page-build-start-display-failures.md). If a menu is visible but an action is denied, see [Permission and Authentication Errors](permission-and-auth-errors.md).

## Before you begin

- [ ] I verified the organization, Project/App name, Page name, URL, and account specified in the guidance.
- [ ] If I searched the list, I cleared the search terms and checked whether tabs other than "Joined" are available.
- [ ] I have not treated a shared link and a grant of access to the item as the same thing.
- [ ] I recorded displayed text such as "Join," "Not assigned," or "Access denied," and the time it occurred.
- [ ] If the network I use has organization connection requirements, I checked those requirements.

> Do not send passwords, API keys, tokens, or Secret values to an administrator or support. When sharing a screen image, limit personal information and business data to what is necessary.

## Isolate the issue by symptom

| Symptom or displayed message | Likely cause | What to check or do | Detailed article |
|---|---|---|---|
| A Project, App, or Page is not in the list or cannot be found by searching | You are using another organization or account; search criteria; you have not joined the Project; or the resource sharing scope does not include you. If pre-install is `ON_OPEN`, installation may not have started yet. | Check the organization and account, clear the search terms, and review the list. Ask the source of the guidance for the official Project/App/Page name and link, and ask to be added as a user. For a pre-install target, ask an administrator to check the target tier, method, and installation progress. | [Open an installed App and get started](../use/open-installed-app.md), [Get started with a shared Project](../use/start-with-shared-project.md), [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| The name is visible but is not a link, "Not assigned" is shown, or "Join" is displayed | You must join the Project, or the guided role differs from your current status. | Check the Project to join, the explanation, and the role that will be granted. If "Would you like to join as an administrator?" appears during normal use, do not confirm unless you have been instructed to administer it; ask how to be added as a user. If there is no join action or it fails, ask an administrator. | [Get started with a shared Project](../use/start-with-shared-project.md) |
| Opening a URL displays "Access denied" or returns you to the list | A link is an entry point, and Project membership, resource sharing, or App availability conditions may be missing. | Before forwarding the URL again, ask an administrator to check the organization, Project membership, target resource sharing scope, and user registration. Creating another Project yourself does not inherit the source's permissions. | [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| Opening a Page URL asks you to sign in, or you still cannot open it after signing in | The Page is "Sign-in required," and Page access permission is also required. | Sign in with the correct account and check the Page name, Project name, and membership status. Knowing the URL alone does not allow access. Ask the Page administrator to check the user's Page access and sharing settings. | [Get started with a shared Project](../use/start-with-shared-project.md), [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| The Page sign-in method differs from what you expected, or an external URL cannot be used | The access mode is "Sign-in optional," "Anonymous only," or "External customer auth," or a URL and authentication for that mode are required. | Users must not change the access mode based on their own judgment; check the guided URL and authentication method. Administrators should check the target Page's "Access Settings" and, if external customer authentication is selected, its connection status. | [Register and publish Pages from the screen](../build/register-and-publish-pages.md) |
| A menu, Agent, Page, or Workflow that others have is not available to you | The home Page configuration, Project role, individual resource publishing/sharing scope, or App-specific conditions differ. | Ask the source of the guidance which feature name is needed. Organization membership or Project membership alone does not necessarily make every feature visible or runnable. Do not work around this by making the sharing scope public; ask an administrator for only the required users and actions. | [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| A feature name or setting such as "API Key Management" is not displayed | Organization availability, contract or usage conditions, permissions, or target Project/App configuration may be involved. | Do not conclude based on whether another screen exists or what another organization displays. Record the target organization, Project, and feature name and ask an administrator. The authoritative availability conditions differ by feature. | [Index of limits and availability conditions](../operations/limits-and-availability-index.md) |
| "Access restricted" is displayed | IP address restrictions are enabled for the organization and the current network is not allowed. | Reconnect to the internal network or VPN specified by the organization. If this does not resolve the issue, provide the "Current IP address" shown on the screen to the organization administrator and check the allow conditions. | [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |

## Order of checks

1. **Confirm the entry point.** Compare the guided URL, item name, account, and organization. Also check that you have not opened an old bookmark or a screen for another organization.
2. **Confirm membership status.** Check whether "Join" is required, whether the status is "Not assigned," and whether the button can be selected. Even if you are prompted for a role, do not select an administrator role that is unnecessary for use.
3. **Check the sharing scope.** Even after you join a Project, Agents, Pages, Workflows, Datasets, and other items may not be visible. Specify the required item name and ask the owner to check its sharing and publishing settings.
4. **For a Page, check the access mode.** "Sign-in required" requires sign-in and Page access permission. "Sign-in optional" and "Anonymous only" may use a shared URL, but users must not change the publishing scope. "External customer auth" can be used only when it is configured.
5. **Check availability conditions.** When the feature itself is not visible, organization configuration, contracts, or the target App's state may be involved in addition to permissions. Do not create an alternative Project or publishing setting based on your own judgment.

## Common misunderstandings

- **Receiving a link means you have already joined**: Not necessarily. A link is an entry point; it does not replace access permission, membership status, or sharing scope.
- **Organization members can use every Project and App**: Not necessarily. Project membership, App availability conditions, individual resource sharing scope, and connection settings may be separately required.
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
