# Release notes

## Version 5.4.0

#### New Features

- The execution report table is now sortable by clicking a column header. [Read more](test_sets.md#sorting-the-execution-report-table)
- Added Step and Stop if fail columns to the execution report table. [Read more](test_sets.md#stop-execution-on-failure)
- Multiple test cases can be selected in the test set definitions (Ctrl/Cmd + click, Shift + click) and moved together with the new *Set step* action. [Read more](test_sets.md#moving-multiple-test-cases-at-once)
- Multiple selected test cases in the test set definitions can now be moved together with drag and drop. [Read more](test_sets.md#dragging-multiple-test-cases)
- Test sets can now be duplicated with the new *Duplicate* button in the Definitions tab or the *Duplicate test set* context menu action. [Read more](test_sets.md#duplicate-test-set)
- The test case details panel now shows the test sets that contain the test case, with links to open them and a *Suspended* label where the test case is deactivated. [Read more](test_case_repository.md#test-set-membership)
- Administrators can now configure how AST execution statuses are mapped to Xray statuses. [Read more](jira_xray.md#status-mapping)
- Administrators can now configure which Xray Test Run custom fields are filled with the test steps table and the report link. [Read more](jira_xray.md#custom-fields)
- Added Xray sync status filters and Xray sync status sorting to the Executions table.
- Trial run logs improvements: every test case has its own trial run window, which is replaced when the test case is run again, and its latest window is selected when you switch to the test case. [Read more](test_case_repository.md#trial-run-window)
- Trial run windows can now be pinned. A pinned window stays open when the same test case is run again, so you can keep and compare the logs of several runs. [Read more](test_case_repository.md#pinning-trial-run-windows)

#### Bug Fixes

- Fixed Xray sync status icons differing between the Executions table and the execution report window.
- Fixed renamed test cases showing their old name in the test set.
- Fixed the `Add testcase` dialog changing its size while typing in its search field.

#### Deprecated

- The self-registration endpoints `POST /api/register` and `GET /api/activate` are deprecated and will be removed in the next release. They are not used by the AST Control Panel.
- The endpoint `GET /SGKB/Jira/XRay/{processExecutorId}` is deprecated. Use `GET /api/execution/xray/{processExecutorId}` instead.

#### Other

- Manage external IDs (bulk upload and download of External Xray and HP ALM IDs) was moved from the test case details panel to Admin → Configuration and is now available to administrators only. [Read more](admin.md#manage-external-ids)
- The Stop if fail setting is now stored for each execution, so later changes to the test set no longer affect existing executions.
- The status sent to Xray is now taken from the execution result.
- Trial runs are stopped automatically when their logs are no longer being followed in the editor, for example after closing the browser tab.
- The database user ID is no longer shown in the User Management table and edit dialog.
- Backend and frontend dependency updates.
