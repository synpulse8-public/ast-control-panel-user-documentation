# Verify operations
Verify operations are used to test for specific conditions and report successes or failures to the AST framework. 

This section details the different types of verification you can perform.

### Verify value operation

The verify value operation allows to test the value of a variable or an avaloq-expression against an expected value. The comparison strategy is as follows (this hierarchy is not adaptable):

- The two values are treated as numbers and compared (with a tolerated deviation of 1.00\times 10<sup>-8</sup>).
- The two values are treated as strings and are compared character after character.
- The expected value is treated as a regular expression. AST tests whether the actual value satisfies the given regularexpression pattern.

The content specified in the `select` attribute can also be verified against an XML schema.

<br>

***`<verify>` | `<value ...>`*** - Compares a value against another one

|Attributes |Description|
|--- | ----|
|*select* (IN) |Variable to be verified.|
|*avaloq-expr* (IN)|The value to be verified is determined by an avaloqexpression.|
|*script-expr* (IN)|The value to be verified is determined by a JavaScriptexpression|
|*value* (IN)|Value to be verified against (expected value). Possibly a regular expression pattern.|
|*schema* (IN)|URI to XML schema. The URI can describe a path to alocal file or a web location (http://...).|
|*raise-msg* (IN)|Raises a customized error message.|

!!! example "Example code 1"
    ~~~xml
    <verify>
      <descn>Verify that a variable contains a number</descn>
      <value select="xfer_doc_id" value="[0-9][0-9]*" />
    </verify>
    ~~~

!!! example "Example code 2"
    ~~~xml
    <verify>
      <descn>Verify that an avaloq-expression equals thespecified number</descn>
      value avaloq-expr="session.bu_id" value="6"/>
    </verify>
    ~~~

!!! example "Example code 3"
    ~~~xml
    <verify>
      <descn>Validate xmlContent against a schema</descn>
      <value select="$xml_content" schema="testcase.xsd" />
    </verify>
    ~~~


### Verify booking operation

The AST tool can verify financial bookings made by an Avaloq order. To identify a specific booking item, you can use one of the following methods:

- **Position ID**: Use the position's ID, which can be any type of position.
- **Money Account**: Use any key to identify a specific money account.
- **Combined Elements**: Use a combination of a container, currency (curry), and an optional recon lot type.

This booking operation ensures that the asynchronous Avaloq booking engine is completely up-to-date, preventing any retrieval of outdated or "stale" information. The verification process is independent of the booking's status, so you can test both temporary and verified bookings. Additionally, you can use the optional event status parameter to specifically check for temporary or reversed (storno) bookings.

***`<verify>` | `<booking ...>`*** - Verifies a booking performed by an order on a position or money account

|Attributes |Description|
|--- | ----|
|*doc* (IN) |Id of order to be verified (mandatory).|
|*pos* (IN) |Position on which the verification is done.|
|*macc* (IN) |Money account on which the verification is done.|
|*cont* (IN) |Container on which the verification is done.|
|*curry* (IN) |Currency of the defined container (required whenparameter container is given).|
|*qty* (IN) |Quantity to be verified (mandatory).|
|*recon_lot_type* (IN) |Recon lot type (optional).|
|*evt-status* (IN) |Event Status of the Booking to be verified (optional).|
|*book_kind* (IN) |Book kind of the booking (optional). <br> Allows to identify different bookings on the same position. A typical usage is to get access to different cost bookings on the same profit/loss account.|

!!! example
    ~~~xml
    <verify>
      <descn>Check pay bookings on client, cost and ZV.CRED</descn>
      <booking doc="$pay" macc="10001401.2003" qty="-129.45" />
      <booking doc="$pay" cont="412.20.00.50" curry="CHF" qty="6" />
      <booking doc="$pay" cont="ZV.KRED" curry="CHF" recon_lot_type="10" qty="123.45" />
    </verify>
    ~~~


### Verify message operation

The message verify operation allows you to check the content of a given message—whether incoming or outgoing—against an expected value. AST uses a specific strategy to perform this check.

- **XPath Evaluation**: If the XPATH parameter is specified, the message is treated as an XML message, and the result of the XPath query is evaluated and checked against the expected value.
- **Tag Verification**: For messages formatted by external regulators like SIC, EuroSIC, SWIFT, or SECOM, AST can verify the presence of expected tags and their corresponding values. This lets you test specific tags while ignoring others that aren't relevant to the comparison.
- **String Comparison**: The two messages are compared as literal strings, character by character.
- **Regular Expression**: AST can also treat the expected value as a regular expression, and then test whether the actual message content satisfies that pattern.


***`<verify ... >` | `<msg ...>`*** - Verifies the content of a message

|Attributes |Description|
|--- | ----|
|*id* (IN)|Id of the message|
|*Expected* (IN)|Expected content of the message. Can either be the full expected content, a regular expression that is to be satisfied or a subset of (SIC, EuroSIC, SWIFT or SECOM) tags and values that must appear in the actual message.|
|Xpath (IN)|For XNK messages an XPATH expression can be set. The evaluated location is tested against the expected attribute.|

### Verify compare-pdfs operation

Differences between two PDF files can be checked using the compare pdfs verify operation. The pdf pages are converted to images with 50 pixel/in. The comparison strategy is as follows:

- One source and one target attribute must be given.
- Only one source and only one target may be given.
- In case of load by id, the operation fetches the directory and name of the file in the database and converts it to bytes for comparison.
- In case of load by path, the operation finds the file given the absolute or relative path directory and its file name. File should be a PDF-file.
- Combination of id and path is possible. (e.g. *source-id* and *target-path*, *source-path* and *target-id*)
- If either of source or target PDF files does not exist or could not be loaded, comparison will fail.
- Comparison between two PDF files will be by pixel and if it exceeds the maximum page pixel errors, the operation returns a failure.
- It's possible to use sub-tags `<page ...>` in the `<compare-pdfs>` tags to be able to define which pages will be compared.
- If no `<page>` is present, it works like before, all pages compared.
- If "save-screenshot-to" attribute is present, it will save the result image base on the declaration value with or without file extension .png.


***`<verify ...>` | `<compare-pdfs ...>`*** - Compares two PDF files

|Attributes|Description|
|---|---|
|*source-id (IN)|Source id|
|*source-path* (IN)|Directory and filename of the source PDF file|
|*target-id* (IN)|Target id|
|*target-path* (IN)|Directory and filename of the target PDF file|
|*max-page-pixel-errors* (IN)|The maximum page pixel errors that is valid. Default is 0.|
|*difference-screenshot* (IN)|Valid values are "onerror" (default) and "always". Saving a screenshot of the comparison to the output folder on error or always. "onerror": Everytime the maximum page pixel errors have been exceeded for each page in the PDF file, or "always".|
|*save-screenshot-to* (IN)|Save the generated screenshot of image based on the declared value of attribute with or without file extension .png.|

!!! example
    ~~~xml
    <verify>
      <descn>Verify if pdf1.pdf is same with pdf2.pdf</descn>
      <compare-pdfs source-path="c:/users/test/document/pdf1.pdf" target-id="611850" max-page-pixels-errors="5" difference-screenshot="onerror" save-screenshot-to="testcases/asset" />
    </verify>
    ~~~


***`<compare-pdfs ...>` | `<page ...>`*** - Sub tag of compare-pdfs tag that allows definition of pages to be compared

|Attributes|Description|
|---|---|
|*numbers* (IN)|Page number/s to be tested. It can contain a comma separated list of numbers or an "*" for all pages (that can be overriden for some specific cases with other entries below it)|
|*x* (IN)|Initial x coord of the portion to be tested for that page. Default is 0. It can be set to a percentage. (e.g. x="/23" means 23%)|
|*y* (IN)|Initial y coord of the portion to be tested for that page. Default is 0. It can be set to a percentage. (e.g. x="/23"means 23%)|
|*width* (IN)|"full" for the complete width or width in pixels of the region to be compared. Default is "full". It can be set to a percentage. (e.g. x="/23" means 23%)|
|*height* (IN)|"full" for the complete width or height in pixels of the region to be compared. Default is "full". It can be set to a percentage. (e.g. x="/23" means 23%)|
|*max-page-pixels-errors* (IN)|Treshold for this page configuration that overrides the previous ones in the `<compare-pdfs>` or previous `<page>` tags.|

!!! example
    ~~~xml
    <verify>
      <compare-pdfs difference-screenshot="onerror" source-path="../issues/pdf/pdf_long1.pdf" target-path="../issues/pdf/pdf_long2.pdf" max-page-pixels-errors="2"> 
        <page numbers="*" max-page-pixels-errors="2" />
        <page numbers="2,3,4,5,6" y="200" x="0" max-page-pixels-errors="2" />
      </compare-pdfs>
    </verify>
    ~~~
