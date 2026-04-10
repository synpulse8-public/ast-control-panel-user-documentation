# Deployment

## Using provided helm chart

### Minimal Settings for Deployment 

In your own  `Values.yaml` you have to set the `imageAst` property and potentially also `imagePullSecrets` if your registry requires authentication. 
The values that are not set in your `Values.yaml` will be taken from the default `Values.yaml` provided in the helm chart. 
Please refer to the `Values.yaml` file in the provided helm chart for more details.

For the solution to be able to start 3 environment variables need to be set:
```properties
SPRING_DATASOURCE_URL=jdbc:oracle:thin:@your_db_address:1521/YOUR_SCHEMA

SPRING_DATASOURCE_USERNAME=your_username

SPRING_DATASOURCE_PASSWORD=your_password
```
Then you need to manually upload your license file, ast.globalsettings and your ast.properties(alternatively see [Mounting of ast.properties](#mounting-of-astproperties-optional)) file in admin section of the app.

If you would need further settings like ingress, resources, port, replicas and others please refer to the `Values.yaml` file in the provided helm chart. For environment variables settings, secret settings, SSL and other please refer to the sections below.
### Setting up environment variables
#### Using env and envSecrets
To set up environment variables for the application, you can use the `env` section in the `Values.yaml` file of the helm chart. Here is an example of how to set up the required environment variables:

```yaml
env:
  SPRING_DATASOURCE_URL: "jdbc:oracle:thin:@your_db_address:1521/YOUR_SCHEMA"
  SPRING_DATASOURCE_USERNAME: your_username
```
For environment variables that contain sensitive information, such as `SPRING_DATASOURCE_PASSWORD`, it is recommended to use Kubernetes secrets. You can create a secret and then reference it in the `Values.yaml` file. Here is an example of how to reference it:
```yaml
envSecrets:
  SPRING_DATASOURCE_PASSWORD:
    secretName: ast-control-panel-db-password-secret
    key: password
```
#### Using the spring additional configuration file
Alternatively, you can set up environment variables by using an additional configuration file. You can create a file named `ast-control-panel.properties` and add your environment variables in the following format:
```properties
spring.datasource.url=jdbc:oracle:thin:@your_db_address:1521
```
Then, you can mount this file as a volume in your deployment and specify the path to this file in the `SPRING_CONFIG_ADDITIONAL_LOCATION` environment variable. Here is an example of how to do this in the `Values.yaml` file:
```yaml
env:
  SPRING_CONFIG_ADDITIONAL_LOCATION: "/deployment/ast-control-panel.properties"
```
You can mount the file as a secret or you can put it into tge ```files``` directory of the helm chart and mount it as a config map. The config map name is ```controlpanel-config``` nad the subpath is ```additional_config.properties```. For more detail see [Mounting Volumes](#mounting-volumes)

### Mounting volumes 
To mount a volume , you can use the `volumes` and `volumeMounts` sections in the `Values.yaml` file. Example of mounting a secret and a config map as volumes:
```yaml
# Volume mounts for the container. Optional.
volumeMounts:
    - name: saml-truststore
      mountPath: /deployments/data/saml-truststore
      readOnly: true
    - name: astconfig-volume
      mountPath: /home/jboss/ast.properties
      subPath: ast.properties
      readOnly: true
# Volumes for the pod. Optional.
volumes:
    - name: saml-truststore
      secret:
        secretName: samlTruststore
    - name: astconfig-volume
      configMap:
        name: astconfig
```
#### Mounting of ast.properties (Optional)
If you want to mount(For more detail see [Mounting Volumes](#mounting-volumes)) the ```ast.properties``` instead of uploading them manually in the admin section of the app, you can use the provided config map. 
You need to put the file you want to mount into the ```files``` directory of the helm chart and then specify the name of the file in the `Values.yaml` file.
For mounting of the ```ast.properties``` you can use the core-config config map. For example, for ```ast.properties``` the subpath is ```ast.properties```.
!!! warning "Important"
    If you want to use this option, you need to specify the path to the properties file in the `ast.core.properties-file.path` property in the admin UI or as an environment variable. 
    Check the [Configuration](admin.md#configuration) section for more details.

### Setting up a keystore for SSL
You can do this by creating a secret with the keystore file and then mounting it as a volume in your deployment. Also you need to setup some environment variables to specify the path to the truststore and its password. 
Here is an example of how to do this in the `Values.yaml` file:
```yaml
# Environment variables for the container. Optional. Add as needed.
env:
  SERVER_SSL_ENABLED: true
  SERVER_SSL_KEY_STORE_TYPE: JKS  #depends o yor preference
  SERVER_SSL_KEY_STORE: file:/deployments/data/ssl-keystore/keystore.jks
  SECURITY_REQUIRE_SSL: true

# Environment variables from Kubernetes secrets. Optional. Add as needed.
envSecrets:
  SERVER_SSL_KEY_STORE_PASSWORD:
    secretName: ssl-password-secret
    key: password

# Volume mounts for the container. Optional.
volumeMounts:
  -   name: ssl-keystore
      mountPath: /deployments/data/ssl-keystore
      readOnly: true

# Volumes for the pod. Optional.
volumes:
  -   name: ssl-keystore
      secret:
        secretName: your-ssl-keystore-secret-name
```

### Setting up a truststore for SSL
You can set up a truststore for SSL by creating a secret with the truststore file and then mounting it as a volume in your deployment(similar as [Setting up a keystore for SSL](#setting-up-a-keystore-for-ssl) ). 
Also, you need to set up some environment variables to specify the path to the truststore and its password. Here is an example of how to do this in the `Values.yaml` file:
```yaml
# Environment variables for the container. Optional. Add as needed.
env:
  JAVAX_NET_SSL_TRUSTSTORE: /deployments/data/truststore.p12

# Environment variables from Kubernetes secrets. Optional. Add as needed.
envSecrets:
  JAVAX_NET_SSL_TRUSTSTORE_PASSWORD:
    secretName: ssl-password-secret
    key: password
# Volume mounts for the container. Optional.
volumeMounts:
  -   name: ssl-truststore
      mountPath: /deployments/data/truststore.p12
      readOnly: true

# Volumes for the pod. Optional.
volumes:
  -   name: ssl-truststore
      secret:
        secretName: your-ssl-truststore-secret-name
```

Another option is to use just the cacerts.
```yaml
# Environment variables for the container. Optional. Add as needed.
env:
  JAVAX_NET_SSL_TRUSTSTORE: /deployments/data/cacerts
# Volume mounts for the container. Optional.
volumeMounts:
  -   name: cacerts
      mountPath: /deployments/data/cacerts
      readOnly: true

# Volumes for the pod. Optional.
volumes:
  -   name: cacerts
      secret:
        secretName: cacerts-secret-name
```

### Setting a custom context path
By default, the application is served at the root context path. If you want to serve the application at a custom context path, you can set the `context-path` property in the Values.yaml file. 
!!! warning "Important"
    The context path setting requires a cookie to work properly. If you set a custom context path, make sure to configure your ingress or load balancer to handle cookies correctly. 
    Otherwise, you may encounter issues with the application. The cookie looks like this: contextPath=/your-context-path

### Other settings
For other possible settings like ingress, resources, port, replicas and others please refer to the `Values.yaml` file in the provided helm chart.

## Using docker
You can run the application in the command line using docker. The app should be deployed like this only for test purposes. For minimal settings you need to set up the same environment variables as for the helm chart deployment. Here is an example of how to run the application using docker with the required environment variables:
```bash
docker run -e SPRING_DATASOURCE_USERNAME=your_username -e SPRING_DATASOURCE_URL=jdbc:oracle:thin:@your_db_address:1521/YOUR_SCHEMA -e SPRING_DATASOURCE_PASSWORD=your_password --network host path/to/your/iamge/ast-control-panel:image_tag
```
After the application is started you need to upload your license file, ast.globalsettings and your ast.properties (alternatively see [Mounting of ast.properties](#mounting-of-astproperties-optional)) file in admin section of the app.

As in the helm chart deployment, you can also set up environment variables using an additional configuration file and mount it as a volume in the docker container. 
You can use the same example of the `ast-control-panel.properties` file and specify the path to this file in the `SPRING_CONFIG_ADDITIONAL_LOCATION` environment variable.

## Using your own deployment setup
You can also deploy the application on your own without using the provided helm chart.
In this case we can provide limited support as we have no knowledge about your infrastructure and deployment setup.

