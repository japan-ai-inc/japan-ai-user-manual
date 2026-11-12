# Run a Published Workflow

This article explains how to enter the information required for a Workflow prepared for business use and check the run results. A Workflow is a feature that carries out predefined processing in sequence or according to conditions.

**Being able to start a run and completing the intended business task are different things.** Check not only the status, but also the output and results of processing such as registration or sending.

This article covers actions users perform from the screen. It does not cover creating, editing, or publishing Workflows, or configuring triggers or scheduled runs.

## Before you begin

- Check the App or Project you will use and the Workflow's name and purpose.
- Obtain guidance from the person in charge about the version approved for business use and the entry point to use.
- Prepare required input values, files, and the period, records, or other targets to process.
- If the Workflow sends information to an external service or updates data, check the targets, destinations, and scope you are allowed to run.
- Know who to consult about run permissions or connection settings.

If you have not opened where you will use it yet, begin with one of these articles.

- [Open an Installed App and Get Started](open-installed-app.md)
- [Get Started in a Shared Project](start-with-shared-project.md)

> Screen labels, placement, and available actions vary by organization configuration, permissions, and Workflow configuration. The following steps describe what to check in a desktop web browser. Do not conclude that publication and approval for business use are complete merely because you can see a Run button.

## 1. Open the Workflow to use

### Open it from an App

1. Open the App you will use.
2. Find the Workflow you need in "Automations" on the dashboard or navigation.
3. Check that the name and description match the process you need.
4. Open the input and run screen from the relevant card or "Run."

In addition to Workflows, "Automations" in an App may list run tasks. Do not select a different run target just because its name is similar. To check organization-wide Workflows and tasks, open "Tasks" from "Other features." In "Tasks," create a task for yourself from a published Workflow in "Find," and check runs, history, and settings in "In use."

For an App that opens a home Page for business use, follow the entry point on the Page or the person in charge's guidance. Not every App displays the same list.

### Open it from a Project

1. Open the shared Project.
2. Open "Tasks" from "Other features." Pin it to the sidebar if needed.
3. For a Workflow you are using for the first time, check the target published Workflow in "Find" and select "Create task." If connection or input settings are required, complete them according to the screen guidance.
4. Check the task you created in "In use," then open the input screen for running it from "Run" on that row.

Editing a Workflow in "Build" and running a task in "In use" are separate actions. For ordinary business use, you do not need to change the process in the editing screen or publish it again.

When opening from a link you were given, also check the displayed App, Project, and Workflow names.

### When you are unsure which version to use

A Workflow distributed in an App and a Workflow created or updated within a Project may be managed differently as run targets. **Instead of substituting a test run in the editing screen, users should use the entry point provided by the person in charge.**

If you have questions such as "The input fields are different even though it should be published" or "The processing has changed since the last update," check with the person in charge before running it. Publication is covered in [Publish a Workflow](../build/publish-workflow.md).

## 2. Check the run method and input fields

On the run screen, check the Workflow's name, description, and the information required for this run.

- In an App, you may see a section such as "Information required to run."
- On a Project run screen, you may see a tab such as "Input."
- If there is a field to choose a run method, select the method provided by the person in charge.
- For a Workflow that does not require input, input fields may not be displayed.

When there are multiple run methods, the input fields change depending on your selection. **After changing the run method, check the input values again.**

Even when the screen displays settings such as the AI model, completion notification destination, or Webhook URL, they are not necessarily input fields that users can edit. For a field with guidance such as "Configured by the developer and cannot be changed," ask the person in charge to make changes as needed.

> Screen runs and automatic runs from events or schedules are separate. Performing this action alone does not start, change, or stop a scheduled run. For the difference, see [Choose an Automatic Execution Method](../getting-started/choose-execution-method.md).

## 3. Enter the required information

Enter information according to field names, descriptions, and required indicators.

| Example input field | What to check |
|---|---|
| Text or long text | Whether the target name, request, and scope to process are complete |
| Numbers | Whether the unit, digits, number of target items, and other details match the description |
| Options | Whether the initially selected value is appropriate and fits this business task |
| URL | Whether it points to the intended materials or target and is the specified type of URL |
| File | Whether it is the correct version and target-period file, and whether the upload has completed |
| Structured input such as JSON | Whether it matches the input examples and format provided by the person in charge |

These are examples of input fields; not every Workflow has the same fields. Even if prior values or default values are displayed, review whether you can use them as they are.

### When using files

1. Check the target file-input field.
2. Select a file or enter a URL using a method permitted by the screen.
3. Check the displayed file name, number of files, and that the upload is complete.
4. If an error occurs, check the guidance about format, size, number of files, and other details.

Supported formats, maximum size and number of files, and whether you can use local files or URLs vary by the input-field configuration. Do not conclude that every part of a file can be read correctly merely because you were able to attach it.

Do not paste passwords, API keys, or similar information into an unrestricted input field that has no explanation of its purpose. If missing connection or Secret settings are displayed, check with the App or Project administrator.

### Input example

For example, if a Workflow named "Sales Summary Report" has fields for the target period, sales file, and output format, enter values so that the period and the dates in the file align.

- Target period: The month you want to aggregate
- Sales file: A file that contains data for that month
- Output format: Select from the formats available on the screen

This example describes how to check consistency among inputs. It does not mean a Workflow with that name or those fields is provided by default.

## 4. Run it

1. Review the Workflow, run method, input values, and files.
2. When you will register, update, or send information, check the target and run scope again.
3. Press the run button, such as "Run," once.
4. If input errors are displayed, correct the indicated fields.
5. When the run starts, check the status and results screen.

You may not be able to run it while a file is uploading, while a run is in progress, when it is disabled, or when you lack run permission. Check messages near the button or elsewhere on the screen.

**Do not repeatedly run the same process just because the response is slow or the screen does not change.** Even if the screen response fails, the process itself may have started. First check the run history and the status of the processing target.

Do not treat closing the screen or moving to another page as stopping the process.

## 5. Check the status and results

### Read the run status

The screen or run history may display statuses such as the following.

| Status | What users should check |
|---|---|
| Running | Wait for completion. Do not start another run; check the status. |
| Succeeded | Check that the output and intended business result are complete. |
| Partially succeeded | Check how far processing completed and whether any processing failed or remains incomplete. |
| Failed | Check the error and processing already completed, then determine whether it is safe to run again. |
| Stopped | Check the results through the point it stopped. It does not necessarily mean the state returned to what it was before the run. |

For detailed causes, check the displayed error and step results. Even "Succeeded" does not guarantee that generated text or aggregate values are correct for the business task.

### Check the output

Open areas such as "Result" and "Steps" on the App results screen or Project run screen, and check the following.

- Whether the target period, customer, records, and other details match the conditions you entered.
- Whether the expected text, numbers, files, or other output is displayed.
- Whether there are blank fields, unusual values, or omissions among the processing targets.
- Whether required subsequent processing has completed.

When the Workflow includes registration, updating, or sending to an external service, also check records or sending history in that service as far as possible. Even if the result is empty, it does not necessarily mean nothing was run.

### Save results

When you can download run data, check that it is different from downloading a business file created by the Workflow itself.

If the results identify a business file or storage location, obtain it according to that guidance and open the file to check its contents. Not every Workflow generates a file.

Retrieved data may contain input information or business information. Follow organizational rules for the save location and people with whom you share it.

## 6. Review results from run history

1. Open "Execution history," "Run history," or "Execution Log" for the target Workflow.
2. Identify the relevant run by its start time, user who ran it, status, or similar information.
3. If filtering by date, status, or similar criteria is available, adjust the conditions.
4. Check the contents of that run from an option such as "Open result."

The same Workflow may list runs by other people or with different input. **Do not assume the first item in the list is your run; also compare the start time and user who ran it.**

The scope of history and detail viewing varies by permissions and other factors. If you cannot find the result, check the target App, Project, Workflow, filter conditions, and viewing permissions. Sharing a result link alone does not give the recipient permission to view it.

## 7. Check before running again

Running again is different from refreshing the previous display. A new run may duplicate registrations, sends, or other actions.

1. Check the prior status and how far processing completed.
2. Check whether content has already been reflected in the external service or update destination.
3. Check whether the cause, such as an input mistake or missing setting, has been resolved.
4. If anything is unclear, consult the person in charge and confirm that you may run it again.
5. Review values, files, and targets on the input screen before running it.

"Run again" on the App results screen is an entry point that returns you to the input and run screen. Do not treat merely returning as completion; check the input and perform the run action. Not every prior input value is necessarily carried over.

"Retry" in a message such as "Could not load the run status" rereads the status. **Distinguish retrying status retrieval from running the Workflow itself again.**

## When things do not go as expected

| Situation | What to check first |
|---|---|
| You cannot find the Workflow | The App, Project, Workflow name, and entry point you were given |
| It is displayed as disabled or you lack permission | Do not change settings yourself. Ask the person in charge to check the active status and your run permission. |
| There is an input error | Required fields and formats such as numbers, URLs, JSON, and attachment conditions |
| It says a connection or setting is required | Ask an administrator to check connections, Secrets, and similar settings. |
| You cannot retrieve the status or steps | Retry the displayed status retrieval. Do not conclude that the process itself failed or stopped. |
| It fails or partially succeeds | Check errors and completed processing, then consider running it again after checking for duplication impact. |
| It succeeds but the result is not what you expected | Compare the input, output, and update destination, and consult the creator about the processing. |

When consulting someone, provide the App, Project, and Workflow names; the run date and time; the result-screen URL or run ID if available; the status; error message; and expected result. When sharing screenshots or input examples, exclude Secret information and unnecessary personal information.

## Completion criteria

- [ ] I selected the correct Workflow and run method.
- [ ] I checked the required input and files.
- [ ] I checked the status after running it.
- [ ] I checked the output and the results of any required registration, updates, or sends.
- [ ] I obtained required deliverables and asked the person in charge about anything incomplete or unclear.

## Next articles to read

- [Publish a Workflow](../build/publish-workflow.md)
- [Debug with Workflow Run History](../build/debug-workflow-run-history.md)
- [Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md)
