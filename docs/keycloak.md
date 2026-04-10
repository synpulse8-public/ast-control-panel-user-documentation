# Keycloak

The keycloak profile allows you to integrate the AST Control Panel with Keycloak for user authentication and role management.


### Prerequisite
- Currently the app does not redirect to the Keycloak login page, the authentication process is handled outside of the application. This means that you need to have a service which adds the authorization header with a valid Bearer token to each request when accessing the AST Control Panel. You can ask your Keycloak admin for more details as we have no knowledge about your setup.
### Setup
!!! info
    All of these environment variables need to be set up in the same place as the variables mentioned in the [Minimal deployment settings](deployment.md#minimal-settings-for-deployment)

1. Firstly the keycloak profile must be set as an env variable in your deployment file:
    ```properties
    SPRING_PROFILES_ACTIVE=prod,api-docs,keycloak
    ```
!!! warning "Important"
    prod and api-docs profiles have to be specified also when using keycloak otherwise the app would start in dev profile which could corrupt the data in your db

2. To set up oauth2 correctly these two variables need to be set with your settings:
    ```properties
    SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI=https://your.keycloak.address.com/realms/your_realm_name

    SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_JWK_SET_URI=https://your.keycloak.address.com/realms/your_realm_name/openid-connect/certs
    ```
!!! info
    For the `SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_JWK_SET_URI` use the JWK URL provided by your keycloak instance. Usually it is the url with /certs at the end. 

3. Other settings that have to configured in an deployment file:
    ```properties
    AST_CONTROL_PANEL_SSO_USERNAME_CLAIM=preferred_username

    AST_CONTROL_PANEL_SSO_ROLES_CLAIM=sourceGroups

    AST_CONTROL_PANEL_SSO_ROLE_MAPPING_ROLE_ADMIN=admin,admin2

    AST_CONTROL_PANEL_SSO_ROLE_MAPPING_ROLE_USER=user,user2
    ```
   
    `AST_CONTROL_PANEL_SSO_USERNAME_CLAIM `- specifies the claim from which the username will be extracted. This username has to be unique

    `AST_CONTROL_PANEL_SSO_ROLES_CLAIM` - specifies the claim from which the roles should be extracted

    `AST_CONTROL_PANEL_SSO_ROLE_MAPPING_ROLE_ADMIN:` specifies which roles from the claims will be mapped as the admin role in the app. It can be more then one role.

    `ROLE_MAPPING_ROLE_USER` specifies which roles from the claims will be mapped as the user role in the app. It can be more then one role. 

4.  If your keycloak instance is using self-signed certificates you need to import the certificate in the java truststore of the application.
See the [Setting up a truststore](deployment.md#setting-up-a-truststore-for-ssl) section.

### Behavior of the application with keycloak profile active
After the keycloak profile activation the authentication process is entirely handled by keycloak. Which will lead to the following changes in the behavior of the application:

- You can navigate directly to the application and you will be logged in right away.

- The authentication endpoint is disabled and each request to the API will require a Bearer token. The API functionality remains the same but you must provide a Bearer token in the Authorization header. You can get the Bearer token from your Keycloak instance (You need to ask you Keycloak admin for more details as we have no knowledge about your setup).

- The user management part is not available when keycloak profile is active. Reason being that the users are managed in keycloak and not in the app. So if you want to add a new user you need to do it in keycloak and not in the app.

- If there was already a user created via ast with the same username as is being used by a keycloak user this user modified to a keycloak user automatically.

- If an ast user is deactivated or was deleted and a keycloak user with the same user name logs in, the keycloak user has precedence as the data in JWT claims are taken into consideration.



 
