# Input Operations

Input operations modify the test object.

### Order creation

The **`new-doc`** operation creates a **new order** in the Avaloq Banking System, supporting all interactions a user would perform in the graphical user interface. The process follows these steps:

- **Create the Order**: A new order is created by executing either an initial workflow action or a context action.
- **Set Order Fields**: You can set the order's fields or execute its methods. Any property defined in the DDIC (Data Dictionary) of the business type can be configured. Values can be entered just as a user would in the GUI, for example, using expressions like `today` or `-1v` for date fields.
- **Close the Order**: The order is then closed by executing a workflow action to store or proceed with it.
- **Retrieve Properties**: You can retrieve properties of the order after its creation.

If order validation failures occur when the final workflow action is executed, they can be overridden using the "add-override" command.


***`<input>` | `<new-doc ...>`*** - Creates a new order using an initial workflow action and a close workflow action.

|Attributes|Description|
|---|---|
|*type* (IN)|Avaloq Meta Type|
|*init-action* (IN)|Initialization workflow action|
|*ctx-action* (IN)|Performs a context action that creates a new order (alternative to the parameter init-action).|
|*close-action* (IN)|Closing workflow action|
|*set* (IN)|Sets 1...n properties of a specific Avaloq Meta Type|
|*scriplet* (IN)|A piece of Avaloq Script code, that is executed. The alias "tdoc" can be used to reference the current order.|
|*do* (IN)|Performs an action|
|*add-override* (IN)|Sets an entry to the add-override table with an optional comment attribute.|
|*script-action* (IN)|Avaloq script function which must return a mem_doc reference. This attribute replaces init-action.|
|*modify-object* (IN)|The id of the object to be modified in this order, instead of creating a new object.|
|*expect-failure-msg* (IN)|Possibly specify a message which is expected in this block of operations (expect-failure-msg). You can use % to specify any sequence of chars.|
|*out* (OUT)|Provides access to all fields of the generated order (DOC). Typically the identifier of the order is retrieved.|


!!! example "Example code"
    ~~~xml
    <input>
    <descn>Creating new payment and proceed</descn>
    <new-doc type="pay" init-action="open_dom" close-action="13">
    <set property="pay_method_id" value="post_esr" />
    <set property="amount" value="123.45" />
    <set property="curry_id" value="CHF" />
    <set property="deb_macc_id" value="10001401.2003" />
    <set property="benef_acc" value="01-4544-9" />
    <set property="benef" value="Credit Suisse" />
    <out property="id" select="payment" />
    </new-doc>
    </input>
    ~~~

<br>

### Manipulating DDIC List Entries

When setting properties on a DDIC list (e.g., realvalu_list), AST automatically adds a row to the list if the row does not exist yet, making the operation transparent to the user.

You can directly manipulate list contents using the `<set property=...>` or `<do stmt=...>` actions, provided the underlying Data Dictionary (DDIC) supports it.

|Action Type|Example Code|Description|
|---|---|--|
|Adding/Editing|`<set property="realvalu_list(1).mtd_id" value="external" />`|Sets a property on a list entry specified by its index (List index of the list entry to be edited).|
|Adding Element (Simple)|`<do stmt="book_list.add" />`|Adds a new element to the list.|
|Adding Comlex Element|`<do stmt="cost_entry(1).entry_list.add(5, $costs, 1)" />`|Adds a list element with additional arguments.|
|Removing Element|`<do stmt="chld_list.remv(1)" />`|Removes the item at the given index.|
|Clearing List|`<do stmt="mkt_rel_list.clear" />`|Clears the whole list.|

### Scriptlets

The doc and new-doc operations support a scriptlet parameter, which allows you to integrate Avaloq Script code directly into the order generation process. This parameter is particularly useful for situations where you need to reference data from the order itself. For example, you can use a scriptlet to fill an order's external reference number with its own ID.

~~~xml
<input> 
  <descn> 
    Check issuing: doc_id is used as check-nr and must be filled in the external reference number of the payment
  </descn>
  <new-doc type="pay" init-action="open_dom" close-action="13">
    <scriptlet>tdoc.extl_ref_nr := tdoc.doc_id</scriptlet>
    <set property="pay_method_id" value="dom" />
    <set property="amount" value="1000.00" />
    <set property="curry_id" value="CHF" />
    ...
  </new_doc>
</input>
~~~

### Overrides and User-Interface Messages

Control over the occurrence of user interface messages and the ability to override these messages is granted by the `ui-msg`element. A message can be matched by providing a pattern. Alternately, in case of an override message, matching can be performed by passing a `code_override` element (ID, name, or `intl_id`).

<br>

***`<input>` | `<new-doc ...>` | `<ui-msg ...>`*** - Verifies User interface messages and overrules override messages.

|Attributes|Description|
|---|---|
|*text* (IN)|Pattern against with an occurring message is compared. The pattern may contain % as wildcard.|
|*override* (IN)|Name, `user_id` or `intl_id` of a override|
|*accept-override* (IN)|Boolean value (`true`/`false`) which decides whether an override is accepted or not.|
|*expect* (IN)|Boolean value (`true`/`false`) which decides whether specified message or override is expected by the testcase. The System reports a failure if no such message or override occurs while execution. This attribute is true by default.|

### Verifying Mandatory Error Messages

When using `<ui-msg>` without accept-override, you are verifying the message. If `expect="true"` (the default), AST enforces that this message appears to confirm validation is working as expected.

!!! example "Example: Verify Failure Message"
    ~~~xml
    <ui-msg text="Mandatory: Business Partner%" expect="true" />
    ~~~

### Accepting or Denying Overrides

If the message is an override (a warning that can be accepted or denied) , set `accept-override` to `true` to proceed or false to deny. You can match the override using a text pattern or directly using its ID.

!!! example "Example: Accept by Pattern"
    ~~~xml
    <ui-msg text="'Forward 1 Date' is in the past!%" accept-override="true" />
    ~~~

!!! example "Example: Deny by Variable"
    ~~~xml
    <variable name="ui_text" avaloq-expr="'''Forward 1 Date'' is in the past!%'" />
    <ui-msg text="{$ui_text}" accept-override="false" />
    ~~~

!!! example "Example: Accept by Override ID"
    ~~~xml
    <ui-msg override="5042" accept-override="true"/>
    ~~~

!!! example "Example: Accept Any Override"
    ~~~xml
    <ui-msg text="%" accept-override="true" expect="false" />
    ~~~

### Order processing

The order processing operation works similarly to the `new-doc` operation, but it's designed to process an existing order. You can identify the specific order you want to work with by either its **`doc_id`** or an **order key**, such as its **external reference number**.

<br>

***`<input>` | `<doc ...>`*** - Processes an existing order.

|Attributes|Description|
|---|----|
|id (IN)|Id of order to be processed. Alternatively an order keycan be used to identify the order to be processed. <br> (All other parameters see the description of the new-doc operation.)|

!!! example "Example code:"
    ~~~xml
    <input>
    <descn>Executing workflow action proceed</descn>
    <doc id="$docId" action="proceed" />
    </input>
    ~~~

<br>

### File processing

The **file input operation** allows you to **upload** or **process a file** within the Avaloq Banking System.

- **Upload**: This action transports and stores a file in the DB IO directory on the Oracle instance.
- **Process**: This action feeds the file into the Avaloq file processing engine, which generates a set of messages and orders.

This operation's processing mode is only compatible with standard Avaloq file types. It is not suitable for processing custom file types, which are handled by program task 927. For custom files, you should use the store action to upload the file and then use the dedicated task (ID 927) to process it.

<br>

***`<input>` | `<file ...>`*** - Upload a file to the Avaloq DB IO directory and process it.

|Attributes|Description|
|---|---|
|*name* (IN)|The filename with which the file is to be handled.|
|*type* (IN)|The type of the file.|
|*action* (IN)|The action to be performed on the file, can either be"store" or "process".|
|*payload* (IN)|The full content of the file.|
|*action* (IN)|Workflow action|
|*out* (OUT)|Indexed access to the generated messages and orders of the file as well as to all fields of the data dictionary FILE_UPL.|

!!! example "Example code:"
    ~~~xml
    <input>
    <file type="KTB20" name="$file_name" action="process">
    <payload>...<payload>
    <out property="msg_id(1)" select="msg_1" />
    <out property="doc_id(2)" select="doc_2" />
    </file>
    </input>
    ~~~

<br>

### Message processing

The **message input operation** allows you to **process messages** within the Avaloq Banking System. This function simulates an external system, sending messages directly to the **Avaloq Messaging Interface (AMI)**, bypassing the Avaloq MQ Bridge.

<br>

***`<input>` | `<msg ...>`*** - Upload a message to the Avaloq Message Interface (AMI) and process it.

|Attributes|Description|
|---|---|
|*network* (IN)|The AMI network of the message (mandatory).|
|*type* (IN)|The message type (optional).|
|*status* (IN)|The expected message status (optional).|
|*payload* (IN)|The raw content of the message.|
|*out* (OUT)|Access to the DDIC MEM_MSG, specifically to thegenerated order with the property "doc_id".|

!!! example "Example code:"
    ~~~xml
    <input>
    <msg network="SIC" type="D10" status="prc">
    <payload>
    <![CDATA[#D10
    <02>002301
    <03>{$traref}
    ...]]>
    </payload>
    <out select="docId" property="doc_id" />
    <out select="doc_status" property="doc.wfc_status_id" />
    </msg>

    </input>
    ~~~

<br>

### Mass pay operation

The mass pay input operation allows and creates the process of mass payment in Avaloq Banking System.

<br>

***`<input>` | `<mass-pay ...>`*** - Executes Mass pay inside an AST test script.

|Attributes|Description|
|---|----|
|*mass-pay-wfc* (IN)|Mass payment of workflow control action id of "mem_doc_mass".|
|*init-action* (IN)|Initialization workflow action|
|*close-action* (IN)|Closing workflow action|
|*form-id* (IN)|Mass payment of form id of "mem_doc_mass"|
|*descn* (IN)|Define the description of the payment (optional)|
|*select* (IN)|Return value of executed Avaloq Script (required)|
|*payment* (IN)|Define the fields and values of "mem_doc_pay" for payment operation.|
|*add-override* (IN)|Sets an entry to the add-override table with an optional comment attribute.|
|*ui-msg* (IN)|Control over the occurrence of user interface messages and the ability to override these messages is granted by the ui-msg element.|
|*scriptlet* (IN)|Some piece of Avaloq Script code, that is executed. The alias "tdoc" can be used to reference the current order.|

!!! example "Example code:"
    ~~~xml
    <input>
    <mass-pay select="sqlResult" form-id="956" mass-pay-wfc="1" init-action="10001" close-action="10167" descn="Payment test">
    <payment>
    <set property="order_type_id" value="1123" />
    <set property="benef_macc_id" value="#168810" />
    <set property="medium_id" value="1" />
    <set property="asset_id" value="#3942" />
    <set property="amount" value="1200" />
    <set property="deb_macc_id" value="#1101963" />
    </payment>
    <add-override name="asset_closed" check="false" />
    <ui-msg override="asset_closed" text="%" accept-override="true" expect="false" />
    <scriptlet> session.debug('Payment'||tdoc.doc_id); </scriptlet>
    </mass-pay>
    </input>
    ~~~

<br>

### Avaloq Script execution

The AST tool can execute sections of the Avaloq scripting language, which is useful for tasks such as string concatenation or retrieving information not available from a standard DDIC (Data Dictionary). However, you should avoid using Avaloq Script if a different method is available.

Syntax and Usage The input for this operation must be valid Avaloq Script; otherwise, an error will be thrown.

- **Return Statement**: The syntax is extended to include a return statement, which lets you assign a value to an AST variable.
- **AST Variables**: Within the Avaloq Script code, you can reference AST variables by using a dollar sign ($) as a prefix to the variable name.

This feature provides a way to interact with data and perform custom logic when standard operations are insufficient.

<br>

***`<input>` | `<avaloq-script ...>`*** - Executes Avaloq Script inside an AST test script

|Attributes|Description|
|---|---|
|*avaloq-script* (IN)|Avaloq Script code to be executed.|
|*select* (OUT)|Return value of executed Avaloq Script (optional).|

!!! example "Example code:"
    ~~~xml
    <variable name="sic_s30" />
    <input>
    <descn>Generating response S30 message for networkSIC</descn>
    <avaloq-script select="sic_s30">
    declare
    l_s30 text;
    begin
    l_s30 := '#S30' || chr(10) || '&lt;12&gt;098719' || chr(10) || '&lt;13&gt;' || msg($outMsgId).extl_ref_nr || chr(10)|| '&lt;92B&gt;' ||to_char(lookup.date($tag_92B),'YYYYMMDD') || chr(10) || '&lt;94&gt;01' || chr(10);
    return l_s30;
    end;
    </avaloq-script>
    </input>
    ~~~

<br>

### JavaScript execution
!!! note
    Starting with AST release 2.4, you can execute JavaScript code within your test scripts. The operation requires a valid JavaScript input; otherwise, an error will be thrown.

AST variables can be bound to JavaScript variables as strings using the parameter attribute. Since AST 3.1, you can also access certain AST APIs via the JavaScript Nashorn engine.

<br>

***`<input>` | `<java-script ...>`*** - Executes Java Script inside an AST test script

|Attributes|Description|
|---|---|
|*script* (IN)|Specifies a script operation. With the lang attribute the script language can be defined. As JavaScript is the only language currently implemented this attribute is optional.|
|*parameter* (IN)|Binds an AST variable specified by value to theJavaScript variable specified by name.|
|*payload* (IN)|JavaScript code. AST variables will be expanded as strings in the script code ({$l_var}, same as in input msg operation).|
|*out* (OUT)|Values of string variables in JavaScript can be passed to AST variables.|


!!! example "Example code (1):"
    ~~~xml
    <input>
    <script lang="javascript">
    <parameter name="alpha" value="$a" />
    <parameter name="betha" value="$b" />
    <parameter name="gamma" value="$c" />
    <parameter name="delta" value="$d" />
    <payload>
    var result = 10;
    var sum;
    var product;
    if (delta == null) {
    result = alpha + betha + gamma;
    }
    sum = Number(alpha) + Number(betha) + Number(gamma);
    product = alpha * betha * gamma;
    result;
    </payload>
    <out property="result" select="l_res" />
    <out property="sum" select="l_sum" />
    <out property="product" select="l_prod" />
    </script>
    </input>
    ~~~

!!! example "Example code (2)(Example for Javascript Nashorn APIs made available by AST):"
    ~~~xml
    <input>
    <script lang="nashorn">
    <payload>
    <![CDATA[
    var XML = Java.type('ch.sag.ast.util.XML');
    var doc1 = XML.evalDocument("<root><state>stateA</state><command action='actionA'/></root>");
    XML.setXPathInDocument(doc1, "/root/state", "stateB");
    var state = XML.evalXPathFromDocument(doc1, '//state');
    var action = XML.evalXPathFromDocument(doc1,'//command/@action');
    var documentAsString = XML.getStringFromDocument(doc1);
    ]]>
    </payload>
    <out property="state" select="l_state" />
    <out property="action" select="l_action" />
    <out property="documentAsString"select="l_documentAsString" />
    </script>
    </input>
    ~~~

<br>

### PL&#47;SQL execution

You can execute PL/SQL code in AST versions 2.6 and later. This operation requires valid PL/SQL code as input; otherwise, it will throw an error. You can also bind AST variables as string variables to the PL/SQL code using the `parameter` attribute.

<br>

***`<input>` | `<util-plsql ...>`*** - Executes PL/SQL inside an AST test script

|Attributes|Description|
|---|---|
|*avaloq-script* (IN)|PL/SQL script code to be executed.|
|*payload* (IN)|PL/SQL script code. AST variables will be expanded as strings in the script code ({$l_var}, same as in input msg operation).|
|*connection* (IN)|Optionally give a connection name as listed by the ext.conn.* properties in *ast.properties* to execute PL/SQL on a separate database.|
|*Select* (OUT)|Target variable where result is stored (optional).|

!!! example "Example code"
    ~~~xml
    <input>
      <descn>Block of pl/sql with return value</descn>
      <util-plsql select="result" descn="PL/SQL block withreturn">
        <payload>
          declare
            l_res number;
          begin
            -- do some fancy stuff
            select max(id) into l_res from doc;
            ? := 'hello doc ' || l_res;
          end
        </payload>
      </util-plsql>
    </input>
    ~~~

<br>

#### Create Package execution

With AST release 2.7 and later, you can create a set of stored procedures and packages. The input for this operation must be valid SQL code for creating a stored procedure package.

<br>

***`<input>` |  `<util-create-package ...>`*** - Executes a create package SQL inside an AST test script

|Attributes|Description|
|---|----|
|*payload* (IN)|SQL CREATE PACKAGE to be executed.|
|*select* (OUT)|Target variable where result is stored (optional).|

!!! example "Example code"
    ~~~xml
    <input>
      <util-create-package select="sqlResult">
        <payload>CREATE OR REPLACE PACKAGE K.AST$TEST# as
          function get_test_val(
          i_msg varchar2
          ,i_tag varchar2
          ) return varchar2;
          end AST$TEST#;
        </payload>
      </util-create-package>
    </input>
    ~~~

<br>

### XML Creation

As of AST Release 2.8, you can create XML content dynamically within your tests using the `util-xml` operation. This allows you to build XML structures on the fly. To use this operation, the input must be a valid `<util-xml>` node structure, and all its attributes must be valid.

<br>

***`<input>` | `<util-xml ...>`*** - Creates XML inside an AST test script

|Attributes|Description|
|---|---|
|*tag-id* (IN)|The structure of the XML nodes separated by "/", in form of<br>tag-id="parentTag/childTag/childTagOfChildTag/..."<br>(Applicable in "util-xml" and "util-xml-node" tags, seebelow)|
|*newline* (IN)|Set what type of character or special character to be use as newline example "\n" for new line or "\r\n" for carriage return.|
|*indent* (IN)|Set what type of character or special character to be use as tabulator "\t" to indent.|
|*version* (IN)|If attribute is present it determines what will be the version of the XML header. Default value is version="1.0". If "version" and "encoding" are both absent, then no header is written.|
|*encoding* (IN)|If attribute is present it determines what will be the encoding of the XML header. Default value is encoding="utf-8". If "version" and "encoding" are both absent, then no header is written.|
|*clear-tags-if-empty* (IN)|If set to true, any `util-xml-node` without a value will not be generated. If set to false, it will be generated.<br><br>(Only applicable in "util-xml" tag)|
|*avaloq-cond* (IN)|Avaloq script expression that must evaluate to a boolean. The statement waits until the condition is satisfied, or a timeout occurs. For unconditional wait the attribute can be left out.<br><br>(Applicable in "util-xml-node" and "util-xml-value" tags, see below)|
|*select* (OUT)|Target variable where result is stored.|

<br>

***`<util-xml ...>` | `<xml-if ...>`*** - Adds the contained util-xml-node or checks the contained xml-if when the condition is satisfied, otherwise evaluates the same way the `<else>` section if contained.

|Attributes|Description|
|---|---|
|*avaloq-cond* (IN)|Avaloq script expression that must evaluate to a boolean. The statement waits until the condition is satisfied, or a timeout occurs. For unconditional wait the attribute can be left out.|

<br>

***`<util-xml ...>` | `<util-xml-node ...>`*** - Create XML node inside util-xml or to another util-xml-node.

|Attributes|Description|
|---|---|
|*tag-id* (IN)|The structure of the XML nodes separated by "/", in form of *tag-id="parentTag/childTag/childTagOfChildTag/..."*|
|*avaloq-cond* (IN)|The condition to be verified in order for the content to beevaluated and executed.|

<br>

***`<util-xml ...>` |  `<util-xml-attribute ...>`*** - Create attribute/s for util-xml-node. Attribute/s will be added to the last node defined in util-xml-node's tag-id attribute.

|Attributes|Description|
|---|---|
|*name* (IN)|Name of the attribute|
|*value* (IN)|Value of the attribute|
|*avaloq-cond* (IN)|The condition to be verified in order for the content to beevaluated and executed.|

<br>

***`<util-xml ...>` |  `<util-xml-value ...>`*** - Create a text value for util-xml-node. Only one value will be added to the last nodedefined in util-xml-node's tag-id attribute.

|Attributes|Description|
|---|---|
|*value* (IN)|Value of the node|
|*avaloq-cond* (IN)|The condition to be verified in order for the content to beevaluated and executed.|

!!! example "Example code"
    ~~~xml
    <util-xml tag-id="root/rootChild" select="ast_variable" newline="\r\n" indent="\t" clear-tags-if-empty="true" version="1.0" encoding="utf-8">
      <xml-if avaloq-cond="1=1">
        <util-xml-node tag-id="nodeParent" avaloq-cond="$ast_variable is not null">
          <util-xml-node tag-id="nodeParent" avaloq-cond="$ast_variable is not null">
            <util-xml-node tag-id="nodeChild" avaloq-cond="$ast_variable is not null">
              <util-xml-attribute name="attr1" avaloq-cond="1=1">value{$ast_variable}</util-xml-attribute>
              <util-xml-value avaloq-cond="$ast_variable is not null">{$ast_variable}</util-xml-value>
            </util-xml-node>
            <util-xml-node tag-id="nodeChild" avaloq-cond="$ast_variable is not null">
          </util-xml-node>
        </util-xml-node>
      </xml-if>
    </util-xml>
    ~~~

<br>

### Task execution

The `task-exec` operation allows you to execute any task within the Avaloq Banking System. A task is identified by its ID or key. This operation supports setting all parameters accepted by the task, including normal input and pseudo parameters, as well as multiple values for a list parameter.

Tasks can be executed either synchronously (using the `show` or `run` modes) or asynchronously (using `submit` or `print`). For asynchronous execution, you can specify a timeout. If the task does not complete within this time frame, its execution is marked as a failure. This feature is useful for verifying that a task is executed within a specific performance time frame.

Additionally, the `task-exec` operation can return the raw output of an executed report.

<br>

***`<input>` | `<task-exec ...>`*** - Executes an Avaloq task.

|Attributes|Description|
|---|---|
|*task-def* (IN)|Task definition (id, internal id or user id).|
|*task-templ* (IN)|Predefined task template (id, internal id or user id)|
|*sync* (IN)|Wait until task is finished in case of asynchronous execution (true\|false), default is false|
|*mode* (IN)|Sets task execution mode exec (run, show in Avaloq GUI) or submit (submit or print in Avaloq GUI) (exec\|submit) default is exec|
|*timeout* (IN)|Sets the timout in seconds for the task synchronization. Must be specified when sync is set to true.|
|*Set* (IN)|Sets 1...n parameters of the task.|
|*select* (IN)|Target variable where the report buffer is stored.|

!!! example "Example code"
    ~~~xml
    <input>
      <descn>Run credit violations task</descn>
      <task-exec task-def="task_pos_cred_viol_gen">
        <set property="LAYOUT_ID" value="UPD_CRED_VIOL_DFLT" />
        <set property="DATE" value="0" />
        <set property="GEN_DOC" value="+" />
        <set property="asset_class_list_id" value="ass_type:afix" />
        <set property="asset_class_list_id" value="ass_type:lcal" />
        <set property="asset_class_list_id" value="ass_type:lfix" />
        <set property="bp_list_id" value="RS" />
      </task-exec>
    </input>
    <input>
      <task-exec task-def="task_fina_serpil" sync="true"timeout="180">
        <set property="DATE_LIST_ID" value="today" />
        <set property="COLLAT_VAL_TYPE_LIST_ID" value="2" />
        <set property="COLLAT_VAL_TYPE_LIST_ID" value="100" />
        <set property="PARALLEL_EXEC_NR_PRC" value="2" />
      </task-exec>
    </input>
    
    <!-- Report execution -->
    <variable name="l_obj_id" avaloq-expr="'#'||lookup.bp_id('RUFRO')" />
    <variable name="l_layout_id" avaloq-expr="lookup.task_layout_id('TASK_CRM_PROFILE.BP_DFLT')" />
    <variable name="l_content" />
    <input>
      <task-exec task-def="task_crm_profile" select="l_content">
        <set property="layout_list_id" value="$l_layout_id" />
        <set property="obj_list_id" value="$l_obj_id" />
      </task-exec>
    </input>
    ~~~

<br>

### Exec operation

The `exec` operation allows you to execute any command-line program from outside the AST framework. This is a flexible tool you can use to interact with your system, perform actions, and even retrieve data from the outside world.

Because this operation acts outside the main target object—the Avaloq Banking System—it can be used for both modifying and querying data. This means you'll find it in both the *input* and *query* operations sections of the documentation.

<br>

***`<input>` | `<cmd-exec ...>`*** - Executes an external command-line program

|Attributes|Description|
|---|---|
|cmd (IN)|The command-line that is executed.|
|stdin (IN)|Name of the variable which holds the string that is fed to standard in of the external program.|
|stdout (IN)|Name of the variable in which stdout is stored (the content that the external program has written to its standard output)|
|exit-code (IN) |Name of the variable in which the exit code of the executed program is stored.|
|stderr|Name of the variable in which stderr is stored (the content the external program has written to its standard error output)|
|timeout (IN) |Time in seconds after which the external program is terminated (if it did not terminate before).|

!!! example "Example code"
    ~~~xml
    <input>
      <descn>run external application: {$app_name}</descn>
      <cmd-exec cmd="$cmd" stdin="$stdin"stdout="stdout" stderr="stderr" timeout="$timeout" exit-code="exit-code" />
    </input>
    ~~~

<br>

### Report operation

The `report` operation allows you to tag your test cases with dynamic AST values and add custom information to output reports.

For an example of how this information is used in a report, please refer to the "Reporting" tab in the *report.xls* file included in the AST release package.

<br>

***`<report ...>`*** - Additional information to the output reports

|Attributes|Description|
|---|---|
|*tag* (IN)|The tag name for the testcase.|
|*value* (IN)|Dynamic AST value to be tagged.|
|*descn* (IN)|The description for the additional report information.|

!!! example "Example code"
    ~~~xml
    <testcase ...>
    <report tag="DOC" value="{$l_doc}" descn="ExampleOrder" />
    </testcase>
    ~~~

<br>

#### REST Requests

The `rest-group` tag allows you to issue arbitrary REST requests using its embedded rest-execute command.

<br>

***`<input>` | `<rest-group ...>`*** - Executes embedded rest-execute and open-banking commands

|Attributes|Description|
|--- |--- |
|*server* (IN)|Url of server Rest services reside.|
|*rest-execute* (EMBEDDED)|Embedded command to issue specific Rest requests. If a Basic Authorization is needed use the `rest.secured` property in the *ast.properties*.|

!!! example "Example code"
    ~~~xml
    <input>
    <descn>Create prerequisites for Restrequests</descn>
    <rest-group server="http://example.com">
    <rest-execute ...>
    </rest-execute ...>
    </rest-group>
    </input>
    ~~~

<br>

***`<input>` | `<rest-group ...>` | `<rest-execute ...>`*** - Issue arbitrary Rest requests

|Attributes|Description|
|---|---|
|*path* (IN)|Path at the server given as attribute in rest-group command the rest service resides at.|
|*type* (IN)|REST method to use. Currently supported: GET, POST, PUT, DELETE.|
|*result* (OUT)|AST test script variable name the result of the Rest request should be saved to.|
|*jsonPointers*, *jsonPointersSeparator* (IN)| These two attributes define JSON Pointers to be saved in the result and the separator used in the list of json pointers (default; )|
|*clientID*, *clientSecret*, *authEndpoint*, *tokenEndpoint*, *bodyForAuthRequest*, *headersForAuthRequest* (IN)| These attributes are available to configure REST requests needing OIDC|
|*ignoreErrors* (IN)| Configure whether to continue the test execution when the REST call returns failure response,by setting the value to true to false|
|*responseStatus*|AST test script variable stores the REST call response status.|
|(PAYLOAD)|Payload for the Rest request. Especially important for Put and Post requests.|

!!! example "Example code"
    ~~~xml
    <variable name="astVariable" />
    <input>
      <descn>Create prerequisites for Rest requests</descn>
      <rest-group server="http://example.com">
      <rest-execute path="/myPath/mySubPath" type="GET" result="$astVariable">
        Optional payload to send with request
      </rest-execute>
      <rest-execute path="/mySecondPath/mySecondSubPath" type="GET">
      </rest-execute>
      <!-- Example with OpenId Connect: -->
      <rest-execute bodyForAuthRequest="user=astuser&password=password" headersForAuthRequest="Content-Type=x-www-form-urlencoded" authEndpoint="http://openidServer/callbackAuth"  clientID="ClientID1" clientSecret="ClientSecret1" path="post/" result="$astVariable2"tokenEndpoint="http://openIdServer/callbackToken" type="POST">
      </rest-execute>
    </rest-group>
    </input>
    ~~~

<br>

***`<input>` | `<rest-group ...>` | `<rest-header ...>`*** - Define a single HTTP header to include in a REST request

|Attributes|Description|
|---|---|
|*payload* (PAYLOAD)|Only one payload element is supported. Format: HeaderName=Value (e.g., Content-Type=application/json).<br> The examples below show how to add a Content-Type header and a Bearer token to a request.|

!!! example "Example code"
    ~~~xml
    <variable name="token" />
    <input>
      <rest-group server="http://localhost:8080/">
        <rest-header>
          <payload>Content-Type=application/json</payload>
        </rest-header>
        <rest-execute path="/api/authenticate" type="POST"result="$token" jsonPointers="/id_token"ignoreErrors="true">
          <payload>
            {
            "username": "***",
            "password": "***"
            }
          </payload>
        </rest-execute>
        </rest-group>
        <rest-group server="http://localhost:8080/">
          <rest-header>
            <payload>Authorization=Bearer ${token}</payload>
          </rest-header>
          <rest-execute path="/api/all-scripts" type="GET"result="$scripts" ignoreErrors="true" />
        </rest-group>
        <rest-group server="http://localhost:8080/">
          <rest-header>
            <payload>Authorization=Bearer ${token}</payload>
          </rest-header>
          <rest-header>
            <payload>Content-Type=application/xml</payload>
          </rest-header>
          <rest-payload>
            <![CDATA[
            <Request>
              <ScriptName>example-script</ScriptName>
              <RunNow>true</RunNow>
            </Request>
            ]]>
          </rest-payload>
        <rest-execute path="api/submit-script" type="POST"result="$response" ignoreErrors="false" />
      </rest-group>
    </input>
    ~~~

<br>

### Open Banking REST Requests

The `open-banking` command allows you to issue REST requests specifically for an Open Banking Project using direct login. This command is embedded within the `rest-group` command.

Once a direct login is successful, the access token is cached internally for one hour. Note that due to its nature, OAuth authentication is not supported.

<br>

***`<input>` | `<rest-group ...>`*** - Executes embedded open-banking and rest-execute commands

|Attributes|Description|
|---|---|
|*server* (IN)|Url of server Rest services reside.|
|*open-banking* (EMBEDDED)|Embedded command to issue specific Open Banking Rest requests.|

!!! example "Example code"
    ~~~xml
    <input>
      <descn>Create prerequisites for Open Banking Restrequests</descn>
      <rest-group server="http://example.com">
        <open-banking ...>
        </open-banking ...>
      </rest-group>
    </input>
    ~~~

<br>

***`<input>` | `<rest-group ...>` | `<open-banking ...>`*** - Issue arbitrary Open Banking specific Rest requests

|Attributes|Description|
|---|---|
|*path* (IN)|Path at the server given as attribute in rest-group command the rest service resides at.|
|*type* (IN)|Rest method to use. Currently supported: GET, POST, PUT, DELETE.|
|*result* (IN)|AST test script variable name the result of the Restrequest should be saved to.|
|*username* (IN)|User name for Open Banking Project direct access Rest request. Due to technical reasons outside of AST only US-ASCII characters are supported.|
|*password* (IN)|Password for Open Banking Project direct access Rest request. Due to technical reasons outside of AST only US-ASCII characters are supported.|
|*consumerkey* (IN)|Consumer key for Open Banking Project direct access Rest request. Due to technical reasons outside of AST only US-ASCII characters are supported.|
|(PAYLOAD)|Payload for the Rest request. Especially important for Put and Post requests.|

!!! example "Example code"
    ~~~xml
    <variable name="astVariable"/>
    <input>
      <descn>Create prerequisites for Open Banking Restrequests</descn>
      <rest-group server="https://apisandbox.openbankproject.com">
        <open-banking username="myUserName" password="myFancyPassword" consumerkey="jhkllkjh-WasZ-2Dka" path="/obp/v2.0.0/root" type="GET" result="$astVariable">
          Optional payload to send with request
        </open-banking>
        <open-banking username="myUserName" password="myFancyPassword" consumerkey="jhkllkjh-WasZ-2Dka" path="/obp/v2.0.0/users/current" type="GET">
        </open-banking>
      </rest-group>
    </input>
    ~~~

<br>

### SOAP requests

The soap-group tag allows to issue arbitrary SOAP requests using its embedded soap-execute command.

<br>

***`<input>` | `<soap-group ...>`*** - Executes embedded soap-execute commands

|Attributes|Description|
|---|---|
|*server* (IN)|Url of server SOAP services reside.|
|*httpHeaders* (IN)|Semicolon separated list of HTTP headers and values that shall get send with the SOAP request.|
|*soap-execute* (EMBEDDED)|Embedded command to issue specific SOAP requests. If a Basic Authorization is needed use the soap.secured property in the *ast.properties*|

!!! example "Example code"
    ~~~xml
    <input>
      <descn>Create prerequisites for SOAPrequests</descn>
      <soap-group httpHeaders="header-name headerValue;header-name2 1" server="http://example.com">
        <soap-execute ...>
        </soap-execute ...>
      </soap-group>
    </input>
    ~~~

<br>

***`<input>` | `<soap-group ...>` | `<soap-execute ...>`*** - Issue arbitrary SOAP requests

|Attributes|Description|
|---|---|
|*path* (IN)|Path at the server given as attribute in soap-group command the rest service resides at.|
|*result* (IN)|AST test script variable name the result of the SOAP request should be saved to.|
|(PAYLOAD)|Payload for the SOAP request. Please note that it has to be provided as XML CDATA.|

!!! example "Example code"
    ~~~xml
    <variable name="astVariable" />
    <input>
      <descn>Create prerequisites for SOAP requests</descn>
      <soap-group httpHeaders="header-name headerValue; header-name2 1" server="http://example.com">
        <soap-execute path="/somePathIfNeeded"result="$astVariable">
          <![CDATA[
          <soapenv:Envelope 
            xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" 
            xmlns:gs="http://www.baeldung.com/springsoap/gen"> 
            <soapenv:Header/>
            <soapenv:Body>
              <gs:getCountryRequest>
                <gs:name>Spain</gs:name>
              </gs:getCountryRequest>
            </soapenv:Body>
          </soapenv:Envelope>
          ]]>
          </soap-execute>
        <soap-execute ...>
         ...
        </soap-execute>
        </soap-group>
    </input>
    ~~~
