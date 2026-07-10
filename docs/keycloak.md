# Keycloak

The Keycloak profile allows you to integrate the AST Control Panel with Keycloak for user authentication and role management.


### Prerequisite

- Currently the app does not redirect to the Keycloak login page; the authentication process is handled outside of the application. This means that you need to have a service which adds the authorization header with a valid Bearer token to each request when accessing the AST Control Panel. You can ask your Keycloak admin for more details, as we have no knowledge about your setup.

### Setup
!!! info
    All of these environment variables need to be set up in the same place as the variables mentioned in the [Minimal deployment settings](deployment.md#minimal-settings-for-deployment)

1. The Keycloak profile must be set as an env variable in your deployment file:
    ```properties
    SPRING_PROFILES_ACTIVE=prod,api-docs,keycloak
    ```
!!! warning "Important"
    prod and api-docs profiles have to be specified also when using Keycloak; otherwise, the app would start in dev profile, which could corrupt the data in your db.

2. To set up OAuth2 correctly these two variables need to be set with your settings:
    ```properties
    SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI=https://your.keycloak.address.com/realms/your_realm_name

    SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_JWK_SET_URI=https://your.keycloak.address.com/realms/your_realm_name/openid-connect/certs
    ```
!!! info
    For the `SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_JWK_SET_URI`, use the JWK URL provided by your Keycloak instance. Usually it is the URL with /certs at the end.

3. Other settings that have to be configured in the deployment file:
    

| Configuration key                                   | Description                                                                 |
|-----------------------------------------------------|-----------------------------------------------------------------------------|
| `AST_CONTROL_PANEL_SSO_USERNAME_CLAIM`              | Specifies the claim from which the username will be extracted. The username must be unique. |
| `AST_CONTROL_PANEL_SSO_ROLES_CLAIM`                 | Specifies the claim from which the roles will be extracted.                 |
| `AST_CONTROL_PANEL_SSO_ROLE_MAPPING_ROLE_ADMIN`     | Specifies which roles from the claims will be mapped to the **admin** role in the application. Multiple roles can be configured. |
| `AST_CONTROL_PANEL_SSO_ROLE_MAPPING_ROLE_USER`      | Specifies which roles from the claims will be mapped to the **user** role in the application. Multiple roles can be configured. |

Example:
```properties
AST_CONTROL_PANEL_SSO_USERNAME_CLAIM=preferred_username

AST_CONTROL_PANEL_SSO_ROLES_CLAIM=sourceGroups

AST_CONTROL_PANEL_SSO_ROLE_MAPPING_ROLE_ADMIN=admin,admin2

AST_CONTROL_PANEL_SSO_ROLE_MAPPING_ROLE_USER=user,user2
```

4. If your Keycloak instance is using self-signed certificates, you need to import the certificate in the Java truststore of the application.
See the [Setting up a truststore](deployment.md#setting-up-a-truststore-for-ssl) section.

### Behavior of the application with Keycloak profile active

After the Keycloak profile is activated, the authentication process is entirely handled by Keycloak. This leads to the following changes in the behavior of the application:

- You can navigate directly to the application and you will be logged in right away.

- The authentication endpoint is disabled and each request to the API will require a Bearer token. The API functionality remains the same but you must provide a Bearer token in the Authorization header. You can get the Bearer token from your Keycloak instance (You need to ask your Keycloak admin for more details as we have no knowledge about your setup).

- The user management part is not available when the Keycloak profile is active. Users are managed in Keycloak and not in the app. If you want to add a new user, you need to do it in Keycloak and not in the app.

- If there was already a user created via AST with the same username as a Keycloak user, this user is modified to a Keycloak user automatically.

- If an AST user is deactivated or was deleted and a Keycloak user with the same username logs in, the Keycloak user has precedence, as the data in JWT claims are taken into consideration.

### Behavior of the application with Keycloak profile deactivated after it was active

If you deactivate the Keycloak profile after it was active, the application will switch back to the default authentication process.

The users that were converted to Keycloak users will be converted back to normal users, and they will be able to log in again with their old credentials.

The users that were created as new users in Keycloak while the profile was active will be deleted. If you want to keep these users, you need to create them again in the app after deactivating the Keycloak profile.





 
