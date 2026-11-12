# The Agent Does Not Reference the Data

This page provides troubleshooting steps for when you want the Agent to refer to materials or Objects, but the content of the materials does not appear in the response, outdated content appears, or the results differ depending on the user. This applies to Agent users and creators. First, distinguish between 'issues with making the Agent refer to materials as a Dataset' and 'issues with the Agent reading Objects or external services using tools'. Simply writing the name of the material in a conversation or instruction does not complete the Agent's reference settings, connection, or permissions.

## Related posts

- [Setting Tools, Skills, and Datasets for Agents](../build/configure-agent-tools-skills-datasets.md)
- [Prepare Dataset and make it available for reference ](../build/prepare-and-reference-datasets.md)
- [Test and improve Agent ](../build/test-and-improve-agents.md)
- [Object Specifications/Restrictions](../build/object-specs-and-limits.md)
- [Choose between Personal Connection, Shared Connection, and Secret ](../operations/choose-personal-shared-connections-and-secrets.md)
- [Gathering information needed for enquiries](collect-info-for-support.md)

## Before getting started

- Identify the relevant organisation, project, or agent name, the user who asked the question, and the document or object you want to check.
- Prepare one short question whose answer can be found in the original text of the document. Including the update date, headings, and unique terms makes it easier to check.
- We will also prepare one question that does not have a correct answer. This is because we do not consider information without a basis to be "referenced".
- Tools that involve external updates, sending emails, or updating records are not executed in this isolation process. First, check with read-only or test data.

> Dataset selection, saving Agent settings, completion of data import, and whether users can view the data are separate conditions. Do not re-register materials or expand the sharing scope if any of these conditions remain unconfirmed.

## Things to check first

- [ ] The correct Dataset is selected in the "Data Integration" section of the target Agent.
- [ ] After selecting "Apply" on the selection screen and reopening Agent, the settings remain.
- [ ] The "Status" of the target data in the Dataset is "Completed"
- [ ] The current version of the material is included, and there is no duplication with older versions or similar materials.
- [ ] The scope of sharing and permissions for users, Agents, Datasets, and necessary tools/connections are aligned.
- [ ] The agent's instructions ask you to refer to the materials and not to make any unfounded additions.

## Troubleshooting by symptom

| Symptoms/Messages Displayed | Main causes | Confirmation and Response | Detailed article |
|---|---|---|---|
| The agent gives an answer that is not in the materials or does not refer to the materials. | Dataset not selected, "Apply" missed, or Agent settings not confirmed as saved | The creator opens 'Data Integration' for the relevant Agent, selects only the relevant one in 'Select Dataset', and clicks 'Apply'. Reopen the settings screen and confirm that the Dataset name remains. | [Configuring Tools, Skills, and Datasets for Agents](../build/configure-agent-tools-skills-datasets.md) |
| "No dataset found" or "No dataset yet" | Different target project/organisation, search criteria, creation/sharing status, loading failure | Check the Organisation/Project and the search criteria. If 'The dataset could not be loaded', do not create it repeatedly and check the displayed content and permissions. | [Prepare the Dataset and make it available for reference ](../build/prepare-and-reference-datasets.md) |
| You can choose a dataset, but you won't use new materials. | Data is incomplete, files added from a PC have not been re-imported after updating, or synchronisation with an external source is incomplete. | In the Dataset data list, check whether the 'Status' of the target data is 'Completed'. Files uploaded from a PC are not eligible for automatic updates. For external sources, 'Reload Dataset' or check the update frequency, and retest with the same question after completion. | [Prepare the Dataset and make it available for reference ](../build/prepare-and-reference-datasets.md) |
| "You cannot edit the shared dataset because you do not have the necessary permissions." | You do not have permission to edit the shared Dataset | Check with the person responsible who has editing permissions regarding the original copy of the document, the scope of sharing, and who is responsible for updates. Do not change the sharing settings solely to dismiss the warning. Check separately whether users can view and whether they can edit. | [Prepare the Dataset and have it referenced ](../build/prepare-and-reference-datasets.md) |
| The creator can read it, but the user cannot, or vice versa. | The sharing scope of the Agent and Dataset does not match the access scope of the user | Check the 'Sharing Settings' on the Dataset side and the scope of users who use the Agent. Even when connecting a private Dataset to a Shared Agent, you must first decide whether the content can be viewed. | [Setting Tools, Skills, and Datasets for Agents](../build/configure-agent-tools-skills-datasets.md) |
| Unable to read Object records, or only able to read some of them | The tool for handling the object is not configured for the agent, or the agent or user is excluded by the record access rule | Check that the tools required for the 'Plugin' have been added and saved. Next, check the access rules for the Object record, the target record, and the assignment to the Agent. Before extending permissions, determine the required read scope. | [Configuring Tools, Skills, and Datasets for Agents](../build/configure-agent-tools-skills-datasets.md)] / [Object Specifications and Restrictions](../build/object-specs-and-limits.md)] |
| "Authentication required" remains, and external data cannot be read even after authentication | Connection not configured/expired, different account, insufficient permissions on the external service side, or connector configuration not completed after app installation/update | Adding tools and authentication are separate operations. Check the account to be connected, the connection status, the permissions for the relevant data, and the expiry date. For apps, check whether a shared or personal connector is required and whether the requirements have changed with the update. Do not paste authentication information into Agent instructions or enquiries. | [Choose between Personal Connection, Shared Connection, and Secret ](../operations/choose-personal-shared-connections-and-secrets.md) |

## 1. Check from the Dataset status

1. Open the relevant Dataset and verify that the name of the document, its source, the date it was updated, and its version are correct.
2. Check the 'Status' of the relevant data in the Data List. The data is being processed while it is marked as "Pending", "In Progress", or "Updating". 'Failed', 'No content' and 'Unreadable' may not be accessible as expected.
3. If a document has been updated, check the update method. Files added from a PC must be re-imported after they have been updated. If the source is an external website or external API, check the results of synchronisation and reloading.
4. Decide whether to keep only the current version or to keep the previous version. When the same content or older versions are duplicated, it becomes difficult to trace the basis for the answers.
5. Once it says "Complete", check the results by asking short questions that have answers in the source text.

## 2. Check the Agent's reference settings and instructions

1. The creator opens "Data Integration" in the Agent settings.
2. In "Select Dataset", tick the relevant Dataset and deselect any unnecessary Datasets.
3. Select "Apply" and check the "Data Integration" field of the Agent and the save status. Simply closing the selection screen will not apply the changes.
4. The instructions clearly state that you should refer to the relevant materials, answer 'Unconfirmed' if there is no evidence, and not consider materials that cannot be referred to as having been read.
5. Both the creator and the intended user should try the same short question and compare the original text of the material with the answer.

> Datasets are suitable for finding information related to questions and providing answers. For purposes such as checking all items in contracts and manuals without missing anything, simply selecting a Dataset does not mean that the entire text has been checked. Clearly specify the target files and prepare a separate procedure to verify the scope that could be retrieved and confirmed.

## Common misconceptions

### If you write the name of the document in the instructions, you do not need to select a Dataset.

The instructions convey the approach to be taken in the response. The operation to set a document as reference information for the Agent is performed in 'Data Integration', and the 'Apply' action and the result of saving are confirmed.

### If you put it in the Dataset, all materials will be used for answers every time.

The Dataset does not guarantee that the entire text will be read in order. Check the correspondence with the question, the status of the materials, the Agent's instructions, and the basis for the answer.

### By adding tools, you can always read objects and external services.

Adding tools, authentication, permissions on the external service side, and record access rules for objects are separate. Check the target and scope that need to be read before testing.

## Before contacting administrators or support

Record the following without including confidential information or the main body of the document. Agent name, Organisation/Project, Dataset or Object name, identifying information of the target data, user type, date and time of operation, question, expected and actual results, data status, confirmed 'Data Integration'/sharing scope/connection status, and display message. For how to summarise enquiry information, please refer to [](collect-info-for-support.md) Collecting Information Required for Enquiries].

## Next article to read

- [Setting Tools, Skills, and Datasets for Agents](../build/configure-agent-tools-skills-datasets.md)
- [Prepare Dataset and make it available for reference ](../build/prepare-and-reference-datasets.md)
- [Test and improve Agent ](../build/test-and-improve-agents.md)
- [Object Specifications/Restrictions](../build/object-specs-and-limits.md)
- [Gathering information needed for enquiries ](collect-info-for-support.md)
