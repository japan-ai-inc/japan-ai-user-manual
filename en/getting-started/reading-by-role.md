# How to Read This Manual by Role

You do not need to read this manual from beginning to end. Start with the articles you need based on whether you currently **use, build, manage, develop, or distribute**.

This article guides you to entry points by role and the order of articles to read next. Procedures for features and detailed permission explanations are covered in their specialized articles.

> A "role" here is a reader category for choosing articles. It does not represent a product role name or operation permission. One person may have multiple roles. The scope you can actually operate varies by settings and permissions for the target organization, Project, and App.

## Choose an entry point based on what you want to do now

| What you want to do now | Article to start with |
|---|---|
| Use an App you were directed to use | [Open an Installed App and Get Started](../use/open-installed-app.md) |
| Use features in a shared Project | [Get Started in a Shared Project](../use/start-with-shared-project.md) |
| Build a system that fits your work | [Choose Features by Goal](choose-features-by-goal.md) |
| Select an App and install it in your organization | [Choose an App on the Marketplace](../use/choose-marketplace-app.md) |
| Manage members, permissions, and connections | [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md) |
| Develop with code, SDKs, or APIs | [Choose Between the Web UI, AI Assistance, SDK, CLI, and API](../development/choose-dev-path.md) |
| Deliver an App you created to users | [Design App Distribution from a Project](../distribution/design-app-distribution-from-project.md) |
| Learn by operating Studio | [Your First Project and Agent](../labs/first-project-and-agent.md) |

## User: Move work forward with prepared features

People who use Apps or Projects do not need to learn building methods or API specifications first. Begin with the resource you were directed to use.

1. **Check the entry point**: For an App, read [Open an Installed App and Get Started](../use/open-installed-app.md); for a Project, read [Get Started in a Shared Project](../use/start-with-shared-project.md).
2. **Proceed to the article for the feature you use**: Choose [Move Your Work Forward from a Page](../use/work-with-pages.md) for screen operations, [Chat with an Agent and Check the Results](../use/chat-with-agents.md) for conversations, or [Run a Published Workflow](../use/run-published-workflow.md) for Workflow runs.
3. **Configure only when your own connection is required**: Check [Connect Your Own External Accounts](../operations/connect-your-external-accounts.md).

If data operations are part of your work, proceed to [View and Update Object Data](../use/view-and-update-object-records.md) or [Use Files in Storage](../use/use-storage-files.md).

**If you only use a shared Project, you do not need to create a new Project yourself.** If you cannot find the target, ask an administrator to check the provided name, participation status, and permission to use it before creating a new one.

## Creator: Build a system that fits the work

People combining Agents, Workflows, data, Pages, and more first decide the purpose and minimum configuration before feature-specific operations.

1. Use [Choose Features by Goal](choose-features-by-goal.md) to organize inputs, outputs, and required features.
2. If you need a new work area, proceed to [Create a Project and Configure Basic Settings](../build/create-project-and-basic-settings.md). When changing an existing Project, first check the target of the change and its scope of impact.
3. Proceed to the building article for the feature you selected. For example, use [Create an Agent and Set Its Instructions and Models](../build/create-agent-instructions-and-models.md) for an Agent or [Configure Workflow Nodes](../build/configure-workflow-nodes.md) for a Workflow.
4. Test before handing it to users. See [Test and Improve an Agent](../build/test-and-improve-agents.md) for Agents and [Test a Workflow Draft](../build/test-workflow-drafts.md) for Workflows.

When adding automatic execution, organize the starting method in [Choose an Automatic Execution Method](choose-execution-method.md). If the meaning of a feature is unclear, return to [Roles of the Components](studio-components.md).

## Installation, management, and operations owner: Maintain a usable state

People responsible for management first check **which organization, Project, and App they manage and to what scope**. Do not assume that the title administrator alone lets you operate every resource.

| Work you handle | Recommended reading order |
|---|---|
| Install a new App from Marketplace | [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md) → [Choose an App on the Marketplace](../use/choose-marketplace-app.md) → [Install an App from the Marketplace](../use/install-marketplace-app.md) → [Complete the Initial Setup of an App](../use/complete-app-setup.md) |
| Manage members and connections | [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md) → [Manage Project and App Members](../operations/manage-project-and-app-members.md) → [Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md) |
| Hand over scheduled runs | [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md) → [Configure Run Result Notifications](../operations/configure-run-result-notifications.md) |
| Respond to an installed App update | [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md) → [Handle Expired Connections and Owner Changes](../operations/handle-expired-connections-and-owner-changes.md), as needed |

Read installation and update checks separately from authentication and member settings. Also avoid confusing management of shared connections with work where a user connects their own external account.

## Developer: Implement screens and processing with code

People who handle Page code, SDKs, CLIs, and APIs should not start with a method list. First check the development method and execution environment assumptions.

1. Choose a development method in [Choose Between the Web UI, AI Assistance, SDK, CLI, and API](../development/choose-dev-path.md).
2. Check execution location and authentication in [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md).
3. Align the target environment in [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md).
4. Proceed to the feature you will implement among Page development, SDK guides, and CLI and API references.

If the person writing code differs from the person managing API keys, permissions, and connections, check the required setup with the management owner. Being able to read developer articles is different from having permission to operate target data and settings.

## Distribution owner: Deliver an App you created

People delivering a completed configuration as an App start with [Design App Distribution from a Project](../distribution/design-app-distribution-from-project.md). Then use [Constraints on Distribution Scope, Dependencies, and Authentication Settings](../distribution/distribution-scope-dependencies-auth.md) to check what to distribute and what to configure in the installing organization.

Next, proceed to the distribution method you handle.

- **Creator installs within the organization**: [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md)
- **Apply for Marketplace listing**: [Submit to the Marketplace](../distribution/submit-to-marketplace.md)

**Starting to use an App in the installing organization and delivering an App from the distribution side are separate jobs.** For the former, read mainly about using Studio and management and operations; for the latter, read mainly about App publishing and distribution.

## When you want to learn or need help

### When you want to learn by doing

If you are building for the first time, start with [Your First Project and Agent](../labs/first-project-and-agent.md). To try a full business application, choose [A Task Management App](../labs/task-management-app.md); to develop a Page with code, choose [Develop a Page with the SDK and CLI](../labs/develop-page-with-sdk-cli.md).

For running training or workshops, or finding tips by goal, see the following articles.

- To run training or a workshop: [Running a Workshop](../labs/run-workshop.md)
- To find tips by goal: [Index of Tips by Goal](../labs/tips-index-by-goal.md)

Hands-on materials teach the sequence of exercises. When expanding them to actual work, also check the settings, permissions, and limits of the features you use.

### When something does not work or is not displayed

Regardless of role, you can find an article based on the symptom.

- Not displayed or unable to join: [Something Is Not Visible or You Cannot Join](../troubleshooting/not-visible-cannot-join.md)
- Blocked by permissions or authentication: [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md)
- App installation or setup does not complete: [App Installation, Initial Setup, or Update Fails](../troubleshooting/app-install-setup-update-failures.md)
- Want to organize information needed for an inquiry: [Collect the Information Needed for an Inquiry](../troubleshooting/collect-info-for-support.md)

## When you have multiple roles

Choose articles not by job title, but by **the work you are doing now**. For example, if a business representative creates an Agent themselves, they read in the "Creator" order; after completion, they move to the "User" articles for daily use. When sharing with a team, check "Installation, management, and operations" as needed.

If you have not yet decided your role or target, review the overall picture in [What Is Studio?](studio-overview.md) and organize the resource you will use in [How Studio Workspace, Project, App, and Marketplace Relate](studio-concepts.md).

To proceed to checking accounts and basic screens, see the next article, [Prerequisites and Basic Screens](getting-started-and-navigation.md).
