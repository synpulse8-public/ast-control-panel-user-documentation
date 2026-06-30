# Getting Started

This getting started guide is for both administrators and test authors. 
It provides a linear workflow to get you up and running quickly, with links to more detailed documentation.

## Before you begin (administrators only)
Prerequisites checklist:

- [System requirements](system_requirements.md)
- [Deployment](deployment.md)
- After signing in with the default administrator (see [First login](#first-login)) and upload the initial [configuration files](configuration_files.md).
- Then modify the default administrator account or create a dedicated admin account and remove the default one. See [Administration](admin.md#user-management)
- If you use Keycloak SSO instead of local accounts, skip signing in with and replacing the default admin account; configuration files must still be uploaded. See [Keycloak](keycloak.md).

## First login
- Open the Control Panel URL
- Sign in (local or SSO) → [Login](login.md)
- You arrive at the [landing page](navigation.md#landing-page)

## Choose your Avaloq instance
- Use **Instance Selection** (top right) to pick the environment under test
- The instances in the dropdown come from `ast.properties`
- Scheduling, landing-page stats, and Executions are scoped to the selected instance

## Your first test run

1. **Open Tests** → [Test case repository](test_case_repository.md)
2. **Create or open a test case** (Create New or tree context menu)
3. **Attach test data** (optional Excel upload in right panel)
4. **Run the test**
    - **Trial Run** — quick run of the open test case from the editor
    - **Schedule** — run now or later (also from test sets)
5. **Check the result**
    - Last run status on the test case → [Reports](reports.md)
    - Or **Executions** tab for all runs → [Executions](executions.md)
6. **Group tests** (optional) — create a [Test set](test_sets.md), then Execute now or Schedule
