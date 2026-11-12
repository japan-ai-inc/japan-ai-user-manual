# Collect the Information Needed for an Inquiry

This page explains how to gather the information needed for an investigation without including confidential information before sending a problem report or inquiry to a JAPAN AI administrator or support. It applies to everyone. Use it if a screen fails, an App cannot be used, the result of a Workflow is unknown, or an SDK/API or CLI fails. Rather than guessing at the cause and sending credentials or business data, associate the target, time, reproduction conditions, displayed result, and checks already performed.

## Related articles

- [Check Deletion, Handover, and Audit Records](../operations/deletion-handover-and-audit-logs.md)
- [A Page Fails to Build, Start, or Display](page-build-start-display-failures.md)
- [Isolate SDK and API Errors](isolate-sdk-api-errors.md)
- [App Installation, Initial Setup, or Update Fails](app-install-setup-update-failures.md)

## Before you begin

An inquiry is intended to enable a third party to trace what happened to the same target. Repeatedly performing actions immediately after an error appears may change the state or run history and may cause duplicate external transmissions or updates. First, check the current state and stop affected processes only to the minimum extent necessary.

> Never include API keys, passwords, Secrets, tokens, Cookies, authentication headers, or MFA codes in inquiries, screenshots, logs, or code snippets. Do not send personal information or business data, such as names, email addresses, phone numbers, addresses, customer data, conversation content, or file content, in a form that is unnecessary for the investigation. If you cannot determine whether merely redacting a value makes it safe, do not send it; consult the person in charge.

## What to check first

- [ ] You have not confused the target organization, Project, App, Page, Agent, Workflow, or other resource.
- [ ] You recorded the date and time of occurrence, time zone, and the person who ran it or the users affected.
- [ ] You noted identifying information displayed on the screen, such as the run ID, error ID, target ID, or URL.
- [ ] You separated the expected result, actual result, and the full displayed error or a safe summary.
- [ ] For an SDK/CLI, you noted the SDK version, CLI version, Node.js version, execution mode, and HTTP status or exit code.
- [ ] You recorded whether you retried, updated, deleted, or redeployed, and the result.
- [ ] Before sending, you confirmed that it contains no credentials, Secrets, tokens, personal information, or business data.

## Troubleshoot by symptom

| Symptom or displayed message | Primary cause | What to check or do | Detailed article |
|---|---|---|---|
| The screen displays "Error ID: `{{errorId}}`", "An error occurred", or a loading failure | One of the target screen, permissions, communication, or service-side state. The cause cannot be identified from the display alone. | If an error ID is available, note it, and record the screen URL, target, time, action, expected result, and actual display. Also check the state before and after refreshing. | [A Page Fails to Build, Start, or Display](page-build-start-display-failures.md) / [Check Deletion, Handover, and Audit Records](../operations/deletion-handover-and-audit-logs.md) |
| A Page shows a blank screen, 404, 401, or an upload or build failure | A mismatch in the distributed item, access settings, Page session, target URL, or registration/switching state | Record the Page name, URL, target version, access settings, failed URL, browser display, and time of occurrence. Do not include Secrets in distributed files or browser logs. | [A Page Fails to Build, Start, or Display](page-build-start-display-failures.md) |
| App installation, setup, or use after an update fails | A mismatch in the installation method, installing organization, settings, shared/personal connectors, Secrets, user permissions, or version application status | Gather the App name, installation method, identification information for the installing Project, current state, added/changed resources/connectors, required setting states, update notices, and affected users. If Marketplace billing is involved, also record the number of seats, whether the consent screen was displayed, and the purchase/retry status. Do not send connection values or Secret values. | [App Installation, Initial Setup, or Update Fails](app-install-setup-update-failures.md) |
| An SDK/API returns 4xx, 5xx, 429, or times out | One of input, authentication, permissions, target, usage controls, communication, or execution mode | Record the API or SDK feature name, target ID, SDK version, Node.js version, execution mode, `statusCode`, start and end times, reproduction steps, and results of retrying or checking the state. | [Isolate SDK and API Errors](isolate-sdk-api-errors.md) |
| The CLI ends with a failure | A mismatch in the connection destination, key, profile, command arguments, API response, or execution environment | Record the executed command with keys and personal information removed, CLI version, Node.js version, exit code, a safe summary of standard error output, and profile and environment names. | [Isolate SDK and API Errors](isolate-sdk-api-errors.md) |
| The result of deletion, uninstallation, or handover is unknown | The request is accepted, processing, complete, or failed, or the wrong target operation was selected | Record the target, operation type, date and time, executor, state before and after deletion, scope of impact, and the period and filter criteria used to check the audit log or run history. | [Check Deletion, Handover, and Audit Records](../operations/deletion-handover-and-audit-logs.md) |

## Organize the information to collect by stage

### 1. Identify the target and time of occurrence

Record the applicable target, such as the organization name, Project name, App name, Page name, Agent name, or Workflow name. If possible, note the ID displayed on the screen, run ID, error ID, public URL, or screen URL. If a URL includes a token, personal information, search term, or confidential parameter, do not send that part.

Next, record the date and time of occurrence with the time zone. For a process whose start and end are known, record both, and summarize whether it was a one-time occurrence or ongoing, as well as the affected users and business scope. If you can reproduce it, record the normal usage steps only to the minimum extent necessary. Do not repeatedly perform actions involving updates, transmissions, or deletion in external services for investigation purposes.

### 2. Separate results from completed checks

Write the expected result and actual result separately. Before recording an error message displayed on the screen, confirm that it contains no keys, tokens, personal information, or business data. For screenshots, check that confidential information does not appear in the browser address bar, notifications, input fields, or logs.

Also record completed checks in order. For example, write statements such as "Confirmed the target Project," "Looked for the run ID in the run history," "Checked the configuration state of the installing organization," "Followed the wait guidance for 429," and "Checked the result after the timeout." Do not reach conclusions based on guesses about the cause; separate confirmed facts from items not yet confirmed.

### 3. Add environment information for SDK/API/CLI

For an SDK/API inquiry, note the package name and SDK version, Node.js version, execution mode (local Node.js, PRS browser, or PRS server), API/SDK method used, HTTP `statusCode`, and timeout configuration and whether a timeout occurred. For a CLI inquiry, add the CLI version, command, exit code, profile and environment names, and a safe summary of standard error output.

The authentication values themselves are unnecessary. Write only the configuration method and confirmation result, such as "Passed a key for the target Project from a secure environment variable" or "Did not configure a key in the PRS browser." For `baseUrl` and URLs as well, excluding secret query strings and tokens, check whether the connection destination matches the execution mode.

> Logs and `responseBody` may contain input values. Before sending the full error text as is, remove API keys, Authorization, Cookies, Secrets, personal information, and body or attachment content. If you cannot safely edit it, send only the error ID, time, target, and status, and retain the original log in an access-controlled location.

## Inquiry template

Copy the following block and mark unnecessary items as "Not applicable." Do not write confidential information inside square brackets.

```text
Subject: I want to check [symptom] for [target feature]

■ Target
Organization / Project / App or resource name: [name and shareable ID]
URL: [URL excluding tokens and personal information]
Run ID / error ID: [only if displayed]

■ Occurrence details
Date and time of occurrence (time zone): [YYYY-MM-DD HH:MM TZ]
Executor and scope of impact: [summary of role, number of people, and business impact]
Expected result: [expected state]
Actual result: [display, state, or safely redacted error message]

■ Reproduction steps and completed checks
1. [action]
2. [action]
Whether you retried or checked the state, and the result: [details]

■ For development or CLI
SDK / CLI / Node.js versions: [value]
Execution mode: [local Node.js / PRS browser / PRS server]
API/SDK method or command: [content excluding confidential values]
HTTP statusCode / CLI exit code: [value]

■ Information not included
API keys, Secrets, tokens, Cookies, authentication headers, personal information, and business data: Not included
```

## Information you must not send

| Category | Do not send | Record instead |
|---|---|---|
| Credentials | API keys, Secrets, passwords, tokens, Cookies, MFA codes, or the values of `Authorization` and `x-api-key` | Whether a key was used, the target Project, configuration method, and the result of checking validity and scope |
| Personal information and business data | Names, email addresses, phone numbers, addresses, customer information, conversation content, input values, file content, or attachment content | Number of records, data type, scope of impact, and safe dummy information for reproduction |
| URLs and logs | Signed URLs, query tokens, complete requests/responses, or unredacted screenshots | URL excluding tokens, error ID, time, status, and redacted summary |

## Before consulting an administrator or support

Immediately before sending an inquiry, reconfirm that you have the target, time, ID, expected and actual results, reproduction steps, and completed checks, and that there is no confidential or personal information. For deletion and handover, also add the target operation, executor, scope of impact, and the criteria used to check the audit log or run history. For details, see [Check Deletion, Handover, and Audit Records](../operations/deletion-handover-and-audit-logs.md).

## Next articles to read

- [A Page Fails to Build, Start, or Display](page-build-start-display-failures.md)
- [App Installation, Initial Setup, or Update Fails](app-install-setup-update-failures.md)
- [Isolate SDK and API Errors](isolate-sdk-api-errors.md)
- [Check Deletion, Handover, and Audit Records](../operations/deletion-handover-and-audit-logs.md)
