# Frequently asked questions
#### After setting a custom context path, the application is not working properly, how to fix it?
Make sure that your ingress or load balancer is configured to handle cookies correctly. 
The context path requires a cookie to work properly, so if the cookies are not handled correctly, the application may not work as expected.
For more information on how to set a custom context path, see the [Setting a custom context path](deployment.md#setting-a-custom-context-path) section in the [Deployment](deployment.md) documentation.

---

#### Scheduled executions are not running, how to fix it?
Restart the pod where the application is running. This will trigger the scheduler to start working again and the scheduled executions should run as expected.

---

#### Can the Avaloq DB connection credentials be stored as a secret?
At the moment it is not possible to store the secrets separately. 
You need to store the ast.properties file as a whole in a secret if you want to use secrets for storing the database connection details.
For more information see [Mounting of ast.properties (Optional)](deployment.md#mounting-of-astproperties-optional) section in the [Deployment](deployment.md) documentation.

---

#### Can the Applications DB credentials be stored as a secret?
Yes, if you decide to use environment variables for setting up the database connection, you can store the password in a secret and reference it in the `Values.yaml` file. For more information see [Setting up environment variables](deployment.md#setting-up-environment-variables) section in the [Deployment](deployment.md) documentation.
If you decide to use a custom configuration file([Addional config file](deployment.md#using-the-spring-additional-configuration-file)) for these variables, then you need to store the whole file as a secret if you want to use secrets for storing the database connection details. For more information refer to the [Deployment](deployment.md) documentation.

---

#### I have set up my Jira Xray / ALM integration, but the solution is throwing a certificate/ssl error. How to resolve this issue?
Most probably your Xray/ALM instance is using a self-signed certificate, so you need to import the certificate in the java keystore/truststore of the application.
You can do this via setting correct environment variables in your deployment file. For more information on how to do this, see the [Setting up a truststore for SSL](deployment.md#setting-up-a-truststore-for-ssl) or [Setting up a keystore for SSL](deployment.md#setting-up-a-keystore-for-ssl).

---

#### After Keycloak activation, I am getting 401 Unauthorized error when trying to access the API, how to fix it?
After the activation of the Keycloak profile, the authentication endpoint is disabled and each request to the API will require a Bearer token. For more information on how to set up Keycloak integration, see the [Keycloak documentation](keycloak.md).

---

#### After the setting of the context path, the application is not working properly.
Make sure that your ingress,load balancer or reverse proxy is configured to handle cookies correctly. 
The context path requires a cookie to work properly, so if the cookies are not handled correctly, the application may not work as expected. 
For more information on how to set a custom context path, see the [Setting a custom context path](deployment.md#setting-a-custom-context-path) section in the [Deployment](deployment.md) documentation.

---

#### The application experienced a crash and the test run logs are not accessible, how to retrieve the logs?
Unfortunately, in this case, the logs are not retrievable as they were not saved in the database. 
The logs are stored in the database only after the test run is finished, so if the application crashes during the test run, the logs will not be saved and therefore not accessible.
