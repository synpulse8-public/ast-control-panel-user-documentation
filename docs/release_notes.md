# Release notes

## Version 4.10.0

#### Breaking Changes
- Removed ApplUserResource API

#### New Features
- Database Maintenance(Deletion of old executions and reports). For more info click [here](admin.md#maintenance).
- When turning off Keycloak profile AST created users are restored to their previous state, meaning they will be able to log in again with their old credentials. For more info click [here](keycloak.md#behavior-of-the-application-with-keycloak-profile-deactivated-after-it-was-active).

#### Bug Fixes
- Fixed avaloq instance dropdown , so it is displayed correctly
- Username filter in schedule table now working correctly
- Fixed Admin dropdown shifting when opened
- Show pending status for ALM instead of N/A
- Fixed error when Keycloak token was missing surname and name claims(should handle service accounts now)
- Fixed issue when username containing @ could not login


#### Other Changes
- Updated frontend dependencies.
- Updated backend dependencies. Using Spring Boot 3.5.14
- Updated AST Core to 3.1.3
- Help button now opens the documentation in a new tab.
