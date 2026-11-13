# Release notes

## Version 5.4.0

#### New Features

- The execution report table is now sortable by clicking a column header. [Read more](test_sets.md#sorting-the-execution-report-table)
- Added Step and Stop if fail columns to the execution report table. [Read more](test_sets.md#stop-execution-on-failure)
- Multiple test cases can be selected in the test set definitions (Ctrl/Cmd + click, Shift + click) and moved together with the new *Set step* action. [Read more](test_sets.md#moving-multiple-test-cases-at-once)
- Multiple selected test cases in the test set definitions can now be moved together with drag and drop. [Read more](test_sets.md#dragging-multiple-test-cases)
- Test sets can now be duplicated with the new *Duplicate* button in the Definitions tab or the *Duplicate test set* context menu action. [Read more](test_sets.md#duplicate-test-set)
- Administrators can now configure how AST execution statuses are mapped to Xray statuses. [Read more](jira_xray.md#status-mapping)
- Administrators can now configure which Xray Test Run custom fields are filled with the test steps table and the report link. [Read more](jira_xray.md#custom-fields)
- Added Xray sync status filters and Xray sync status sorting to the Executions table.

#### Bug Fixes

- Fixed Xray sync status icons differing between the Executions table and the execution report window.
- Fixed renamed test cases showing their old name in the test set.

#### Other

- Manage external IDs (bulk upload and download of External Xray and HP ALM IDs) was moved from the test case details panel to Admin → Configuration and is now available to administrators only. [Read more](admin.md#manage-external-ids)
- The Stop if fail setting is now stored for each execution, so later changes to the test set no longer affect existing executions.
- The status sent to Xray is now taken from the execution result.
- Trial runs are stopped automatically when their logs are no longer being followed in the editor, for example after closing the browser tab.
- Trial run logs improvements.
- The database user ID is no longer shown in the User Management table and edit dialog.
- Backend and frontend dependency updates.
