# Prepare a Dataset and Make an Agent Reference It

A **Dataset** is a collection of data that imports business materials and external data for Agents and Workflows to reference through RAG search. This article explains creating a Dataset, importing data, synchronization and reload, configuring an Agent, and checking reference results.

- Configure a Dataset for an Agent: [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- Dataset specifications and limits: [Dataset Specifications and Limits](dataset-specs-and-limits.md)
- When materials are not reflected in answers: [The Agent Does Not Reference the Data](../troubleshooting/agent-not-using-data.md)

## What are Datasets and RAG references?

Data imported into a Dataset can be used by an Agent or Workflow to find information relevant to a question or processing task. "Data Integration" on the screen explains: **"RAG search of datasets is available from agents and workflows."**

A RAG reference is a mechanism that searches for information relevant to a question and uses it in an answer. Even when you configure a Dataset, the full text of all materials is not necessarily used in every answer. For a use case such as checking every provision of a particular contract without omissions, design a separate procedure that explicitly identifies the target file and verification scope.

| Purpose | Example suited to a Dataset | What to decide beforehand |
|---|---|---|
| Internal inquiries | Answering based on regulations, FAQs, and product materials | Authoritative materials, update owner, and target users |
| Business support | Finding procedures and past cases to create answer drafts | Information allowed in answers and reviewer |
| Workflow processing | Searching materials related to input and passing them to subsequent processing | Processing that uses search results and handling on failure |

A Dataset has a different purpose from ordinary file storage. Merely storing materials does not make them available for RAG reference; you must import them into a Dataset and confirm that processing is complete.

## Before you begin

- Confirm that the target organization, Project, and Dataset are correct.
- Confirm that you have the permissions required to create and edit Datasets and add data. Permissions and displayed operations may differ by organization settings.
- For the materials to import, decide the authoritative version, update owner, and contact for updates. Do not mix old versions or materials with the same content.
- When importing personal information, confidential information, or third-party information, check the sharing scope and internal rules.
- When configuring an Agent, prepare test questions and passages in the materials that support their answers.

> **Check the scope for sharing a Dataset separately from the scope for sharing an Agent.** If you share an Agent connected to a private Dataset, a warning appears that other users may also be able to view the Dataset content.

## 1. Create a Dataset

1. Open "Data Integration" in the target organization or Project.
2. In "Dataset List," select "Create dataset."
3. In "Dataset Name," enter a name that shows the purpose and scope of the materials. For example, use a name such as "HR Regulations_FY2026" that distinguishes the version and target.
4. If it appears, check "Sharing Settings." For an organization Dataset, select "Private (only you)" or "Shared (entire organization or selected departments)." For a Project, "Accessible only to creator" or "Accessible to all project members" may appear.
5. If it appears, check the departments permitted to reference it in "Department Settings" or "Shared Departments."
6. Select "Add."

If you cannot create the Dataset or select the button, you may lack permission. Check that you have permission to create and update Datasets in the organization or Project, and consult an administrator or Project administrator.

### How to decide the name and sharing scope

| Item | How to decide | Example |
|---|---|---|
| Name | Make the material's purpose, target, and version clear | "Sales Proposal Materials_Current Version" |
| Private | When only the creator prepares and verifies it | Checking materials before publishing |
| Shared | When users or creators reference it in common | Department-wide business procedure |
| Shared departments | When limiting it to departments that may reference it | Regulations for the HR department |

> When you share a Dataset, the materials become referenceable in the selected scope. Check not only whether the materials themselves may be shared, but also whether their content may be shown when someone asks through an Agent.

## 2. Import data

If the data-addition screen does not appear immediately after creation, open the target Dataset and select "Data Upload." When "No data yet" appears on the data details screen, you can also add data from "Data Upload."

1. Select "Data Upload."
2. Select the import source and specify the materials or acquisition target. Depending on the screen, you may be able to select files from a PC, web URLs, or configured external data integrations.
3. Check the file name, URL, target folder, and other required items for the selected import source.
4. Select the execution action such as "Upload."
5. Return to the Dataset data list and check the "Status" of the data you added.
6. Confirm that it is "Completed" before proceeding to reference testing.

Importing may take time to complete. Treat data marked "Pending," "In Progress," or "Updating" as being processed. Data marked "Failed," "No content," or "Unreadable" may not be referenced as expected as it is. Check the material, access to the acquisition source, and supported format, and review the data as needed.

> Repeatedly registering the same material makes it difficult to determine whether an old or new version should be referenced. For updates, decide beforehand whether to replace existing data or retain it as a version. For conditions such as format, capacity, and item count, see [Dataset Specifications and Limits](dataset-specs-and-limits.md).

### When importing PDFs

If "AI OCR for PDF" appears, it is a setting that detects and processes images in PDFs with an AI model. Whether it appears may vary by organization settings. You can change this setting after creating the Dataset. Credits are used for each PDF page.

If you disable AI OCR for PDF, future imports do not use the Dataset's PDF processing method for AI OCR. The actual extraction method for PDFs imported through external integrations may vary by connection source and file processing settings, so check import results. It does not affect data already imported, and changing the setting does not reprocess it. If you need to change existing extraction results, do not treat the setting change alone as complete; check the on-screen guidance and the person responsible for how to update the target data. You may not be able to change AI OCR for PDF for a Dataset configured with a dedicated processing method.

## 3. Configure synchronization and reload

For a Dataset that uses an external web or external API as its acquisition source, check settings as needed to import current data.

1. Select the target Dataset in "Dataset List."
2. Open "Advanced Settings."
3. Check "Update Frequency" and select the needed frequency.
4. Select "Update," and check the data-list status after the next update.

"Update Frequency" sets the frequency of automatic updates. **Files uploaded from a PC are not subject to automatic updates.** If you update materials on your PC, you must import the updated files into the Dataset.

In "Data Integration" within a Project, you can check Datasets linked to the Project from a list and import needed Datasets. Merely adding a Dataset outside the Project to reference candidates does not necessarily make that Dataset usable. After linking, check the list and reference permissions.

To update the acquisition source's content immediately, select "Reload dataset." Reloading retrieves current data from the source (web or external API) and reindexes it. You cannot reload while an update is in progress.

> Changes at the acquisition source do not necessarily appear in the Dataset immediately. After automatic updating or reloading, check the data-list status and reference results using representative questions. You may not be able to update if external-integration authentication has expired.

## 4. Make an Agent reference it

When Dataset processing is "Completed," configure it for the Agent.

1. Open settings for the target Agent.
2. Open "Data Integration."
3. In "Select Dataset," check the Dataset you prepared.
4. Confirm that no unnecessary Datasets are selected.
5. Select "Apply."
6. Confirm that the Dataset name appears in the Agent's "Data Integration" section and that Agent settings were saved.

Even when you can select multiple Datasets, we recommend initially limiting them to those necessary for the business operation. Selecting similar or old materials at the same time makes answer grounds harder to check. For detailed configuration steps, see [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md).

## 5. Test reference results

1. Open the target Agent.
2. Enter a question whose answer you can check in advance using information in the Dataset.
3. Check the answer content and, if it appears, "Answer Data References."
4. Compare it with the original materials and confirm that the needed materials are used and old materials are not mixed in.
5. If it differs from expectations, check the question, Agent instructions, Dataset selection, data-list status, and sharing scope in order.

| What to check | Example |
|---|---|
| Match with the question | Did the question include terms and conditions in the material? |
| Import status | Is the target data "Completed"? |
| Material correctness | Does it contain only current-version materials? |
| Agent settings | Is the target Dataset selected in "Data Integration"? |
| Sharing scope | Is the scope appropriate for the questioner and Agent user? |

If an answer differs from the materials or does not reference them, do not immediately conclude that the materials do not exist; check the Dataset status and settings. [The Agent Does Not Reference the Data](../troubleshooting/agent-not-using-data.md) covers troubleshooting.

## Checklist

- [ ] Decided the authoritative version, update owner, and update method for materials
- [ ] Can distinguish purpose, target, and version from the Dataset name
- [ ] Sharing settings and shared departments fit the materials' viewing scope
- [ ] The "Status" of imported data is "Completed"
- [ ] Confirmed that files added from a PC are not subject to automatic updates
- [ ] Checked the external-data update frequency or how to use "Reload dataset"
- [ ] Selected only needed Datasets in the Agent's "Data Integration" and selected "Apply"
- [ ] Compared answers and original materials using representative questions
- [ ] Checked the Dataset reference scope when sharing the Agent

## Next article to read

- [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- [Dataset Specifications and Limits](dataset-specs-and-limits.md)
- [The Agent Does Not Reference the Data](../troubleshooting/agent-not-using-data.md)
