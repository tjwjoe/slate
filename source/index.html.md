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

> where
* businessProcessId is the id of the business process the status is sent for
* status is a string describing the state of the business process
* signers is an array of objects containing information on each signer. 
  * signers.has_signed indicates if the signer has signed. 
  * signers.sequence_number exists only if the signers are required to sign in a sequence for the business process.


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

Attribute | Type | Description | Required/Optional
----------|------|-------------|------------------


# E. Documents

# F. Business Processes
