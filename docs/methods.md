## Definition and use of Methods
AST test script syntax supports outsourcing functionality in **methods**. This mechanism can be applied to implement often-used test script sub-steps in a single AST method. This helps to build **re-usable AST test script steps**.

Methods have to have these attributes and elements:

- **Parameters**: Parameters are variables used to pass values from the test script calling the method to the method itself (e.g., "A" and "B"). They are used in the same way as the elements `<argument>` and `<variable>`. These parameters can be defined with default values and are optionally overwritten when using the method (see `invoke-method` on how to invoke a method).
- **Return**: Defines the value(s) to be returned.
- The name of the method must correspond with the name of the XML file.

~~~xml
<method name="sag/XYZ/foo">
  <descn> ... </descn>
  <parameter name="A" default="A_default_value" />
  <parameter name="B" />
  ...
  <return name="Name1" select="..." />
  <return name="Name2" select="..." />
</method>
~~~

A method can be invoked in an AST test script or even from within another method. If a parameter is not used in the invocation, then the attribute default in the parameter definition inside the method is used. A method can have single or multiple returns by using attribute select for single return and element out for multiple returns.

The invocation of an AST Method has the following syntax:

***`<ivoke-method ...>`***  - Executes a method.

|Attributes|Description|
|---|---|
|*name* (IN)|Name of the method (Path to method from ast-lib directory) |
|*avaloq-cond/script-cond* (IN)|Avaloq script condition or script condition in an other script language.|
|*ignore-blank-setters* (IN)|Defaults to true if not set, it will decide if empty strings should be passed as parameters or ignored.|
|*select* (OUT)|Return value of executed method (optional and will be ignored if out tag is present).|

|Element *parameter*|Description|
|---|---|
|*name*|Name of method parameter (must be present in the method definition)|
|*value*|Parameter value.|

|Element *out*|Description|
|---|---|
|*name*|Name of method output. (must be same as the return name in method) |
|*select*|Return value of executed method.|

~~~xml
<invoke-method name="sag/XYZ/foo" avaloq-cond="$var = 0">
  <parameter name="A" value="$var" />
  <parameter name="B" value="3" />
  <out name="NameOfReturn1" select="myResultVarOut1" />
  <out name="NameOfReturn2" select="myResultVarOut2" />
</invoke-method>
~~~

### Example of a test case without methods
Methods offer significant value for the flow and reusability of a test case, as demonstrated in the example from section [Simple test case example](test_script_and_test_data.md#example-of-a-simple-test-case).
    
When methods are not used, a test case consists of a sequence of input statements.

!!! example "Example of a test case without methods"

    
    ~~~xml
    <?xml version="1.0" encoding="UTF-8"?>
    <testcase bu="AAA" name="0 - Create Address & Person" user="avaloq" version="1.0">
      <!-- Local variable definition -->
      <variable name="addr_doc_id" />
    
      <!-- Create Address -->
      <input>
        <new-doc type="addr" init-action="1100" close-action="1090">  
          <set ... />
          ...
          <out property="id" select="addr_doc_id" />
      
      </input>
    
      <!-- Create Person -->
      <input>
        <descn>Natürliche Person erfassen</descn>
        <new-doc type="person" init-action="1100" close-action="1019">
          <set ... />     
          ...
          <out property="id" select="person_doc_id" />            
        </new-doc>
      </input>
    
      ...
    </testcase>
    ~~~ 


### Example test case using methods
For example, the generation of a Person object can be extracted into a separate, reusable method that is generally applicable and can be utilized by other test cases.

!!! example "Example test case using methods"
    
    
    ~~~xml
    <?xml version="1.0" encoding="UTF-8"?>
    <method name="CreatePerson">
      <descn>Method creating a natural person</descn>
    
      <!-- Call this method with this script.
      
      <invoke-method name="CreatePerson" >
        <parameter name ="in_addr_ID" value="$l_obj_addr_id"/>
        <parameter name ="in_first_name" value="$l_first_name"/>
        <parameter name ="in_middle_name" value="$l_middle_name"/>
        <parameter name ="in_name" value="$l_name"/>
        <parameter name ="in_sort_alpha" value="PERSON_${l_sort_alpha}"/>
        <parameter name ="in_gender" value="$l_gender"/>
        <parameter name ="in_lang" value="$l_lang"/>
        <parameter name ="in_country_nati" value="$l_country"/>
        <parameter name ="in_birthdate" value="-${l_age}"/>
        <parameter name ="in_ident_type" value="ID"/>
        <parameter name ="in_ident_country_id" value="$l_country"/>
        <parameter name ="in_ident_nr" value="$l_ident_nr"/>
        <out name="out_person_id" select="l_obj_person_id"/>
      </invoke-method>
      
      -->
    
      <variable name="person_doc_id"/>
      
      <parameter name ="in_addr_ID"/>
      <parameter name ="in_first_name" default="Hans"/>
      <parameter name ="in_middle_name" default="Martin"/>
      <parameter name ="in_name" default="Schweizer"/>
      <parameter name ="in_sort_alpha"/>
      <parameter name ="in_gender" default="M"/>
      <parameter name ="in_lang" default="G"/>
      <parameter name ="in_country_nati" default="CH"/>
      <parameter name ="in_birthdate" default="-42y"/>
      <parameter name ="in_ident_type" default="ID"/>
      <parameter name ="in_ident_country_id" default="CH"/>
      <parameter name ="in_ident_nr" default="756.1993.3991.12"/>
    
      <input>
        <descn>Natürliche Person erfassen</descn>
        <new-doc type="person" close-action="1019" init-action="1100">
          <set property="form_id" value="917"/>
          <set property="eff_date" value="today"/>
          <set property="open_date" value="today"/>
          <set property="person_det.person_type_id" value="person_natural"/>
          <set property="person_name_list(1).first_name" value="$in_first_name"/>
          <set property="person_name_list(1).middle_name" value="$in_middle_name"/>
          <set property="person_name_list(1).last_name" value="$in_name"/>
          <set property="sort_alpha" value="$in_sort_alpha"/>
          <set property="obj_extn.person_sym" value="$in_sort_alpha"/>
          <set property="gender_id" value="$in_gender"/>
          <set property="lang_id" value="$in_lang"/>
          <set property="person_det.country_nati_id" value="$in_country_nati"/>
          <set property="birth_date" value="$in_birthdate"/>
          <set property="domi_addr_id" value="$in_addr_ID"/>
          <do stmt="person_ident_list.add"/>
          <set property="person_ident_list(1).ident_type_id" value="$in_ident_type"/>
          <set property="person_ident_list(1).issue_country_id" value="$in_ident_country_id"/>
          <set property="person_ident_list(1).ident_nr" value="$in_ident_nr"/>
          <out property="id" select="person_doc_id"/>
        </new-doc>
      </input>
    
        <variable name="a_status_2" avaloq-expr="doc_person($person_doc_id).wfc_status_id"/>
    
        <verify>
          <descn>Verifizieren Orderstatus </descn>
          <value select="a_status_2" value="90"/>
        </verify>
    
        <variable name="obj_person_id" avaloq-expr="'#'||doc_person($person_doc_id).person_id"/>
    
        <input>
          <descn> Object PersonID = {$obj_person_id} </descn>
        </input>
    
      <!--   define out parameter -->
      <return name="out_person_id" select="obj_person_id"/>
      
    </method>
    ~~~

Within a test case, the full code implementation can be concisely replaced by a method call. This call requires passing only the necessary parameters for execution.

It is not required to supply every possible parameter. If a parameter is omitted from the method call, the method will attempt to utilize its 'default' attribute if such an attribute has been defined. In the absence of a defined 'default' attribute, the value for that parameter remains undefined for the duration of the call.

This approach simplifies the structure of the test case and increases its readability.

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<testcase bu="AAA" name="0 - Create Address & Person" user="avaloq" version="1.0">
  <!-- Local variable definition -->
  <variable name="addr_doc_id" />

  <!-- Create Address -->
  <invoke-method name="CreateAddress">
    ...
    <out name="out_addr_id" select="l_obj_addr_id"/>
  </invoke-method>

  <!-- Create Person -->
  <invoke-method name="CreatePerson" >
    <parameter name ="in_addr_ID" value="$l_obj_addr_id"/>
    <parameter name ="in_first_name" value="Hans"/>
    <parameter name ="in_name" value="Muster"/>

    <parameter name ="in_gender" value="M"/>
    <parameter name ="in_lang" value="G"/>
    <parameter name ="in_country_nati" value="CH"/>
    
    <parameter name ="in_ident_type" value="ID"/>
    <parameter name ="in_ident_country_id" value="CH"/>
    <parameter name ="in_ident_nr" value="756.1993.331.12"/>
    <out name="out_person_id" select="l_obj_person_id"/>
  </invoke-method>
  ...
</testcase>
~~~