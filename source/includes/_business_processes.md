# F. Business Processes 

Business processes are used to represent processes involving files, that are usually conducted in a corporate setting, such as signing of files. 
A business process stores information on the entire flow of the process and other information essential to generate the final output of the process.

## Business Process Attributes

> A **signers** object:

```json
{
    "signer_id": string,
    "signer_name": string,
    "signer_email": string,
    "sequence_number": number,
    "is_mandatory": boolean,
    "has_signed": boolean,
    "esignatures": {
        "type": string,
        "is_mandatory": boolean,
        "placement": { 
            "page": number, 
            "x": string, 
            "y": string 
        },
        "dimensions": { 
            "width": string, 
            "height": string 
        },
        "signature": string,
        "signature_hash": string,
        "ip_address": string,
        "timestamp": date
    }[],
    "digi_signatures": {
        "type": string,
        "is_mandatory": boolean,
        "placement": { 
            "page": number, 
            "x": string, 
            "y": string 
        },
        "dimensions": { 
            "width": string, 
            "height": string 
        },
        "signature": string,
        "ip_address": string,
        "timestamp": date
    }[],
    "custom_texts": {
        "descriptor": string,
        "is_mandatory": boolean,
        "placement": { 
            "page": number, 
            "x": string, 
            "y": string 
        },
        "dimensions": { 
            "width": string, 
            "height": string 
        },
        "text": string,
        "text_hash": string,
        "ip_address": string,
        "timestamp": date
    }[]
}
```

> A **sequential_requirement** object:

```json
{
    "sequence_number": number,
    "signers": {
        "signer_id": string,
        "signer_name": string,
        "signer_email": string
    }[]
}

```

> A **completion_requirement** object

```json
{
    "minimum": number
}
```

> A **history** object:

```json
{
    "action": string,
    "actor": {
        "id": string,
        "email": string,
        "name": string
    },
    "timestamp": date,
    "transaction_hash": string
}

```

Attribute | Type | Description
----------|------|-------------
id | string | Unique identifier of the business process
type | string | String specifying the type of business process. Currently, only “signature" is supported.
date_created | date | Time the business process is created. Cannot be changed after the business process is created.
expiration_time | date | An optional attribute that indicates the time the business process expires. Beyond which, signers cannot add signatures. However, the expiration time can be extended by the owner.
document_id | string | Id of the document the business process is attached to.
allow_download | boolean | Boolean indicating if the signers are allowed to manually download the signed pdf on Dedoco’s signing app after signing.
signers | object[] | Array of objects. The structure of an object is given on the right.<br>Contains completed signatures. Only applicable to 'signature" business processes. 
sequential_requirement | object[] | Array of objects. The structure of an object is given on the right.<br>Each signer is only given one sequence number. Signers are only allowed to sign when signers with a lower sequence number have signed. Only applicable for sequential “signature” business processes.
completion_requirement | object | Contains attribute(s) each determining the completion of the business process. The business process is completed when at least one of these requirement(s) is met. Its structure is given on the right.
status | string | Value could be “pending”, “completed”, “expired” or “voided”. “pending” indicates that the business process is valid, incomplete and can still have signatures added. “completed” indicates that the business process is valid and completed. “expired” indicates that the business process is valid, incomplete and can only be extended by the owner. “voided” indicates that the document is invalid and cannot be edited.
history | object[] | Array of objects involving changes made to the business process. The structure of an object is given on the right.

> A **signers.esignatures** object:

```json
{
    "type": string,
    "is_mandatory": boolean,
    "placement": { 
        "page": number, 
        "x": string, 
        "y": string 
    },
    "dimensions": { 
        "width": string, 
        "height": string 
    },
    "signature": string,
    "signature_hash": string,
    "ip_address": string,
    "timestamp": date
}

```

> A **signers.esignatures.placement** object:

```json
{ 
    "page": number,
    "x": string, 
    "y": string 
}

```

> A **signers.esignatures.dimensions** object:

```json
{ 
    "width": string,
    "height": string 
}

```

> A **signers.digi_signatures** object:

```json
{
    "type": string,
    "is_mandatory": boolean,
    "placement": { 
        "page": number, 
        "x": string, 
        "y": string 
    },
    "dimensions": { 
        "width": string, 
        "height": string 
    },
    "signature": string,
    "ip_address": string,
    "timestamp": date
}

```

> A **signers.digi_signatures.placement** object:

```json
{ 
    "page": number,
    "x": string, 
    "y": string 
}

```

> A **signers.digi_signatures.dimensions** object:

```json
{ 
    "width": string, 
    "height": string 
}
```

> A **signers.custom_texts** object: 

```json
{
    "descriptor": string,
    "is_mandatory": boolean,
    "placement": { 
        "page": number, 
        "x": string, 
        "y": string
    },
    "dimensions": { 
        "width": string, 
        "height": string
    },
    "text": string,
    "text_hash": string,
    "ip_address": string,
    "timestamp": date
}

```

> A **signers.custom_texts.placement** object:

```json
{ 
    "page": number,
    "x": string, 
    "y": string 
}

```

> A **signers.custom_texts.dimensions** object:

```json
{ 
    "width": string, 
    "height": string
}
```

Child attribute | Type | Description
----------------|------|-------------
signers.signer_id | string | Identifier of the signer. Will be the user id of the signer if the signer is a Dedoco user.
signers.signer_name | string | Name of the signer. Will be displayed in email if Dedoco’s email service is used.
signers.signer_email | string | Email address of the signer. Used by Dedoco’s email service and used to identify if the signer is a Dedoco user.
signers.signer_phone | string | Phone number of the signer. May be used for SMS OTP.
signers.sequence_number | number | Sequence number of the signer if the signers are required to sign in a sequence. Starts from 1. 0 is used if there is no sequence.
signers.is_mandatory | boolean | Boolean indicating whether or not the signer is required to sign for the business process to be completed.
signers.has_signed | boolean | Boolean indicating whether or not the signer has completed his/her signature for the business process.
signers.esignatures | object[] | Array of objects. The structure of an object is given on the right.
signers.esignatures.type | string | Value can be “drawing”, “text” or “upload”. “drawing” indicates that the electronic signature was electronically drawn. “text” indicates that the electronic signature was typed. “upload” indicates that the electronic signature was an uploaded image.
signers.esignatures.is_mandatory | boolean | Boolean indicating whether or not the electronic signature is mandatory.
signers.esignatures.placement | object | Object containing information on where the electronic signature is placed on the file. Its structure is given on the right.
signers.esignatures.placement.page | number | Page number of the page on which the electronic signature is placed. Starts from 1.
signers.esignatures.placement.x | string | Float string which indicates the horizontal distance the top left corner of the electronic signature box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the placement of the electronic signature box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
signers.esignatures.placement.y | string | Float string which indicates the vertical distance the top left corner of the electronic signature box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the placement of the electronic signature box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
signers.esignatures.dimensions | object | Object containing information on the size of the electronic signature on the file. Its structure is given on the right.
signers.esignatures.dimensions.width | string | Float string which indicates the width of the electronic signature box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the width of the electronic signature box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the electronic signature box for the entire signature box to be contained within the page) is less than 1. 
signers.esignatures.dimensions.height | string | Float string which indicates the height of the electronic signature box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the height of the electronic signature box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the electronic signature box for the entire signature box to be contained within the page) is less than 1.
signers.esignatures.signature | string | Base64 string of the electronic signature.
signers.esignatures.signature_hash | string | SHA3-256 hash of the electronic signature in hexadecimal format. 
signers.esignatures.ip_address | string | Ip address of the signer.
signers.esignatures.timestamp | date | Time the electronic signature was received by Dedoco API.
signers.digi_signatures | object[] | Array of objects. The structure of an object is given on the right.
signers.digi_signatures.type | string | Value can be “ndi” or “blockchain”. “ndi” indicates that the digital signature was performed via the NDI Document Signing Service. “blockchain” indicates that the digital signature was performed using Dedoco’s blockchain service. Currently, only “ndi” is supported.
signers.digi_signatures.is_mandatory | boolean | Boolean indicating whether or not the digital signature is mandatory.
signers.digi_signatures.placement | object | Object containing information on where the digital signature is placed on the file. Its structure is given on the right.
signers.digi_signatures.placement.page | number | Page number of the page on which the digital signature is placed. Starts from 1.
signers.digi_signatures.placement.x | string | Float string which indicates the horizontal distance the top left corner of the digital signature box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the placement of the digital signature box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the digital signature box for the entire signature box to be contained within the page) is strictly less than 1.
signers.digi_signatures.placement.y | string |Float string which indicates the vertical distance the top left corner of the digital signature box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the placement of the digital signature box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the digital signature box for the entire signature box to be contained within the page) is strictly less than 1.
signers.digi_signatures.dimensions | object | Object containing information on the size of the digital signature on the file. Its structure is given on the right.
signers.digi_signatures.dimensions.width | string | Float string which indicates the width of the digital signature box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the width of the digital signature box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the digital signature box for the entire signature box to be contained within the page) is less than 1.
signers.digi_signatures.dimensions.height | string | Float string which indicates the height of the digital signature box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the height of the digital signature box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the digital signature box for the entire signature box to be contained within the page) is less than 1.
signers.digi_signatures.signature | string | Digital signature in hexadecimal format.
signers.digi_signatures.ip_address | string | Ip address of the signer.
signers.digi_signatures.timestamp | date | Time the digital signature was received by Dedoco API.
signers.custom_texts | object[] | Array of objects. The strucutre of an object is given on the right.
signers.custom_texts.descriptor | string | Brief description of custom text. Helps the signer know what to fill into the custom text field.
signers.custom_texts.is_mandatory | boolean | Boolean indicating whether or not filling in the custom text is mandatory.
signers.custom_texts.placement | object | Object containing information on where the custom text is placed on the file. Its structure is given on the right.
signers.custom_texts.placement.page | number | Page number of the page on which the custom text is placed. Starts from 1.
signers.custom_texts.placement.x | string | Float string which indicates the horizontal distance the top left corner of the custom text box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the placement of the custom text box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the custom text box for the entire box to be contained within the page) is strictly less than 1.
signers.custom_texts.placement.y | string | Float string which indicates the vertical distance the top left corner of the custom text box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the placement of the custom text box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the custom text box for the entire box to be contained within the page) is strictly less than 1.
signers.custom_texts.dimensions | object | Object containing information on the size of the custom text on the file. Its structure is given on the right.
signers.custom_texts.dimensions.width | string | Float string which indicates the width of the custom text box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the width of the custom text box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the custom text box for the entire box to be contained within the page) is less than 1.
signers.custom_texts.dimensions.height | string | Float string which indicates the height of the custom text box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the height of the custom text box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the custom text box for the entire box to be contained within the page) is less than 1.
signers.custom_texts.text | string | Value of the custom text filled in by the signer.
signers.custom_texts.text_hash | string | SHA3-256 hash of the custom text’s value in hexadecimal format.
signers.custom_texts.ip_address | string | Ip address of the signer.
signers.custom_texts.timestamp | date | Time the custom text was received by Dedoco API.
sequential_requirement.sequence_number | number | Sequence number of the signer(s) listed within the object. Starts from 1.
sequential_requirement.signer.signer_id | string | Id of the signer.
sequential_requirement.signer.signer_name | string | Name of the signer.
sequential_requirement.signer.signer_email | string | Email address of the signer.
completion_requirement.minimum | number | Minimum number of signers who have signed for the business process to be ‘completed’.
history.action | string | Description of the action recorded in the history attribute.
history.actor.id | string | User id of the entity that took the action.
history.actor.email | string | Email address (if any) of the entity that took the action.
history.actor.name | string | Name (if any) of the entity that took the action.
history.timestamp | date | Time the action took place.
history.transaction_hash | string | Hash of the blockchain transaction updating the data (only if there are any changes in data) on the blockchain due to the action that took place. Can be used to view the transaction details on the blockchain’s explorer.

## Endpoint: Get Request Links

**GET /public/business-processes/{business_process_id}/links HTTP/1.1**<br>
Gets generated links for each requestee specified in the business process.

<aside class="notice">
Can only be called successfully if the business process is “pending”.
</aside>

**Authorization:**<br> 
Bearer \<jwt_token>

Header Parameter:

Parameter | Type | Description | Required/Optional
----------|------|-------------|-------------------
jwt_token | string | Token obtained by the client through the Get JWT Token endpoint. | Required

Path Parameter: 

Parameter | Type | Description | Required/Optional
----------|------|-------------|-------------------
business_process_id | string | Id of the business process to get generated links for. | Required

Responses:

> Response body for code 200:

```json
{ 
    "links": {
        "documentId": string,
        "documentName": string,
        "businessProcessId": string,
        "signerId": string,
        "signerName": string,
        "signerEmail": string,
        "link": string
    }[]
}

```

> Response body for code 4xx:

```json
{
    "statusCode": number,
    "message": string,
    "error": string
}
```

> where **statusCode** is the status code of the error,<br>
**message** is a string describing the cause of error and<br>
**error** is a string describing the type of error.

> Response body for code 5xx:

```json
{
    "statusCode": number,
    "message": string,
    "error": string 
}

```

> where **statusCode** is the status code of the error,<br>
**message** is a string describing the cause of error and<br>
**error** is a string describing the type of error.


Code | Description
-----|-------------
200 | Request links have been successfully retrieved.
4xx | Errors caused by API consumers. Error codes such as 400, 401, 403 and 404 can be expected if incorrect requests are made to the API.
5xx | Errors caused by the API provider or its dependencies. Error codes such as 500, 502 and 503 can be expected if there is an issue on the API side.

> A **links** object:

```json
{
    "documentId": string,
    "documentName": string,
    "businessProcessId": string,
    "signerId": string,
    "signerName": string,
    "signerEmail": string,
    "link": string
}

```

Child Attribute | Type | Description
----------------|------|-------------
links | object[] | Array of objects. The structure of an object is given on the right.<br>Contains information on the signing links for each signer involved.
links.documentId | string | Id of the document the link attribute in the same object is for.
links.documentName | string | Name of the document the link attribute in the same object is for.
links.businessProcessId | string | Id of the business process the link attribute in the same object is for.
links.signerId | string | Id of the signer the link attribute in the same object is for.
links.signerName | string | Name of the signer the link attribute in the same object is for.
links.signerEmail | string | Email of the signer the link attribute in the same object is for.
links.link | string | The base URL of the signing link for the specified signer. To complete the signing link, append the base64 encoding of the file retrieval URL for Dedoco to retrieve the relevant file to sign on. The file retrieval URL should accept a GET request for Dedoco to retrieve the relevant file and should return a JSON object `{ file: string }` where file is the base64 string of the retrieved pdf.<br>For example, if the file retrieval URL is “https://www.sample-url.com/path/file-id”, the base64 encoding would be “aHR0cHM6Ly93d3cuc2FtcGxlLXVybC5jb20vcGF0aC9maWxlLWlkIA==”. Appending “aHR0cHM6Ly93d3cuc2FtcGxlLXVybC5jb20vcGF0aC9maWxlLWlkIA==” to the base URL obtained from this request would complete the signing link. Note that the file retrieval URL appended should always be retrieving the most up-to-date (i.e. with signatures if there were previous signers) pdf. In the case of a business process with a signing sequence defined, the client should send out the links in the same sequence (even though Dedoco prevents a signer from signing before the previous signer has signed) and make sure that the appended URL retrieves the latest pdf with signatures (if any). And in the case of a business process where signers are allowed to sign simultaneously or in any order, the client can send out the links to all the signers at the same time but should make sure that the appended URL always retrieves the latest pdf with signatures (if any) so that the signers will receive the right pdf to sign on.

> Sample Request Header:

```http
GET https://beta-api.dedoco.com/api/v1/60581dbbc79151135ddd6b5a/links HTTP/1.1
Authorization: Bearer <token>
```

> Sample Request Body:

```json
{ }
```

> Sample Response Header:

```http
HTTP/1.1 200 OK
```

> Sample Response Body:

```json
{
  "links": [
    {
      "documentId": "60581dbbc79151135ddd6b58",
      "documentName": "sample_pdf",
      "businessProcessId": "60581dbbc79151135ddd6b5a",
      "signerId": "SIG_60581dbbc79151135ddd6b5b",
      "signerName": "Alice Tan",
      "signerEmail": "alicetan@gmail.com",
      "link": "https://sample-url.com/public/sign/60581dbbc79151135ddd6b5a/SIG_60581dbbc79151135ddd6b5b"
    },
    {
      "documentId": "60581dbbc79151135ddd6b58",
      "documentName": "sample_pdf",
      "businessProcessId": "60581dbbc79151135ddd6b5a",
      "signerId": "SIG_60581dbbc79151135ddd6b5c",
      "signerName": "Bobby Chia",
      "signerEmail": "bobbychia@gmail.com",
      "link": "https://sample-url.com/public/sign/60581dbbc79151135ddd6b5a/SIG_60581dbbc79151135ddd6b5c"
    }
  ]
}

```

<aside class="success">
A sample request and response are given on the right.
</aside>
