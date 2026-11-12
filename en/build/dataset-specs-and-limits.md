# Dataset Specifications and Limits

A Dataset is an information source that imports business materials and external data for Agents and Workflows to reference through **RAG search**. Confirm the supported formats, update methods, and characteristics during reference in advance to prevent issues such as old information being used after a material update or expected materials not appearing in an answer.

- To prepare and import data: [Prepare a Dataset and Make an Agent Reference It](prepare-and-reference-datasets.md)
- To check limits and availability conditions across features: [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
- If an Agent does not reference materials: [The Agent Does Not Reference the Data](../troubleshooting/agent-not-using-data.md)

## Scope of this article

This article explains the **conditions for importing** data into a Dataset, the **conditions for synchronizing and reloading** external data, and the **characteristics when referencing** it from Agents and Workflows. For procedures for creating, sharing, and uploading a Dataset, see [Prepare a Dataset and Make an Agent Reference It](prepare-and-reference-datasets.md). For Agent configuration, see [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md).

The displayed import sources, available actions, and features vary depending on your subscription plan, organization settings, permissions, and connected external services. For limits or contract-specific capacity not included in this article, prioritize the values displayed on screen and confirmation with your administrator or the JAPAN AI CS team.

## Import, synchronization, and reference specifications

| Item | Specification | Notes |
|---|---|---|
| Import source | In "Data Upload," you can select "File," "URL," or "External API." | Use External API when the applicable Data Integration has been configured and authenticated in your organization settings. |
| File formats | Standard files that you can select on the screen are TXT, CSV, DOCX, PPTX, PDF, XLSX, MBOX, HTML, HTM, Markdown (.md), MP3, MP4, WAV, WEBM, PNG, JPG, JPEG, WebP, HEIC, and HEIF. | Formats displayed and selectable on the screen may differ from content that can actually be processed. Encrypted, corrupted, or unreadable files may be marked "Unreadable." |
| URL | You can specify a URL to import web content. | The URL must be a valid HTTP or HTTPS URL. Import may not be possible depending on access restrictions, the status of the retrieval source, and page content. |
| External data | Import sources include Google Drive, Confluence, Dropbox, Box, Salesforce, OneDrive, SharePoint, Slack, Chatwork, Zendesk, kintone, Notion, and GENIEE SFA. | Availability depends on the organization's provision status, connection settings, and permissions of the connected account. Even when connected, data cannot be retrieved without permission to view the target data. |
| PDF extraction method | For PDFs, you can select "Text Detection" or "OCR," and may be able to select "AI OCR" depending on availability. | When you enable "AI OCR for PDF," the AI model detects and processes images in PDFs. Data that has already been imported is not automatically reprocessed after you disable it. |
| Update frequency | You can select the automatic update frequency under "Update Frequency." | **Files uploaded from a PC are not subject to automatic updates.** If you update a file, import the updated version. |
| Reload Dataset | "Reload Dataset" retrieves the latest data again from the source (web/external API) and reindexes it. | You cannot reload while an update is in progress. After reloading, also check the results using the "Status" in the data list and representative questions. |
| Processing status | The data list displays statuses such as "Pending," "In Progress," "Completed," "Updating," "Deleting," "Failed," "No content," and "Unreadable." | Before referencing data in an Agent or Workflow, confirm that the target data is "Completed." Data that is being processed, has failed, has no content, or is unreadable may not be referenced as expected. |
| Sharing and reference | You can share a Dataset privately, with the entire organization, or with selected departments. Within a Project, you may see options for the creator only or all Project members. | If you share an Agent connected to a private Dataset, other users may be able to view its contents. Check separately the sharing scope for materials and the scope in which it is acceptable to use them in answers through an Agent. |
| Datasets linked to a Project | You can link an organization-wide or global Dataset to a Project for use. | A linked Dataset is read-only within the Project. If you need to change its name, update frequency, reload it, delete it, or change its data content, address it in the source where it was created or consider unlinking it from the Project. |
| RAG search | Agents and Workflows can perform RAG searches against configured Datasets. | Even if you configure a Dataset, all materials are not necessarily used in every answer. Parts judged relevant to the question are searched and used. |

## Limits and limit values

Only values that could be confirmed are listed below. If the screen displays limits for item count, capacity, or credits, prioritize the displayed values that apply to your organization.

| Item | Limit or limit value | Conditions and notes |
|---|---:|---|
| File size | Up to 200 MB per 1 file | This is the limit when uploading files to a Dataset. Files of 200 MB or more cannot be uploaded. |
| URL-based import | 1 to 100 items per 1 request | This applies when importing URLs through the Dataset API. Each URL must be a valid HTTP or HTTPS URL. The code that was checked does not indicate fixed limits for crawl scope, number of levels, or total pages in the web UI. |
| Automatic update options | None, every 3 hours, every 6 hours, every 12 hours, every 24 hours, every 72 hours, every 7 days, every 15 days, every 30 days | These are update settings for web or external API sources. Files uploaded from a PC are not subject to automatic updates. These values do not guarantee the execution time or retrieval completion time. |
| AI OCR credits | Consumed for each 1 PDF page | This applies when using "AI OCR for PDF" or "AI OCR." Whether it is available and can run based on remaining balance and limits depends on the organization, plan, and credit settings. |

## Common misconceptions

### If I add it to a Dataset, the updated version is reflected automatically

Automatic updates apply to data from web or external API sources. They do not apply to files uploaded from a PC. When you replace a material, import the updated version and decide whether to keep or delete the old version. For external data, check the processing status and reference results after the update frequency has elapsed or after running "Reload Dataset."

### If it is shown as "Completed," it can answer every question correctly

"Completed" confirms that import processing has finished. It does not guarantee that the answer to a question is correct or up to date, or that required evidence is selected. Check that the materials are current, the Dataset selected for the Agent, the wording of the question, and the answer data references.

### A Dataset always reads the full text of long documents and checks everything

Dataset RAG is a **mechanism that searches for and references parts relevant to a question**. It is not suited to exhaustive coverage or full-text checks of long documents. For work such as checking every clause in a contract without omissions or listing every revision in regulations, specify the target files and review scope, and compare the retrieved content through a separate procedure. This guidance is for using the product safely.

### If an Agent is shared, everyone can use its Dataset in the same way

You must check Agent sharing separately from Dataset sharing and view availability. The scope that users can view, edit, and reference varies based on users, departments, Projects, authentication for connected services, and permissions. Test confidential materials with the minimum necessary sharing scope.

## If you cannot confirm it

Isolate the issue in the following order.

1. Confirm that the target "Dataset," data name, source, update date and time, and sharing scope are correct.
2. Check "Status" in the data list. If it is "Failed," "No content," or "Unreadable," review the source file, URL, permissions on the external service side, and connection status.
3. For web or external API updates, check "Update Frequency" and the last update status, and run "Reload Dataset" if necessary. Wait for updates in progress to finish.
4. In the Agent, confirm that only the required Datasets are configured in "Data Integration" and that the answer data references match the source materials.
5. If you have reached a limit, cannot upload, cannot select External API, or see a credit-related message, contact your organization administrator with the screen display, time of the action, and target Dataset. Do not include the body of confidential materials or authentication information in the contact.

For isolating problems where data is not referenced, see [The Agent Does Not Reference the Data](../troubleshooting/agent-not-using-data.md).

## Next articles

- [Prepare a Dataset and Make an Agent Reference It](prepare-and-reference-datasets.md)
- [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
- [The Agent Does Not Reference the Data](../troubleshooting/agent-not-using-data.md)
