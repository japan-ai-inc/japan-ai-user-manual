# Glossary

This glossary groups terms that appear in Studio manuals and screens by purpose. Refer to it when you encounter an unfamiliar term or want to check the difference between similar features.

**The definitions here are intended to help you understand terms. Whether features are available and the scope of operations vary by availability conditions, organization settings, and permissions.** Check the article for each feature for procedures and limit values.

## Terminology in this manual

- Feature names are generally written as **Agent, Workflow, Skill, Page, Object, Dataset, and Storage**. Screens may display Japanese names or plural forms.
- The area for building and management is called **Studio Workspace**, distinguished from an organization and Project.
- Unless otherwise stated, **App** means Studio App.
- "User," "creator," and "operations owner" describe the role of a person reading an article. They do not represent product role names or granted permissions.

## 1. Places to use and units to manage

| Term | Meaning | Common point of confusion |
|---|---|---|
| **Organization** | A unit for managing users' affiliation, organization settings, availability conditions, and more. | Even with the same account, visible content and permissions may differ by organization used. |
| **Studio Workspace** | An area for creating and managing Agents, Workflows, Pages, and more by Project. Open it from "STUDIO" > "Workspace" in the left navigation. | It is not another name for an independent organization or Project. If "Workspace" means an organization on another screen, check the context. |
| **Project** | A unit that groups the resources, members, and settings required for work. | It is not only a place for creators. Users may also use a shared Project. |
| **Resource** | A general term for components that are built or used for work, such as Agents, Workflows, Pages, and Objects. | Whether resources can be added to or reused in a Project varies by feature and setting. |
| **Studio App (App)** | A business application provided for users by combining screens, processing, and more. It may be distributed and installed for use. | It does not mean a Page alone. Creating a Project also does not complete distribution or listing. |
| **Apps** | The App list opened from "STUDIO" > "Apps" in the left navigation, with the "My Apps", "Marketplace", and "Organization Apps" tabs. | The "Organization Apps" tab is shown only to organization administrators and others with permission to install Apps. |
| **Marketplace** | An entry point for finding available Apps and checking their content and installation conditions. | Its purpose differs from the place where you open installed Apps. Not every App is provided through Marketplace. |

[How Studio Workspace, Project, App, and Marketplace Relate](studio-concepts.md) explains these relationships in detail.

## 2. Features that make up AI, processing, and screens

| Term | Meaning | Examples and notes |
|---|---|---|
| **Agent** | A feature where AI answers or performs processing in response to instructions and requests. | Answers to inquiries and report drafts. Material search and external operations require feature-specific settings. |
| **Model / LLM** | A model that handles AI processing within Agents and Workflows, such as understanding and generating text. LLM is short for large language model. | An Agent is not the model itself; it is configured with instructions, tools to use, and more. |
| **Prompt** | An instruction or request sent to AI. | Instructions that define an Agent's role and requests entered by users each time have different purposes. |
| **Workflow** | A feature that defines and runs the order and conditions of processing by connecting nodes. | Combines AI processing, search, branches, data operations, and more. It is not limited to connecting multiple Agents. |
| **Node** | A unit of processing that makes up a Workflow. | AI generation, branches, tool calls, and more. A configuration may use output from an earlier process as input for the next one. |
| **Variable** | A mechanism for referencing input values and processing results by name. | Inquiry body and classification result. Check the value type and where it can be referenced before use. |
| **Skill** | A collection of procedures, knowledge, related materials, and more that an Agent reuses. | Rules for creating reports, for example. Depending on its content, it may include tools and scripts. |
| **Tool** | A feature that performs a specific operation, such as search, information retrieval, or data update. | While a Skill describes how to complete work, a Tool performs a specific operation. |
| **Plugin** | The name for extensions and management areas that handle tools, Skills, external connections, and more. | The "Plugins" screen distinguishes tools, Skills, and connectors. Do not treat them all as the same type of execution tool. |
| **Page** | A Web screen for displaying information and allowing input and operations. | Input forms and dashboards. It is a component that provides an App screen, and custom development may require technical knowledge. |
| **Studio Assistant** | An AI that builds Agents, Workflows, Pages, and more in a Project when you tell it your goals and conditions. It is the main way to build without writing code. | Requires the feature to be enabled for your organization and Editor or higher permission in the Project. It is not shown to users with the Member role. |
| **Canvas** | A document or other artifact created and edited in a conversation with an Agent. In the Japanese UI it is shown as 「キャンバス」. | It can be published with a specified scope (organization or public). |
| **Response comparison** | A feature that shows candidate responses from multiple models (or the same model with different effort) for the same input, so you can choose which candidate to adopt. | The feature must be enabled for your organization. When used from a Page, the viewer must be a signed-in JAPAN AI user; it is not available on Pages with anonymous access or external customer authentication. |

For how to use the features differently, see [Roles of the Components](studio-components.md).

## 3. Data, materials, and files

| Term | Meaning | Examples and notes |
|---|---|---|
| **Object (Custom Object)** | A feature that defines fields and manages business data as records. | Inquiries, deals, and tasks. This manual mainly uses "Object." |
| **Field** | A data field managed in an Object. | "Inquiry number," "assignee," and "response status." Define types such as strings and numbers. |
| **Record** | One item of data registered in an Object. | One inquiry is one record, with values such as assignee and response status. |
| **Schema** | A structural definition that defines data fields, types, and more. | In Object design, it determines "which values go in which fields." It is distinct from individual records. |
| **Dataset** | A group of materials collected for AI to search and reference needed information. | Product manuals and FAQs. Material ingestion and configuration on the referencing side are required. |
| **Knowledge / Knowledge Base** | Terms referring to knowledge AI references or materials for that knowledge. | Existing materials use these as feature names related to Datasets. Check the scope indicated by each screen or material; do not always treat them as exactly synonymous with Dataset. |
| **RAG** | A method where AI searches for related information and generates an answer using the results as a reference. | A Dataset groups materials; RAG is a method that uses search results in answers. It does not mean every material is read in full each time or guarantee answer completeness. |
| **Storage** | A feature for storing and managing files and folders. | PDFs, images, CSV files, and generated reports. Saving a file alone does not make it searchable in a Dataset. |

> **"Task" has different meanings depending on the context.** The most confusing case is conversations: **in the Japanese UI, the list of conversations with Agents in a Project is labeled 「タスク」 (task), while conversations in an App are labeled 「チャットルーム」 (chat room).** "Task" can also mean an execution setting managed in a Project's Task Scheduler, or one record in an Object for work management. Articles distinguish these by adding a qualifier, such as "business task."

## 4. Stages of starting, running, and providing

| Term | Meaning | Common point of confusion |
|---|---|---|
| **Trigger** | An event or condition that starts processing. | Consider separately the Workflow definition of what to process and when and in response to what it starts. A Workflow trigger runs as the person who first published that start node. |
| **Task Scheduler** | A feature for configuring run conditions, such as dates and recurrence, for Agents and Workflows in a Project, and checking the task list and history. | Distinguish it from Workflow configuration, publishing, and start-node event settings. Tasks run as their creator. It is also different from the conversation list labeled 「タスク」 in a Project. |
| **Automation (automatic run)** | A general term for mechanisms that start processing without a person, such as Workflow triggers and Task Scheduler tasks. | They are disabled automatically when the owner leaves the organization. Workflows use only shared connections, so automatic runs require a shared connection. |
| **Webhook** | A mechanism that notifies a specified receiver of an event through an HTTP request. | The receiving side requires settings such as authentication, input content, and the processing to start. |
| **Run / run history** | Running defined processing / a record of its status, results, and more. | Saving a definition and the result of processing actually running are different. Review history before deciding about failures or reruns. |
| **Draft / test** | Content being edited / an operation that checks how that content works. | Even a test may cause external sending or data updates. A draft does not necessarily have no impact. |
| **Publish** | Making a feature available or provided through the prescribed procedure. | Publishing a Workflow or Page and distributing or listing an App have different meanings and procedures. "Publish" does not mean "available to everyone on the internet." |
| **Install** | Bringing a distributed App into an installing organization for use. | It does not necessarily mean that required external connections and user settings are also complete. |
| **Version / revision** | An edition used to identify updated content. | Check what is versioned, such as an App, Page, or SDK. Items with the same name may differ in content or supported conditions. |

For how to choose a starting method, see [Choose an Automatic Execution Method](choose-execution-method.md).

## 5. Members, permissions, and external connections

| Term | Meaning | Caution |
|---|---|---|
| **Member** | A person who participates in an organization, Project, or similar resource. | Distinguish the general meaning of participant from the on-screen role name Member (「メンバー」 in the Japanese UI). |
| **Permission / role / policy** | A permission allows viewing, running, updating, and more; a role groups permissions for assignment; a policy defines access conditions and allow or deny rules. | Project roles are Admin, Editor, and Member. The Member role can still run Agents and Workflows and create many resources. Editors cannot manage members. There is no read-only role; read-only access requires a custom role. See [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md). |
| **IAM** | A mechanism that manages who can perform which operations on which resources. | Short for Identity and Access Management. Seeing a button on screen does not necessarily mean you have permission to run it. |
| **Authentication / authorization** | Authentication verifies the person or credentials in use; authorization decides whether that operation is permitted. | Being able to sign in does not necessarily mean you can view or update target data. |
| **External connection / connector** | A connection setting for using an external service from a tool or similar feature. | Check which service is used, by whose account, and within what scope. |
| **Personal / shared / organization-wide connection** | Connections have three layers: a personal connection uses your own account, a shared (Project) connection is used for runs by everyone in the Project, and an organization-wide connection is used across the organization. | Agents in a Project use "the runner's personal connection → the Project's shared connection → the organization-wide connection" in that order; the organization-wide connection serves as an automatic fallback. Workflows use only shared connections. Sharing a connection does not distribute credential values to everyone. |
| **Credential** | Information used to verify a person or credentials when accessing external services and more. | API keys and access tokens. Expiration or revocation may require reauthentication. |
| **Secret** | A confidential value, such as an API key or password, managed without disclosure; also the managed item itself. | Distinguish which feature manages it, such as "Project Secrets." Project Secrets can be read only by Page server environment variables and API call nodes, not by Agents. |
| **Scope** | The range of operations allowed for a Project API key, such as `object:read` or `object:delete`. | Scopes cannot be changed after the key is created. Check the required scopes in each API article. |

**Adding a Skill, installing an App, or registering a Secret does not by itself complete all required permissions and external connections.** Check separately the person who runs it, the connection used, and the resource operated.

## 6. Terms used in development and integration

| Term | Meaning | Notes |
|---|---|---|
| **Web UI** | A screen operated from a browser. | A method for configuring and using features through forms, lists, and more. |
| **API** | An interface for calling features from a program. | Check the authentication method, input, response, and limits for each endpoint. |
| **SDK** | A development library that makes it easier to use APIs and more from code. | Studio SDK and Web UI operations do not necessarily correspond one-to-one. Check the version and execution environment you use. |
| **CLI** | A mechanism operated by entering commands in a terminal. | Studio's CLI is `jai-studio`. It is a different entry point from writing SDK code. |
| **Project API key** | Credentials for API use related to a Project. | A key is valid only while its creator is an active member of the Project. Treat it as a secret. Do not include it in code distributed to browsers, public materials, or logs. |
| **Hosted Page / Page runtime environment** | A Hosted Page is a Page hosted by Studio. The Page runtime environment (PRS) runs that Page and relays API calls from the browser. | The available APIs and authentication methods differ between the browser and server sides of a Page and external development environments. |
| **Deploy** | Place created code or deliverables in an environment where they run. | Deployment, granting users permissions, and listing on Marketplace are not the same operation. |

> **Distinguish between Public APIs and APIs for Hosted Pages by endpoint and authentication.** Even if both descriptions handle the same Object or Storage, you cannot necessarily apply one directly to another execution environment. [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md) covers details.

## Next articles to read

| What you want to check | Article |
|---|---|
| Relationships between Studio Workspace, Project, and App | [How Studio Workspace, Project, App, and Marketplace Relate](studio-concepts.md) |
| Roles and combinations of components | [Roles of the Components](studio-components.md) |
| Preparation and screens before starting | [Prerequisites and Basic Screens](getting-started-and-navigation.md) |
| Organization, Project, and App permissions | [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md) |
| Choosing between connections and confidential values | [Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md) |
| Execution environment and authentication during development | [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md) |
