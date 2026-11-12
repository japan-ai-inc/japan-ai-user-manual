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
| **Studio Workspace** | An area for creating and managing Agents, Workflows, Pages, and more by Project. | It is not another name for an independent organization or Project. If "Workspace" means an organization on another screen, check the context. |
| **Project** | A unit that groups the resources, members, and settings required for work. | It is not only a place for creators. Users may also use a shared Project. |
| **Resource** | A general term for components that are built or used for work, such as Agents, Workflows, Pages, and Objects. | Whether resources can be added to or reused in a Project varies by feature and setting. |
| **Studio App (App)** | A business application provided for users by combining screens, processing, and more. It may be distributed and installed for use. | It does not mean a Page alone. Creating a Project also does not complete distribution or listing. |
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

> **"Task" has different meanings depending on the context.** It may mean one record in an Object for work management, work requested from an Agent, or an execution setting managed on the "Tasks" page. Articles distinguish these by adding a qualifier, such as "business task" or "execution task."

## 4. Stages of starting, running, and providing

| Term | Meaning | Common point of confusion |
|---|---|---|
| **Trigger** | An event or condition that starts processing. | Consider separately the Workflow definition of what to process and when and in response to what it starts. |
| **Task** | A page that manages Workflows and the legacy Task Scheduler together. Use "In use" to check the status and results of created tasks, "Find" to create tasks from published Workflows, and "Build" to create and publish Workflows. | Open it from "Other features" and pin it to the sidebar as needed. Distinguish it from "tasks" in business data. |
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
| **Member** | A person who participates in an organization, Project, or similar resource. | Distinguish the general meaning of participant from the on-screen role name "Member." Joining does not by itself permit every operation. |
| **Permission / role / policy** | A permission allows viewing, running, updating, and more; a role groups permissions for assignment; a policy defines access conditions and allow or deny rules. | Do not determine the scope of operations on every resource based on a role name alone. Check the target resource and applied settings. |
| **IAM** | A mechanism that manages who can perform which operations on which resources. | Short for Identity and Access Management. Seeing a button on screen does not necessarily mean you have permission to run it. |
| **Authentication / authorization** | Authentication verifies the person or credentials in use; authorization decides whether that operation is permitted. | Being able to sign in does not necessarily mean you can view or update target data. |
| **External connection / connector** | A connection setting for using an external service from a tool or similar feature. | Check which service is used, by whose account, and within what scope. |
| **Personal connection / shared connection** | A connection used personally / a connection prepared for shared use in a Project or similar resource. | What is shared is the connection usage setting; it does not mean distributing credential values to everyone. Check the account used at run time. |
| **Credential** | Information used to verify a person or credentials when accessing external services and more. | API keys and access tokens. Expiration or revocation may require reauthentication. |
| **Secret** | A confidential value, such as an API key or password, managed without disclosure; also the managed item itself. | Distinguish which feature manages it, such as "Project Secrets." Saving a value and completing a tool connection are different. |

**Adding a Skill, installing an App, or registering a Secret does not by itself complete all required permissions and external connections.** Check separately the person who runs it, the connection used, and the resource operated.

## 6. Terms used in development and integration

| Term | Meaning | Notes |
|---|---|---|
| **Web UI** | A screen operated from a browser. | A method for configuring and using features through forms, lists, and more. |
| **API** | An interface for calling features from a program. | Check the authentication method, input, response, and limits for each endpoint. |
| **SDK** | A development library that makes it easier to use APIs and more from code. | Studio SDK and Web UI operations do not necessarily correspond one-to-one. Check the version and execution environment you use. |
| **CLI** | A mechanism operated by entering commands in a terminal. | Studio's CLI is `jai-studio`. It is a different entry point from writing SDK code. |
| **Project API key** | Credentials for API use related to a Project. | Treat it as a secret. Do not include it in code distributed to browsers, public materials, or logs. |
| **Hosted Page / PRS** | A Hosted Page is a Page hosted by Studio; PRS is short for Pages Rendering Service, which provides it. | The available APIs and authentication methods differ between the browser and server sides of a Page and external development environments. |
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
