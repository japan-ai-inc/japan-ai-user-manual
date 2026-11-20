# Design App Distribution from a Project

When delivering functionality created in a Project as an App, first decide whether to **install it for users in the same organization** or **offer it on the Marketplace**. Your choice changes the publishing destination, review process, installer, update flow, and contact point.

A Project is a unit for creation and management, while an App is a unit for delivery to users. Sharing a Project does not make it an App, and creating an App does not list it on the Marketplace. For terminology and relationships, see [How Studio Workspace, Project, App, and Marketplace Relate](../getting-started/studio-concepts.md).

## Related articles

- [How Studio Workspace, Project, App, and Marketplace Relate](../getting-started/studio-concepts.md)
- [Self-Install in Your Organization as the Creator](self-install-in-organization.md)
- [Submit to the Marketplace](submit-to-marketplace.md)

## Scope of this article

This article helps creators select a distribution route and design responsibility sharing from before publishing through end of operations. For screen operations to create an App, in-organization installation, and Marketplace submission, see the respective related articles.

## Who can do what

| Operation | Requirements and owner |
|---|---|
| In-organization self-install (prepare a version, install, update) | Editor or higher in the source Project |
| Submit to the Marketplace | All three of the following: (1) JAPAN AI has enabled Marketplace publishing for the organization, (2) the person is an organization administrator or has the "Studio App: Publish" permission, (3) the person is an Editor or higher in the target Project |
| Marketplace review, approval, listing, and general availability | JAPAN AI |
| Updates of Apps installed from the Marketplace | Updated automatically after a new generally available version is published. Customers cannot choose a version and there is no rollback. |

Condition (1) is enabled per organization by JAPAN AI and cannot be resolved by changing permissions within your organization. If "Marketplace" is not displayed, contact JAPAN AI; if (2) or (3) is missing, ask your organization administrator.

## Before you begin

Before deciding the distribution route, organize the following:

- **Users and installation destinations**: Decide whether the App is for the same organization only or also for customer organizations through the Marketplace.
- **App boundary**: Inventory which Pages, Agents, Workflows, Skills, automatic executions, Datasets, Custom Objects, custom roles, and Project Storage to include in the App, and what to configure at the installation destination. For Custom Objects, only the schema and access settings are distributed; records are not included. Only Storage files can be distributed as initial data. For configurations that cannot be published and the limits, see [Distribution Scope, Dependencies, and Authentication Constraints](distribution-scope-dependencies-auth.md).
- **Connections and confidential information**: Separate required connectors into requirements to include in the distribution package and connections to configure at the installation destination. Do not include Secret values, API keys, tokens, or the provider's connection state in the distribution package. Decide between shared and personal connectors, the person who configures them at the installation destination, and representative checks.
- **Operational responsibility**: Decide who creates release notes, provides installation guidance, checks updates, serves as the contact during incidents, and decides when support ends.

> Directly sharing the source Project with users and installing it as an App are different. Even when made into an App, provider-specific IDs, URLs, authentication information, and production data do not necessarily work as-is at the installation destination. For distributable Page design, see [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md).

## Choose a distribution route

In "App publishing," you can select "Within organization" or "Marketplace" as the publishing destination. The former is in-organization self-install; the latter is Marketplace distribution reviewed by JAPAN AI.

| Decision factor | In-organization self-install | Marketplace publishing |
|---|---|---|
| Intended installation destination | The same organization as the provider | Customer organizations served through the Marketplace |
| Publishing destination selection | "Within organization" | "Marketplace" |
| Review and listing | JAPAN AI review and Marketplace listing are not required | After submission, JAPAN AI manages review, delivery stage, and listing |
| Installation starting point | Select "Install in this organization" on the creator side | The customer checks content and conditions on the Marketplace and installs it |
| Installation destination | A dedicated Project separate from the provider. Users and access rights are managed individually | The customer organization's installation destination. Initial setup and user configuration may be required |
| Update approach | Prepare a new version and check the application result with "Update installed app" | When a new version is approved and made generally available, existing installations are updated automatically. Customers cannot choose a version and there is no rollback. Update completion and usage confirmation are separate |
| Best suited for | Internal validation, departmental rollouts, or when you manage installation destinations yourself | Offering to multiple customer organizations where reviewed and listed delivery is required |

Even when using both routes for the same App, separate the target organizations, installation owners, settings, and check records for each route. Completing an in-organization installation does not by itself complete Marketplace review or listing.

## Design the lifecycle

Distribution does not end when publishing. Make the following flow traceable by responsible person and check result.

```text
Release preparation
  → Review or in-organization installation
  → General availability
  → Customer/user installation
  → Update
  → End of support and retirement
```

| Stage | What the creator decides | Main owner and check |
|---|---|---|
| Release preparation | Finalize the App name, target resources, home screen, version, changes, and required settings at the installation destination. Check issues and warnings in "Pre-publish check." | Creator. Record changes in "Release notes." |
| In-organization installation | After "Check app resources," install the prepared version into the target organization. | Creator or organization installation owner. Check the installed version and user behavior. |
| Marketplace review | After "Submit for review," you cannot submit a new release from the same App until review is complete. | Submission requester and JAPAN AI. If rejected, check the reason, correct it, and resubmit. |
| General availability and listing | An approved version becomes "Generally available," and JAPAN AI manages Marketplace listing. | JAPAN AI. The creator compares the listing content with the delivered version. |
| Customer installation | After installation, the customer checks common settings, user access, and personal connections. | Customer installation owner and users. The creator provides required settings and the contact point. |
| Update | Separately check changes, setting changes, impact on access, and application results at the installation destination. There is no rollback, so fix problems in a new version. | Creator, customer operations owner, and users. An applied automatic update is not a substitute for business checks. |
| End of support | Decide the replacement version, end-of-use date, retention and handover, and inquiry contact. Individually check the impact of making a delivered version "Deprecated" and how existing installations are handled. | Creator, customer owner, and JAPAN AI. |

For Marketplace details, see [Submit to the Marketplace](submit-to-marketplace.md).

> "Approval & publication" and "Generally available" do not mean customer setup is complete, all updates are complete, or users can complete their work. Check connections, Secrets, access, and representative work under the installation destination's and users' conditions.

## Separate roles

| Role | Main responsibility |
|---|---|
| Creator | Clarifies the distribution target, dependencies, release notes, installation procedure, and the impact of changes and termination. |
| Submission requester | Checks content submitted to the Marketplace and tracks review, rejection, and approval statuses. This person is not necessarily the creator. |
| In-organization installation owner | Installs into the target organization, configures common settings and user access, and performs post-installation checks. |
| Customer installation and operations owner | Installs from the Marketplace, configures organization-specific connections, Secrets, and members, and verifies work after updates. |
| JAPAN AI | Manages review, approval, delivery stage, listing, and customer distribution for the Marketplace route. |
| User | Works with the granted App, completes their own setup where personal authentication is required, and reports usage issues. |

## Common misconceptions

| Misconception | Correct approach |
|---|---|
| Sharing a Project means App distribution is unnecessary | If you only build and use it collaboratively, you can choose Project sharing. First decide whether it must be delivered as a separate App for users. |
| In-organization installation also published it to the Marketplace | In-organization installation and Marketplace publishing are different destinations. The latter requires submission and JAPAN AI review and management. |
| Customers can use it immediately after approval | Installation, common settings, personal connections, access, and representative-work checks are still required. |
| Automatic updates require no confirmation | Applying an update is separate from settings and business results at the installation destination. Check based on changes and user conditions. |
| If an update causes problems, you can return to the previous version | There is no rollback. Publish a corrected new version. |
| Retiring an App immediately deletes customers' Apps | Future delivery and deletion of existing installations are handled separately. Decide the owner for impact, retention, and uninstallation. |

## If you are unsure

If you have not decided the publishing destination, required permissions, owner of installation-destination settings, or impact of updates and retirement, do not proceed with publishing, submission, or installation. Record the target App, source Project, intended organization, users, included resources, required connections, and the screen messages, then consult the organization administrator or JAPAN AI. Do not include Secret values, API keys, tokens, or production data in inquiries.

## Next article

- Distribute only within your organization: [Self-Install in Your Organization as the Creator](self-install-in-organization.md)
- Submit to the Marketplace: [Submit to the Marketplace](submit-to-marketplace.md)
- Design Page settings for distribution: [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md)
- Check post-installation settings: [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- Check post-update impact: [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md)
