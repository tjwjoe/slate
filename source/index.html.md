---
title: Dedoco Platform Open API Specifications

includes: 
  - folders
  - documents
  - business_processes

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
Application Type: [ Internal Use | Public SaaS | Enterprise App | Development | Testing | Others ]<br>
Application URL:

After your application has been reviewed and approved, you will receive an email containing the API credentials to access the Dedoco EA API platform. 

# B. Sample API Flows

<img src="images/sample_api_flows.png">

# C. Authentication

Dedoco uses JWT tokens to allow access to Dedoco’s public API. Clients can request for a JWT token through the Get JWT token endpoint. Dedoco expects for the JWT token to be included in all API requests to the server in a header that looks like the following:

Authorization: Bearer \<jwt_token>

where jwt_token should be replaced with the obtained JWT token. The following section describes the attributes of the JWT token.

## JWT Payload

Attribute | Type | Description
----------|------|-------------
subId | string | Id of the subject (i.e. client id).
subTyp | string | Type of the subject. “public” in this case.
usrName | string | Name of the client’s user the client is requesting the JWT token on behalf of. This attribute will not exist if not provided in the Get JWT token endpoint.
usrEmail | string | Email address of the client’s user the client is requesting the JWT token on behalf of. This attribute will not exist if not provided in the Get JWT token endpoint.
iat | number | Unix time the JWT token was issued at.
exp | number | Unix time the JWT token will expire by.
v | number | Version number of Dedoco’s API that generated the JWT token.

## Endpoint: Get JWT Token<a name="get-jwt-token"></a>
**POST /public/auth/token HTTP/1.1**<br>
Generates a JWT token.

**Authorization:**<br>
Basic <client_id> <client_secret>

**Content-Type:**<br>
application/json
  
Header Parameters:

Parameters | Type | Description | Required/Optional
-----------|------|-------------|------------------
client_id | string | Id sent to the client during the onboarding process | Required
client_secret | string | Secret sent to the client during the onboarding process. | Required

**Request Body:**

> The callback url for fileCallback should accept POST requests with the following request body: 

```json
{
  "businessProcessId": string,
  "file": string
}
```

> where **businessProcessId** is the id of the business process the updated file is sent for and<br>
**file** is the base64 string of the updated pdf.

> The callback url for callbackStatus should accept POST requests with the request body:

```json
{
  "businessProcessId": string,
  "status": string,
  "signers": {
    "id": string,
    "name": string,
    "email": string,
    "sequence_number": number,
    "has_signed": boolean
  }[]
}
```

> where **businessProcessId** is the id of the business process the status is sent for,<br>
**status** is a string describing the state of the business process,<br>
**signers** is an array of objects containing information on each signer,<br> 
**signers.has_signed** indicates if the signer has signed, and<br>
**signers.sequence_number** exists only if the signers are required to sign in a sequence for the business process.


Attribute | Type | Description | Required/Optional
----------|------|-------------|------------------
fileCallback | string | Client’s callback url for Dedoco to send updated files to. | Required 
statusCallback | string | Client’s callback url for Dedoco to notify the client on changes in the statuses of the client’s business processes. | Required
userName | string | Name of the client’s user the client is requesting the JWT token on behalf of. | Optional
userEmail | string | Email address of the client’s user the client is requesting. | Optional

**Responses:**

> Response body for code 201:

```json
{
  "token": string
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
-----|------------
201 | JWT token has been successfully granted.
4xx | Errors caused by API consumers. Error codes such as 400, 401, 403 and 404 can be expected if incorrect requests are made to the API.
5xx | Errors caused by the API provider or its dependencies. Error codes such as 500, 502 and 503 can be expected if there is an issue on the API side.

Child attribute | Type | Description
----------------|------|-------------
token | string | Base64 string of the encoded JWT token. Refer to JWT payload for more details.

> Sample Request Header:

```http
POST https://beta-api.dedoco.com/api/v1/public/auth/token HTTP/1.1
content-type: application/json
Authorization: Basic <id> <secret>
```

> Sample Request Body:

```json
{
   "fileCallback": "https://sample-url.com/file-callback",
   "statusCallback": "https://sample-url.com/status-callback",
   "userName": "Jim Lee",
   "userEmail": "jimmylee@gmail.com"
}
```

> Sample Response Header:

```http
HTTP/1.1 201 Created
```

> Sample Request Body

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWJJZCI6IjhiMjIyY2JiLTA2ZTMtNGY5Yi1iNzljLTA4Y2FjMjdlOGZhYSIsInN1YlR5cCI6InB1YmxpYyIsInVzck5hbWUiOiJKaW0gTGVlIiwidXNyRW1haWwiOiJqaW1teWxlZUBnbWFpbC5jb20iLCJ2IjoiMSIsImlhdCI6MTYxNjc0MjMzNSwiZXhwIjoxNjE2Nzg1NTM1fQ...."
}
```

<aside class ="success">
A sample request and response are given on the right.
</aside>



