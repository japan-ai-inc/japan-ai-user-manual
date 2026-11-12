# Agent Specifications and Limits

An Agent is a configuration unit that runs in conversations by combining instructions, models, reference information, and available capabilities. This article lists only the Agent-specific configuration conditions and limits that could be confirmed. For configuration steps, see [Create an Agent and Set Its Instructions and Models](create-agent-instructions-and-models.md). For tools, Skills, and Datasets, see [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md). For availability conditions across contracts, plans, and features, see [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md).

## Scope of this article

This article covers specifications directly related to creating and configuring Agents and chatting with them. It does not cover Dataset ingestion conditions, Storage capacity, or contractual user counts and credits. When the displayed choices or number of items that can be created varies by organization settings, permissions, or plan, it is not listed as a value common to all organizations.

## Basic configuration specifications

| Item | Specification | Notes |
|---|---|---|
| Agent name | A name is required. | This is the display name used to identify the Agent in lists. See "Limits and values" for the character limit. |
| Description, tags, and icon | You can configure a description, tags, and an icon. | Use the description to explain the purpose to users and tags for classification. Distinguish these from the Agent name. |
| Instructions | You can set the role, decision criteria, output format, and prohibited actions in "Instructions (System Prompt)." | The character limit for instructions could not currently be confirmed. Even when long instructions are necessary, check the input errors on the screen and the actual responses. |
| Enabled status | You can use and preview an "Enabled" Agent. | You cannot enable it while required fields are empty. You must save to verify configuration changes. |
| Prompt Templates | You can register "Prompt Templates." | This setting shows examples of use in conversations; it does not replace the instructions themselves. |
| Per-user settings | When you enable "Allow User Customization," you can add each user's custom instructions to the system prompt. | Shared Agent instructions and each user's custom instructions are separate settings. |

## Model and response specifications

| Item | Specification | Notes |
|---|---|---|
| Available Models | In "Available Models," you can limit the choices or select "Allow all models." | The models you can view and select depend on the models available to the organization and its settings. Writing a model name in the instructions does not make it available. |
| Default model | You can select "Set as default" from the added choices. | You cannot remove the model currently set as default. Set another choice as the default first. |
| Model capability support | Some models do not support file uploads, plugins, or image and video uploads. | Even if the Agent allows a capability, you cannot use it when the selected model does not support it. |
| Max Output Tokens | You can set the output amount for one response in "Max Output Tokens." | See "Limits and values" for the configurable range. The actual output amount is also affected by the selected model and input content. |
| Default Effort | You can set reasoning depth in "Default Effort." | If a user selects an effort level in chat, that selection takes priority. If no setting is specified, the model's standard setting is used. |
| Past Conversation Reference | In "Past Conversation Reference," you can set the number of past messages referenced in a response. | A larger number can make it easier to retain a longer context, but increases credit consumption. |

## Tools, Skills, Datasets, and sharing specifications

| Item | Specification | Notes |
|---|---|---|
| Plugins | In "Plugins," you can add tools and Skills for the Agent to use. | The choices you can add depend on availability in the organization, permissions, and connection and authentication status. A common maximum number that can be added to one Agent could not be confirmed. |
| Skill visibility | If you include a private Skill in an Agent published to the organization, other users cannot use that Skill. | Follow "Visibility change required" on the screen to align the Agent's intended users with the Skill's visibility. |
| Data Integration | In "Data Integration," you can select Datasets to use as reference information when responding. | Selecting a Dataset does not automatically complete document ingestion or grant users reference permission. When sharing an Agent that includes private Datasets, confirm that intended users can access them. |
| Agent access permissions | You can configure use and management permissions for the organization, departments, and members. | Agents created in a Project are handled on the premise that all Project members can use them. Check both Project and Agent permissions for individual editing and use availability. |
| Whether creation is available and number created | Agent creation and the number of Agents that can be created are controlled by the organization's plan and settings. | This article does not cover values by plan. Check the on-screen guidance and [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md). |
| Sharing organization Agents | Organization IAM settings and permissions may apply when sharing or changing the settings of an Agent used within the organization. | If an operation is not displayed or cannot be saved, check not only the people with whom you share but also permission to change Agent settings. |

## Specifications for chatting with Agents and attaching files

| Item | Specification | Notes |
|---|---|---|
| Attachable file formats | In "Allowed File Upload Types," you can limit the allowed categories. If not specified, supported formats are applied by default. | You cannot use a supported format when the selected model does not support file uploads. |
| Number of attachments | In "Max File Upload Count," you set the number that can be attached to one message. | The Agent setting takes priority, and the default is used when it is not set. See the next section for the configurable range and default value. |
| Attachment file size | For chat attachments, the pre-check limit differs for PDFs and other files. | Even when a file is within the size limit, it must meet conditions such as file format, Agent settings, and selected-model support. |
| File handling | When "Treat Files as Text" is enabled, supported files can be expanded as text. | When disabled, files are saved to the workspace. A setting to send PDFs directly to an LLM is meaningful only with supported models. |
| Plugin calls | In "Max Plugin Call Count," you can set the number of calls in one response. | Some plugin calls may not be displayed. |

## Limits and values

The following table lists only values and applicable conditions that could be confirmed in code. It does not list values for creation counts or availability that depend on plans or organization settings.

| Item | Limit or value | Conditions and notes |
|---|---|---|
| Agent name | 255 characters | The "Agent Name" when creating or updating. At least 1 character is required. |
| Max Output Tokens | 1,000–1,000,000 | The input range when explicitly setting "Max Output Tokens." The output limit when not set is not a fixed value because it depends on model-side conditions. |
| Past Conversation Reference | 0–500 exchanges; default: 50 exchanges | The screen uses the number of conversation exchanges. Internally, it is stored as 0–1,000 individual messages, with a default of 100 messages. |
| Temperature | 0–2 | The input range when explicitly setting "Temperature." |
| Max Plugin Call Count | 1–200 calls; default: 20 calls | The configurable range and default for one response. |
| Max File Upload Count | 1–20 files | The configurable range for "Max File Upload Count." The default in the settings screen is 5. However, if no value is saved on the Agent, normal chat attachment processing uses 10 files. Check the actual Agent setting. |
| Attached PDF size | 200 MiB or less | A pre-check for attaching PDFs in normal chats with an Agent. PDFs over 200 MiB cannot be attached. |
| Attached file size | 512 MiB or less | The server-side check for each file attached in normal chats with an Agent. PDFs must also meet the 200 MiB condition above. |

## Common misunderstandings

- **Adding a model to "Available Models" does not necessarily mean that all users can use the same model.** Check availability in the organization, prohibited-use settings, and model-supported capabilities.
- **Sharing an Agent does not automatically share private Skills or private Datasets with users.** Check whether users can access each resource.
- **Setting the attachment count to 20 does not necessarily mean you can send any 20 files.** The format, size of each file, and model support must also meet the conditions.
- **Increasing "Max Output Tokens" does not always produce a long response.** It is affected by model constraints, input content, conversation context, and an interrupted response.
- **Enabling an Agent and granting permissions to its intended users are not the same.** Check the enabled status, Agent access permissions, and permissions for Skills, Datasets, and connections separately.

## When you cannot confirm something

If choices do not appear, you cannot save or enable an Agent, or intended users cannot use it, check the following in order.

1. Check "Available Models," the Agent's "Enabled" status, access permissions, and the target Project.
2. Check the choices in "Plugins" and "Data Integration," and the visibility, ingestion status, and connection and authentication status of Skills and Datasets.
3. When attaching files, check "Allowed File Upload Types," attachment count, file format and size, and support for the selected model.
4. If guidance about creation capability or the number that can be created appears, contact the organization's administrator or the person responsible for checking contracts and plans. Include the displayed message, target organization and Project, Agent name, and operation date and time.
5. If you need evidence for a value, do not infer a value that is not in "Limits and values" in this article. Check with the product owner or support desk.

## Next articles

- [Create an Agent and Set Its Instructions and Models](create-agent-instructions-and-models.md)
- [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- [Test and Improve an Agent](test-and-improve-agents.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
