# Integration of Script Languages
In addition to the AST script language used for writing test cases, AST permits the execution of Avaloq Script expressions and code blocks. However, the use of Avaloq Script has limitations, particularly when handling large strings due to Avaloq's buffer sizes and the need to escape characters within AST variables used in Avaloq Script.

## JavaScript Integration

With AST release 2.4 and later, a general use of Avaloq independent script languages was introduced. The only non-Avaloq script language currently implemented is JavaScript. The use of JavaScript is available with the following AST operations and control statements:

- `<variable>` tag, specifically with the attribute script-expr.
- `<input>` script operation.
- `<verify>` value operation, specifically with the attribute script-expr.
- `<wait>` statement, specifically with the attribute script-cond.
- Conditional execution of test steps, using the attribute script-cond.
- `<invoke-method>`, using the attribute script-cond.

The type of script language to be used can be specified either in the *ast.properties* file with the `setting ast.base.dflt.script_lang = javascript`, or directly with the attribute `lang="javascript"` when used with any script-expr or script-cond attribute. For a detailed description of these, refer to chapter [JavaScript execution](input_operations.md#javascript-execution)

## Binding of AST Variables to JavaScript

There are two methods available for passing AST variables to JavaScript code.

#### Automatic Detection

The first method is analogous to the one used with Avaloq-Script. AST variables within the script code are automatically detected and their string value is bound to the corresponding JavaScript variable. Unlike in Avaloq-Script, AST variables will not be literally expanded into the script code. This practice prevents issues that can occur when AST variables contain specific characters such as apostrophes or linefeeds.

#### Input Script Operation

The second method provides a more precise way to bind AST variables to JavaScript variables and also facilitates passing results from script expressions back to AST. This method is exclusively available with the input script operation.

!!! info "More information"
    For further information please see chapter [JavaScript execution](input_operations.md#javascript-execution) and [PL/SQL execution](input_operations.md#plsql-execution)