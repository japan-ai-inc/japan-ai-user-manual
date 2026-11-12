# How Studio Workspace, Project, App, and Marketplace Relate

In Studio, you can create features to fit your work or use features that have already been prepared. Studio Workspace, Project, Studio App, and Marketplace are the terms used in this context.

Think of **Studio Workspace as the area for building and management, Project as the unit that groups resources and members, App as the application provided to users, and Marketplace as the entry point for finding and installing Apps** to distinguish them.

This article explains how the four terms relate and which entry point to use for your goal. Instructions for creating or installing and detailed permission settings are covered in other articles.

## Distinguish the four terms

| Term | Role | Example of when to use it |
|---|---|---|
| **Studio Workspace** | An area for creating and managing Agents, Workflows, Pages, and more by Project | Create features that fit your work or review an existing configuration |
| **Project** | A unit that groups the resources and members required for work | Use sales support Agents, materials, screens, and more together as a team |
| **Studio App** | A business application provided for users. You can also use an App by installing a distributed App. | Use prepared screens and processes for day-to-day work |
| **Marketplace** | An entry point for finding available Apps and reviewing their content and installation conditions | Find an App that fits your work and consider installing it |

In this manual, "Studio Workspace" refers to the area for building and management. "Workspace" is not used to mean the same thing as a Project or organization.

## How Studio Workspace and Project relate

In Studio Workspace, you build and manage by Project.

For example, a Project called "Sales support" might combine an Object for deal information, an Agent that answers questions, and a Page that provides an input screen. This manual calls these components **resources**.

```text
Create and manage in Studio Workspace
  └─ Project: Sales support
       ├─ Members who use it
       └─ Resources required for work
            ├─ Agents and Workflows
            ├─ Objects, Datasets, and Storage
            └─ Pages and more
```

This diagram is a conceptual diagram for organizing what a Project handles. It does not show the placement of screen menus or mean that every resource is exclusive to a Project. How existing resources are added or reused varies by feature and setting.

**A Project is not a place only for creators.** People who do not build can also use permitted Agents, Pages, Workflows, and more in a shared Project. Joining a Project is different from being able to edit every feature.

## How Project and App relate

Projects and Apps are not completely separate mechanisms. One way to use Studio is to provide and distribute screens and processes configured in a Project as an App for users.

- **When focusing on a Project**: Consider which resources to use, who participates, and what scope to manage.
- **When focusing on an App**: Consider which work users can perform and which screens and processes they use.

For example, a creator prepares screens and processing in a sales support Project, then introduces it to users as a sales support App.

However, creating a Project does not by itself publish it to the Marketplace. To distribute it as an App, review what to distribute and the required connections, then complete the prescribed publishing and installation procedures. **Using it in a Project, distributing it as an App, and listing it on the Marketplace are separate stages.**

This also does not mean that users of a shared Project must turn it into an App or install it from the Marketplace.

## How App and Marketplace relate

Marketplace is where you find Apps and consider installing them. Its purpose differs from opening an installed App for daily work.

```text
Find an App in Marketplace
  → Review its content and installation conditions
  → A person with the required permissions installs it
  → Configure initial settings, external connections, and user settings as needed
  → Use the installed App
```

This flow is an overview for installing from Marketplace. Apps may also be provided within an organization without using Marketplace.

Also, **installation does not necessarily mean that every required setting is complete.** Apps that require a connection to an external service or user authentication may have some unavailable features until that setup is complete.

In the Apps list, distinguish between views for using installed Apps and views for finding Apps in Marketplace. Depending on administrative permissions, an option to manage Apps in the organization may also appear. Displayed items and available operations vary by organization settings, availability conditions, and permissions.

## Choose an entry point for your goal

| Your situation | What to check first | Detailed article |
|---|---|---|
| An administrator has instructed you to use an App | The target App and required initial setup or personal authentication | [Open an Installed App and Get Started](../use/open-installed-app.md) |
| A Project has been shared with you | The Project you join and the features you will use | [Get Started in a Shared Project](../use/start-with-shared-project.md) |
| You want to find an existing App for your work | The App's content and installation conditions in Marketplace | [Choose an App on the Marketplace](../use/choose-marketplace-app.md) |
| You want to create features for your work yourself | The Project to build in Studio Workspace | [Create a Project and Configure Basic Settings](../build/create-project-and-basic-settings.md) |
| You want to deliver what you created as an App | The distribution target, installing organization, and required settings | [Design App Distribution from a Project](../distribution/design-app-distribution-from-project.md) |

If you cannot find an App or Project, check the target name, participation status, and permission to use it with an administrator before creating a new one elsewhere.

## Next articles to read

- Learn the role of each component: [Roles of the Components](studio-components.md)
- Learn differences in permissions: [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- Return to the Studio overview: [What Is Studio?](studio-overview.md)
