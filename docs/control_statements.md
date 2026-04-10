# Control Statements

Control statements are used to manage the flow of a test run and define specific settings.

### Session control Statement

The `session` statement allows you to switch the user, business unit, and even the Avaloq instance within a single test case. This is useful for simulating "four-eye checks" or performing cross-business-unit tests in a MESI (Multi-Entity Single Instance) environment. All operations executed after a session switch respect the security and business unit restrictions of the new user.


***`<session ...>`***  - Sets the current user and business unit. Instance is switched if defined.

|Attributes|Description|
|---|---|
|*user* (IN)|User under which the test case is executed. The user can also be an Intermediary oracle user, also named `IMEDuser` if Avaloq is configured to use those.|
|*bu* (IN)|The business unit the session should run in. Optionalparameter – when not specified the business unit remainsunchanged and only the user is switched.|
|*mode* (IN)|<ul><li>By default the Avaloq user session is in batch mode. </li> <li>By switching the session to interface mode ASTconnects to Avaloq the same way as the Avaloq Clientdoes.</li><li> Previous to Avaloq Release 3.1 interface mode wasmandatory if user interface messages where to be handled(see `<ui-msg>`)</li></ul></td>|
|*instance* (IN)|Avaloq instance as defined in *ast.properties* (in a MEMIsetup, see restricted availability).|

!!! example
    ~~~xml
    <!-- Connect to instance_B -->
    <session user="test_user_a" bu="AAA"instance="instance_B" mode="interface">
    ~~~


### Wait control statement

The `wait` statement can either halt test execution for a specified duration or suspend it until a given condition is met.


***`<wait ...>`*** -  Waits until a given time has passed or until a condition is satisfied.

| Attributes | Description |
| --- | ---- |
| *timeout* (IN)|The amount of time to wait, in seconds.|
|*avaloq-cond* (IN)|Avaloq script expression that must evaluate to a boolean. The statement waits until the condition is satisfied, or a timeout occurs. For unconditional wait the attribute can be left out.|
|*script-cond* (IN)|Same as avaloq-cond but with a JavaScript expression.|
|*raise* (IN)|Boolean flag to indicate if a failure should be raised when the condition remains unsatisfied after the given time has passed.|
|*raise-msg* (IN)|Message that is raised.|
|*signal* (IN)|Wait until signal "key" is set (value is not null). See also the section about test case scenarios. Value can be a string constant or AST variable|

!!! example
    ~~~xml
    <wait avaloq-cond="1=2" raise-msg="one was never two"timeout="5" />
    <wait timeout="10" />
    ~~~


### Controlling the flow with IF conditions

The Tag `<if> ... </if>` can be inserted under the testcase tag or directly under the method tag (bubble) and allows AST to evaluate a condition in Avaloq and only if this resolves to true, to execute the content of the `<if>`.

This content may be formed of `<variable>`, `<input>`, `<query>` and `<verify>` commands.

- ***`<if ...>`*** - Control flow of execution and skip entire sections of tests in case condition is not satisfied.
- ***`<else>`*** - In case condition is not satisfied and `<else>...</else>` tag is present under the `<if>` tag, the operation/s under the `<else>` tag will be executed instead.

|Attributes|Description|
|---| ----|
|*avaloq-cond* (IN)| The condition to be verified in order for the content to beevaluated and executed|

Example code - IF (1):

~~~xml
<if avaloq-cond="avaloq expression"> ... </if>
~~~

Example code - ELSE (2):

~~~xml
<if avaloq-cond="avaloq expression">
  ...
<else>
  ...
</else>
</if>
~~~

### Controlling the flow with Select case

The Tag `<select> ... </select>` can be inserted under the testcase tag or directly under the method tag (bubble) and allows AST toevaluate a condition in Avaloq and only if one of its resolves is true, to execute the content of the `<case> ... </case>`.

This content may be formed of `<variable>`, `<input>`, `<query>` and `<verify>` commands.

***`<select ...>`*** - Select allows an avaloq-expr to be tested for equlity against a list of values.

|Attributes|Description|
|---| ----|
|*avaloq-expr* (IN)|The avaloq expresion to be verified in order for the.content to be evaluated and executed|

!!! example
    ~~~xml
    <select avaloq-expr="avaloq expression"> ... </select>
    ~~~

***`<case ...>`*** - Control flow of execution and skip entire sections of tests in case value is not equal to the avaloq-expr attribute of the `<select>` tag.

|Attributes|Description|
|---| ----|
|*Value* (IN)|The value to be verified in order for the content to beevaluated and executed|

!!! example
    ~~~xml
    <select avaloq-expr="avaloq expression">
      <case value="...">
        ...
      </case>
    </select>
    ~~~

***`<else ...>`*** - In case condition is not satisfied and `<else> ... </else>` tag is present under the `<select>` tag, the operation/s under the `<else>` tag will be executed instead.

!!! example
    ~~~xml
    <select avaloq-expr="avaloq expression">
      ...
      <else>
        ...
      </else>
    </select>
    ~~~

### Setting control statement

The setting control statement allows defining parameters of the test execution.

***`<setting ...>`*** - Define parameters of test execution

|Attributes|Description|
|---|----|
|*Property* (IN)|Name of the property to be set. The only parameter that is currently available is the application log level that is used to filter logging events.|
|*Value* (IN)|Value of the property.|

!!! example
    ~~~xml
    <setting property="ast.base.log_level" value="detail" />
    <setting property="ast.base.log_level" value="step" />
    ~~~


### For-Loop control flow statement

The for-loop control statement allows the body of the tag to be executed repeatedly to know about the sequencing of each iteration.

Remark: Define the attributes that determine the number of iterations and the execution logic. The loop iterates based on the number of tokens split by the delimiter from the "select" result or the "avaloq-list" result, assigning each token to the "target" variableRef.

***`<for ...>`*** - Define the attributes on how many times will iterate and execute depending on thenumber of tokens splitted by the delimeter from the "select" or result of "avaloq-list" and assign it to the "target" variableRef. Define either "select" or "avaloq-list" on how to iterate.

|Attributes|Description|
|---|----|
|*select* (IN)| This attirbute is a type of VariableRef containing the reference to an AST variable.|
|*avaloq-list* (IN)| This attribute is a type of AvqExpr containing an Avaloq scripting expression defining a list on which to iterate. Define in delim attribute which property you read for each element of the list.|
|*delim* (IN)| This attribute defines the type of delimeter to be use to split the content of the variable to have a loop on each token. If avaloq-list is specified, it instead defines the property of the object to be read for each element of the list.|
|*target* (IN)|This attirbute is a type of VariableRef with the AST variable containing the value of the single token taken from the "select" variable.|

!!! example
    ~~~xml
    <for select="one;two;three" delim=";" target="$single_value">
      ...
    </for>
    ~~~


AST currently provides one property namely *ast.base.log_level*. This defines the application log level. Valid values are *testcase*, *step*, *operation* and *detail*.
