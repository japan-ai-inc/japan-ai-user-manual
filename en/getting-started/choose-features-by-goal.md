# Choose Features by Goal

When you want to create something in Studio, do not begin by gathering every feature. First decide **"who inputs what and what they need to receive to move their work forward."** Combining only the features needed for that goal makes it easier to try, operate, and change.

This article is a decision guide for creators choosing an initial configuration. For an explanation of each feature itself, see [Roles of the Components](studio-components.md).

## 1. Check whether something you can use already exists

If there is already an App that fits your goal or a Project you can join, you may not need to create a new one.

- **An installed App exists**: Check the usage guide for that App and the scope you can use.
- **A shared Project exists**: Check the available Agents, Workflows, Pages, and more.
- **There is a candidate in Marketplace**: Check its features, installation conditions, required connections, and costs, then consult the person responsible for installation.
- **Existing resources cannot meet the goal**: Organize what is missing and consider creating something new or changing the existing configuration.

When reusing existing resources, do not confuse linking with copying. Before proceeding, check whether changing the original configuration would affect other users.

## 2. State the goal in one sentence

Writing it in the following form helps you organize the required processing and data.

> [User] uses [input information] to obtain [required result].

For example, make it specific as follows.

> An inquiry representative uses a customer's question and product manuals to obtain a draft response whose basis can be reviewed.

At this stage, you can use a configuration that first verifies whether a draft is useful, rather than extending it to "send responses automatically."

Also decide the following four points.

| What to check | Example |
|---|---|
| Input | Question text, target period, PDF, deal number |
| Output | Draft response, aggregation table, updated response status |
| Points for human review | Basis for the response, update target, before external sending |
| Condition for deciding it worked | It answers representative questions with a basis and indicates when the materials do not contain an answer. |

## 3. Choose the core processing

### Use an Agent as the core when the approach should vary by request

Consider this for work where the approach changes for each request, such as organizing information while interacting with users, answering questions, or drafting text.

Example: Consider the next action based on deal notes, or ask follow-up questions about materials.

Configure the required materials and tools, and try representative requests. Creating an Agent does not by itself make all internal data or external services available.

### Use a Workflow as the core when you want to define the order and branches

Consider this for work where you want to make the processing flow explicit, such as obtaining input, classification, conditional processing, and data registration.

Example: Classify an inquiry, extract required items based on the classification, and record them.

A Workflow is not only a feature for arranging Agents. You can incorporate AI models and Agents where needed. **Defining the order of processing is different from making AI output identical every time.**

Even when scheduled runs or event-based starts are necessary, first verify the processing from input through output, then choose how to start it. [Choose an Automatic Execution Method](choose-execution-method.md) compares starting methods.

### Add a Skill when you want to reuse a common approach repeatedly

Consider this when you repeatedly use the same procedure, check items, or output conventions in requests to an Agent.

Example: Summarize the items to include in reports or the order for checking during research.

You can first try it in Agent instructions and organize it as a Skill once the reusable scope is clear. A Skill is not a substitute for Workflow processing definitions or authentication settings for external services.

## 4. Choose how to handle required information

| How you want to handle information | Configuration to consider | Caution when choosing |
|---|---|---|
| Find explanations related to a question from many materials | Agent + Dataset | Material ingestion and reference settings are required. Search results alone do not guarantee coverage of the full text. |
| Register and update items such as deals and tasks by field | Object + required method of operation | Decide the fields, data types, and view and update scope. |
| Store attachments and generated deliverables as files | Storage + required read/write processing | Storing alone does not configure search or analysis processing. |
| Review or compare a specific document from beginning to end | Consider processing that reads the file directly | Check supported formats, reading scope, capacity, and more. Process long documents by chapter or page and manage unprocessed parts. |
| Reference or update an existing external service | A supported tool and connection, or integration design by a developer | Check supported operations, authentication, execution principal, and availability conditions. |

**Distinguish work that searches for part of materials from work that must review materials without omissions.**

For example, you can consider a Dataset for answering questions about regulations. In contrast, when comparing all clauses in a contract, do not rely only on retrieved passages; you need a design that checks whether the entire target document was processed. Even when passing a file directly, do not assume that the entire text was read reliably.

Also, when information already exists in a customer management system, copying all of it into an Object is not necessarily best. Decide first which is the source of truth and how updates are reflected.

## 5. Decide whether users need a screen

### When chat or an existing execution screen is enough

If users only need to enter questions or requests and receive results, first try the existing operating screen. You do not need to create a dedicated Page from the start.

### When input forms or lists are needed

Consider a Page when you want to standardize input fields, view multiple data items in a list, or run fixed operations from buttons.

Create display and operation mechanisms in a Page and connect it to the required data and processing. Providing a screen alone does not automatically complete data storage or AI processing. Custom screens and integrations may require Web development knowledge.

## 6. Narrow the initial configuration by type of work

The following are design examples. They do not represent Apps provided with all settings complete or combinations available in every environment.

| Goal | First configuration to try | Add when needed | First point to check |
|---|---|---|---|
| Use product manuals to create draft responses | Agent + Dataset | Skill; Object for inquiry records | Responses with a basis and handling questions the materials do not answer |
| Standardize inquiry classification and recording | Workflow + Object | A Page for representatives to review | Classification result, registered fields, and what happens to records on failure |
| Register and update team tasks | Object + available data operation screen | Page; Agent to organize status | Fields and permissions, and whether the people who need it can update status |
| Create reports repeatedly using the same conventions | Agent + required source data | Skill; Workflow for standardized parts; Storage for deliverables | Whether all required items are present and numbers and sources can be reviewed |

When in doubt, use **whether the configuration lets you enter one representative item and verify the required result** as the criterion, rather than the number of features.

## 7. Choose how to build after deciding the configuration

"What to combine" and "how to create and operate it" are separate decisions.

- **Web UI and AI building assistance**: Consider methods for reviewing settings on screen or conveying requirements to create a configuration.
- **SDK, CLI, and API**: When operation or integration from code is needed, check the supported scope with a developer.

Even for the same feature, creation, editing, execution, and reference may not all be possible through the same method. The scope also varies by SDK execution location and version, authentication method, and server-side support. Do not conclude that integration with any external production system is supported merely because you can call an API.

[Choose Between the Web UI, AI Assistance, SDK, CLI, and API](../development/choose-dev-path.md) covers specific choices.

## Checklist before you start building

- [ ] I can explain the goal as "who uses what to obtain what."
- [ ] I checked whether an existing App or Project can handle it.
- [ ] I decided the core processing and where the required information will be stored and referenced.
- [ ] I distinguished whether material search is sufficient or full-text review is required.
- [ ] I organized the users, access permissions, and places where external service authentication is needed.
- [ ] I decided where a person will review before data updates, deletion, or external sending.
- [ ] I prepared test input and a way to review the result.

You can add features unrelated to the goal later. First confirm that the necessary processing and data work, then add screens and automatic execution after operational issues become apparent.

## Next articles to read

| What you want to do next | Article |
|---|---|
| Review the roles of the features | [Roles of the Components](studio-components.md) |
| Choose between scheduled and event-based starting methods | [Choose an Automatic Execution Method](choose-execution-method.md) |
| Compare Web UI and code-based development | [Choose Between the Web UI, AI Assistance, SDK, CLI, and API](../development/choose-dev-path.md) |
| Learn by building | [Your First Project and Agent](../labs/first-project-and-agent.md) |
| Find tips by goal | [Index of Tips by Goal](../labs/tips-index-by-goal.md) |
