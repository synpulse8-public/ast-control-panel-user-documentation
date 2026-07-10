# System requirements

The solution uses these technologies and frameworks:

- React 18
- Java 17 with Spring Boot 3.5.x
- Oracle DB(Client owned)

### Minimal requirements for deployment

#### Environment
- Environment capable of running a docker container.
- Modern web browser on the client side (e.g., Chrome, Edge) to access the AST Control Panel

#### Network
- Avaloq instance is reachable from the machine where AST is deployed.
- Port 8080 by default, should be available for the application to run. This can be configured in the deployment file. See the [Deployment](deployment.md) section for more details.

#### Memory
- Minimum 8 GB RAM recommended for standard deployments.
- RAM requirements scale with usage — the following factors influence memory consumption: Number of parallel executions scheduled. 

!!! info
    As a general guideline: for environments with up to 5 parallel executions, 8 GB is sufficient. For higher parallelism, 16 GB or more is recommended. Contact Synpulse for a sizing consultation if needed.

#### Database
- Oracle DB (client-owned), minimum 10 GB initial storage.
- Database size will grow over time based on the volume of test executions and stored results.

!!! info
    Regular database maintenance is strongly recommended. AST provides a built-in maintenance feature to delete obsolete test cases, execution results, and related data. It is the client's responsibility to perform periodic maintenance to prevent excessive database growth and potential performance degradation. See the [Maintenance](admin.md#maintenance) section of this documentation for details. 