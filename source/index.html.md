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
subId | string<br><br>Id of the subject (i.e. client id).
subTyp | string<br><br>Type of the subject. “public” in this case.
usrName | string<br><br>Name of the client’s user the client is requesting the JWT token on behalf of. This attribute will not exist if not provided in the Get JWT token endpoint.
usrEmail | string<br><br>Email address of the client’s user the client is requesting the JWT token on behalf of. This attribute will not exist if not provided in the Get JWT token endpoint.
iat | number<br><br>Unix time the JWT token was issued at.
exp | number<br><br>Unix time the JWT token will expire by.
v | number<br><br>Version number of Dedoco’s API that generated the JWT token.
