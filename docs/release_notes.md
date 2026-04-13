# Release notes

## Version 4.9.0

#### New Features

- Defects field added to Xray payload

#### Bug Fixes

- In test sets the reports tab did not display information correctly
- Fixed user management module user list
- Add bulk sync endpoint for ALM/XRAY and implement XRAY retry logic
- Fixed incorrect credentials not throwing error
- Fixed execution name not displaying when rescheduling an execution

#### Other Changes

- Upgrade to React 18
- Upgrade to ast-core 3.11.2
- Dependencies update

---

## Version 4.9.1

#### Bug Fixes

- View of execution log caused to spam requests

---

## Version 4.9.2

#### Bug Fixes

- Rearrange ALM payload to fix bad request during sync

---

## Version 4.9.3

#### Bug Fixes

- Trim ALM response and refresh cookies for every sync

---

## Version 4.9.4

#### Bug Fixes

- ALM Store and reuse session cookies

---

## Version 4.9.5

#### Bug Fixes

- Set loggedInAt for ALM session
