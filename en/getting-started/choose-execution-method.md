# Choose an Automatic Execution Method

When running processing in Studio, think separately about **"what to run" and "what starts it."**

For example, having a Workflow that creates a weekly report does not make it run every week by itself. Choose whether a person runs it, it runs at a specified date and time, or it runs when data changes.

This article explains when to use Workflow triggers and date-and-time starts managed in "Tasks," including manual runs as a comparison before you begin automation. Specific setup steps and the list of supported events are covered in the respective setup articles.

## Separate these two decisions first

| What to decide | Example |
|---|---|
| **Processing to run** | Create a report with an Agent; retrieve, aggregate, and register information with a Workflow |
| **What starts it** | A person runs it; a scheduled date and time arrives each week; a matching email is received |

If you want to start by selecting features such as Agents and Workflows, see [Choose Features by Goal](choose-features-by-goal.md).

## Choose an execution method by goal

| Work condition | First choice | Why to choose it |
|---|---|---|
| A person decides the input and whether it is appropriate to run each time | **Manual run** | The person can check the input and target before starting. |
| Run at a specified date and time or on a cycle such as daily or weekly | **Date-and-time start managed in "Tasks"** | You can manage execution tasks for Agents or Workflows based on the date and time. |
| Run in response to an event, such as receiving an email or changing data | **Workflow event trigger** | You can configure the target event and conditions, and pass its information to the processing. |
| Run in response to a notification from an external system | **Consider a Webhook trigger** | If available, it can use an incoming HTTP request from an external source. You must confirm the connection, authentication, and input format. |

These are selection guidelines. Available triggers and setup screens vary by availability conditions, organization settings, permissions, and the target Workflow. Confirm that the candidate method is available in the environment you will actually use.

> A "trigger" is what starts processing. Screens and technical materials may use the term to include manual runs. In this article, event-based triggers are called "event triggers," and date-and-time settings are called "schedules."

## 1. When to choose a manual run first

Start with a manual run for work such as the following:

- Change the target period, files, or request details each time.
- Have a person decide whether to run before external sending or data updates.
- Check the input, result, and impact of newly created processing.

For example, a person selects a target deal and generates a draft report. At first, try a configuration where you can review the result instead of including automatic sending.

For Workflows, distinguish between a creator testing a draft and a user running a published Workflow. **Even a test run may update actual data or send information externally, depending on the processing it includes.** Do not assume that it is a safe simulation merely because it is called a test.

Also, starting processing manually is different from asking for human approval at each operation during processing. Design the processing to include the necessary review points.

## 2. Use "Tasks" when the date and time are fixed

Manage execution tasks that start at a date and time on the "Tasks" page. There, check and configure the Agent or Workflow to run and the date, time, and recurrence conditions.

For example, the following work may be a candidate:

- Aggregate information from the previous day every morning.
- Create a status report every week.
- Run prepared processing at a specified date and time.

Decide the following before choosing it.

| What to check | Example decision |
|---|---|
| Execution target | Which Agent or Workflow to run |
| Input and target period | Whether the same input is sufficient every time and how to identify "the previous day" or "the previous week" within the processing |
| Date, time, and recurrence | At what time and how often to run it, and whether the screen's time zone and next run date and time are as intended |
| End and stop | How long it continues and who stops it when it is no longer needed |
| Result review | Where to view history, whether notifications are needed, and who responds to failures |

Setting a schedule does not automatically change the input period or ensure that the required data is available. For example, even for a morning aggregation, information may not be reflected if the source data update is delayed.

**The specified start time and the time when processing completes are not the same.** Set up operations that let you review the result, including run status and responses from external services.

## 3. Use a Workflow trigger when matching an event

A Workflow event trigger starts processing when a target event occurs and meets configured conditions.

Candidates include receiving emails, Object data changes, and Storage file operations. However, conditions differ by service and event type. This does not mean that it can detect every change in an external service.

### Three decisions for an event-based method

1. **Where the event occurs**: A target account, Project, Object, folder, and so on.
2. **Which event to target**: Available types and filtering conditions, such as receiving, creating, or updating.
3. **What to pass as input**: Values the Workflow requires, such as a target record, email body, or file information.

For example, when you want to organize content after a file is added to a target folder, check which folder it is, whether the added event is supported, and whether the file can be passed to the processing. Choosing only a storage location does not configure automatic execution.

### When using external notifications with a Webhook

If you can use a "Webhook trigger," you can start a Workflow from an HTTP request sent by an external system. Configure the Webhook URL generated after publishing in the external system, and check the recipient, authentication method including signature verification, data format to send, and how it corresponds to Workflow inputs.

If the external side does not send a notification, it cannot run in response to that notification. Direct execution from an SDK or API is another option, but check the supported scope in the developer guides.

### Separate publishing processing from preparing connections

Even when the processing content of a Workflow is ready, preparation may remain, such as authentication for the connection destination, event conditions, and trigger registration.

**Do not consider automatic execution ready merely because it worked manually or could be published.** Use an event that meets the actual conditions to check both the start and result.

## When deciding between date-and-time and event starts

Decide based on what you want the processing to align with.

| Goal | Decision |
|---|---|
| Summarize data as it exists each morning | A date-and-time start is a candidate. Decide the target period and aggregation time. |
| Process each data item when it is added | A supported event trigger is a candidate. |
| Process data after source data preparation is complete | Check whether an event representing completion is available. Merely delaying the time does not guarantee preparation is complete. |
| Many exceptions require deciding the target or whether to run each time | Keep a manual run, or automate only standardized parts. |

Date-and-time starts managed in "Tasks" and Workflow triggers are classifications that distinguish the purpose and entry point configured by users. They do not mean that they use separate execution infrastructure internally.

If you configure both a date-and-time start and an event start for the same processing, check whether the same target might be processed twice. Adding execution methods does not necessarily make the process more reliable.

## Checklist before automating

- [ ] I decided the target data and success conditions for the processing.
- [ ] I checked the result and the impact of updates or sending with test input.
- [ ] I confirmed whose permissions and account will access data and operate external services.
- [ ] I confirmed the date, time, and time zone, or the event target and conditions.
- [ ] I decided who handles expired credentials and changes in responsibility.
- [ ] I decided where to review run history and who handles failures.
- [ ] I checked whether reruns or duplicate starts might register or send the same data twice.
- [ ] I confirmed how to stop and change it. Confirm separately whether stopping also affects processing that is already running.

Even if no result appears on the screen, the processing itself may still be continuing. Do not rerun it immediately; first check the history and the status of the processing destination.

At first, try it with limited targets. Check not only normal cases but also cases with no target, incomplete input, and connection errors before expanding use.

## Next articles to read

| What you want to do next | Article |
|---|---|
| Reconsider which feature to use for processing | [Choose Features by Goal](choose-features-by-goal.md) |
| Use a published Workflow manually | [Run a Published Workflow](../use/run-published-workflow.md) |
| Configure event-based start conditions | [Configure Workflow Triggers](../build/configure-workflow-triggers.md) |
| Configure date-and-time starts for Agents and Workflows | [Configure the Task Scheduler](../build/configure-task-scheduler.md) |
| Change, stop, or hand over scheduled runs | [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md) |
| Configure notifications for run results | [Configure Run Result Notifications](../operations/configure-run-result-notifications.md) |
