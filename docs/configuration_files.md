# Configuration Files

After deploying the application, you can configure it by uploading configuration files in the [Configuration](admin.md#configuration) section. Admin role is required to upload these files.
Another option is to mount these files as volumes or secrets in the [deployment](deployment.md#mounting-of-astproperties-optional). The application will read the configuration files from the mounted path.

## Required configuration files

### License file
The license file is required for the tests to run. You can upload the license file in the [Configuration](admin.md#configuration) section or mount it as a volume or secret in the deployment. 
The file will be provided by Synpulse and it is usually named ast.license.

### Global settings file
The global settings file configures the Avaloq business user. This file is required for the tests to run.
This file can be provided by Synpulse and it is usually named ast.globalsettings. You can upload the global settings file in the [Configuration](admin.md#configuration) section or mount it as a volume or secret in the deployment.
Example of the content of the global settings file:
```properties
# ###########################################################
# AST Global Setting File
#  
# $Id$
# ###########################################################

# ###########################################################
# AST
# ###########################################################

ast_user1 = avaloq
ast_user2 = avaloq
ast_bu1 = AAA
ast_bu2 = AAA



# ###########################################################
# 
# ###########################################################
```

### Properties file
The properties file is mainly used for configuring the connection to the Avaloq. This file is required for the tests to run.
This file can be provided by Synpulse and it is usually named ast.properties. You can upload the properties file in the [Configuration](admin.md#configuration) section or mount it as a volume or secret in the deployment.
Example of the Avaloq connection in properties file:
```properties
avq.conn.INSTANCE_DEV.host = 100.10.10.20
avq.conn.INSTANCE_DEV.port = 1523
avq.conn.INSTANCE_DEV.sid = sid
avq.conn.INSTANCE_DEV.user = k
avq.conn.INSTANCE_DEV.password = password

avq.conn.INSTANCE_PROD.host = 100.10.10.21
avq.conn.INSTANCE_PROD.port = 1523
avq.conn.INSTANCE_PROD.sid = sid
avq.conn.INSTANCE_PROD.user = k
avq.conn.INSTANCE_PROD.password = password
```
!!! info
    The INSTANCE_DEV and INSTANCE_PROD are names which will be shown in the application when you select the Avaloq instance to run the tests against. 
    You can change these names to whatever you want, but make sure that the names are unique and do not contain spaces.

### Report template files (optional)
Upload of these files is optional as default templates are provided.
The report template files ( `report_template_for_html_report.xslt` and `report_template_for_excel_report.xls`) are used for customizing the content and format of Excel reports and HTML reports. For more information on report customization, see the [Reports](reports.md) documentation.
You can upload the report template files in the [Configuration](admin.md#configuration) section or mount them as volumes or secrets in the deployment.