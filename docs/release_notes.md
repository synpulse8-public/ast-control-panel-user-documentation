# Release notes

## Version 4.11.0

!!!danger "Important"
    The 4.11.0 release will be the last one for the 4.x release line, and it will be supported only by patch versions of critical bugs.
    

    This release(or its future patch versions) are a prerequisite for the next major release, which will be 5.0.0 (New Frontend).
    


#### Bug Fixes
- Fixed issue with running a testcase with a method was throwing a 500 error
- Fixed issue where variables were not correctly replaced in the payload of a test case when running it.

#### Other Changes
- Updated backend dependencies.
- Updated AST Core to 3.12.0 

## Version 4.11.1

#### Bug Fixes
- Fixed issue of schedule view not working because of null xray logs
