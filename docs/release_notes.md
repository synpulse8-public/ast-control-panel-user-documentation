# Release notes

## Version 5.3.0

#### New Features

- Added Skipped and Cancelled as execution results. [Read more](test_sets.md#reports-tab)
- New dialog for moving test cases in a test set. [Read more](test_sets.md#move-dialog)
- Administrators can now pick a custom header ribbon color with a color picker, in addition to the 6 predefined colors. [Read more](admin.md#configuration)


#### Bug Fixes

- Fixed schedule dialog to display the current time correctly.
- Fixed issue where folders were showing incorrect number of scripts.
- Fixed import dialog to show the correct script type.
- Prevented scripts of method type from being incorrectly added to test sets.
- Fixed the log filter returning no results when the search text started with a space.
- Fixed the Executions table not showing running executions when the Running filter was selected.


#### Other

- Test cases already in the test set are now preselected and greyed out in the Add Test Case dialog. [Read more](test_sets.md#add-test-cases-to-already-existing-test-set)
- The Run sequentially checkbox was replaced by the radio buttons *Add each test case as a step* and *Add as one parallel step*. [Read more](test_sets.md#add-new-test-set)
- Test sets are ordered alphabetically in the Test Sets screen.
- Replaced stop execution icon in the Executions table.
- Removed test case list from the schedule dialog on the Test Sets screen.
- Test set editor view mode styling enhanced for more clarity.
- Increased the rows-per-page options in the Executions table to 30–100.
- Backend and frontend dependency updates.
