# Check Execution Consumption and Usage

In JAPAN AI usage, you can check credits **consumed as a result of running** Agents, Workflows, models, tools, and similar features. In contrast, the charge for a paid App is a separate item calculated as **unit price × number of active members**. Do not confuse execution consumption with App charges.

This article explains how administrators check execution consumption in the organization's "Credit Usage" and identify causes of increases. For App users, charges, and contract limits, see [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md). For where to find limits and availability conditions by feature, see [Index of Limits and Availability Conditions](limits-and-availability-index.md).

## Scope of this article

This article covers credit usage for the whole organization. Select a month and switch between the daily, LLM, tool, per-App, and per-Project views to check the breakdown. In some organizations, the menu is labeled usage instead of credit usage (「利用状況」 in the Japanese UI).

The Project **"Usage"** screen is a separate screen for checking created resource counts, recorded credit activities, Storage, and similar items. Here, "included in usage-limit calculation" means the Project's portion of organization-wide usage; it is not a Project-specific limit (the Storage capacity limit, however, is per Project; see [Index of Limits and Availability Conditions](limits-and-availability-index.md)). Even when Project processing is suspected, narrow by user, run type, and date in organization Credit Usage, then individually check the target Agent, Workflow, Task, and run history.

## Before you begin

- Check that you have opened the correct organization. Credit usage is aggregated by organization.
- Organize the target month, the date of the event you want to check, and the expected users, Agents, and Workflows. Check scheduled runs that cross months in the month in which they ran.
- Check that you can access the screen as an organization administrator. Organization "Credit Usage" is an administrator screen. If you lack access permission, "You do not have permission to access credit usage" appears.
- Do not treat a figure as identical to an App monthly charge, invoice amount, or contract limit. First distinguish execution consumption from App charges according to the purpose of your check.

> The figures in "Credit Usage" are not themselves counts of how often users opened a screen or installed an App. They aggregate credit consumption based on the amount of text generated and features used. Do not determine invoice amounts or App contract conditions from this screen alone.

## Distinguish execution consumption from App charges

Before checking, organize the type of the increased figure as follows.

| Check target | Main trigger | Screen to check first | Note when deciding |
|---|---|---|---|
| Execution consumption | Processing such as Agent responses, Workflows and scheduled runs, LLMs, tools, data integrations, and SPEECH transcription | Organization "Credit Usage" | Narrow the cause by date, user, run type, LLM, and tool |
| App charges | Installing a paid App, adding members, monthly renewal | The App's "Settings" > "Members" and the charge confirmation | Monthly charge = unit price × active members. It is settled automatically by the active member count at monthly renewal, and removing members lowers the next renewal charge |

The daily view also displays "App seats," so not every increase in credits is caused by execution. If "App seats" has increased, check the target App, active member count, and next renewal in [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md), rather than the execution breakdown.

Conversely, if "Agents," "Workflows/Data Integration," or "SPEECH transcription" has increased, investigate usage as consumption associated with execution. Check the target run history as well to determine which processing a single run performed.

## Use Project "Usage" and organization "Credit Usage" differently

Project **"Usage"** lets you check created counts for pages, Objects, Agents, Workflows, Datasets, triggers, and similar resources in the Project, as well as Storage and recorded credit activities tied to the Project. Because aggregation times differ by data source, displayed values may differ from current values. Do not conclude that a Project is unused just because there is no record or there are zero records.

"Usage" for an installed App does not display a list of the App's internal resources; it primarily displays recorded values such as credit usage, AI requests, automation runs, and Storage. Use Project usage to identify potential causes, and check organization-wide execution consumption and monthly analysis in "Credit Usage" in the next section.

> The bar shown on a Project card compares recorded values with the portion included in the organization-wide usage-limit calculation. It does not directly show remaining contract limit or the App user limit. Do not treat Project resource count, Storage, organization credits, and App seat charges as the same figure.

## 1. Open the organization's "Credit Usage"

1. Select the target organization in JAPAN AI.
2. Open **"Credit Usage"** from organization settings (in some organizations, the menu is labeled usage instead).
3. Select the month you want to check in the month selector at the top of the screen. First explicitly select the month whose increase you want to check, rather than assuming the current month.
4. Check the displayed **"Credits used."**
5. If aggregation fails, "Failed to load graph" or "No records" may appear. In this case, do not conclude that the number is zero; take the measures described later.

Displayed items and filter items vary according to organization settings and contract. Do not estimate a category that is not on the screen from another figure.

## 2. Check the date and users that increased in "Daily"

First select **"Daily"** and check when and in which category the increase occurred.

1. Select **"Daily"** and check **"Daily credit trends."**
2. Select the bar for the day that increased and filter by date. If you do not select a day, you can check aggregation for the entire selected month.
3. In the list, check **"Name/Email address,"** **"Workflow,"** **"Data Integration,"** **"SPEECH transcription,"** **"Agent,"** and **"Total."** Use column headings to check users and categories with high consumption from the top.
4. Search by name or email address if necessary. In organizations where you can select a department, narrow to the target department and compare.
5. If you need to check only API usage, use **"API only."** Do not assess it mixed with usage through normal screen operations.
6. If you need to share or retain aggregation results, use **"Download CSV."** Because CSV files can contain user names and email addresses, keep the storage location and sharing scope to the minimum necessary.

> "Total" in the user list is aggregation for the selected month, day, and filter conditions. Do not directly compare increases or decreases between figures with different selection conditions. Record the conditions you checked before comparing.

## 3. Investigate execution details by "LLM" and "Tools"

When Agent consumption stands out, separately check models and tools.

### Check by LLM

1. Select **"LLM."**
2. In the list and graph, check **"Input credits,"** **"Output credits,"** **"Credits used,"** and **"Users"** for each LLM.
3. Check whether input or output is increasing and which model is concentrated.
4. If necessary, select **"Organization-wide"** or **"By user"** from **"Download CSV."** Limit the sharing scope of data by user.

Input and output are a breakdown of "Credits used." Do not add them again to the total column.

### Check by tool

1. Select **"Tools."**
2. Check **"Tool,"** **"Credits used,"** **"Usage,"** **"Unit,"** and **"Users."**
3. Check increases for each displayed tool.
4. If you find high consumption, check the relevant Agent settings, repeated runs, user operations, and run history.

If a tool name or unit is not displayed, the tool was not used in the selected period or is not provided to your organization.

## 4. Identify the source in the per-App and per-Project views

To see which App or Project consumed credits, switch to the **per-App** or **per-Project** view. When you find an App or Project with high consumption, check its "Usage" screen and the target Agents, Workflows, and run history.

## 5. Order for checking Workflows and automation

When you believe a Workflow or automation is the cause, do not decide to stop or change it based on usage alone. Check in order: the per-user breakdown for the increased day, the target Workflow, Task, and run history, and then frequency, end date, notification destination, and creator.

Because repeated runs can affect consumption, check run frequency together with monthly usage. For configuration, see [Configure the Task Scheduler](../build/configure-task-scheduler.md); for changes, stopping, and handover, see [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md).

## Common misunderstandings

- **Because we use an App, all of the increase is App charges**: This is incorrect. Check the App's charges separately from execution consumption for Agents, Workflows, and similar activities within it.
- **Removing a user does not change App renewal charges**: This is incorrect. Paid Apps are charged by the number of active members, so removing a member lowers the next renewal charge (the current month is not refunded). See [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md).
- **Add LLM input/output to "Credits used"**: This is incorrect. Input and output are a breakdown of Agent usage.
- **Project resource usage is low, so execution consumption is also low**: Not necessarily. Project creation counts, Storage, and recorded activities are separate from organization credit execution consumption.
- **There are no records, so there were no runs**: Before deciding, check for a display loading failure and differences in selected month, day, and filter conditions.

## Troubleshooting

| Situation | What to check and do |
|---|---|
| "Credit Usage" is not displayed | Check that the correct organization is selected and that you are accessing it as an administrator. In some organizations, the menu is labeled usage instead. |
| It says you do not have access permission | Ask an administrator with permission to view organization usage. Do not infer organization-wide consumption from an individual screen alone. |
| A figure suddenly increased | First identify the date in Daily, then check the user categories, LLM, tools, and the per-App/per-Project views in that order. If "App seats" is the cause, return to [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md). |
| You cannot identify the Workflow source of consumption | Narrow candidates from the per-user list on the target date, then check the Workflow, "Tasks," and run history. Do not stop multiple automations based on guesswork. |
| You want to share a CSV | Check whether it may include user names and email addresses, and keep recipients, retention period, and storage location to the minimum necessary. |
| The graph or breakdown fails to load | Record filters such as month, day, department, and API only, then display it again later. If it continues, make an inquiry including organization name, check time, selection conditions, displayed message, and target Agents/Workflows. |

## Checklist

- [ ] I selected the correct organization and month to check
- [ ] I distinguished whether the increase is execution consumption or App charges
- [ ] I checked the increase date, users, and categories in "Daily"
- [ ] I narrowed by department, API only, or user name as needed
- [ ] I checked the breakdown of Agent consumption in "LLM" and "Tools"
- [ ] I checked the source App or Project in the per-App and per-Project views
- [ ] I compared Workflow/automation usage with run history
- [ ] I decided to check App charges separately in [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md)
- [ ] I checked the CSV sharing scope and storage location

## Related articles and next articles to read

- Check App users, charges, and contract limits: [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md)
- Find the source of truth for limits and availability conditions: [Index of Limits and Availability Conditions](limits-and-availability-index.md)
- Configure Tasks: [Configure the Task Scheduler](../build/configure-task-scheduler.md)
- Change, stop, and hand over scheduled runs: [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md)
