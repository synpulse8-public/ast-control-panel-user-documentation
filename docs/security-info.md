# Security

This section outlines how users are managed and secured within the AST Control Panel, covering login methods, user administration, and system-level security best practices.

## User Access and Administration

The AST Control Panel handles user authentication either by the application’s internal account system or by an external IAM ([Keycloak](keycloak.md)).
For more information refer to the [Login and User Management](login.md) section.


## Secure Communication (HTTPS/SSL)

!!! warning
    By default, the AST Control Panel uses HTTP. Because sensitive information, including passwords, can be easily viewed or altered over the network in HTTP, it is strongly recommended to enable network encryption (SSL/HTTPS).

- **Automatic Redirection**: If you enable SSL, any unsecured HTTP requests will be automatically forwarded to HTTPS.
- **SSL Configuration**: For detailed instructions on setting up SSL, refer to the [Setting up truststore](deployment.md#setting-up-a-truststore-for-ssl) or [Setting up keystore](deployment.md#setting-up-a-keystore-for-ssl) section in the deployment documentation.

## Protecting Sensitive Credentials

For more information refer to [Environment Secrets](deployment.md#using-env-and-envsecrets)

## Avaloq Security
AST does not allow direct access to Avaloq by users. Avaloq credentials are kept in the ast.properties file. Read and write access to this file should be restricted to AST users with admin role.

All activities made by a user, leading to test cases execution against Avaloq, have to be embedded in a testcase and saved.
(version control applies, previous versions are kept and are auditable)

Given AST testcases are used to prepare synthetic data and simulate complex testcases, optimal results are achieved by using the Avaloq k-user.
However, if security limitations forbid usage of k-user, it is possible to set up a non k-user as a proxy user for AST. See section [below](#setting-up-a-non-k-user) for instructions.

Logs of test case execution are stored and secured in the database. Log are kept read-only. 
Test execution logs are separated from AST application logs. Application logs are stored on the pod.

### Setting up a non k-user
Create a Technical User: In Avaloq, create a technical user as a parameterization. In this example, we'll call it `MDB$TEST_PROXY`:

```sql
[user 1.0]  
user        MDB$TEST_PROXY  
name        "MDB$TEST_PROXY"  
type        tech  
restricted  true  
end user
```
Create SQL Objects and Grants: Execute the following SQL commands to create the user and grant the necessary permissions:
```sql
CREATE USER MDB$TEST_PROXY IDENTIFIED BY TEST_PROXY;
GRANT EXECUTE ANY PROCEDURE TO MDB$TEST_PROXY;
GRANT SELECT ANY SEQUENCE TO MDB$TEST_PROXY;
GRANT SELECT ANY TABLE TO MDB$TEST_PROXY;
GRANT CREATE SESSION TO MDB$TEST_PROXY;
```
Add a Proxy User: Using SQL Developer, add this user as an Avaloq proxy user. Verify this step with your Avaloq administrator to ensure no other users besides the AST user are affected by the i_val list.


```sql
begin
session#.open_session;
base_par_intf#.base_par#set_val(
i_intl_id => 'avq.user'
,i_item => 'proxy_session'
,i_val => 'MDB$TEST_PROXY'
);
commit;
end;
```
Reset the Session: To apply the changes, reset the session with this command:


```sql
exec session#.reset_package;
```
You can now verify that the user is listed as a proxy user by running the following query:


```sql
select base_par_intf#.base_par#val(
'avq.user',
'proxy_session'
)
from dual;
```
After these steps, you should be able to connect to the database in SQL Developer and open a session on behalf of another user.


```sql
exec k.session#.open_session(i_oracle_user => 'avaloq');
```
!!! warning "Important"
    Finally, remember to enable the ast.proxy.avaloq.session.user property in ast.properties (e.g., ast.proxy.avaloq.session.user=avaloq). 
    This allows for readiness checks and initial evaluations before the first test run, which defines the specific Avaloq user you'll be using.
