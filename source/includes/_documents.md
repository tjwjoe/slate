# E. Documents<a name="documents"></a>
Documents are used to represent the uploaded files. A document can have zero to multiple business processes attached to it. 
However, at any point in time, there can only be at most one “pending” business process attached to it. 

## Attributes

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
id | string | Unique identifier of the document.
name | string | Name of the document set by the creator of the document. Does not need to follow the original name of the uploaded document and could be used as a descriptor.
file_type | string | File type of the uploaded file. Accepted values are “pdf” and “json”.
date_created | date | Time the document is created. Cannot be changed after the document is created.
document_hashes | string[] | Array of SHA3-256 hashes. First hash will be the hash of the original document. Subsequently, upon completion of a business process attached to the document, the hash of the resultant document will be generated and added to this array.
parent_folder | string | Id of the parent folder.
business_processes | string[] | Array of ids of attached business processes. Each document can only have at most one “pending” business process at any time.
status | string | Value could be “active”, “closed” or “voided”. “active” indicates that the document is valid and can still be edited. “closed” indicates that the document is valid but cannot be edited. “voided” indicates that the document is invalid and cannot be edited.
history | object[] | Array of objects involving changes made to the document. The structure of an object is given on the right.

Child attribute | Type | Description
----------------|------|-------------
history.action | string | Description of the action recorded in the history attribute.
history.actor.id | string | User id of the entity that took the action.
history.actor.email | string | Email address (if any) of the entity that took the action.
history.actor.name | string | Name (if any) of the entity that took the action.
history.timestamp | date | Time the action took place.
history.transaction_hash | string | Hash of the blockchain transaction updating the data (only if there are any changes in data) on the blockchain due to the action that took place. Can be used to view the transaction details on the blockchain’s explorer.

## Endpoint: Add Document
**POST /public/documents HTTP/1.1**<br>
Adds a document with at most one business process to a folder.

**Authorization:**<br>
Bearer <jwt_token>

**Content-Type:**<br>
application/json

Header Parameter:

Parameter | Type | Description | Required/Optional
----------|------|-------------|------------------
jwt_token | string | Token obtained by the client through the Get JWT Token endpoint. | Required

Request Body:

> A **business_process** object:

```json
{
    "type": string,
    "expiration_time": number,
    "is_sequential": boolean,
    "allow_download": boolean,
    "signers": {
        "signer_email": string,
        "signer_name": string,
        "sequence_number": number,
        "esignatures": { 
            "placement": { 
                "page": number, 
                "x": string, 
                "y": string 
            }, 
            "dimensions": { 
                "width": string, 
                "height": string 
            } 
        }[],
        "digi_signatures": { 
            "type": string, 
            "placement": { 
                "page": number, 
                "x": string, 
                "y": string 
            }, 
            "dimensions": { 
                "width": string,
                "height": string 
            } 
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
            } 
        }[]
    }[],
    "completion_requirement": {
        "min_number": number
    }
}

```

Attribute | Type | Description | Required/Optional
----------|------|-------------|------------------
document_name | string | Name of the document (not required to be the same as the original file name). Set by the document’s creator. | Required
date_created | number | Date of the document's creation in Unix time | Required
file_type | string | File type of the document. Currently, only “pdf” is accepted. | Required
document_hash | string | SHA3-256 hash of the document in hexadecimal. | Required
parent_folder | string | Id of folder to add document to. | Required
business_process | object | Object containing details of the business process to be added to the document. The structure of the object is shown on the right. | Optional

> A **business_process.signers** object:

```json
{
    "signer_email": string,
    "signer_name": string,
    "sequence_number": number,
    "esignatures": { 
        "placement": { 
            "page": number, 
            "x": string, 
            "y": string 
        }, 
        "dimensions": { 
            "width": string, 
            "height": string 
        } 
    }[],
    "digi_signatures": { 
        "type": string, 
        "placement": { 
            "page": number, 
            "x": string, 
            "y": string 
        }, 
        "dimensions": { 
            "width": string, 
            "height": string 
        } 
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
        } 
    }[]
}
```

> A **business_process.signers.esignatures** object:

```json
{ 
    "placement": { 
        "page": number, 
        "x": string, 
        "y": string 
    }, 
    "dimensions": { 
        "width": string, 
        "height": string 
    } 
}

```

> A **business_process.signers.esignatures.placement** object: 

```json
{ 
    "page": number, 
    "x": string, 
    "y": string 
}
```

> A **business_process.signers.esignatures.dimensions** object:

```json
{ 
    "width": string, 
    "height": string 
} 
``` 

> A **business_process.signers.digi_signatures** object:

```json
{ 
    "type": string,
    "placement": { 
        "page": number, 
        "x": string, 
        "y": string 
    }, 
    "dimensions": { 
        "width": string, 
        "height": string 
    } 
}
```

> A **business_process.signers.digi_signatures.placement** object:

```json
{ 
    "page": number, 
    "x": string, 
    "y": string 
}
```

> A **business_process.signers.digi_signatures.dimensions** object:

```json
{ 
    "width": string, 
    "height": string 
}
```

> A **business_process.signers.custom_texts** object:

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
    }
}

```

> A **business_process.signers.custom_texts.placement** object:

```json
{ 
    "page": number,
    "x": string, 
    "y": string 
}

```

> A **business_process.signers.custom_texts.dimensions** object:

```json
{ 
    "width": string, 
    "height": string 
}
```

Child Attribute | Type | Description
----------------|------|-------------
business_process.type | string | Currently, the only supported business process type is “signature”.
business_process.expiration_time | string | Expiration time of the business process in Unix. Value must be higher than the current Unix time unless there is no expiration time. Use 0 if there is no expiration time.
business_process.is_sequential | boolean | Boolean indicating if the business process requires signers to sign in a sequence.
business_process.allow_download | boolean | Boolean indicating if the signers are allowed to manually download the signed pdf on Dedoco’s signing app after signing.
business_process.signers | object[] | Array of objects containing details about the signers of the document. The structure of an object is given on the right.
business_process.signers<br>.signer_email | string | Email address of the signer.
business_process.signers<br>.signer_name | string | Name of the signer.
business_process.signers<br>.sequence_number | number | Sequence number of the signer if the business process requires signers to sign in a sequence. Starts from 1. 0 is used if there is no sequence.
business_process.signers<br>.esignatures | object[] | Array of objects. The structure of an object is given on the right.<br>Each object represents an electronic signature placeholder. An electronic signature, as opposed to a digital signature, is an image that is drawn, typed or uploaded by the signer. Use an empty array to indicate that electronic signature is not used. Note that due to the nature of how a digital signature is computed, all signers of the same business process can only and have to sign either using electronic signatures or digital signatures.
business_process.signers<br>.esignatures.placement | object | Object containing information on where the electronic signature is placed on the file. Its structure is shown on the right.
business_process.signers<br>.esignatures.placement.page | number | Page number of the page on which the electronic signature is placed. Starts from 1.
business_process.signers<br>.esignatures.placement.x | string | Float string which indicates the horizontal distance the top left corner of the electronic signature box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the placement of the electronic signature box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
business_process.signers<br>.esignatures.placement.y | string | Float string which indicates the vertical distance the top left corner of the electronic signature box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the placement of the electronic signature box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
business_process.signers<br>.esignatures.dimensions | object | Object containing information on the size of the electronic signature on the file. Its structure is shown on the right.
business_process.signers<br>.esignatures.dimensions.width | string | Float string which indicates the width of the electronic signature box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the width of the electronic signature box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the electronic signature box for the entire signature box to be contained within the page) is less than 1. Note that currently, even though this value will be validated and stored, the stored value will not be used by Dedoco’s signing app because electronic signatures are displayed in a fixed height:width ratio (1:2 specifically) using business_process.signers.esignatures.dimensions.height as reference.
business_process.signers<br>.esignatures.dimensions.height | string | Float string which indicates the height of the electronic signature box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the height of the electronic signature box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the electronic signature box for the entire signature box to be contained within the page) is less than 1.
business_process.signers<br>.digi_signatures | object[] | Array of objects. The structure of an object is given on the right.<br>Each object represents a digital signature placeholder. A digital signature, as opposed to an electronic signature, is a signature derived cryptographically using the signer’s credentials. Use an empty array to indicate that digital signature is not used. Note that due to the nature of how a digital signature is computed, all signers of the same business process can only and have to sign either using electronic signatures or digital signatures.
business_process.signers<br>.digi_signatures.type | string | Values could be “ndi” or “blockchain”. Currently, only “ndi” is supported. Note that only one “ndi” signature can be added for a signer.
business_process.signers<br>.digi_signatures.placement | object | Object containing information on where the digital signature is placed on the file. Its structure is given on the right.
business_process.signers<br>.digi_signatures.placement.page | number | Page number of the page on which the digital signature is placed. Starts from 1.
business_process.signers<br>.digi_signatures.placement.x | string | Float string which indicates the horizontal distance the top left corner of the digital signature box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the placement of the electronic signature box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
business_process.signers<br>.digi_signatures.placement.y | string | Float string which indicates the vertical distance the top left corner of the electronic signature box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the placement of the electronic signature box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
business_process.signers<br>.digi_signatures.dimensions | object| Object containing information on the size of the digital signature on the file. Its structure is given on the right.
business_process.signers<br>.digi_signatures.dimensions.width | string | Float string which indicates the width of the digital signature box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the width of the digital signature box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the digital signature box for the entire signature box to be contained within the page) is less than 1. Note that currently, even though this value will be stored, the stored value will not be used by Dedoco’s signing app because only “ndi” digital signatures are supported and their sizes are fixed.
business_process.signers<br>.digi_signatures.dimensions.height | string | Float string which indicates the height of the digital signature box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the height of the digital signature box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the digital signature box for the entire signature box to be contained within the page) is less than 1. Note that currently, even though this value will be stored, the stored value will not be used by Dedoco’s signing app because only “ndi” digital signatures are supported and their sizes are fixed.
business_process.signers<br>.custom_texts | object[] | Array of objects. The structure of an object is given on the right.<br>Note that due to the nature of how a digital signature is computed, there cannot be custom texts if the business process has more than one signers and “ndi” digital signatures are used (i.e. an empty array is expected).
business_process.signers<br>.custom_texts.descriptor | string | Brief description of custom text. Helps the signer know what to fill into the custom text field. If any of the special values is used, the custom text field will be displayed in the signing app differently from a typical custom text field. Currently, the special values are the strings “Actual Date” and “Custom Date”. If “Actual Date” is used, the signing app will generate the signer’s current date within the custom text box when the signer accesses the signing link (i.e. the signer does not get to edit the value of the custom text). If “Custom Date” is used, the signing app will display a calendar for the signer to choose a date.
business_process.signers<br>.custom_texts.is_mandatory | boolean | Boolean indicating whether or not filling in the custom text is mandatory.
business_process.signers<br>.custom_texts.placement | object | Object containing information on where the custom text is placed on the file. Its structure is given on the right.
business_process.signers<br>.custom_texts.placement.page | number | Page number of the page on which the custom text is placed. Starts from 1.
business_process.signers<br>.custom_texts.placement.x | string | Float string which indicates the horizontal distance the top left corner of the custom text box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the placement of the custom text box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the custom text box for the entire box to be contained within the page) is strictly less than 1.
business_process.signers<br>.custom_texts.placement.y | string | Float string which indicates the vertical distance the top left corner of the custom text box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the placement of the custom text box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the custom text box for the entire box to be contained within the page) is strictly less than 1.
business_process.signers<br>.custom_texts.dimensions | object | Object containing information on the size of the custom text on the file. Its structure is given on the right.
business_process.signers<br>.custom_texts.dimensions.width | string | Float string which indicates the width of the custom text box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page.<br>For example, to set the width of the custom text box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the custom text box for the entire box to be contained within the page) is less than 1. Note that if the business_process.signers.custom_texts.descriptor is “Actual Date” or “Custom Date”, even though this value will be validated and stored, the stored value will not be used by Dedoco’s signing app because “Actual Date”s and “Custom Date”s are displayed in a fixed height:width ratio (1:4 specifically) using business_process.signers.custom_texts.dimensions.height as reference.
business_process.signers<br>.custom_texts.dimensions.height | string | Float string which indicates the height of the custom text box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page.<br>For example, to set the height of the custom text box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the custom text box for the entire box to be contained within the page) is less than 1.
business_process.<br>completion_requirement.min_number | number | Minimum number of signers who have signed for the business process to be ‘completed’. If the business process has a signing sequence defined, this number is expected to be equal to the total number of signers of the business process.

Responses: 

> Response body for code 201

```json
{ 
    "document": Document,
    "businessProcess": BusinessProcess,
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

> Response body for code 5xx:

```json
{
    "statusCode": number,
    "message": string,
    "error": string 
}
```

Code | Description
-----|------------
201 | Document, along with its business process (if any), has been successfully added to the specified folder. 
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
document | Document | A Document object. Refer to Documents for more details.
businessProcess | BusinessProcess | A BusinessProcess object. Refer to Business Processes for more details.
links | object[] | Array of objects containing information on the signing links for each signer involved. The structure of an object is given on the right.
links.documentId | string | Id of the document the link attribute in the same object is for.
links.documentName | string | Name of the document the link attribute in the same object is for.
links.businessProcessId | string | Id of the business process the link attribute in the same object is for.
links.signerId | string | Id of the signer the link attribute in the same object is for.
links.signerName | string | Name of the signer the link attribute in the same object is for.
links.signerEmail | string | Email of the signer the link attribute in the same object is for.
links.link | string | The base URL of the signing link for the specified signer. To complete the signing link, append the base64 encoding of the file retrieval URL for Dedoco to retrieve the relevant file to sign on. The file retrieval URL should accept a GET request for Dedoco to retrieve the relevant file and should return a JSON object `{ file: string }` where file is the base64 string of the retrieved pdf.<br>For example, if the file retrieval URL is “https://www.sample-url.com/path/file-id”, the base64 encoding would be “aHR0cHM6Ly93d3cuc2FtcGxlLXVybC5jb20vcGF0aC9maWxlLWlkIA==”. Appending “aHR0cHM6Ly93d3cuc2FtcGxlLXVybC5jb20vcGF0aC9maWxlLWlkIA==” to the base URL obtained from this request would complete the signing link.<br>Note that the file retrieval URL appended should always be retrieving the most up-to-date (i.e. with signatures if there were previous signers) pdf. In the case of a business process with a signing sequence defined, the client should send out the links in the same sequence (even though Dedoco prevents a signer from signing before the previous signer has signed) and make sure that the appended URL retrieves the latest pdf with signatures (if any). And in the case of a business process where signers are allowed to sign simultaneously or in any order, the client can send out the links to all the signers at the same time but should make sure that the appended URL always retrieves the latest pdf with signatures (if any) so that the signers will receive the right pdf to sign on.

> Sample request header:

```http
POST https://beta-api.dedoco.com/api/v1/public/documents HTTP/1.1
content-type: application/json
Authorization: Bearer <token>
```

> Sample request body:

```json
{
   "document_name": "sample_pdf",
   "date_created": 1616387681,
   "file_type": "pdf",
   "document_hash": "45c6fca6181822fe43912280bedd1f5766174ed69abd3b74cd3c3c0fbb8fe2e8",
   "parent_folder": "60581dbbc79151135ddd6b57",
   "business_process": {
       "type": "signature",
       "expiration_time": 0,
       "is_sequential": false,
       "allow_download": true,
       "signers": [
           {
               "signer_email": "alicia@gmail.com",
               "signer_name": "Alicia Macaron",
               "sequence_number": 0,
               "esignatures": [
                   {
                       "placement": {
                           "page": 1,
                           "x": "0.5",
                           "y": "0.5"
                       },
                       "dimensions": {
                           "width": "0.01",
                           "height": "0.005"
                       }
                   }
               ],
               "digi_signatures": [],
               "custom_texts": []
           }
       ],
       "completion_requirement": {
           "min_number": 1
       }
   }
}
```

> Sample response header:

```http
Sample Response:
HTTP/1.1 201 Created
```

> Sample response body:

```json
{
  "document": {
    "id": "605d8bdf00f7ab4d00541d3b",
    "name": "sample_pdf",
    "file_type": "pdf",
    "date_created": "2021-03-22T04:34:41.000Z",
    "document_hashes": [
      "45c6fca6181822fe43912280bedd1f5766174ed69abd3b74cd3c3c0fbb8fe2e8"
    ],
    "business_processes": [
      "605d8bdf00f7ab4d00541d3c"
    ],
    "status": "active",
    "parent_folder": "60581dbbc79151135ddd6b57",
    "history": [
      {
        "action": "create Document",
        "actor": {
          "id": "8b222cbb-06e3-4f9b-b79c-08cac27e8faa",
          "email": "jimmylee@gmail.com",
          "name": "Jim Lee"
        },
        "timestamp": "2021-03-22T04:34:41.000Z",
        "transaction_hash": ""
      },
      {
        "action": "add Business Process (Signature) with id: 605d8bdf00f7ab4d00541d3c",
        "actor": {
          "id": "8b222cbb-06e3-4f9b-b79c-08cac27e8faa",
          "email": "jimmylee@gmail.com",
          "name": "Jim Lee"
        },
        "timestamp": "2021-03-22T04:34:41.000Z",
        "transaction_hash": ""
      }
    ]
  },
  "businessProcess": {
    "id": "605d8bdf00f7ab4d00541d3c",
    "type": "signature",
    "date_created": "2021-03-22T04:34:41.000Z",
    "expiration_time": "1970-01-01T00:00:00.000Z",
    "document_id": "605d8bdf00f7ab4d00541d3b",
    "allow_download": true,
    "signers": [
      {
        "has_signed": false,
        "signer_id": "SIG_605d8bdf00f7ab4d00541d3d",
        "signer_name": "Alicia Macaron",
        "signer_email": "alicia@gmail.com",
        "sequence_number": 0,
        "esignatures": [
          {
            "placement": {
              "page": 1,
              "x": "0.5",
              "y": "0.5"
            },
            "dimensions": {
              "width": "0.01",
              "height": "0.005"
            }
          }
        ],
        "digi_signatures": [],
        "custom_texts": []
      }
    ],
    "sequential_requirement": [],
    "completion_requirement": {
      "min_number": 1,
      "overriding_signers": []
    },
    "status": "pending",
    "history": [
      {
        "action": "create Business Process (Signature)",
        "actor": {
          "id": "8b222cbb-06e3-4f9b-b79c-08cac27e8faa",
          "email": "jimmylee@gmail.com",
          "name": "Jim Lee"
        },
        "timestamp": "2021-03-22T04:34:41.000Z",
        "transaction_hash": ""
      }
    ]
  },
  "links": [
    {
      "documentId": "605d8bdf00f7ab4d00541d3b",
      "documentName": "sample_pdf",
      "businessProcessId": "605d8bdf00f7ab4d00541d3c",
      "signerId": "SIG_605d8bdf00f7ab4d00541d3d",
      "signerName": "Alicia Macaron",
      "signerEmail": "alicia@gmail.com",
      "link": "https://sample-url.com/public/sign/605d8bdf00f7ab4d00541d3c/SIG_605d8bdf00f7ab4d00541d3d"
    }
  ]
}
```

<aside class="success">
A sample request and response are given on the right.
</aside>


## Endpoint: Void Document
**PUT /public/documents/{document_id}/status HTTP/1.1**<br>
Changes a document’s status to “voided”. Statuses of attached business processes will also be changed to “voided”. 
“voided” status indicates that the document is invalid and cannot be edited anymore. 

**Authorization:**<br>
Bearer \<jwt_token>

<aside class="notice">
Only the owner of the document is authorised to make the request. 
</aside>

<aside class="warning">
WARNING: this action is irreversible!
</aside>

Header Parameter:

Parameter | Type | Description | Required/Optional
----------|------|-------------|-------------------
jwt_token | string | Token obtained by the client through the Get JWT Token endpoint. | Required

Path Parameter:

Parameter | Type | Description | Required/Optional
----------|------|-------------|-------------------
document_id | string | Id of the document to void. | Required

Request Body:

Attribute | Type | Description | Required/Optional
----------|------|-------------|--------------------
value | string | New status of the document to change to. Only the value “voided” is accepted. | Required
request_date | number | Date of request in Unix time. | Required

Responses:

> Response body for code 4xx:

```json
{
  "statusCode": number,
  "message": string,
  "error": string 
}

```

> where **statusCode** is the status code of the error, 
**message** is a string describing the cause of error and 
**error** is a string describing the type of error.

> Response body for code 5xx: 

```json
{
    "statusCode": number,
    "message": string,
    "error": string 
}

```

> where **statusCode** is the status code of the error, 
**message** is a string describing the cause of error and 
**error** is a string describing the type of error.

Code | Description
-----|-------------
201 | Document has been successfully voided. Returns an empty JSON object `{ }` as its body.
4xx | Errors caused by API consumers. Error codes such as 400, 401, 403 and 404 can be expected if incorrect requests are made to the API.
5xx | Errors caused by the API provider or its dependencies. Error codes such as 500, 502 and 503 can be expected if there is an issue on the API side.

> Sample request header:

```http
PUT https://beta-api.dedoco.com/api/v1/60581f41c79151135ddd6b65/status HTTP/1.1
content-type: application/json
Authorization: Bearer <token>

``` 

> Sample request body: 

```json
{
   "status": "voided",
   "request_date": 1616388028
}

```

> Sample response header:

```http
HTTP/1.1 200 OK
```

> Sample response body: 

```json
{ }
```
