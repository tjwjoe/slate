---
title: Dedoco Platform Open API Specifications

search: true
---

# Introduction

Welcome to Dedoco Platform API Specifications for Developers!

This document contains the API specifications for integration with the Dedoco Platform. The Dedoco platform manages business and document processes and stores the relevant evidence on blockchain. Dedoco APIs interface between the frontend, application logic and the blockchain smart contracts, allowing the document process to be managed in a trusted and immutable manner.

Note for Dedoco Partners integration with API: The data schema and API endpoints described below are generic for the Dedoco platform. When required (depending on the type of integration), custom endpoints can be provided based on the partner’s processes.

### Version History
Date | Content
-----| --------
10 Mar 2021 | Version 0.5<br>Initial Release
12 Mar 2021 | Version 0.6<br>Added attributes details
15 Mar 2021 | Version 0.6a<br>Added API Developer Account process
26 Mar 2021 | Version 0.6b<br>Added sample requests and responses

# A. API Developer Account
To have access to our Early Access (EA) API Platform, please provide the following information and submit your application request to [developers@dedoco.com](mailto:developers@dedoco.com).

Name of Organization:<br>
Business Address:<br>
Business Country:<br>
Business Website URL:<br>

Contact Person (Full Name):<br>
Contact Person (Email):<br>
Contact Person (Mobile):<br>

Application Name:<br>
Application Description/Use Case:<br>
Application Type: [ Internal Use | Public SaaS | Enterprise App | Development | Testing | Others]<br>
Application URL:

After your application has been reviewed and approved, you will receive an email containing the API credentials to access the Dedoco EA API platform. 

# B. Sample API Flows

# C. Authentication

Dedoco uses JWT tokens to allow access to Dedoco’s public API. Clients can request for a JWT token through the Get JWT token endpoint. Dedoco expects for the JWT token to be included in all API requests to the server in a header that looks like the following:

Authorization: Bearer \<jwt_token>

where jwt_token should be replaced with the obtained JWT token. The payload of the JWT token has the following attributes.

## JWT Payload

Attribute | Description
--- | ---
subId | **string**<br>Id of the subject (i.e. client id).
subTyp | **string**<br>Type of the subject. “public” in this case.
usrName | **string**<br>Name of the client’s user the client is requesting the JWT token on behalf of. This attribute will not exist if not provided in the Get JWT token endpoint.
usrEmail | **string**<br>Email address of the client’s user the client is requesting the JWT token on behalf of. This attribute will not exist if not provided in the Get JWT token endpoint.
iat | **number**<br>Unix time the JWT token was issued at.
exp | **number**<br>Unix time the JWT token will expire by.
v | **number**<br>Version number of Dedoco’s API that generated the JWT token.

## Endpoints
### 1. Get JWT Token
POST /public/auth/token<br>
Generates a JWT token.

Authorization:<br>
Basic <client_id> <client_secret>

Content-Type:<br>
application/json
  
Header Parameters:
Parameters | Type | Description | Required/Optional
-----------|------|-------------|------------------
client_id | string | Id sent to the client during the onboarding process | Required
client_secret | string | Secret sent to the client during the onboarding process. | Required

Request Body:

> The callback url for fileCallback should accept POST requests with the request body 

```json
{
    businessProcessId: string,
    file: string
}
```

> where businessProcessId is the id of the business process the updated file is sent for and file is the base64 string of the updated pdf.

> The callback url for callbackStatus should accept POST requests with the request body:

```json
{
  businessProcessId: string,
  status: string,
  signers: {
    id: string,
    name: string,
    email: string,
    sequence_number: number,
    has_signed: boolean
  }[]
}
```

> where businessProcessId is the id of the business process the status is sent for,
status is a string describing the state of the business process,
signers is an array of objects containing information on each signer, 
signers.has_signed indicates if the signer has signed, and
signers.sequence_number exists only if the signers are required to sign in a sequence for the business process.


Attribute | Type | Description | Required/Optional
----------|------|-------------|------------------
fileCallback | string | Client’s callback url for Dedoco to send updated files to. | Required 
statusCallback | string | Client’s callback url for Dedoco to notify the client on changes in the statuses of the client’s business processes. | Required
userName | string | Name of the client’s user the client is requesting the JWT token on behalf of. | Optional
userEmail | string | Email address of the client’s user the client is requesting. | Optional

#### Responses

> Body for code 201:

```json
{
  token: string
}
```

> Body for code 4xx:

```json
{
  statusCode: number,
  message: string,
  error: string
}
```

> where statusCode is the status code of the error, 
message is a string describing the cause of error and error is a string describing the type of error.

> Body for code 5xx:

```json
{
    statusCode: number,
    message: string,
    error: string
}
```

> where statusCode is the status code of the error, 
message is a string describing the cause of error 
and error is a string describing the type of error.

Code | Description
-----|------------
201 | JWT token has been successfully granted.
4xx | Errors caused by API consumers. Error codes such as 400, 401, 403 and 404 can be expected if incorrect requests are made to the API.
5xx | Errors caused by the API provider or its dependencies. Error codes such as 500, 502 and 503 can be expected if there is an issue on the API side.

Child attribute | Description
----------------|------------
token | string<br>Base64 string of the encoded JWT token. Refer to JWT payload for more details.

> Sample Request:

```http
POST https://beta-api.dedoco.com/api/v1/public/auth/token
content-type: application/json
Authorization: Basic <id> <secret>
{
   "fileCallback": "https://sample-url.com/file-callback",
   "statusCallback": "https://sample-url.com/status-callback",
   "userName": "Jim Lee",
   "userEmail": "jimmylee@gmail.com"
}
```

> Sample Response:

```http
HTTP/1.1 201 Created
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWJJZCI6IjhiMjIyY2JiLTA2ZTMtNGY5Yi1iNzljLTA4Y2FjMjdlOGZhYSIsInN1YlR5cCI6InB1YmxpYyIsInVzck5hbWUiOiJKaW0gTGVlIiwidXNyRW1haWwiOiJqaW1teWxlZUBnbWFpbC5jb20iLCJ2IjoiMSIsImlhdCI6MTYxNjc0MjMzNSwiZXhwIjoxNjE2Nzg1NTM1fQ...."
}

```

# D. Folders
Folders are used to group relevant document(s). A folder can have multiple documents but must contain at least one document. 

## Attributes

> Objects in history attribute:

```json
{
  action: string,
  actor: {
    id: string,
    email: string,
    name: string
  },
  timestamp: date,
  transaction_hash: string
}
```

Attribute | Description
----------|------------
id | string<br>Unique identifier of the folder.
name | string<br>Name of the folder set by the creator of the folder. Could be used as a descriptor.
date_created | date<br>Time the folder is created. Cannot be changed after the folder is created.
child_documents | string[]<br>Array of ids of the documents contained in the folder.
linked_folders | string[]<br>Array of ids of linked folders (if any). Linking of folders is an optional functionality to indicate the relevance of the folders to each other but at the same time retaining their segregation as separate folders.
status | string<br>Value could be “active”, “closed” or “voided”. “active” indicates that the folder is valid and still in use. “closed” indicates that the folder is valid but not in use at the moment. “voided” indicates that the folder is invalid and no longer in use.
history | object[]<br>Array of objects involving changes made to the folder

Child Attribute | Description
----------------|------------
history.action | string<br>Description of the action recorded in the history attribute.
history.actor.id | string<br>User id of the entity that took the action.
history.actor.email | string<br>Email address (if any) of the entity that took the action.
history.actor.name | string<br>Name (if any) of the entity that took the action.
history.timestamp | date<br>Time the action took place.
history.timestamp_hash | string<br>Hash of the blockchain transaction updating the data (only if there are any changes in data) on the blockchain due to the action that took place. Can be used to view the transaction details on the blockchain’s explorer.

## Endpoints

### 1. Create Folder
POST /public/folders<br>
Creates a folder along with documents and business processes. 
Each new document can only have one new business process attached. 
Existing folders can be linked to the folder to be created.

Authorization:<br>
Bearer \<jwt_token>

Content-Type:<br>
application/json

Header Parameter:

Parameter | Type | Description | Required/Optional
----------|------|-------------|-------------------
jwt_token | string | Token obtained by the client through the Get JWT Token endpoint. | Required

Request Body:

> Object details for documents attribute:

```json
{
    name: string,
    file_type: string,
    document_hash: string
}
```

> Object details for business_processes attribute:

```json
{
    type: string,
    expiration_time: number,
    document_id: number,
    is_sequential: boolean,
    allow_download: boolean,
    signers: {
      signer_email: string,
      signer_name: string,
      sequence_number: number,
      esignatures: { placement: { page: number, x: string, y: string }, dimensions: { width: string, height: string } }[ ],
      digi_signatures: { type: string, placement: { page: number, x: string, y: string }, dimensions: { width: string, height: string } }[ ],
      custom_texts: { descriptor: string, is_mandatory: boolean, placement: { page: number, x: string, y: string }, dimensions: { width: string, height: string } }[ ]
    }[ ],
    completion_requirement: {
      min_number: number
    }
}
```

Attribute | Type | Description | Required/Optional
----------|------|-------------|------------------
folder_name | string | Name of the folder. Set by the folder's creator. | Required
date_created | date | Date of folder creation in Unix time. | Required
documents | object[] | Array of objects --> | Required
linked_folders | string[] | Array of ids of linked folders (if any). | Optional (Use an empty array if not used)
business_processes | object[] | Array of objects --> | Optional (Use an empty array if not used)

Child attribute | Description
----------------|------------
documents.name | string<br>Name of the document. Does not need to follow the original name of the file and could be used as a descriptor.
documents.file_type | string<br>File type of the file used as the document. Accepted values are “pdf” and “json”, but only “pdf” is supported at the moment.
documents.document_hash | string<br>SHA3-256 hash of the file in hexadecimal format without the ‘0x’ prefix.
business_processes.type | string<br>Currently, the only supported business process type is “signature”.
business_processes.expiration_time | number<br>Expiration time of the business process in Unix. Value must be higher than the current Unix time unless there is no expiration time. Use 0 if there is no expiration time.
business_processes.document_id | number<br>Index of the document in this request body’s documents attribute to attach the business process to.
business_processes.is_sequential | boolean<br>Boolean indicating if the business process requires signers to sign in a sequence. Note that signers have to sign in a sequence if digital signatures are used.
business_processes.allow_download | boolean<br>Boolean indicating if the signers are allowed to manually download the signed pdf on Dedoco’s signing app after signing.
business_processes.signers | object[]<br>
business_processes.signers.signer_email | string<br>Email address of the signer.
business_processes.signers.signer_name | string<br>Name of the signer.
business_processes.signers.sequence_number | number<br>Sequence number of the signer if the business process requires signers to sign in a sequence. Starts from 1. 0 is used if there is no sequence.
business_processes.signers.esignatures | object[]<br>
business_processes.signers.esignatures.placement | object<br>
business_processes.signers.esignatures.placement.page | number<br>Page number of the page on which the electronic signature is placed. Starts from 1.
business_processes.signers.esignatures.placement.x | string<br>Float string which indicates the horizontal distance the top left corner of the electronic signature box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page. For example, to set the placement of the electronic signature box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
business_processes.signers.esignatures.placement.y | string<br>Float string which indicates the vertical distance the top left corner of the electronic signature box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page. For example, to set the placement of the electronic signature box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
business_processes.signers.esignatures.dimensions | object<br>
business_processes.signers.esignatures.dimensions.width | string<br>Float string which indicates the width of the electronic signature box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page. For example, to set the width of the electronic signature box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the electronic signature box for the entire signature box to be contained within the page) is less than 1. Note that currently, even though this value will be validated and stored, the stored value will not be used by Dedoco’s signing app because electronic signatures are displayed in a fixed height:width ratio (1:2 specifically) using business_processes.signers.esignatures.dimensions.height as reference.
business_processes.signers.esignatures.dimensions.height | string<br>Float string which indicates the height of the electronic signature box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page. For example, to set the height of the electronic signature box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the electronic signature box for the entire signature box to be contained within the page) is less than 1.
business_processes.signers.digi_signatures | object[]<br>
business_processes.signers.digi_signatures.type | string<br>Values could be “ndi” or “blockchain”. Currently, only “ndi” is supported. Note that only one “ndi” signature can be added for a signer.
business_processes.signers.digi_signatures.placement | object<br>
business_processes.signers.digi_signatures.placement.page | number<br>Page number of the page on which the digital signature is placed. Starts from 1.
business_processes.signers.digi_signatures.placement.x | string<br>Float string which indicates the horizontal distance the top left corner of the digital signature box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page. For example, to set the placement of the electronic signature box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
business_processes.signers.digi_signatures.placement.y | string<br>Float string which indicates the vertical distance the top left corner of the electronic signature box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page. For example, to set the placement of the electronic signature box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the electronic signature box for the entire signature box to be contained within the page) is strictly less than 1.
business_processes.signers.digi_signatures.dimensions | object<br>
business_processes.signers.digi_signatures.dimensions.width | string<br>Float string which indicates the width of the digital signature box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page. For example, to set the width of the digital signature box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the digital signature box for the entire signature box to be contained within the page) is less than 1. Note that currently, even though this value will be stored, the stored value will not be used by Dedoco’s signing app because only “ndi” digital signatures are supported and their sizes are fixed.
business_processes.signers.digi_signatures.dimensions.height | string<br>Float string which indicates the height of the digital signature box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page. For example, to set the height of the digital signature box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the digital signature box for the entire signature box to be contained within the page) is less than 1. Note that currently, even though this value will be stored, the stored value will not be used by Dedoco’s signing app because only “ndi” digital signatures are supported and their sizes are fixed.
business_processes.signers.custom_texts | object[]<br>
business_processes.signers.custom_texts.descriptor | string<br>Brief description of custom text. Helps the signer know what to fill into the custom text field. If any of the special values is used, the custom text field will be displayed in the signing app differently from a typical custom text field. Currently, the special values are the strings “Actual Date” and “Custom Date”. If “Actual Date” is used, the signing app will generate the signer’s current date within the custom text box when the signer accesses the signing link (i.e. the signer does not get to edit the value of the custom text). If “Custom Date” is used, the signing app will display a calendar for the signer to choose a date.
business_processes.signers.custom_texts.is_mandatory | boolean<br>Boolean indicating whether or not filling in the custom text is mandatory.
business_processes.signers.custom_texts.placement | object<br>
business_processes.signers.custom_texts.placement.page | number<br>Page number of the page on which the custom text is placed. Starts from 1.
business_processes.signers.custom_texts.placement.x	| string<br>Float string which indicates the horizontal distance the top left corner of the custom text box is from the left edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the width of the page. For example, to set the placement of the custom text box’s top left corner to the center of the page horizontally, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the width of the custom text box for the entire box to be contained within the page) is strictly less than 1.
business_processes.signers.custom_texts.placement.y | string<br>Float string which indicates the vertical distance the top left corner of the custom text box is from the top edge of the page. The represented float value is a fraction (in decimal form) whose denominator is the height of the page. For example, to set the placement of the custom text box’s top left corner to the center of the page vertically, a float string of “0.5” should be used. Minimum represented float value is 0 and maximum represented float value (depends on the height of the custom text box for the entire box to be contained within the page) is strictly less than 1.
business_processes.signers.custom_texts.dimensions | object<br>
business_processes.signers.custom_texts.dimensions.width | string<br>Float string which indicates the width of the custom text box. The represented float value is a fraction (in decimal form) whose denominator is the width of the page. For example, to set the width of the custom text box to be half of the page’s width, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the x-coordinate of the custom text box for the entire box to be contained within the page) is less than 1. Note that if the business_processes.signers.custom_texts.descriptor is “Actual Date” or “Custom Date”, even though this value will be validated and stored, the stored value will not be used by Dedoco’s signing app because “Actual Date”s and “Custom Date”s are displayed in a fixed height:width ratio (1:4 specifically) using business_processes.signers.custom_texts.dimensions.height as reference.
business_processes.signers.custom_texts.dimensions.height | string<br>Float string which indicates the height of the custom text box. The represented float value is a fraction (in decimal form) whose denominator is the height of the page. For example, to set the height of the custom text box to be half of the page’s height, a float string of “0.5” should be used. Minimum represented float value is strictly more than 0 and maximum represented float value (depends on the y-coordinate of the custom text box for the entire box to be contained within the page) is less than 1.
business_processes.completion_requirement.min_number | number<br>Minimum number of signers who have signed for the business process to be ‘completed’. If the business process has a signing sequence defined, this number is expected to be equal to the total number of signers of the business process.

Responses:

Code | Body | Description
-----|------|------------
201 | 
4xx | 
5xx | 



# E. Documents

# F. Business Processes
