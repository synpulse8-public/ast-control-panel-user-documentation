# Release notes

## Version 5.1.0

#### Breaking changes

- Endpoint `api/admin/logfile` was removed. No replacement available.
- Payload change in POST `api/process-executor-groups/aggregated` endpoint due to multi-option filtering. Check the API specification in the solution. [Read more](admin.md#api)

#### New Features
 
- Stop execution if a testcase fails. [Read more](test_sets.md#stop-execution-on-failure)
- Multi-option filtering in Execution view. Filter executions by multiple values per column simultaneously. [Read more](executions.md#filtering)
- Concurrent testcase editing. [Read more](test_case_repository.md#concurrent-editing)
- AI assisted test generation. Documentation pending.

#### Other

- Updated to Java 25 and Spring Boot 4.1.x
- Frontend and Backend dependency updates
- Various security fixes
- Various code refactoring for maintainability


