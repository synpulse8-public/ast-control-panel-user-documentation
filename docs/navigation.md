# Landing Page and Navigation

### Landing Page
After successfully logging in, you will be directed to the landing page of the AST application. This page displays an overview of your testing activities and provides quick access to various sections of the application. The landing page is designed to give you a snapshot of your testing environment, including recent test cases, scheduled runs, and quick actions for common tasks.

![navigation.png](assets/ControlPanelDocu/landing-page.png)
<figcaption>AST homepage after logging in</figcaption>

Short description of the sections on the landing page is provided in the table below:

| Menu              | Description                                                                                                                                                  |
|-------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Test Cases        | Shows the total number of created testcases.                                                                                                                 |
| Test Sets         | Shows the total number of created test sets.                                                                                                                 |
| Scheduled runs    | Shows the number of scheduled executions on the current Avaloq environment.                                                                                          |
| Recent Test Cases | Displays the last 5 test cases modified by the currently logged-in user. Click on a test case to open.                                                                   |
| Recent Run Status | Displays the last 5 executions started by the currently logged-in user on the current Avaloq environment with their status. Click on the execution to see more details. |
| Quick Actions     | Quick links to actions                                                                                                                                       |



### Main navigation menu (Left side)

| Menu                                          | Description                                                                            |
|-----------------------------------------------|----------------------------------------------------------------------------------------|
| ![home.png](assets/ControlPanelDocu/home.png) | Navigates back to the main dashboard or landing page of the application.               |
| **[Tests](test_case_repository.md)**          | Displays and manages individual test cases. This is the core repository for all tests. |
| **[Sets](test_sets.md)**                      | Organizes multiple tests into logical groups or suites for streamlined execution.      |
| **[Executions](executions.md)**                 | Displays scheduled and finished executions. Provides execution details.                |
| **[Analytics](analytics.md)**                 | Provides insights and reports on test execution results.                               |

Each tab navigates you to separate sections described in the table and in the following chapters in more detail.


### Secondary navigation menu (Right side)

| Menu                                                  | Description                                                                                                                 |
|-------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| **Instance Selection**                                | Used to choose the environment or system under test.                                                                        |
| ![question.svg](assets/ControlPanelDocu/question.svg) | Opens this documentation in a new tab.                                                                                      |
| ![gear.svg](assets/ControlPanelDocu/gear-six.svg)     | Contains administrative settings for managing users, permissions, and system configurations. See [Administration](admin.md) |
| ![user.svg](assets/ControlPanelDocu/user-circle.svg)  | Manages the current user's profile, settings, and login information. See [Account Information](login.md#account-settings)   |