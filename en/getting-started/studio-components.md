# Roles of the Components

In Studio, you combine AI-powered processing, business data, and user-facing screens to create systems that fit your work.

This article explains the roles of Agents, Workflows, Skills, Pages, Objects, Datasets, and Storage, and how the features relate to one another. It is intended to help you understand what each feature is responsible for, not to provide building instructions.

If you want to first check how they relate to Projects and Apps, see [How Studio Workspace, Project, App, and Marketplace Relate](studio-concepts.md).

## The seven features to understand first

| Feature | Main role | Work example |
|---|---|---|
| **Agent** | AI provides answers or performs processing in response to instructions and requests | Answer inquiries, organize information, draft text |
| **Workflow** | Runs a preassembled flow of processing | Retrieve information → classify → branch by condition → register data |
| **Skill** | Groups procedures, knowledge, related materials, and more that an Agent reuses | Rules for creating reports, research approach |
| **Page** | Provides Web screens where users view information and perform operations | Input forms, list screens, dashboards |
| **Object** | Defines fields and manages business data as records | Inquiry number, assignee, response status |
| **Dataset** | Groups materials that AI searches and references | Internal regulations, product manuals, FAQs |
| **Storage** | Stores and manages files and folders | Attached PDFs, images, CSV files, generated reports |

These categories help you understand the differences in roles. You do not need to use every feature. Displayed features and available operations vary by availability conditions, organization settings, and permissions.

## Handle processing: Agent, Workflow, and Skill

### Agent: Receive requests and provide answers or processing

Configure an Agent with the work it handles and its response policy. It receives user requests to create text or organize reference information.

With the required settings, you can configure it to search Datasets or use tools to operate data and external services. Creating an Agent does not by itself make every material or service available.

For example, assign it the role of creating draft responses based on inquiry content and product manuals.

### Workflow: Build a flow of processing

In a Workflow, connect units of processing called "nodes" and configure the flow from input through results.

In addition to connecting multiple Agents, you can combine AI model processing, material searches, branches, loops, data operations, and more.

For example: "receive an inquiry → classify it → route processing by classification → register a response record." You can also call an Agent only where needed.

> A Workflow defines a flow of processing. If it includes AI generation or responses from external services, it does not guarantee the same result every time.

### Skill: Reuse an Agent's way of working

A Skill groups procedures, cautions, reference materials, and more for completing work. Depending on its contents, it may combine tools and scripts.

For example, make approaches reusable such as "include a conclusion, reason, and sources in responses" or "check these items in reports."

**A Skill is neither an Agent itself nor a processing definition that connects Workflow nodes.** Using a Skill does not grant authentication for external services or data access permissions.

### How the three relate

- An **Agent** provides answers or performs processing in response to requests.
- A **Workflow** defines which processing runs in which order and under which conditions.
- A **Skill** provides reusable procedures and knowledge for an Agent to complete work.

You can use them separately or in combination. For example, one possible configuration has a Workflow request a draft response from an Agent that uses a Skill. The combinations you can actually use require configuration and verification.

## Handle information: Object, Dataset, and Storage

All three handle information, but their primary uses differ.

### Object: Manage data with fields

In an Object, define the fields required for work and manage each item of data as a record.

For example, register an inquiry number, content, assignee, and response status in an "Inquiry" Object. Use it for work that searches tabular data or updates status.

In addition to fields shown on screen, design the data types and the scope that can be viewed and updated.

### Dataset: Search materials that support answers and processing

A Dataset is a group of materials that AI searches and references for needed information. For example, import product manuals and FAQs so that an Agent can find information related to an inquiry.

You must import the materials and configure them for reference from an Agent or other feature. When materials are updated, check their ingestion and synchronization status.

Searching through a Dataset does not mean reading every registered material in full each time. For work that requires full-text comparison or extraction without omissions, separately design the method for reading materials as well.

### Storage: Store files

Storage is where files such as PDFs, images, and CSV files are kept. You can organize attachments received in work and reports generated by processing.

To use stored files from an Agent, Workflow, Page, or other feature, configure the required reading or integration.

**Placing a file in Storage and importing a material into a Dataset to make it searchable are not the same operation.** Do not assume that saving a file automatically adds it as evidence for every Agent response.

### Example of using them differently in the same work

| Information handled | Feature mainly used | How it is managed |
|---|---|---|
| Inquiry assignee and response status | Object | Register and update as records with fields |
| Product manuals referenced when answering | Dataset | Import so related explanations can be searched |
| Screen captures attached to an inquiry | Storage | Save as files and reference from required processing |

The same source material may be used for multiple purposes. In that case, decide the role and update method for each storage destination. Data in different locations does not necessarily stay in the same state automatically.

## Create an entry point for users: Page

A Page is a Web screen for displaying information and allowing users to enter information and perform operations. For example, you can provide an inquiry registration form, a list of response statuses, or a dashboard of aggregation results.

By implementing the required integration, a Page can display and update Object data and call processing by Agents or Workflows.

**A Page does not mean the entire App.** It is a component that handles a screen, and you separately combine the data to display and processing to call. You can also use a configuration where chat or another feature is the entry point without creating a Page.

Some work can proceed through screen operations or requests to AI, but developing custom screens or processing may require Web development knowledge.

## Example combination: Support inquiry handling

The following is a configuration example showing the role of each feature. It does not represent a standard App with settings already complete.

1. Provide an inquiry input screen and a list of response statuses in a **Page**.
2. Store inquiry content, assignee, and status in an **Object**.
3. If there are attachments, save them in **Storage**.
4. Prepare product manuals and FAQs that support answers in a **Dataset**.
5. An **Agent** references relevant materials and creates a draft response. A **Skill** groups the procedures and cautions for creating responses.
6. Build processing you want to standardize in a **Workflow**.
7. The representative reviews the basis and content of the response before using it for work.

You do not need this entire configuration from the beginning. You can first create a mechanism where an Agent references materials through a Dataset, then add management data and screens when needed.

## What to check before connecting features

Merely placing features does not make them available to one another. Configure and check the following as well:

- **Reference and connection destinations**: Which materials, data, files, and processing to use.
- **Permissions**: Who can view, run, register, and update.
- **Credentials**: Which account operates external services.
- **Verification**: Whether expected results are produced and unintended updates or sending do not occur.

Operating external services requires supported tools and connection settings. SDKs and APIs are ways to use features from code; they do not eliminate the need for these permissions or authentication.

## Next articles to read

| What you want to learn | Article |
|---|---|
| Check the relationships between Projects and Apps | [How Studio Workspace, Project, App, and Marketplace Relate](studio-concepts.md) |
| Choose features that fit your goal | [Choose Features by Goal](choose-features-by-goal.md) |
| Start creating an Agent | [Create an Agent and Set Its Instructions and Models](../build/create-agent-instructions-and-models.md) |
| Import materials and have them referenced | [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md) |
