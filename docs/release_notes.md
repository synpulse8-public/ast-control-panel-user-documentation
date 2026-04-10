# Release notes

## Version 4.8.0

#### Breaking Changes

!!! warning "Important!"
    You have to only adjust only properties that you have set in your deployment file. Properties that can be set via the UI will be adjusted automatically.

##### Xray properties changes:

~~~ 
parexa.ast-control-panel.integration.xray.sync.enabled → ast-control-panel.integration.xray.sync-enabled 
~~~
~~~
parexa.ast-control-panel.integration.xray.sync.endpoint → ast-control-panel.integration.xray.sync-endpoint
~~~
In other Xray properties only the parexa prefix was removed

##### ALM properties changes:
~~~
parexa.ast-control-panel.integration.alm.endpoint.login → ast-control-panel.integration.alm.endpoint-login
~~~
~~~
parexa.ast-control-panel.integration.alm.endpoint.session → ast-control-panel.integration.alm.endpoint-session
~~~
In other ALM properties only the parexa prefix was removed

##### Other properties changes:
~~~
parexa.ast-control-panel.backend.max-process-duration-in-hours → ast-control-panel.execution.max-process-duration-in-hours
~~~
~~~
parexa.ast.core.properties-file.path →  ast-core.properties-file-path
~~~
~~~
parexa.ast-control-panel.web.domain →  ast-control-panel.web.domain
~~~
~~~
parexa.ast-control-panel.execution.max-parallel-processes → ast-control-panel.execution.max-parallel-processes
~~~

#### New Features

- Keycloak integration - see documentation [here](keycloak.md)
- Added downloading of historical execution data
- Added Execution Id and External Xray Id as columns in the schedule view

#### Bug Fixes

- Fixed testcase ordering by priority
- Fixed testcase sequence issue in testset
- Disable download button when date range is incomplete in historical executions
- Fixed timestamp constraint that caused a crash on startup

#### Other Changes

- Changed docker image to a rootless image
- Added curl to image
- Updated core to version 3.11.0
- Updated dependency versions, mainly spring boot to version 3.5.7

---

## Version 4.8.1

#### Bug Fixes

- Fixed error when changing the context path

---

## Version 4.8.2

#### Bug Fixes

- Add fallback to prevent OPTIONS returning a 401 response

---

## Version 4.8.3

#### Bug Fixes

- Moved URL parameters to the body for scheduling 

---

## Version 4.8.5

#### Bug Fixes

- Moved URL parameters to the body for testset scheduling

---

## Version 4.8.6

#### Bug Fixes

- Displaying Xray token correctly in frontend
- Xray token retrieved correctly from DB

---

## Version 4.8.7

#### Bug Fixes

- Saving throwing error when not using override on Alm properties
- Remove extra segment from login endpoint path, causing 405 error in ALM

---

## Version 4.8.8

#### Bug Fixes

- Fix ALM 401 error on the sync endpoint

---

## Version 4.8.9

#### Bug Fixes

- Removed method causing to reuse old cookies in ALM and add cookie logging

---

## Version 4.8.10

#### Bug Fixes

- Fix 404 on ALM auth and session endpoints

---

## Version 4.8.11

#### Bug Fixes

- Combine cookies from login and session requests in ALM

---

## Version 4.8.12

#### Bug Fixes

- Use ALM username in payload
