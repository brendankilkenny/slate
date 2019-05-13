<!-----
title: TransferMate Payments API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:

search: true
----->


**Internal Revision History Tracking (Latest at the top)**
 * Rev. 011 (08-May-2019) - Final Formal Review Draft
   * Updated with comments from an internal peer review.
 * Rev. 010 (08-May-2019) - Updated with a few comments from the formal review.
   * Updated the [login flow](#login-flow) diagram and the [get spot rates flow](#get-spot-rates-flow) diagram with update from Brendan.
   * Added the [Send Query](#send-query) request although this may or may not be required. 
   * Compared the current text against a SWAGGER output doc provided by Brendan.
   * Added the statement "The quoted rates are the market rates. Margins and commissions are not applied." to the "Get Spot Rates" bullet in the [Performing Payment Transactions](#performing-payment-transactions) section.
   * In [Get Supported Holiday Regions](#get-supported-holiday-regions) section, updated the request type from POST to GET.
 * Rev. 009 (01-May-2019) - Second Formal Review Draft  
   * Updated with comments from an internal peer review.
 * Rev. 008 (30-Apr-2019): 
   * Added the remainder of the [Performing Payment Transactions](#performing-payment-transactions) section and the associated APIs.
   * Added the [Calendar Utilities](#calendar-utilities) section and associated APIs. 
 * Rev. 007 (29-Apr-2019): 
   * Updated the login flow image (now shows the Alt text :-)).
   * Added first part of the [Performing Payment Transactions](#performing-payment-transactions) section (3 steps).
   * Added introductory text for JSON examples.
   * Removed the Request Parameters heading and just added introductory text to simplify the structure a little more.
   * Added the [Errors Codes Reference](#error-codes-reference) section at the end.
 * Rev. 006 (26-Apr-2019): 
   * Updated with comments from first formal review
   * Broke API Ref section into categories (bank accounts, beneficiary accounts, and so on)
   * Added styles for Notes, Cautions, and Warnings 
 * Rev. 005 (24-Apr-2019) - First Formal Review Draft 
   * Updated with comments from an internal peer review.
 * Rev. 004 (23-Apr-2019): 
   * Added the [Beneficiary Account Management](#beneficiary-account-management) section and corresponding APIs.
   * Added the [Payment Method/Transaction Type Values](#payment-methodtransaction-type-values), [Transit Code Values](#transit-code-values), [Transfer Reasons](#transfer-reasons), [Payment Status Codes](#payment-status-codes), and [Country Reference by System ID](#country-reference-by-system-id) tables to the Codes and Message Reference section.
   * Adopted the tabular format proposed in Rev. 003. Since most of this is common and repeated, I wonder if we should put in a common area and refer to it as appropriate.
 * Rev. 003 (18-Apr-2019): 
   * Added the Connect to the Payments API (Alternative Tabular Format) section as an example of the format proposed by the development team and the sample table provided by Brendan. If the team is happy with this format, we can adopt it for all the endpoint requests.
   * Added the full Country Codes (ISO 3116) section. 
 * Rev. 002 (17-Apr-2019): 
    * Revised "Bank Account Management" section based on feedback from the pilot project and input from Brendan.
    * Started restructuring the content to separate out product features and usage scenarios (describe first) from API details (provided in an "API Reference"), similar to the way it is done in TW.)
    * Added "Currency Codes" section and placeholder for the "Country Codes" section.
 * Rev. 001 (16-Apr-2019): 
   * Added early draft of Introductory section and Getting Started section based on content provided by Brendan.
   * Added first draft of Authentication section and its three subsections.


# Payments API Overview

Welcome to the TransferMate developer portal. As a leader in international payments, TransferMate gives businesses the power to make faster international transfers with no Business-to-Business (B2B) payment fees using our fully-regulated payments network.

We designed the TransferMate Payments Application Programming Interface (API) to integrate our international payments technology directly into your payments or accounting software. The key benefits of direct integration include:

 * Administrative time savings 
 * Payment time savings
 * Streamlined reconciliation of payment status and Foregin Exchange (FX) information

At the most fundamental level, when you pay with TransferMate you:

 * Lock in a real-time exchange rate
 * Make a payment to a TransferMate account in your country
 * Enable TransferMate to release the funds to your beneficiary in their country

From a business process perspective, our Payments API enables companies to:

 * Obtain and book a payment with a currency rate
 * Authorize or cancel payments
 * Add payer and payee beneficiary bank accounts
 * Get an updated payment status for use in your payments or accounting software

## Getting Started

The Payments API is a REST API that accepts standard HTTP commands (GET, POST, and PUT), returns JSON-encoded responses, and uses standard HTTP response codes. The Payments service includes pre-defined domain-specific endpoints, such as banks and beneficiaries. 

To use the Payments API, you must be authenticated by verifying your identity. See [Authentication](#authentication) for more information. 

To help you use the Payments API, we provide several use cases to get started and reference information that describes each API request in a consistent manner. These API request sections include: 

 * General HTTP Request Information
 * Request Body Payload Example
 * Example Response
 * Error Response 

<aside class="notice">
In the Payments API, IDs are of type <code>Varchar</code>. This is important because IDs are often represented as numerical types.
</aside>

Our mission is to provide customers with everything they need to use the Payments API effectively. If you have issues while using the API, discover shortcomings, or have suggestions for improvements, please do not hesitate to [contact us]. 

[contact us]:https://www.transfermate.com/


## Authentication

Information security is critical, especially in web-based applications. The Payments service uses the OAuth2 framework for advanced authentication. For details of the OAuth2 framework, see the [Oauth2] website. 

[Oauth2]:https://oauth.net/2/

When you log in using your username and password, the Payments service generates an access token. You can use this token for verification in your API requests. The token is valid for a short period, typically 48 hours or until you log out.

You include the token in the ``Authorization: Bearer…`` header for all interactions with the Payments API. 

<aside class="caution">
Authentication codes grant you special privileges to access and use the Payments API. Keep these codes safe and avoid sharing them in insecure environments.
</aside>

Authentication enables you to:

 * [Connect to the Payments Service](#connect-to-the-payments-service) <BR>When you connect (log in) to the Payments service, you receive an access token that you provide for verification in subsequent API requests. The access token is valid for a short time, typically 48 hours or until you log out.
 * [Disconnect from the Payments Service](#disconnect-from-the-payments-service) <BR>When you disconnect from (log out of) the Payments service, you can no longer use API calls and the service revokes the access token granted to you when you logged in.  
 * [Retrieve Historical Connection Information](#retrieve-historical-connection-information) <BR>You can get information about past connections. You can specify the period from which you want to retrieve connection information and there are options for sorting the retrieved information.

The following figure shows the log in flow:

<a name="login-flow"></a><img src="login_flow.jpg" alt="drawing" title="TransferMate Payments Service Log In Flow" width="600"/>

<!---![Log In Process Flow Diagram](login_flow.jpg)-->

## Bank Account Management

The TransferMate Payments API allows an authenticated user to manage personal bank account information. As a regulated entity and to ensure we follow anti-money laundering best-practice, TransferMate must collect the details of the funding bank account in every transaction. 

Using our Payments API, an authenticated user can:

  * [Add a New Bank Account](#add-a-new-bank-account) <BR>You add a new bank account by providing the account details. If the account is created successfully, the service assigns and returns an account ID. When you add a new bank account, the service performs rigorous validation and may report a variety of errors. 

  * [View Existing Bank Account Details](#view-existing-bank-account-details) <BR>You can retrieve the details of existing bank accounts. There is an option to retrieve all active or inactive accounts and you can choose to paginate the results with a certain number of bank accounts presented per page.

  * [Edit Existing Bank Account Details](#edit-existing-bank-account-details) <BR>You can edit a bank account name or deactivate an account. You *cannot* recover or re-use an inactive account. Inactive accounts are removed from all live lists. To view inactive accounts, see [View Existing Bank Account Details](#view-existing-bank-account-details).

  * [Verify Existing Bank Account Details](#verify-existing-bank-account-details) <BR>You can verify the details of an existing bank account. The service uses an internal verifier to perform checks on the provided bank account details to ensure conformance to standards. <BR>To get meaningful results, you must provide at least an International Bank Account Number (IBAN), a Bank Information Code (BIC), or other pertinent bank identification information, for example, a bank number or sort code. <BR>The verifier returns codes and messages that explain the results of the verification. See [Bank Verifier Codes and Messages](#bank-verifier-codes-and-messages) for more information.

The information maintained for a bank account includes the following:

<pre class="center-column">
 - Account ID
 - Account Name
 - Account Description
 - Bank Name
 - Country
 - Currency of Account
 - Payment Type
 - Direct Debit Capability 
 - Wire Transfer Capability
 - Bank Details (such as the IBAN and SWIFT/BIC codes)
 - Active Indicator (indicates if the bank account is active or inactive)
 - Editable Indicator (indicates if the bank details are editable or not)
</pre>


## Beneficiary Account Management

A beneficiary account is the destination of a funds transfer. For example, a supplier bank account in an invoice payments system can be a beneficiary account.

Using the Payments API, you can:

 * [Add a New Beneficiary Account](#add-a-new-beneficiary-account)
   <BR>A beneficiary bank account must be verified before it is added to the system. The bank verifier provides an indication of possible issues when adding a new beneficiary account. 
   <BR>The business team adds details, such as payment types (for example, Direct Debit, ACH, or Wire), when the appropriate details are received.

 * [Delete an Existing Beneficiary Account](#delete-an-existing-beneficiary-account)
   <BR>If a beneficiary account is added in error or with incorrect details, you can delete it.

    <aside class="notice">
    You cannot delete a beneficiary account if there are transactions pending on that account.
    </aside>

 * [Edit an Existing Beneficiary Account](#edit-an-existing-beneficiary-account)
   <BR>Once a beneficiary account is created, you can edit certain details. 
   <BR>You can edit the bank address, names associated with the account, and some non-mandatory fields.
   <BR>You cannot edit account details such as the account ID or IBAN.

 * [View Beneficiary Accounts](#view-beneficiary-accounts)
   <BR>You can get a list of beneficiary accounts filtered by name (a single return) or country (multiple returns).


## Performing Payment Transactions

The TransferMate Payments service enables you to make payments to third parties.

Using the Payments API, you make a payment using the following three steps, the first of which is optional: 

 1. [Get and Review a Currency Exchange Rate](#get-and-review-a-currency-exchange-rate) (Optional)<BR>You can query the exchange rate for a currency. The returned rate remains valid for 90 seconds. The Payments service does not create a record in the TransferMate system in this case. If you are happy with the retrieved rate, you can use it in the lock-in and authorization steps that follow. 

 2. [Lock in an Exchange Rate for a Payment Transaction](#lock-in-an-exchange-rate-for-a-payment-transaction)<BR>This operation is similar to the action in the previous step. In this case however, the Payments service creates a record in the TransferMate system.

 3. [Authorize One or More Payment Transactions](#authorize-one-or-more-payment-transactions)<BR>You can authorize one or more payments in a batch transaction. If the batch transaction includes payment transactions that are not recognized by the service, these are highlighted as erroneous payment transactions. 

In addition to the steps provided above, the Payments API provides other useful features that relate to transactions: 

 * [Get Transaction Summary](#get-transaction-summary)<BR>Using this feature, you can retrieve details of a specific transaction or group of transactions. You can specify filter values to narrow the returned transactions to exactly the subset you want. For example, you can set a filter value on the `ref_number` field to retrieve just the details of the one transaction that matches the value of `ref_number` field. Alternatively, you can set filters on the `bank_id_from` field, the `date_from` field, and the `date_to` field, to retrieve transactions associated with a specific bank between the from and to dates you specify.

 * [Get Spot Rates](#get-spot-rates)<BR>Using this feature, you can retrieve spot rates for currency excahange transactions. You specify the amount you want to exchange and the pairs of to/from currencies for which you wish to get the relevant exchange rate information. The Payments service responds with bid, ask, and middle rates and the corresponding amounts in both the to and from currencies you specify. The times associated with the quoted rates are also included. The quoted rates are the market rates. Margins and commissions are not applied.

The following figure shows the get spot rates flow:

<a name="get-spot-rates-flow"></a><img src="get_spot_rates_flow.jpg" alt="drawing" title="Payments API Get Spot Rates Flow" width="600"/>

* [Send Query](#send-query)<BR>Using this feature, a user/client can request some additional information or submit an
issue relating to particular transaction. This is an out-of-band process that creates a ticket in the
internal system to which a customer service agent responds. 

## Calendar Utilities

The Payments API provides two utilities related to calendar functions:

 * [Check for Holiday](#check-for-holiday)<BR>Using this feature, you can check if there are any holidays that coincide with the data returned for the suppling region.

 * [Get Supported Holiday Regions](#get-supported-holiday-regions)<BR>Using this feature, you can retrieve a list of the holiday regions supported by the Google Calendar API.


<!-- API REFERENCE STARTS HERE -->

# Authentication

## **Connect to the Payments Service**

You connect to the Payments service to log in. You do this by sending an HTTP **POST** request with the required account login information to the ``/connect`` endpoint.

| | |
|-|-|
|**Request**| **POST** `/api/connect` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `-` |
|||


### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "username":"ba11b15fd7d94b0abfd732a286f534cf",
    "password":"C=EapNMbw+TL5jCs5P&HJtq4u=wB2^!?"
}
```

> The following is an older request body payload example that has been deprecated. This example is only included for reasons of backward compatibility. For future compatibility and greater security, avoid using this payload format.

```json
{
    "username":"ba11b15fd7d94b0abfd732a286f534cf",
    "password":"C=EapNMbw+TL5jCs5P&HJtq4u=wB2^!?",
    "endpoint":"https://customersiteid999.transfermate.com",
    "app_secret":"bcef4674#a653efa98700e830_cc7106e"
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter|Description      |Type|Size|Mandatory|Default Value|
---------------|-----------|:--:|:--:|:-------:|-------------|
`username`|The name a user provided when creating an account.|Varchar|32|Yes|-|
`password`|The password a user provided when creating an account.|Varchar|32|Yes|-|
| | | | | |


### **Example Response**

> The JSON structure in a successful response is as follows:

```json
{
    "token":"6e12dac6d8e172f304b5edb94c4312d3ae307dc71f9a49e0f89baa4cecd52623-58406"
}
```

If the connect request is successful, the service returns an access token.


Field | Description | Type
----- | ----------- | ----
`token` | An access token to use as verification in service requests. | Varchar
| | 


### **Errors**

If the connect request is not successful, the service responds with an error code, rejects the request, and denies access to the Payments service. Some example error responses are provided. 

<!-- #### Error Code 403 - Incorrect Credentials -->
> Error Code 403 - Incorrect Credentials

```json
{
    "stat" : "incorrect credentials. caution: multiple incorrect attempts"
}
```

<!-- #### Error Code 403 - Missing or Malformed Endpoint -->
> Error Code 403 - Missing or Malformed Endpoint

```json
{
    "stat" : "missing or malformed endpoint"
}
```

<!-- #### Error Code 403 - Too Many Incorrect Attempts -->
> Error Code 403 - Too Many Incorrect Attempts 

```json
{
    "stat" : "too many incorrect attempts. account is locked"
}
```

<aside class="notice">
In the case of too many login errors, the account is locked, and you must contact an administrator to unlock it.
</aside>


The JSON structure associated with the error responses has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **Disconnect from the Payments Service**

You disconnect from the Payments service to log out. You do this by sending an HTTP **GET** request to the `/disconnect` endpoint. The access token that you used for API request verification while connected becomes invalid. 

| | |
|-|-|
|**Request**| **GET** `/api/disconnect` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | - |
| | |

### **Request Body Payload Example**

None.

<!--### **Request Parameters**

None.-->

### **Example Response**

If the disconnect request is successful, the service responds with a logged out message.

> The returned JSON structure is like this:

```json
{
    "stat" : "logged out"
}
```

Field | Description | Type
----- | ----------- | ----
`stat` | A message that indicates the status. | Varchar
| | 

### **Errors**

If the disconnect request is not successful, the service responds with an error code, rejects the request and does not perform the log out operation. The relevant error response is provided. 

<!--#### Error Code 403 - Session Not Available or Incorrect SSO Token-->
> Error Code 403 - Session Not Available or Incorrect SSO Token

```json
{
    "stat" : "this session is not available, or the sso token is incorrect"
}
```

The JSON structure associated with the error response has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **Retrieve Historical Connection Information**

You can retrieve details of historical Payments service connections within a specific time period. You do this by sending an HTTP **POST** request to the `/connection_history` endpoint. The response JSON structure contains details of each connection. The details include the user ID, the date and time, the IP address, the country, and the local date and time.

| | |
|-|-|
|**Request**| **POST** `/api/connection_history` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "date_from" : "02-02-2018",
    "date_to" : "20-02-2018",
    "order_by" : "ID",
    "order_type" : "ASC",
    "page_no": "1"
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter|Description      |Type|Size|Mandatory|Default Value|
---------------|-----------|:--:|:--:|:-------:|-------------|
`date_from`|Specifies the start date of the period for which you want to retrieve connection information. <BR>The format must be dd-mm-yyyy.|date|-|Yes|-|
`date_to`|Specifies the end date of the period for which you want to retrieve connection information. The parameter is optional. If unspecified, the end date is the current date. <BR>The format must be dd-mm-yyyy.|date|-|No|-|
`order_by`|The field to use for sorting. Specifying the `ID` field gives the connections in chronological order by ID. |Varchar|20|No|-|
`order_type`|The sort order type, which can be `ASC` (ascending) or `DESC` (descending).|Varchar|20|No|-|
`page_no`|A page navigation value for pagination and retrieving a specific page in the grid. <BR>`LB>>Need a better description here.`|Varchar|-|Yes|-|
| | | | | |


### **Example Response**

If the request is successful, the service returns a response. 

> The returned JSON structure is like this:

```json
{
    "errors" : [],
    "rows" : [
    {
        "uid" : "0",
        "date" : "Feb 02, 2018 16:47:32",
        "ip" : "193.105.145.29",
        "country" : "Ireland",
        "local" : "Feb 02, 2018 16:47:32"
    },
    {
        "uid" : "1",
        "date" : "Feb 02, 2018 16:47:33",
        "ip" : "193.105.145.29",
        "country" : "Ireland",
        "local" : "Feb 02, 2018 16:47:33"
    },
    {
        "uid":"2",
        "date":"Feb 02, 2018 16:47:34",
        "ip":"193.105.145.29",
        "country":"Ireland",
        "local":"Feb 02, 2018 16:47:34"
    }
    ...
    ],
    "message" : []
}
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`errors` | A list of error codes. <BR>`LB>>How is this different for the normal error codes?`| list
`rows` | A list of connections. | list
`uid` | The ID of the user that connected. | Varchar
`date` | The date and time of the connection. | Varchar
`ip` | The IP address from which the connection was made. | Varchar
`country` | The country from which the connection was made. | Varchar
`local` | The local date and time of the connection. <BR>`LB>>How is this different than the date parameter above?` | Varchar
`message` | One or more messages associated with the request. <BR>`LB>>Do we have any more details on these messages or their intended use?`| list
 | | 


### **Errors**

If the request is not successful, the service responds with an error code, rejects the request, and does not provide the historical connection information. The relevant error response is provided. 

<!--#### Error Code 403 - Invalid Parameter Format-->
> Error Code 403 - Invalid Parameter Format

`LB>>Need to confirm that the following is a 403 error code.`

```json
{
    "stat" : "invalid parameter format"
}
```

The JSON structure associated with the error response has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 

# Bank Accounts

## **Add a New Bank Account**

You add a new bank account by sending an HTTP **PUT** request with the required account information to the ``/add_bank`` endpoint. 


<aside class="warning">
In earlier versions of this API, you could add a new bank account with an HTTP <b>POST</b> request. The <b>POST</b> method is now deprecated in favor of the <b>PUT</b> request. We recommend that you use the <b>PUT</b> request because the <b>POST</b> method will be removed from future API versions.
</aside>


| | |
|-|-|
|**Request**| **PUT** `/api/add_bank` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
  "country_id":"14", 
  "currency_type_id":"EUR",
  "iban_account_number":"bsb_14",
  "transit_code":"082082",
  "swift":"321894",
  "bank_name":"Allied Bank"
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter|Description|Type|Size|Mandatory|Default Value|
---------------|-----------|:--:|:--:|:-------:|-------------|
`country_id`|An ID from the list of valid country IDs, for example, `IE` (Ireland).  See [Country Reference by System ID](#country-reference-by-system-id) for more information.<BR>`LB>Should this be an ISO country code now?`|Varchar|3|Yes|-|
`currency_type_id`|An ID from the list of valid currency IDs, for example, `EUR` (Euro). <BR>See [Currency Codes (ISO 4217)](#currency-codes-iso-4217) for more information.|Varchar|3|Yes|-|
`iban_account_number`|A valid IBAN account number. <BR>For more information, see Appendix A → IBAN A/C Number. <LB>``LB>>Which section in Appendix A applies here?. Also we should give an example number if we can.``|TBD|TBD|Yes|-|
`transit_code`|A code from the list of valid transit codes, for example, `sort_code_2` (Sort Code). <BR>See [Transit Code Values](#transit-code-values) for more information.|TBD|TBD|Yes|-|
`swift`|The Swift or IBAN code if known, for example, ``321894``. <BR>``LB>>Seems to be contradiction here; column 5 indicates this parameter is mandatory.``|Varchar|50|Yes|-|
`bank_name`|The name of the bank account, for example, ``Allied Bank``.|Varchar|50|Yes|-|
 | | | | | |


### **Example Response**

If the add bank request is successful and the service allocates an account ID, the service returns a response.

> The JSON structure of the response is as follows:

```json
{
    "stat" : "success",
    "qid" : 321894
}
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat` | The status of the request, ``success``. | Varchar
`qid` | The ID of the bank account, for example, ``321894``. | Integer
 | |


> If the add bank request is successful, but an account ID is *not* allocated, the JSON structure in the response is as follows:

```json
{
    "stat":"success_noid",
    "message":"The bank has been successfully added, but the qid could not be returned in time for this call" 
}
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat` | The status of the operation, ``success_noid``. | Varchar
`message` | The following text message "The bank has been successfully added, but the ``qid`` could not be returned in time for this call." | Varchar
 | | 


### **Errors**

If the add bank request is not successful, the service responds with an error code, rejects the request, and does not create the bank account. Some examples of error responses are provided.

<!--#### Error Code 531 - Account Exists, Unknown Currency, or Operation Unsuccessful-->
> Error Code 531 - Account Exists, Unknown Currency, or Operation Unsuccessful

```json
[
    {
        "ID": "account_number",
        "description": "Already exists!"
    },
    {
        "ID": "currency_id",
        "description": "Unknown currency"
    },
    {
        "ID": "db_action",
        "description": "Operation is not successful! Please try again."
    }
]
```

<!--#### Error Code 532 - One or More JSON Attributes Missing or Malformed-->
> Error Code 532 - One or More JSON Attributes Missing or Malformed

```json
{
    "stat" : "One or more JSON attributes missing or malformed: country_id"
}
```

<!--#### Error Code 533 - Invalid Token or Missing Return Data-->
> Error Code 533 - Invalid Token or Missing Return Data

```json
{
    "stat" : "Invalid token or missing return data"
}
```

The JSON structures associated with the error responses have the following parameters:.

Field | Description | Type
----- | ----------- | ----
`ID`  | The source of the error, for example, account number, currency ID, or database action. | Varchar
`description`  | A description of the error encountered. | Varchar
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **View Existing Bank Account Details**
You can get a list of active or inactive bank accounts with details by sending an HTTP **POST** request to the ``/view_banks`` endpoint. 

Optionally, you can paginate the results to retrieve a certain number of bank accounts per page. If there are more than 25 bank accounts in the response, pagination occurs automatically.

| | |
|-|-|
|**Request**| **POST** `/api/view_banks` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |


### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "show" : 1,
    "page_no" : 1,
    "with_pagination_data" : 1
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter|Description      |Type|Size|Mandatory|Default Value|
---------------|-----------|:--:|:--:|:-------:|-------------|
`show`|Include active or inactive bank accounts in the result. <BR>Set to `1` to return active accounts only. <BR>Set to ``0`` to return inactive accounts only.|Integer|TBD|Yes|TBD|
`page_no`|Enable or disable pagination of the result. If there are more than 25 bank accounts in the response, pagination occurs automatically. <BR>Set to ``1`` to enable pagination. <BR>Set to ``0`` to disable pagination.|Integer|TBD|No|TBD|
`with_pagination_data`|Attach or exclude pagination metadata for the result. <BR>Set to ``1`` to attach pagination metadata. <BR>Set to ``0`` to exclude pagination metadata. <BR>Setting the value of this parameter to ``1`` changes the returned structure from a list to an object. |Integer|TBD|No|TBD|
 | | | | | |

### **Example Response**

If the view bank account details request is successful, the service returns a response. 

> The returned JSON structure is as follows:

```json
[
    {
        "uid": 49665,
        "id": "49665",
        "account_name": "Test",
        "account_description": "None",
        "bank_name": "CAISSE DESJARDINS DE JONQUIERE",
        "country": "Canada",
        "currency": "Canada Dollars (CAD)",
        "payment_type": ",Wire Transfer,",
        "direct_debit": "0",
        "wire_transfer": "1",
        "is_default": " ",
        "bank_details": "Account (or IBAN) Number: 0xx-xxxx-xx64,SWIFT/BIC
Code: Cxx-xxxx-xxMM,",
        "active": "1",
        "actions": "Edit"
    },
    {
        "uid": 45240,
        "id": "45240",
        "account_name": "None",
        "account_description": "None",
        "bank_name": "TORONTO DOMINION BANK",
        "country": "Canada",
        "currency": "Canada Dollars (CAD)",
        "payment_type": ",Wire Transfer,",
        "direct_debit": "0",
        "wire_transfer": "1",
        "is_default": "√",
        "bank_details": "Account (or IBAN) Number: 1xx-xxxx-xx11,Transit Number & Institution Number: 0xx-xxxx-xx60,SWIFT/BIC Code: Txx-xxxx-xxOR,",
        "active": "1",
        "actions": "Edit"
    },
    {
        "uid": 63645,
        "id": "63645",
        "account_name": "None",
        "account_description": "None",
        "bank_name": "ABN AMRO BANK N.V.",
        "country": "Netherlands (Holland)",
        "currency": "Euro (EUR)",
        "payment_type": ",Wire Transfer,",
        "direct_debit": "0",
        "wire_transfer": "1",
        "is_default": " ",
        "bank_details": "Account (or IBAN) Number: Nxx-xxxx-xx17,",
        "active": "1",
        "actions": "Edit"
    },
    {
        "uid": 63625,
        "id": "63625",
        "account_name": "None",
        "account_description": "None",
        "bank_name": "AEGON BANK NV",
        "country": "Netherlands (Holland)",
        "currency": "Euro (EUR)",
        "payment_type": ",Wire Transfer,",
        "direct_debit": "0",
        "wire_transfer": "1",
        "is_default": "√",
        "bank_details": "Account (or IBAN) Number: Nxx-xxxx-xx20,",
        "active": "1",
        "actions": "Edit"
    },
    {
        "uid": 63551,
        "id": "63551",
        "account_name": "None",
        "account_description": "None",
        "bank_name": "BARCLAYS BANK PLC",
        "country": "United Kingdom",
        "currency": "United Kingdom Pounds (GBP)",
        "payment_type": ",Wire Transfer,",
        "direct_debit": "0",
        "wire_transfer": "1",
        "is_default": " ",         "bank_details": "Account (or IBAN) Number: 3xx-xxxx-xx59,Sort Code:
2xx-xxxx-xx15,SWIFT/BIC Code: Bxx-xxxx-xxXX,",
        "active": "1",
        "actions": "Edit"
    },
    {
        "uid": 51413,
        "id": "51413",
        "account_name": "None",
        "account_description": "None",
        "bank_name": "CITIBANK, N.A.",
        "country": "United Kingdom",
        "currency": "United Kingdom Pounds (GBP)",
        "payment_type": ",Wire Transfer,",
        "direct_debit": "0",
        "wire_transfer": "1",
        "is_default": "√",
        "bank_details": "Account (or IBAN) Number: Gxx-xxxx-xx83,SWIFT/BIC
Code: Cxx-xxxx-xxCA,",
        "active": "1",
        "actions": "Edit"
    },
    {
        "uid": 42087,
        "id": "42087",
        "account_name": "None",
        "account_description": "None",
        "bank_name": "WELLS FARGO BANK, NATIONAL ASSOCIATION",
        "country": "USA",
        "currency": "United States of America Dollars (USD)",
        "payment_type": ",Wire Transfer,",
        "direct_debit": "0",
        "wire_transfer": "1",
        "is_default": "√",
        "bank_details": "Account (or IBAN) Number: 1xx-xxxx-xx11,ABA /
Routing Number: 0xx-xxxx-xx03,SWIFT/BIC Code: Wxx-xxxx-xxXX,",
        "active": "1",
        "actions": "Edit"
    }
]
```

The response contains a list of bank details for each bank. The bank account details include the following parameters: 

Field | Description | Type
----- | ----------- | ----
`uid` | The internal unique ID of the bank, for example ``49665``. | Integer
`id` | The bank account ID. | Varchar
`account_name` | The name of the bank account. | Varchar
`account_description` | A text description for the bank account. | Varchar
`bank_name` | The name of the bank that manages the account. | Varchar
`country` | The country in which the bank is located. | Varchar
`currency` | The currency of the bank account. | Varchar
`payment_type` | The payment type, for example, ``Wire Transfer``. | Varchar
`direct_debit` | A flag to indicate if direct debit is enabled for the bank account. <BR>This is ``1`` if direct debit is enabled and ``0`` if direct debit is not enabled. | Integer
`wire_transfer` | A flag to indicate if wire transfers are enabled for the bank account.<BR>This is ``1`` if wire transfers are enabled and ``0`` if wire transfers are not enabled. | Integer
`is_default` | `LB>>Need a description here!`<BR>This flag is ``√`` if ... or blank if... | Varchar
`bank_details` | Additional information about the bank account, such as the IBAN number and the SWIFT/BIC code. | Varchar
`active` | A flag to indicate if the bank account is active or inactive.<BR>This is ``1`` if the bank is active and ``0`` if the bank is inactive. | Varchar
`actions` | Specifies the actions that you can perform on the account, for example, ``Edit``. | Varchar
 | | | | | |


### **Errors**

If the view existing account request is not successful, the service returns an error code, rejects the requet, and does not return the existing account details. The relevant error response is provided.

<!--#### Error Code 500 - Invalid Parameter Format-->
> Error Code 500 - Invalid Parameter Format

```json
{
    "stat" : "invalid parameter format"
}
```

The JSON structure associated with the error response has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **Edit Existing Bank Account Details**
You edit a bank account name or deactivate an account by sending an HTTP **POST** request to the ``/edit_bank`` endpoint. 

<aside class="notice">
You <i>cannot</i> recover or re-use an inactive account. Inactive accounts are removed from all live lists. See <a href="#view-existing-bank-account-details">View Existing Bank Account Details</a> to view inactive accounts.
</aside>


| | |
|-|-|
|**Request**| **POST** `/api/edit_bank` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |


### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
  "id":65400,
  "account_name" : "Pork Pie Plc.",
  "is_active":1
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter|Description      |Type|Size|Mandatory|Default Value
---------------|-----------------|:--:|:--:|:-------:|-------------|
`id`|The ID of the bank account.|Integer|TBD|Yes|-
`account_name`|The updated name of the bank account.|Varchar|50|No|-
`is_active`|Set to ``0`` to *permanently* deactivate the account.<BR>Set to ``1`` to keep the account active.|Varchar|50|No|1
 | | | | | |

### **Example Response**

If the request is successful, the service returns a response.

> If the account is active (``active = 1``), the response is as follows:

```json
{
    "id": "65400",
    "account_name": "Pork Pie Plc.",
    "account_description": null,
    "bank_name": "NATIONAL AUSTRALIA BANK LIMITED",
    "country": "Australia",
    "currency": "Euro (EUR)",
    "payment_type": "\nWire Transfer\n",
    "direct_debit": "0",
    "wire_transfer": "1",
    "is_default": " ",
    "bank_details": "Account (or IBAN) Number: Bxx-xxxx-xx10\nBSB: 0xx-xxxxxx82\n",
    "active": "1",
    "actions": "Edit"
}
```

> If the account is inactive (``active = 0``), the response is as follows: 

```json
{
    "stat": "65389 has been disabled"
}
```

### **Errors**

If the edit account request is not successful, the service responds with an error code. If the service returns an error, it rejects the request and does not edit the account information. Some examples of error responses are provided.

<!--#### Error Code 500 - Invalid Parameter Format-->
> Error Code 500 - Invalid Parameter Format

```json
{
    "stat" : "invalid parameter format"
}
```

<!--#### Error Code 535 - No Return Data-->
> Error Code 535 - No Return Data

```json
{
    "stat" : "no return data. incorrect or inactive bank account"
}
```

The JSON structure associated with the error responses has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **Verify Existing Bank Account Details**

You can verify existing bank account details by sending an HTTP **POST** request to the ``/verify_bank`` endpoint. 

The request invokes an internal verifier that performs checks on the provided bank account details to ensure conformance to standards.

You must provide a valid value for at least one of the following parameters in the request:

 * ``iban`` (International Bank Account Number)
 * ``natid`` (bank number, bank/branch code, sort code, or clearing code of a bank)
 * ``bic`` (Bank Information Code)

The response always contains a ``code`` and a ``message`` tag that provide an explanation of the result. For more information, see [Bank Verifier Codes and Messages](#bank-verifier-codes-and-messages).

| | |
|-|-|
|**Request**| **POST** `/api/verify_bank` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |


### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "country" : "US",
    "currency" : "USD",
    "account_number" : "012345678",
    "iban" : "IBAN format of account number",   `LB>>This should be an actual number.`
    "natid" : "National identification number", `LB>>This should be an actual number.`
    "bic" : "BIC or SWIFT Code"                 `LB>>This should be an actual number.`
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter|Description|Type|Size|Mandatory|Default Value
---------------|-----------|:--:|:--:|:-------:|-------------|
`country`|A two-letter ISO country code that identifies the country of the bank, for example, ``US`` (United States). <BR>See [Country Codes (ISO 3116)](#country-codes-iso-3116) for more information.|Char|2|Yes|-
`currency`|A three-character ISO currency code, for example, ``USD``. Specify if known, otherwise use ``n/a``. <BR>See [Currency Codes (ISO 4217)](#currency-codes-iso-4217) for more information. |Varchar|3|Yes|``n/a``
`account_number`|The account number of the bank account you want to verify. <BR>``LB>>Is this account_number different than the account ID?`` |Varchar|20|Yes|-
`iban`|The International Bank Account Number (IBAN) of the account.|Varchar|20|Yes|-
`natid`|The national identification number of the bank, which is also called ABA/Routing Number, Sort Code, Transit Number, BSB, Bank Code, or Bank Clearing Number.|Varchar|20|Yes|-
`bic`|The Bank Identification Code (BIC), also known as the SWIFT Code.|Varchar|20|Yes|-
 | | | | | |

### **Example Response**

If the verify account request is successful, the service returns a response. 

> The returned JSON structure is as follows:

```json
{
    "code": "1400",
    "bankname": "FIRST CENTURY BANK, NATIONAL ASSOCIATION",
    "bankid": "52661",
    "unqkey": "97365396",
    "message": "Valid data provided!"
}
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`code` | The ... `LB>>What is this?` | Varchar
`bankname` | The name of the bank. | Varchar
`bankid` | The ID of the bank. | Varchar
`unqkey` | The unique internal ID of the bank. | Varchar
`message` | A message phrase that provides information about the verification. | Varchar
 | | 


### **Errors**

If the verify account request is not successful, the service responds with an error code, rejects the request and does not perform the verification. Some examples of error responses are provided.

<!--#### Error Code 500 - Invalid Parameter Format-->
> Error Code 500 - Invalid Parameter Format

```json
{
    "stat" : "invalid parameter format"
}
```

<!--#### Error Code 534 - Examples-->
> Error Code 534 - Examples

```json
{
    "code": "1220",
    "message": "National Bank Code does not exist or the selected country does not provide National Bank Code!" 
}
{
    "code": "1020",
    "message": "Selected country and IBAN country do not match!"
}
```

The JSON structures associated with the error responses have the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
`code`  | An error code. | Varchar
`message`  | A description associated with an error code. | Varchar
| | 

# Beneficiary Accounts

## **Add a New Beneficiary Account**

You add a new beneficiary account by sending an HTTP **POST** request with the required account information to the ``/add_beneficiary`` endpoint. 

| | |
|-|-|
|**Request**| **POST** `/api/add_beneficiary` |
|**Host** | https://payments.transfermate.com |
|**Content Type**| `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
  "payee_name": "Bogo MIPS PLC",
  "country_id": 102,
  "state": "",
  "currency_type_id": "EUR",
  "account_number_type": "iban_3",
  "account_number": "NL65ABNA0576565935",
  "transit_code_type": "sort_code_2",
  "transit_code": "110022",
  "payee_address": "address on one line",
  "bank_branch_address": "bank address on one line",
  "account_type": "resident_30",
  "for_further_credit_to": "follow-on further documentation",
  "def_payment_reference": "default payment reference",
  "def_transfer_reason": "Some reason",
  "email": "acm2e@wily.com",
  "email_alert_flag": 0
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter | Description | Type | Size | Mandatory | Default Value | 
---|---|---|---|---|---|
`payee_name` | A unique payee name used to identify the beneficiary account. | Varchar | 50 | Yes | - | 
`country_id` | A country ID from the list of system IDs. <BR>See [Country Reference by System ID](#country-reference-by-system-id) for more information.<BR>`LB>>Should this be updated to use an ISO code instead of a system ID?` | Integer | - | Yes | - | 
`state` | The appropriate state in US and Canadian jurisdictions. For all other jurisdictions, leave blank. | Varchar | 50 | Yes | - | 
`currency_type_id` | An ID from the list of currency IDs. <BR>See [Currency Codes (ISO 4217)](#currency-codes-iso-4217) for more information. | Varchar | 3 | Yes | - | 
`beneficiary_type_id` | The beneficiary type. Possible values are: <BR> - `3` for an individual client <BR> - `23` for a corporate client <BR>If omitted, the default is `3` (individual client). | Integer | - | No | - | 
`account_number_type` | The account number type. The value must be `account_number_1` or `iban_3`. If the value is `iban_3`, the `transit_code_type` and `transit_code` fields can be left blank. | - | - | Yes | - | 
`account_number` | The IBAN code if known, otherwise the bank account number of the beneficiary. | - | - | Yes | - | 
`transit_code_type` | The transit code type. This is a required field, but it can be left blank. See `account_number_type` description. | Varchar | - | Yes | - | 
`transit_code` | The transit code. This is a required field, but it can be left blank. See `account_number_type` description. | Varchar | - | Yes | - | 
`swift` | The SWIFT code if known. This is a required field if the account number is supplied instead of the IBAN. See the `account_number_type` description. | Varchar | 50 | No | - | 
`payee_address` | The address of the payee. All details are included in one field. | Varchar | 255 | Yes | - | 
`state`<BR>`LB>>Suspect duplicate` | The state associated with the beneficiary account in US and Canadian jurisdictions.<BR>`LB>>The size can't be 2! State codes are 5 chars, for example, US-AL for Alabama?` | Varchar | 2 | No | - | 
`bank_branch_address` | The address of the beneficiary's bank. All details are included in one field. | Varchar | 255 | Yes | - | 
`account_type` | The account type. For India, this field is mandatory. Possible values are: <BR> - `resident_30` - Resident <BR> - `resident_31` - Non-resident | Varchar | 12 | No<BR>(Yes in India) | - | 
`payee_address_`<BR>`to_account_type` | `LB>>Need a description?` This attribute is required, but it can be left blank. | - | - | Yes | - | 
`payee_address_`<BR>`to_country_state` | `LB>>Need a description?` This attribute is required, but it can be left blank. | - | - | Yes | - | 
`bank_branch_address_`<BR>`to_account_type` | `LB>>Need a description?` This attribute is required, but it can be left blank. | - | - | Yes | - | 
`for_further_credit_to` | The name of the final beneficiary if this is a 4th party payment. This is a compliance requirement. | Varchar | 50 | No | - | 
`def_payment_reference` | An ID from the list of reference IDs to be used. Detailed in Appendix A `LB>>What table in Appendix A does this refer to?` | Varchar | 20 | No | - | 
`email` | A valid email address if the beneficiary is to receive updates on transactions. | Varchar | 50 | No | - | 
`email_alert_flag` | A flag that indicates if the beneficiary is to receive transaction updates or not. <BR>Set to `1` to enable emails updates. If the `email` field is populated, set the value of this field to `1`. <BR>Set to `0` to disable email updates. | Bit | 1 | No | 1 | 
 | | | | | |

### **Example Response**

If the add beneficiary account request is successful and an account ID is allocated, the service provides a response. 

> The returned JSON structure is as follows:

```json
{
    "stat" : "success",
    "qid" : 541238
}
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat` | The status of the request, ``success``. | Varchar
`qid` | The ID of the account, for example, ``541238``. | Integer
 | |


### **Errors**

If the add beneficiary bank account request is not successful, the service responds with an error code, rejects the request, and does not create the new beneficiary account. Some examples of the error responses are provided.

<!--#### Error Code 521 - Account Number Exists, Client ID Exists, or Unsuccessful Operation-->
> Error Code 521 - Account Number Exists, Client ID Exists, or Unsuccessful Operation


```json
[
    {
        "ID": "iban_account_number",
        "description": "Already exists!"
    },
    {
        "ID": "client_id",
        "description": "Already exists!"
    },
    {
        "ID": "db_action",
        "description": "Operation is not successful! Please try again."
    }
]
```

<!--#### Error Code 522 - One or More JSON Attributes Are Missing or Malformed-->
> Error Code 522 - One or More JSON Attributes Are Missing or Malformed

```json
{
    "stat" : "One or more JSON attributes missing or malformed: country_id"
}
```

<!--#### Error Code 523 - Invalid Token or Missing Return Data-->
> Error Code 523 - Invalid Token or Missing Return Data

```json
{
    "stat" : "Invalid token or missing return data"
}
```

The JSON structures associated with the error responses have the following parameters:

Field | Description | Type
----- | ----------- | ----
`ID`  | The source of the error, for example, iban account number, client ID, or database action. | Varchar
`description`  | A description of the error encountered. | Varchar
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **Delete an Existing Beneficiary Account**

You delete a beneficiary account by sending an HTTP **POST** request with the required account information to the ``/delete_beneficiary`` endpoint. 

| | |
|-|-|
|**Request**| **POST** `/api/delete_beneficiary` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type**| `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "id": 1234567890
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter | Description | Type | Size | Mandatory | Default Value | 
---|---|---|---|---|---|
`id` | The ID of the beneficiary account to delete. | Integer | - | Yes | - | 
 | | | | | |

### **Example Response**

If the delete beneficiary account request is successful, the service returns a response. 

> The returned JSON structure is as follows:

```json
{
    "stat" : "success"
}
```

Field | Description | Type
----- | ----------- | ----
`stat` | The status of the request, ``success``. | Varchar
 | | 


### **Errors**

If the delete beneficiary account request is not successful, the service responds with an error code, rejects the request, and does not delete the beneficiary account. An example error response is provided. 

<!--#### Error Code 500 - Invalid Parameter Format--> 
> Error Code 500 - Invalid Parameter Format 

`LB>>Need to verify the error code number 500 is correct here`

```json
{
    "stat" : "invalid parameter format"
}
```

The JSON structure associated with the error response has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **Edit an Existing Beneficiary Account**

You edit an existing beneficiary account by sending an HTTP **POST** request with the required account information to the ``/edit_beneficiary`` endpoint. 

| | |
|-|-|
|**Request**| **POST** `/api/edit_beneficiary` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type**| `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows. This example sets a new address for the beneficiary account that has ID 1234567890.

```json
{
    "id" : 1234567890,
    "bank_branch_address" : "new_address"
}
```

The request body may include one of more of the following parameters:

Input Parameter | Description | Type | Size | Mandatory | Default Values | Notes | 
---|---|---|---|---|---|---|
`id` | The ID of the beneficiary account to be edited. | Integer | - | Yes | - | Cannot be edited. | 
`payee_name` | A unique payee name that identifies the beneficiary account. | Varchar | 50 | Yes | - | - | 
`payee_address` | The address of the payee. | Varchar | 50 | No | - | - | 
`bank_branch_address` | The address of the branch of the payee's bank. | - | - | - | - | - | 
`account_type` | The account type. <BR>Possible values are: <BR> - `resident_30` - Resident <BR> - `resident_31` - Non-resident<BR>For India, this field is mandatory. | Varchar | 12 | Yes | - | - | 
`payee_address_`<BR>`to_account_type` | `LB>>Need a description here?` | - | - | - | - | - | 
`bank_branch_address_`<BR>`to_account_type` | `LB>>Need a description here?` | - | - | - | - | - | 
`for_further_credit_to` | Name of final beneficiary if this is a 4th party payment. This is required for compliance reasons. | Varchar | 50 | No | - | - | 
`def_payment_reference` | An ID from the list of reference IDs to be used. Refer to the details in Appendix A.<BR>`LB>>Which table in the Appendix?` | Varchar | 20 | No | - | - | 
`def_trans_reason` | A general transaction reason code (for demographic purposes). <BR>See [Transfer Reasons](#transfer-reasons) for more information. | Integer | - | No | - | - | 
`email` | A valid email address if the beneficiary is to receive updates on transactions. | Varchar | 50 | No | - | - | 
`email_alert_flag` | A flag that indicates if the beneficiary is to receive transaction updates or not. <BR>Set to `1` to enable emails updates. If the `email` field is populated, set the value of this field to `1`.<BR>Set to `0` to disable email updates. | Bit | 1 | No | 1 | - | 
 | | | | | |


### **Example Response**

If the edit beneficiary account request is successful, the service returns a response.

> The returned JSON structure is as follows:

```json
{
    "stat" : "success"
}
```

The response includes the following parameters: 

Field | Description | Type
----- | ----------- | ----
`stat` | The status of the request, ``success``. | Varchar
 | | 


### **Errors**

If the edit beneficiary account request is not successful, the service responds with an error codes, rejects the request, and does not update the beneficiary account. Some examples of error responses are provided.

<!--#### Error Code 500 - Invalid Parameter Format-->
> Error Code 500 - Invalid Parameter Format

```json
{
    "stat" : "invalid parameter format"
}
```

<!--#### Error Code 521 - Name Already Exists-->
> Error Code 521 - Name Already Exists

```json
[
    {
        "ID": "payee_name",
        "description": "Already exists!"
    }
]
```

The JSON structures associated with the error responses have the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
`ID`  | The source of the error, for example, account number, currency ID, or database action. | Varchar
`description`  | A description of the error encountered. | Varchar
| | 

## **View Beneficiary Accounts**

You view beneficiary accounts by sending an HTTP **POST** request with the required account information to the ``/view_beneficiary`` endpoint.

| | |
|-|-|
|**Request**| **POST** `/api/view_beneficiaries` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type**| `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
  "beneficiary_name" : "Bogo MIPS PLC"
}
```

The request body can include one of the following parameters:

Input Parameter | Description | Type | Size | Mandatory | Default Values | 
---|---|---|---|---|---|
`beneficiary_name` | The name of a single beneficiary account. | Varchar | 50 | Yes | - | 
`beneficiary_country` | A country in which there are one or more beneficiary accounts. <BR>`LB>>Is this string identifying a country or a country code? From the size, it seems like a string.` | Varchar | 50 | No | - | 
 | | | | | |

### **Example Response**

If the view beneficiary accounts request is successful, the service returns a response. 

> The returned JSON structure is as follows:

```json
[
  {
    "uid": 637104,
    "payee_name": "Bogo MIPS PLC",
    "country_id": 102,
    "state": "",
    "currency_type_id": "EUR",
    "account_number_type": "iban_3",
    "account_number": "NL65ABNA0576565935",
    "transit_code_type": 7,
    "payee_address": "address on one line",
    "bank_branch_address": "bank address on one line",
    "account_type": "resident_30",
    "for_further_credit_to": "follow-on further documentation",
    "def_payment_reference": "default payment reference",
    "def_transfer_reason": "Some reason",
    "email": "acm2e@wily.com",
    "email_alert_flag": 0
  }
]
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`uid` | The ID of the beneficiary account. | Varchar <BR>`LB>>In JSON, it looks like an integer?`
`payee_name` | The name of the payee. | Varchar
`country_id` | The ID of the payee's country. | Integer
`state` | The state of the payee in Canada and US jurisdictions, otherwise blank. | Varchar
`currency_type_id` | The currency type. <BR>See [Currency Codes (ISO 4217)](#currency-codes-iso-4217) for more information. | Varchar
`account_number_type` | One of the following values: <BR>- `account_number_1` <BR>- `iban_3` | Varchar
`account_number` | The beneficiary account number. | Varchar
`transit_code_type` | The type of transit code. <BR>See [Transit Code Values](#transit-code-values) for more information. | Varchar
`payee_address` | The address of the payee. | Varchar
`bank_branch_address` | The address of the branch of the payee's bank. | Varchar
`account_type` | The account type. <BR>Possible values are: <BR>- `resident_30` - Resident <BR>- `resident_31` - Non-resident<BR>For India, this field is mandatory. | Varchar
`for_further_credit_to` | The name of final beneficiary if this is a 4th party payment. This is required for compliance reasons. | Varchar 
`def_payment_reference` | Arbitrary text that identifies the transfer for the beneficiary. | Varchar
`def_transfer_reason` | The reason for the transfer. `LB>>Shown as a string but should it be a value from the [Transfer Reasons](#transfer-reasons) table.` | Varchar
`email` | The email address of the payee. | Varchar
`email_alert_flag` | A flag that idicates if the beneficiary is to receive transaction updates or not. <BR>A value of `1` indicates that email updates are enabled. <BR>A value of `0` indicates that email updates are not enabled. | Bit
 | | 


### **Errors**

If the view beneficiary account request is not successful, the service responds with an error code, rejects the request, and does not return the beneficiary account details. An example error response is provided. 

<!--#### Error Code 500 - Invalid Parameter Format-->
> Error Code 500 - Invalid Parameter Format

```json
{
    "stat" : "invalid parameter format"
}
```

The JSON structure associated with the error response has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 


# Payment Transactions

## **Get a Currency Exchange Rate for Review**

You can retrieve and review a currency exchange rate by sending an HTTP **POST** request with the required information to the ``/get_booking_rate`` endpoint. 

<aside class="notice">
In request responses: <BR>1. If a field exists and is not empty (for example, it contains an error message or a notice), the transaction <b>may not</b> be processed. Always display messages of this type to the user or client. For example, if same currency payments/transactions are not allowed, notify the user.<BR>2. If the value of the <code>forbidden</code> field is <code>1</code>, the current transaction is not allowed and is <b>not</b> processed. The default value is <code>0</code>, that is, a transaction is processed normally. 
</aside>

| | |
|-|-|
|**Request**| **POST** `/api/get_booking_rate` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |


### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
  "bank_id_from" : 123456,
  "bank_pay_type" : 2,
  "beneficiary_data": [
    {
    "beneficiary_data_currency" : "USD",
    "beneficiary_data_id" : 654321,
    "beneficiary_data_amount" : 2000.00,
    "beneficiary_data_user_reference" : "tst ref 248",
    "beneficiary_data_transfer_reason" : 7,
    "reconciliation_id": "somerefid"
    },     ...
  ]
}
```

<!--### **Request Parameters**-->
The request body includes the following parameters:

Input Parameter | Description | Type | Size | Mandatory | Default Values | 
---|---|---|---|---|---|
`bank_id_from` | The ID of the bank from which the funds originate. This is the bank's own account ID. | Integer | - | Yes | - | 
`bank_pay_type` | The payment type. Possible values are: <BR>– `1`, Direct Debit/ACH<BR>– `2`, Wire Transfer | Integer | - | Yes | - | 
`beneficiary_data` | A list of beneficiary payment attribute groups. | List | - | Yes | - | 
`beneficiary_data_id` | The ID of the beneficiary to whom the payment is to be made. | Integer | - | Yes | 1 |
`beneficiary_data_currency` | The ID of the currency corresponding to the beneficiary account. Accounts can have more than one currency. See [View Beneficiary Accounts](#view-beneficiary-accounts) to retrieve this information. | Varchar | 5 | Yes | - | 
`beneficiary_data_amount` | The amount of money to send to the beneficiary in their currency. | Decimal | 11,2 | Yes | - | 
`beneficiary_data_`<BR>`user_reference` | A reference to be sent to the receiving bank. | Varchar | 20 | Yes | - | 
`beneficiary_data_`<BR>`transfer_reason` | The transfer reason. Typically set to the default value `7` (Bill Pay). | Varchar | 20 | Yes | 7 | 
`reconciliation_id` | An optional custom identifier for client-side reconciliation. | Varchar | 52 | No | - | 

### **Example Response**

> The JSON structure in the response is as follows:

```json
[
    {
        "uid": 9875641,
        "payee_name": "China Beneficiary XXX",
        "foreign_amount": "USD 2000.00",
        "service_type": "Standard",
        "bene_payment_type": "Wire Transfer",         
        "currency_rate_per_unit": "1 CAD = 0.76449 USD,1 USD = 1.30806 CAD",
        "conversion_meta": {
            "conversion": {
                "from": "CAD",
                "to": "USD",
                "input": 1,
                "output": 0.76449
            },
            "inverse": {
                "from": "USD",
                "to": "CAD",
                "input": 1,
                "output": 1.30806
            }
        },
        "amount_from_bef_fee": "CAD 2616.12",
        "fee": "5.00",
        "total_trans_amount_from": "CAD 2621.12",
        "transfer_reason": "7",
        "transfer_reason_id": "None",
        "reference": "tst ref 246",
        "forbidden": "0"
    }
]
```

If the request is successful, the response includes the following parameters: 

Field | Description | Type
----- | ----------- | ----
`uid` | The internal unique ID of the beneficiary. | Integer
`payee_name` | The name of the beneficiary. | Varchar
`foreign_amount` | The amount to be paid in the source bank's currency. | Varchar
`service_type` | The type of the service. | Varchar
`bene_payment_type` | The type of the payment. | Varchar
`currency_rate_per_unit` | The currency exchange rate and its inverse. | Varchar
`conversion_meta` | A container for conversion details. | -
`conversion` and <BR>`inverse` | Sub-containers for conversion details. | -
`from` | The currency to convert from. | Varchar
`to` | The currency to convert to. | Varchar
`input` | The amount to convert. | Decimal? `LB>>Need to confirm this!`
`output` | The exchange rate. | Decimal
`amount_from_ben_fee` | The amount on which the transaction fee is based. | Decimal
`total_trans_amount_from` | The total amount for the transaction, which includes the transaction fee. | Decimal
`transfer_reason` | The reason for the transfer, typically `7` (Bill Pay). | Varchar
`transfer_reason_id` | The ID of the transfer reason. | Varchar
`reference` | A reference for the transaction. | Varchar
`forbidden` | A flag that indicates if the transaction can be processed or not. <BR>A value of `0` indicates that the transaction can be processed normally.<BR>A value of `1` indicates that the transaction cannot be processed. | Varchar
| | 

### **Errors**

If the get and review exchange rate request is not successful, the service responds with an error code, rejects the request, and does not retrieve the exchange rate information. Some example error responses are provided. 

<!--#### Error Code 540 - Invalid Parameter Format-->
> Error Code 540 - Invalid Parameter Format

```json
{
    "stat" : "invalid parameter format"
}
```

<!--#### Error Code 541 - Payment Type Invalid-->
> Error Code 541 - Payment Type Invalid

```json
[
    {
        "ID": "bank_pay_type",
        "description": "Payment type is not valid!"
    },
    {
        "ID": "db_action",
        "description": "Operation is not successful! Please try again."
    }
]
```

<!--#### Error Code 543 - Invalid Combination of Parameters or Payment Type Not Supported-->
> Error Code 543 - Invalid Combination of Parameters or Payment Type Not Supported

```json
{
    "stat" : "invalid combination of bank/beneficiary currency parameters or payment type not supported for beneficiary"
}
```

<!--#### Error Code 545 - One or More JSON Attributes Missing or Malformed-->
> Error Code 545 - One or More JSON Attributes Missing or Malformed

```json
{
    "stat" : "one or more JSON attributes missing or malformed: currency_type"
}
```

<aside class="notice">
In this error message structure, the <code>currency_type</code> parameter is just an example. Any of the request input parameters can be reported in an error structure like this.
</aside>


<!--#### Error Code 546 - One or More JSON Attributes Failed Validation-->
> Error Code 546 - One or More JSON Attributes Failed Validation

```json
{
    "stat" : "contents of one or more JSON attributes failed validation"
}
```

The JSON structures associated with the error responses have the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat` | A status message that describes the error encountered. | Varchar
`ID` | The ID of the error message. | Varchar
`description` | A descirption of the error. | Varchar
| | 


## **Lock in an Exchange Rate for a Payment Transaction**

You lock in a currency exchange rate for your transaction by sending an HTTP **POST** request with the required information to the ``/book`` endpoint.

| | |
|-|-|
|**Request**| **POST** `/api/book` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "bank_id_from" : 63645,
    "bank_pay_type" : 2,
    "beneficiary_data" : [
      {
              "beneficiary_data_id" : 220865,
              "beneficiary_data_currency" : "EUR",
              "beneficiary_data_amount" : 100.00,
              "beneficiary_data_user_reference" : "api ref 8",
              "beneficiary_data_transfer_reason" : "Bill Pay",  LB>>In the get rate call, this is a number?
              "reconciliation_id": "6300fb78-fa97-4f2a-9b6e-1028199a35ea"
      },
      {
              "beneficiary_data_id" : 199641,
              "beneficiary_data_currency" : "NOK",
              "beneficiary_data_amount" : 100.00,
              "beneficiary_data_user_reference" : "api ref 9",
              "beneficiary_data_transfer_reason" : "Bill Pay",
              "reconciliation_id": "6300fb78-fa97-4f2a-9b6e-1028199a35ea"
      }
    ]
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter | Description | Type | Size | Mandatory | Default Values | 
---|---|---|---|---|---|
`bank_id_from` | The ID of the bank from where the funds originate. This is the bank's own account ID. | Integer | - | Yes | - | 
`bank_pay_type` | The payment type. Possible values are: <BR>– `1`, Direct Debit/ACH<BR>– `2`, Wire Transfer | Integer | - | Yes | - | 
`beneficiary_data` | A list of beneficiary payment attribute groups. | List | - | Yes | - | 
`beneficiary_data_id` | The ID of the beneficiary to whom the payment is to be made. | Integer | - | Yes | 1 |
`beneficiary_data_currency` | The ID of the currency corresponding to the beneficiary account. Accounts can have more than one currency. See [View Beneficiary Accounts](#view-beneficiary-accounts) to retrieve this information. | Varchar | 5 | Yes | - | 
`beneficiary_data_amount` | The amount of money to send to the beneficiary in their currency. | Decimal | 11,2 | Yes | - | 
`beneficiary_data_`<BR>`user_reference` | A reference to be sent to the receiving bank. | Varchar | 20 | Yes | - | 
`beneficiary_data_`<BR>`transfer_reason` | The transfer reason. Typically set to the default value `7` (Bill Pay). <BR>`LB>>In the get rate call, this is a numeric type?` | Varchar | 20 | Yes | 7 | 
`reconciliation_id` | An optional custom identifier for client-side reconciliation. | Varchar | 52 | No | - | 

### **Example Response**

> The JSON structure in a successful response is as follows:

```json
[
    {
        "uid": 220865,
        "payee_name": "Maginot Line",
        "foreign_ammount": "EUR 100.00",
        "service_type": "Standard",
        "bene_payment_type": "Wire Transfer",
        "currency_rate_per_unit": "1 EUR = 1.00000 EUR,1 EUR = 1.00000 EUR",
        "conversion_meta": {
            "conversion": {
                "from": "EUR",
                "to": "EUR",
                "input": 1,
                "output": 1.0
            },
            "inverse": {
                "from": "EUR",
                "to": "EUR",
                "input": 1,
                "output": 1.0
            }
        },
        "amount_from_bef_fee": "EUR 100.00",
        "fee": "15.00",
        "total_trans_amount_from": "EUR 115.00",
        "transfer_reason": "Bill Pay",
        "transfer_reason_id": "Bill payment",
        "reference": "api ref 8",
        "forbidden": "0",
        "alerts": "Please note for same currency payments outside the SEPA area, the receiving bank may charge a receiving fee to the receiver.For more info click www.transfermate.com/chat,,NB: You have submitted a transaction with the same parameters (amount, currency and beneficiary details) in the last 24 hours. To ensure this is not a duplicate payment, check previous submissions on your Accounts Summary.",         
        "transaction_id": "T684736"
    },
    {
        "uid": 199641,
        "payee_name": "Norwegian Supplier",
        "foreign_ammount": "NOK 100.00",
        "service_type": "Standard",
        "bene_payment_type": "Wire Transfer",
        "currency_rate_per_unit": "1 EUR = 9.37072 NOK,1 NOK = 0.10672 EUR",
        "conversion_meta": {
            "conversion": {
                "from": "EUR",
                "to": "NOK",
                "input": 1,
                "output": 9.37072
            },
            "inverse": {
                "from": "NOK",
                "to": "EUR",
                "input": 1,
                "output": 0.10672
            }
        },
        "amount_from_bef_fee": "EUR 10.67",
        "fee": "5.00",
        "total_trans_amount_from": "EUR 15.67",
        "transfer_reason": "Bill Pay",
        "transfer_reason_id": "None",
        "reference": "api ref 9",
        "forbidden": "0",
        "alerts": "NB: You have submitted a transaction with the same parameters (amount, currency and beneficiary details) in the last 24 hours. To ensure this is not a duplicate payment, check previous submissions on your Accounts Summary.",         
        "transaction_id": "T684737"
    }
]
```

If the request is successful, the response includes the following parameters.

Field | Description | Type
----- | ----------- | ----
`uid` | The internal unique ID of the beneficiary. | Integer
`payee_name` | The name of the beneficiary. | Varchar
`foreign_amount` | The amount to be paid in the source bank's currency. | Varchar
`service_type` | The type of the service. | Varchar
`bene_payment_type` | The type of the payment. | Varchar
`currency_rate_per_unit` | The exchange currency rate and its inverse. | Varchar
`conversion_meta` | A container for conversion details. | -
`conversion` and <BR>`inverse` | Sub-containers for conversion details. | -
`from` | The currency to convert from. | Varchar
`to` | The currency to convert to. | Varchar
`input` | The amount to convert. | Decimal? `LB>>Need to confirm this!`
`output` | The exchange rate. | Decimal
`amount_from_ben_fee` | The amount on which the transaction fee is based. | Decimal
`total_trans_amount_from` | The total amount for the transaction, which includes the transaction fee. | Decimal
`transfer_reason` | The reason for the transfer, typically `7` (Bill Pay). | Varchar
`transfer_reason_id` | The ID of the transfer reason. | Varchar
`reference` | A reference for the transaction. | Varchar
`forbidden` | A flag that indicates if the transaction is allowed or not. <BR>When set to `1`, the transaction is not allowed and is *not* processed. <BR>When set to `0` (the default value), the transaction proceeds normally. | Varchar
`alerts` | A message to draw the user's attention to a possible anomaly that the user should check. | Varchar
`transaction_id` | The ID assigned to the transaction. | Varchar
| | 

### **Errors**

If the lock-in exchange rate request is not successful, the service responds with one an error code, rejects the request, and does not lock in the exchange rate. Some examples of error responses are provided. 


<!--#### Error Code 500 - Invalid Parameter Format-->
> Error Code 500 - Invalid Parameter Format

`LB>>In the Get Rate and Review API, this is shown as error code 540. Which is correct?`

```json
{
    "stat" : "invalid parameter format"
}
```

<!--#### Error Code 542 - Payment Blocked-->
> Error Code 542 - Payment Blocked

```json
{
  "stat" : "forbidden",
  "message" : "payment is blocked and cannot be authorized",
  "reason_code" : 999
}
```

<!--Error Code 545 - One or More JSON Attributes Missing or Malformed-->
> Error Code 545 - One or More JSON Attributes Missing or Malformed

```json
{
    "stat" : "one or more JSON attributes missing or malformed: currency_type"
}
```

<aside class="notice">
In this error message structure, the <code>currency_type</code> parameter is just an example. Any of the request input parameters can be reported in an error structure like this.
</aside>

<!--#### Error Code 546 - One or More JSON Attributes Failed Validation-->
> Error Code 546 - One or More JSON Attributes Failed Validation

```json
{
    "stat" : "contents of one or more JSON attributes failed validation"
}
```

The JSON structures associated with the error responses have the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat` | The status of the request. | Varchar
`message` | A message associated with the status. | Varchar
`reason_code` | The reason code associated with the satus. | Integer
| | 


## **Authorize One or More Payment Transactions**

You authorize one or more payment transactions by sending an HTTP **POST** request with the required information to the ``/authorize`` endpoint.

| | |
|-|-|
|**Request**| **POST** `/api/authorize` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
  "login_terms_of_use" : 1,
  "approve" : 1,
  "auth_trans_ids" : [
      "T784880",
      "T784881",
      "TXXXXX"
  ]
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter | Description | Type | Size | Mandatory | Default Values | 
---|---|---|---|---|---|
`login_terms_of_use` | A flag to indicate that the user has accepted the terms and conditions. The agreed terms and conditions should be shown to all users prior to payment authorization. <BR>`LB>>Shouldn't the default be 0 here?` | Bit | 1 | Yes | 1 | 
`approve` | Indicates the action to take. <BR>Set to `1` to approve payments. <BR>Set to `0` to cancel payments.<BR>`LB>>Shouldn't the default be 0 here, that is cancel unless the user approves?` | Bit | 1 | Yes | 1 | 
`auth_trans_ids` | A list of transaction IDs to approve. | List | - | Yes | - | 
`metadata` | A flag to include payment transaction metadata with the authorization request. <BR>Set to `1` to include metadata. <BR>Set to `0` to exclude metadata.  | Boolean | - | No | 0 | 

<aside class="notice">
Including <code>metadata</code> has an impact on how quickly the request is processed.
</aside>

### **Example Response**

> The JSON structure in a successful response is as follows:

```json
{
    "transactions": [
        {
            "benef_id": "231426",
            "payee_name": "China Beneficiary 1003",
            "t_datetime_submitted": "2018-09-21 09:52:00",
            "foreign_amount": "USD 2106.00",
            "currency_rate_per_unit": "0.77352",
            "amount_from_bef_fee": "CAD 2722.62",
            "fee": "5.00",
            "total_trans_amount_from": "CAD 2727.62",
            "reference": "tst ref 246",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": "B544748",
            "ref_number": "T784880",
            "payment_status": "Awaiting Funds",
            "approve": null,
            "payment_status_abrv": "pending",
            "client_data": "Some Reconcile Ident"
        },
        {
            "benef_id": "231427",
            "payee_name": "China Beneficiary 963",
            "t_datetime_submitted": "2018-09-21 09:52:00",
            "foreign_amount": "USD 2107.00",
            "currency_rate_per_unit": "0.77352",
            "amount_from_bef_fee": "CAD 2723.91",
            "fee": "5.00",
            "total_trans_amount_from": "CAD 2728.91",
            "reference": "tst ref 246",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": "B544748",
            "ref_number": "T784881",
            "payment_status": "Awaiting Funds",
            "approve": null,
            "payment_status_abrv": "pending",
            "client_data": "Some Reconcile Ident 2"
        }
    ],
    "unknown_tids": [
        "TXXXXX"
    ],
    "metadata": [
        {
            "batch_sum_details": "Domestic Amount: CAD 5,456.53 Reference Number: B544748",
            "usr_bank_from_data": "Wire Transfer from your bank account: Bank Name: TORONTO DOMINION BANK Bank Country: Canada Account Number: 1xxxxxx-xx11 Transit Number & Institution Number: 0xx-xxxx-xx60 SWIFT/BIC Code: Txx-xxxx-xxOR Currency: CAD",
            "transfer_type": "Country Local Bank Transfer",
            "currency": "CAD",
            "amount": "CAD 5456.53",
            "particular": "Mr Test Test",
            "account_name": "Interpay Ltd",
            "bank_name": "ROYAL BANK OF CANADA",
            "bank_country": "Canada",
            "account_number": "1071596",
            "transit_code": "000306702",
            "swift_bic": "ROYCCAT2",
            "ref_number": "B544748"
        }
    ]
}
```

If the request is successful, the response includes the following parameters.

Field | Description | Type
----- | ----------- | ----
`transactions` | A list of one or more payment transactions. | List
`benef_id` | The ID of the beneficiary. | Varchar
`payee_name` | The name of the beneficiary. | Varchar
`t_datetime_submitted`| The date and time at which the transaction was submitted. | Varchar 
`foreign_amount` | The amount to be paid in the source bank's currency. | Varchar
`currency_rate_per_unit` | The currency exchange rate and its inverse. | Varchar
`amount_from_ben_fee` | The amount on which the transaction fee is based. | Decimal
`fee` | The fee for the payment transaction. | Decimal
`total_trans_amount_from` | The total amount for the transaction, which includes the transaction fee. | Decimal
`reference` | A reference for the payment transaction. | Varchar
`tran_type` | The payment transaction type. <BR>See [Payment Method/Transaction Type Values](#payment-methodtransaction-type-values) for possible values. | Varchar
`bene_payment_type` | The beneficiary payment type. <BR>See [Payment Method/Transaction Type Values](#payment-methodtransaction-type-values) for possible values. | Varchar
`batch_ref_num` | The reference number of a batch process into which several payment transactions are combined. | Varchar
`ref_num` | The reference number of a payment transaction. | Varchar
`payment_status` | The status of a payment transaction. <BR>See [Payment Status Codes](#payment-status-codes) for possible values. | Varchar
`approve` | A flag to indicate the approval status. <BR> - A value of `1` indicates the transaction is approved. <BR> - A value of `0` indicates the transaction is not approved. <BR> - A value of `null` indicates that the approval is pending. | Varchar
`payment_status_abrv` | An abbreviation of the payment status. | Varchar
`client_data` | A custom statement associated with the payment transaction. | Varchar
`unknown_tids` | A list of erroneous payment transaction IDs that the system does not recognized. | List
`metadata` | A list of metadata associated with the batch payment transaction. | List
`batch_sum_details` | A text description that summarizes the batch transaction. | Varchar
`usr_bank_from_data` | A text description that summarizes the source bank account information. | Varchar
`transfer_type` | The type of payment transaction. | Varchar
`currency` | The currency of the payment transaction. | Varchar
`amount` | The payment transaction amount with currency indicated. | Varchar
`particular` | A custom statement associated with the batch payment transaction. | Varchar
`account_name` | The name of the source bank account. | Varchar
`bank_name` | The name of the source bank. | Varchar
`bank_country` | The country of the source bank. | Varchar
`account_number` | The source bank account number. | Varchar
`transit_code` | The transit code. <BR>See [Transit Code Values](#transit-code-vales) for possible values. | Varchar
`swift_bic` | The SWIFT or Bank Identification Code (BIC) of the source bank account. | Varchar
`ref_number` | The reference number of the batch in which multiple payment transactions are included for approval. | Varchar
| | 

Also included is an example where the request sets the `approve` parameter to `0`. This cancels the payment requests. Notice that the values in the `payment_status` and `payment_status_abrv` fields indicate that the payment transaction is cancelled by the approver. 

> The returned JSON structure for a cancelled payment request is as follows:

```json
{
    "transactions": [
        {
            "benef_id": "220865",
            "payee_name": "Maginot Line",
            "t_datetime_submitted": "2018-08-30 12:27:00",
            "foreign_amount": "EUR 328.00",
            "currency_rate_per_unit": "0.66060",
            "amount_from_bef_fee": "CAD 496.52",
            "fee": "15.00",
            "total_trans_amount_from": "CAD 511.52",
            "reference": "api ref 99",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": null,
            "ref_number": "T760840",
            "payment_status": "Cancelled by approver",
            "approve": null,
            "payment_status_abrv": "cancelled_by_approver",
            "client_data": "Test reconcile 3"
        },
        {
            "benef_id": "197279",
            "payee_name": "Singapore Beneficiary 1",
            "t_datetime_submitted": "2018-08-30 12:27:00",
            "foreign_amount": "USD 428.00",
            "currency_rate_per_unit": "0.77224",
            "amount_from_bef_fee": "CAD 554.23",
            "fee": "15.00",
            "total_trans_amount_from": "CAD 569.23",
            "reference": "api ref 98",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": null,
            "ref_number": "T760841",
            "payment_status": "Cancelled by approver",
            "approve": null,
            "payment_status_abrv": "cancelled_by_approver",
            "client_data": "Test reconcile 3"
        }
    ]
}
```

### **Errors**

If the approve request is not successful, the service responds with an error code, rejects the request, and does not process the approval. Some examples of error responses are provided. 


<!--#### Error Code 500 - Invalid Parameter Format-->
> Error Code 500 - Invalid Parameter Format

`LB>>In the Get Rate and Review API, this is shown as error code 540. Which is correct?`

```json
{
    "stat" : "invalid parameter format"
}
```

<!--#### Error Code 544 - Terms and Conditions Not Agreed-->
> Error Code 544 - Terms and Conditions Not Agreed 

```json
{
    "stat" : "terms and conditions not signed"
}
```

<!--#### Error Code 547 - None of the Submitted Transaction IDs Are Recognized-->
> Error Code 547 - None of the Submitted Transaction IDs Are Recognized 

```json
{
    "stat" : "no valid transactions ids submitted"
}
```

The JSON structure associated with the error responses has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 



## **Get Transaction Summary**

You retrieve a summary of transactions by sending an HTTP **POST** request with the required information to the ``/get_summary`` endpoint.


| | |
|-|-|
|**Request**| **POST** `/api/get_summary` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "ref_number" : "B544748"
}
```

> A second example is:

```json
{
    "bank_id_from":"45240",
    "date_from" : "01/09/2018",
    "date_to" : "30/09/2018"
}
```

The request body can include one or more of the following parameters to filter the response results.

<!--### **Request Parameters**-->

Input Parameter | Description | Type | Size | Mandatory | Default Values | 
---|---|---|---|---|---|
`currency_from` | Specify a currency ID to filter transactions that have only used a specific from currency. | Varchar | 10 | No | - | 
`bank_id_from` | Specific a home/company bank ID to filter transactions for that specific bank account. | Integer | - | No | - | 
`ref_number` | Specify a batch (B) or transaction (T) number to filter transactions associated with that B or T number. | Varchar | 20 | No | - | 
`username` | Specify a user name to filter transactions associated with that user. | Varchar | 20 | No | - | 
`beneficiary` | Specific a beneficiary ID to filter transactions for that beneficiary. | Integer | - | No | - | 
`reconciliation_`<BR>`id` | Apply a full or partial case-sensitive search on the stored `client_data` field. This field is used in combination with other search parameters (such as `bank_id_from` or `beneficiary`) for optimum performance. | Varchar | - | No | - | 
`date_from` | Specify a date (in the format dd/mm/yyyy) to filter transactions that occurred after that date. | Date | - | No | - | 
`date_to` | Specify a date (in the format dd/mm/yyyy) to filter transactions that occurred before that date . | Date | - | No | - | 
`transactions_`<BR>`statuses` | Specify one or more status values to use to filter transactions. See [Payment Status Code](#payment-status-codes) for possible values. | List | - | No | - | 


### **Example Response**

If the get transaction summary request is successful, the service returns a response. 

> An example of the JSON structure in the response is as follows. In this case, the request returns all transactions that have a `batch_ref_num` value of `B544748`.

```json
{
    "transactions": [
        {
            "benef_id": "231426",
            "payee_name": "China Beneficiary 1003",
            "t_datetime_submitted": "2018-09-21 09:52:00",
            "foreign_amount": "USD 2106.00",
            "currency_rate_per_unit": "0.77352",
            "amount_from_bef_fee": "CAD 2722.62",
            "fee": "5.00",
            "total_trans_amount_from": "CAD 2727.62",
            "reference": "tst ref 246",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": "B544748",
            "ref_number": "T784880",
            "payment_status": "Awaiting Funds",
            "payment_status_abrv": "pending",
            "client_data": "Some Reconcile Ident"
        },
        {
            "benef_id": "231427",
            "payee_name": "China Beneficiary 963",
            "t_datetime_submitted": "2018-09-21 09:52:00",
            "foreign_amount": "USD 2107.00",
            "currency_rate_per_unit": "0.77352",
            "amount_from_bef_fee": "CAD 2723.91",
            "fee": "5.00",
            "total_trans_amount_from": "CAD 2728.91",
            "reference": "tst ref 246",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": "B544748",
            "ref_number": "T784881",
            "payment_status": "Awaiting Funds",
            "payment_status_abrv": "pending",
            "client_data": "Some Reconcile Ident 2"
        }
    ],
    "metadata": [
        {
            "batch_sum_details": "Domestic Amount: CAD 5,456.53 Reference Number: B544748",
            "usr_bank_from_data": "Wire Transfer from your bank account:
Bank Name: TORONTO DOMINION BANK Bank Country: Canada Account Number: 1xxxxxx-xx11 Transit Number & Institution Number: 0xx-xxxx-xx60 SWIFT/BIC Code: Txx-xxxx-xxOR Currency: CAD",
            "transfer_type": "Country Local Bank Transfer",
            "currency": "CAD",
            "amount": "CAD 5456.53",
            "particular": "Mr Test Test",
            "account_name": "Interpay Ltd",
            "bank_name": "ROYAL BANK OF CANADA",
            "bank_country": "Canada",
            "account_number": "1071596",
            "transit_code": "000306702",
            "swift_bic": "ROYCCAT2",
            "ref_number": "B544748"
        }
    ]
}
```

> The following is a second example. In this case, the request returns transactions where the source bank ID, `bank_id_from` is `45240` and the transaction date is between September 1 and September 30, 2018.

```json
{
    "transactions": [
        {
            "benef_id": "231426",
            "payee_name": "China Beneficiary 1003",
            "t_datetime_submitted": "2018-09-21 09:52:00",
            "foreign_amount": "USD 2106.00",
            "currency_rate_per_unit": "0.77352",
            "amount_from_bef_fee": "CAD 2722.62",
            "fee": "5.00",
            "total_trans_amount_from": "CAD 2727.62",
            "reference": "tst ref 246",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": "B544748",
            "ref_number": "T784880",
            "payment_status": "Awaiting Funds",
            "approve": null,
            "payment_status_abrv": "pending",
            "client_data": "Some Reconcile Ident"
        },
        {
            "benef_id": "231427",
            "payee_name": "China Beneficiary 963",
            "t_datetime_submitted": "2018-09-21 09:52:00",
            "foreign_amount": "USD 2107.00",
            "currency_rate_per_unit": "0.77352",
            "amount_from_bef_fee": "CAD 2723.91",
            "fee": "5.00",
            "total_trans_amount_from": "CAD 2728.91",
            "reference": "tst ref 246",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": "B544748",
            "ref_number": "T784881",
            "payment_status": "Awaiting Funds",
            "approve": null,
            "payment_status_abrv": "pending",
            "client_data": "Some Reconcile Ident 2"
        },
        {
            "benef_id": "197279",
            "payee_name": "Singapore Beneficiary 1",
            "t_datetime_submitted": "2018-09-03 11:02:00",
            "foreign_amount": "USD 428.00",
            "currency_rate_per_unit": "0.77173",
            "amount_from_bef_fee": "CAD 554.60",
            "fee": "15.00",
            "total_trans_amount_from": "CAD 569.60",
            "reference": "api ref 98",
            "tran_type": "Wire Transfer",
            "bene_payment_type": "Wire Transfer",
            "batch_ref_num": null,
            "ref_number": "T763646",
            "payment_status": "Awaiting Authorization",
            "approve": "\n                ",
            "payment_status_abrv": "await_auth",
            "client_data": "Immedis59"
        }
    ],
    "metadata": []
}
```

### **Errors**

If the get transaction summary request is not successful, the service responds with an error code, rejects the request, and does not return any transaction data. An example error response is provided. 

<!--#### Error Code 500 - Invalid Parameter Format--> 
> Error Code 500 - Invalid Parameter Format 

`LB>>Need to verify the error code number 500 is correct here`

```json
{
    "stat" : "invalid parameter format"
}
```

The JSON structure associated with the error response has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 

## **Get Spot Rates**

You get spot exchange rates for specific currency pairs by sending an HTTP **POST** request with the required information to the ``/get_rates`` endpoint. The quoted rates market rates without the application of margins or commissions. 

| | |
|-|-|
|**Request**| **POST** `/api/get_rates` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
| | |

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "amount": 1511,
    "pairs": [
        {
            "src": "GBP",
            "dst": "EUR"
        },
        {
            "src": "GBP",
            "dst": "USD"
        },
        {
            "src": "GBP",
            "dst": "HKD"
        },
        {
            "src": "GBP",
            "dst": "NZD"
        }
    ]
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter | Description | Type | Size | Mandatory | Default Values | 
---|---|---|---|---|---|
amount | The amount to convert. | Decimal | - | Yes | (format 11[,2]) | 
pairs | A list of the source and destination currency pairs to obtain conversion information for. | List | - | Yes | - | 
src | The source currency. | Varchar | - | Yes | - | 
dst | The destination currency. | Varchar | - | Yes | - | 


### **Example Response**

If the retrieve spot rates request is successful, the service provides a response. 

> An example of the JSON structure in the response is as follows:

```json
[
    {
        "curr": "GBPEUR",
        "bid": "1.13820",
        "bid_dt": "2018-06-08 09:57:19",
        "ask": "1.13832",
        "ask_dt": "2018-06-08 09:57:19",
        "mid": "1.13826",
        "mid_dt": "2018-06-08 09:57:19",
        "bid_amount_to": "1719.82",
        "bid_amount_from": "1511.00",
        "ask_amount_to": "1720.00",
        "ask_amount_from": "1511.00",
        "mid_amount_to": "1719.91",
        "mid_amount_from": "1511.00",
        "tm_gmt_time": "2018-06-08 09:57:19"
    },
    {
        "curr": "GBPUSD",
        "bid": "1.33904",
        "bid_dt": "2018-06-08 09:57:19",
        "ask": "1.33914",
        "ask_dt": "2018-06-08 09:57:19",
        "mid": "1.33910",
        "mid_dt": "2018-06-08 09:57:19",
        "bid_amount_to": "2023.29",
        "bid_amount_from": "1511.00",
        "ask_amount_to": "2023.44",
        "ask_amount_from": "1511.00",
        "mid_amount_to": "2023.38",
        "mid_amount_from": "1511.00",
        "tm_gmt_time": "2018-06-08 09:57:23"
    },
    {
        "curr": "GBPHKD",
        "bid": "10.50636",
        "bid_dt": "2018-06-08 09:57:19",
        "ask": "10.50736",
        "ask_dt": "2018-06-08 09:57:19",
        "mid": "10.50686",
        "mid_dt": "2018-06-08 09:57:19",
        "bid_amount_to": "15875.11",
        "bid_amount_from": "1511.00",
        "ask_amount_to": "15876.62",
        "ask_amount_from": "1511.00",
        "mid_amount_to": "15875.87",
        "mid_amount_from": "1511.00",
        "tm_gmt_time": "2018-06-08 09:57:27"
    },
    {
        "curr": "GBPNZD",
        "bid": "1.90757",
        "bid_dt": "2018-06-08 09:57:19",
        "ask": "1.90825",
        "ask_dt": "2018-06-08 09:57:19",
        "mid": "1.90733",
        "mid_dt": "2018-06-08 09:57:29",
        "bid_amount_to": "2882.34",
        "bid_amount_from": "1511.00",
        "ask_amount_to": "2883.37",
        "ask_amount_from": "1511.00",
        "mid_amount_to": "2881.98",
        "mid_amount_from": "1511.00",
        "tm_gmt_time": "2018-06-08 09:57:27"
    }
]
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`curr` | A representation describing the from/to currency pair for a conversion. For example, `GBPNZD` represents a British pound to New Zealand dollar conversion. | Varchar
`bid` | The bid exchange rate. | Integer
`bid_dt` | The time associated with the bid exchange rate. | Integer
`ask` | The ask exchange rate. | Integer
`ask_dt` | The time associated with the ask exchange rate. | Integer
`mid` | The middle exchange rate. | Integer
`mid_dt` | The time associated with the middle exchange rate. | Integer
`bid_amount_to` | The bid rate amount in the to currency. | Integer
`bid_amount_from` | The bid rate amount in the from currency. | Integer
`ask_amount_to` | The ask rate amount in the to currency. | Integer
`ask_amount_from` | The ask rate amount in the from currency. | Integer
`mid_amount_to` | The middle rate amount in the to currency. | Integer
`mid_amount_from` | The middle rate amount in the from currency. | Integer
`tm_gmt_time` | The time of the conversion in Greenwich Mean Time (GMT). | Integer
 | |


### **Errors**

If the retrieve spot rates request is not successful, the service responds with an error code, rejects the request, and does not return exchange rate information. Some examples of the error responses are provided.

<!--#### Error Code 403 - Invalid Parameter Format-->
> Error Code 403 - Invalid Parameter Format

`LB>>Need to confirm that the following is a 403 error code.`

```json
{
    "stat" : "invalid parameter format"
}
```

<!--#### Error Code 403 - Source/Destination currency is not available-->
> Error Code 403 - Source/Destination currency is not available

`LB>>Need to confirm that the following is a 403 error code.`

```json
{
    "stat" : "Source/Destination currency is not available"
}
```

The JSON structure associated with the error responses has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **Send Query**

You initiate a query for additional information or submit an issue relating to a particular transaction by sending an HTTP **POST** request with the required information to the ``/send_query`` endpoint. `LB>>There is a note in the specification indicating "(temporarily disabled until further notice)". Need confirmation if we should comment this section out for now.`

| | |
|-|-|
|**Request**| **POST** `/api/send_query` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `"Authorization" : "Bearer eyJ0eXAiOiJKV1Q…"` |
|||

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "ref_number" : "T12345",
    "quote_question" : "Some query related to transaction T12345"
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter | Description | Type | Size | Mandatory | Default Values | 
---|---|---|---|---|---|
ref_number | The number of the transaction related to the query. | Varchar | 6 | No | - | 
quote_question | The question to ask. | Varchar | 512 | Yes | - | 

### **Example Response**

If the initiate query or submit issue request is successful, the service provides a response and logs a ticket for the attention of a customer service representative. 

> An example of the JSON structure in the response is as follows:

```json
{
    "stat" : "success"
}
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat` | The status of the request, for example, `success`. | Varchar


### **Errors**

If the initiate query or submit issue request is not successful, the service responds with an error code, rejects the request, and does not log a ticket for the attention of a customer service representative. An example of the error response is provided.

<!--#### Error Code 403 - Invalid Parameter Format-->
> Error Code 403 - Invalid Parameter Format

`LB>>Need to confirm that the following is a 403 error code.`

```json
{
    "stat" : "invalid parameter format"
}
```


## **Check for Holiday**

You check for a holiday by sending an HTTP **POST** request with the required information to the ``/check_holiday`` endpoint.

| | |
|-|-|
|**Request**| **POST** `/api/check_holiday` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `-` |
|||

### **Request Body Payload Example**

> An example of the JSON structure in the request is as follows:

```json
{
    "country":"irish",
    "date" : "2018-04-30"
}
```

The request body includes the following parameters:

<!--### **Request Parameters**-->

Input Parameter | Description | Type | Size | Mandatory | Default Values | 
---|---|---|---|---|---|
country | The region to be checked for a holiday. See the Example Response to the [Get Supported Holiday Regions](#get-supported-holiday-regions) API for supported values. | String | - | Yes | - | 
Date | The date to be checked for a holiday. | Date | - | Yes | - | 
| | | | | |

### **Example Response**

> The JSON structure in a successful response is as follows:

```json
{
    "supplied_date": "2018-03-30",
    "holidays": [
        "Good Friday"
    ]
}
```

The response includes the following parameters:

Field | Description | Type
----- | ----------- | ----
`supplied_date` | The date provided by the user. | Varchar 
`holidays` | A list of one or more holiday descriptions. | List
 | |

### **Errors**

If the request is not successful, the service responds with an error code, rejects the request, and does not provide holiday information. Some examples of the error responses are provided.

<!--#### Error Code 403 - Invalid Parameter Format-->
> Error Code XXX - No Holiday Event

`LB>>Need the error code number here.`

```json
{
    "stat" : "no holiday event on 2018-03-31"
}
```

<!--#### Error Code 403 - Invalid Parameter Format-->
> Error Code XXX - Incorrect Data Format

`LB>>Need the error code number here. Also, should this be Date format?`

```json
{
    "stat" : "incorrect data format, should be YYYY-MM-DD""
}
```

The JSON structure associated with the error responses has the following parameters:

Field | Description | Type
----- | ----------- | ----
`stat`  | A status message that describes the error encountered. | Varchar
| | 


## **Get Supported Holiday Regions**

You request a list of the supported holiday regions by sending an HTTP **GET** request with the required information to the ``/get_holiday_regions`` endpoint.

| | |
|-|-|
|**Request**| **GET** `/api/get_holiday_regions` |
|**Host** | `https://payments.transfermate.com` |
|**Content Type** | `application/json` |
|**Headers** | `-` |
|||

### **Request Body Payload Example**

None.

### **Example Response**

If the request is successful, the service returns a response. 

The response contains a list of supported holiday regions.  

> An example of the returned JSON structure is as follows:

```json
[
    "australian",
    "austrian",
    "brazilian",
    "canadian",
    "china",
    "christian",
    "danish",
    "dutch",
    "finnish",
    "french",
    "german",
    "greek",
    "hong_kong_c",
    "hong_kong",
    "indian",
    "indonesian",
    "iranian",
    "irish",
    "islamic",
    "italian",
    "japanese",
    "jewish",
    "malaysia",
    "mt",
    "mexican",
    "new_zealand",
    "norwegian",
    "philippines",
    "polish",
    "portuguese",
    "russian",
    "singapore",
    "sa",
    "south_korea",
    "spain",
    "swedish",
    "taiwan",
    "thai",
    "uk",
    "usa",
    "vietnamese"
]
```

### **Errors**

None.


# Codes and Message Reference

## Bank Verifier Codes and Messages

Code|Message|Type
----|-------|----
1000|Valid IBAN|Passed
1001|Valid IBAN ``LB>>What is the difference between codes 1000 and 1001?``|Passed
1010|No country supplied!|-
1020|Selected country and IBAN country do not match!|-
1030|Invalid country ISO 2 letter or selected country does not provide IBAN!|-
1040|Invalid IBAN length!|-
1050|Invalid IBAN structure!|-
1060|Invalid checksum!|-
1070|Please enter either SWIFT/BIC Code or National Bank Code!|-
1100|Valid BIC/SWIFT|Passed
1101|Valid BIC/SWIFT ``LB>>What is the difference between 1100 and 1101?``|Passed 
1110|No country supplied!|-
1120|Selected country and BIC/SWIFT country do not match!|-
1130|Invalid BIC/SWIFT length!|-
1140|Invalid BIC/SWIFT structure!|-
1150|BIC/SWIFT does not exist or the selected country does not provide BIC/SWIFT|-
1200|Valid National Bank Code|Passed
1210|No country supplied!|-
1220|National Bank Code does not exist or the selected country does not provide National Bank Code!|-
1301|Linked field does not exist!|-
1302|Please select a field type!|-
1303|Field is mandatory!|-
1304|Please enter either SWIFT/BIC Code or National Bank Code!|-
1305|If this is the correct Bank ``LB>>Message is incomplete?`` |-
1306|Different banks corresponding to the given bank data!|-
1307|You have not entered a valid account number!|-
1308|No country and/or currency supplied!|-
1309|The IBAN is mandatory for this country and currency!|-
1310|The SWIFT/BIC is mandatory for this country and currency!|-
1400|Valid data provided!|Passed
1410|Please provide the necessary fields!|-
 | | 

## Country Codes (ISO 3116) 

Country<BR>Code|Country|State|State Code|Available<BR>Currencies
--|-------------|-|-|--
AX | Aland Islands||| EUR, GBP, USD
AL | Albania ||| EUR, GBP, USD
DZ | Algeria ||| EUR, GBP, USD
AS | American Samoa ||| EUR, GBP, USD
AD | Andorra ||| EUR, GBP, USD
AO | Angola ||| EUR, GBP, USD
AI | Anguilla ||| EUR, GBP, USD
AQ | Antarctica ||| EUR, GBP, USD
AG | Antigua and Barbuda ||| EUR, GBP, USD
AR | Argentina ||| EUR, GBP, USD 
AM | Armenia ||| EUR, GBP, USD
AW | Aruba ||| EUR, GBP, USD
AU | Australia ||| AUD, CAD, EUR, <BR>GBP, NZD, SEK, <BR>SGD, USD 
AT | Austria ||| EUR, GBP, USD 
AZ | Azerbaijan ||| EUR, GBP, USD
BS | Bahamas ||| EUR, GBP, USD
BH | Bahrain ||| BHD, EUR, GBP, <BR>USD 
BD | Bangladesh ||| EUR, GBP, USD
BB | Barbados ||| EUR, GBP, USD
BE | Belgium ||| EUR, GBP, USD
BZ | Belize ||| EUR, GBP, USD
BJ | Benin ||| EUR, GBP, USD 
BM | Bermuda ||| EUR, GBP, USD
BT | Bhutan ||| EUR, GBP, USD
BO | Bolivia ||| EUR, GBP, USD 
BQ | Bonaire, <BR>Saint Eustatius <BR>and Saba ||| EUR, GBP, USD
BW | Botswana ||| BWP, EUR, GBP, <BR>USD
BV | Bouvet Island ||| EUR, GBP, USD
BR | Brazil ||| EUR, GBP, USD
IO | British Indian <BR>Ocean Territory ||| EUR, GBP, USD
BN | Brunei Darussalam ||| EUR, GBP, USD
BG | Bulgaria ||| BGN, EUR, GBP, <BR>USD
BF | Burkina Faso ||| EUR, GBP, USD
CV | Cabo Verde ||| EUR, GBP, USD
KH | Cambodia ||| EUR, GBP, USD
CM | Cameroon ||| EUR, GBP, USD
CA | Canada | Alberta<BR>British Columbia<BR>Manitoba<BR>New Brunswick<BR>Newfoundland and Labrador<BR>Northwest Territories<BR>Nova Scotia<BR>Nunavut<BR>Ontario<BR>Prince Edward Island<BR>Quebec<BR>Saskatchewan<BR>Yukon Territory | CA-AB<BR> CA-BC<BR> CA-MB<BR> CA-NB<BR>CA-NL<BR><BR>CA-NT<BR>CA-NS<BR>CA-NU<BR>CA-ON<BR>CA-PE<BR>CA-QC<BR>CA-SK<BR>CA-YT | AUD, CAD, EUR, <BR>GBP, NZD, USD
KY | Cayman Islands ||| EUR, GBP, USD
TD | Chad ||| EUR, GBP, USD
CL | Chile ||| EUR, GBP, USD
CN | China ||| AUD, EUR, GBP, <BR>HKD, USD
CX | Christmas Island ||| AUD, EUR, GBP, <BR>USD
CC | Cocos (Keeling) <BR>Islands ||| AUD, EUR, GBP, <BR>USD
CO | Colombia ||| EUR, GBP, USD
KM | Comoros ||| EUR, GBP, USD
CG | Congo ||| EUR, GBP, USD
CK | Cook Islands ||| EUR, GBP, NZD, <BR>USD
CR | Costa Rica ||| EUR, GBP, USD
CI | Cote D'Ivoire ||| EUR, GBP, USD
HR | Croatia ||| EUR, GBP, HRK, <BR>USD
CW | Curacao ||| EUR, GBP, USD
CY | Cyprus ||| EUR. GBP, USD
CZ | Czechia ||| CZK, EUR, GBP, <BR>PLN, USD
DK | Denmark ||| AUD, DKK, EUR, <BR>GBP, NZD, USD
DJ | Djibouti ||| EUR. GBP, USD
DM | Dominica ||| EUR, GBP, USD
DO | Dominican Republic ||| EUR, GBP, USD
EC | Ecuador ||| EUR, GBP, USD
SV | El Salvador ||| EUR, GBP, USD
EE | Estonia ||| EUR, GBP, USD
ET | Ethiopia ||| EUR, GBP, USD
FK | Falkland Islands <BR>(Malvinas) ||| EUR, GBP, USD
FO | Faroe Islands ||| EUR, GBP, USD
FJ | Fiji ||| EUR, GBP, USD
FI | Finland ||| EUR, GBP, USD
FR | France ||| CAD, EUR, GBP, <BR>USD
GF | French Guiana ||| EUR, GBP, USD
PF | French Polynesia ||| EUR, GBP, USD
TF | French Southern <BR>Territories ||| EUR, GBP, USD
GA | Gabon ||| EUR, GBP, USD
GM | Gambia ||| EUR, GBP, USD
GE | Georgia ||| EUR, GBP, USD
DE | Germany ||| AUD, CAD, CHF, <BR>DKK, EUR, GBP, <BR>HRK, JPY, NOK, <BR>NZD, PLN, SEK, <BR>USD
GH | Ghana ||| EUR, GBP, USD
GI | Gibraltar ||| EUR, GBP, USD
GR | Greece ||| CHF, EUR, GBP, <BR>USD
GL | Greenland ||| EUR, GBP, USD
GD | Grenada ||| EUR, GBP, USD
GP | Guadeloupe ||| EUR, GBP, USD
GU | Guam ||| EUR, GBP, USD
GT | Guatemala ||| EUR, GBP, USD
GG | Guernsey ||| EUR, GBP, USD
GQ | Guinea, Equatorial ||| EUR, GBP, USD
GY | Guyana ||| EUR, GBP, USD
HM | Heard and <BR>Mc Donald Islands ||| EUR, GBP, USD
HN | Honduras ||| EUR, GBP, USD
HK | Hong Kong ||| AUD, EUR, GBP, <BR>HKD, USD
HU | Hungary ||| EUR, GBP, HUF, <BR>USD
IS | Iceland ||| EUR, GBP, USD
IN | India ||| AUD, EUR, GBP, <BR>INR, USD
ID | Indonesia ||| EUR, GBP, USD
IE | Ireland ||| AUD, CAD, CHF, <BR>EUR, GBP, HKD, <BR>JPY, NZD, SEK, <BR>SGD, THB, USD, <BR>ZAR
IM | Isle of Man ||| EUR, GBP, USD
IL | Israel ||| CHF, EUR, GBP, <BR>ILS, USD
IT | Italy ||| EUR, GBP, USD
JM | Jamaica ||| EUR, GBP, USD
JP | Japan ||| CAD, EUR, GBP, <BR>JPY, USD
JE | Jersey ||| EUR, GBP, USD
JO | Jordan ||| EUR, GBP, USD
KZ | Kazakhstan ||| EUR, GBP, USD
KE | Kenya ||| EUR, GBP, KES, <BR>USD
KI | Kiribati ||| AUD, EUR, GBP, <BR>USD
KR | Korea, <BR>Republic of (South) ||| EUR, GBP, USD
XK | Kosovo ||| EUR, GBP, USD
KW | Kuwait ||| EUR, GBP, KWD, <BR>USD
KG | Kyrgyzstan ||| EUR, GBP, USD
LA | Lao People's <BR>Democratic <BR>Republic (Laos) ||| EUR, GBP, USD
LV | Latvia ||| EUR, GBP, USD
LS | Lesotho ||| EUR, GBP, USD
LR | Liberia ||| EUR, GBP, USD
LI | Liechtenstein ||| CAD, CHF, EUR, <BR>GBP, USD
LT | Lithuania ||| EUR, GBP, USD
LU | Luxembourg ||| EUR, GBP, USD
MO | Macao ||| EUR, GBP, HKD, <BR>USD
MK | Macedonia ||| EUR, GBP, USD
MG | Madagascar ||| EUR, GBP, USD
MW | Malawi ||| EUR, GBP, USD
MY | Malaysia ||| AUD, EUR, GBP, <BR>USD
MV | Maldives ||| EUR, GBP, USD
ML | Mali ||| EUR, GBP, USD
MT | Malta ||| EUR, GBP, USD
MH | Marshall Islands ||| EUR, GBP, USD
MQ | Martinique ||| EUR, GBP, USD
MR | Mauritania ||| EUR, GBP, USD
MU | Mauritius ||| EUR, GBP, USD
YT | Mayotte ||| EUR, GBP, USD
MX | Mexico ||| EUR, GBP, MXN, <BR>USD
FM | Micronesia, <BR>Federated States of ||| EUR, GBP, USD
MC | Monaco ||| EUR, GBP, USD
MN | Mongolia ||| EUR, GBP, USD
ME | Montenegro ||| EUR, GBP, USD
MS | Montserrat ||| EUR, GBP, USD
MA | Morocco ||| EUR, GBP, USD
MZ | Mozambique ||| EUR, GBP, USD
NA | Namibia ||| EUR, GBP, USD, <BR>ZAR
NR | Nauru ||| AUD, EUR, GBP, <BR>USD
NP | Nepal ||| EUR, GBP, USD
AN | Netherlands <BR>Antilles ||| EUR, GBP, USD
NL | Netherlands <BR>(Holland) ||| EUR, GBP, JPY,USD
NC | New Caledonia ||| EUR, GBP, USD
NZ | New Zealand ||| AUD, CAD, EUR, <BR>GBP, JPY, NZD, <BR>USD
NI | Nicaragua ||| EUR, GBP, USD
NE | Niger ||| EUR, GBP, USD
NG | Nigeria ||| EUR, GBP, USD
NU | Niue ||| EUR, GBP, NZD, <BR>USD
NF | Norfolk Island ||| AUD, EUR, GBP, <BR>USD
MP | Northern <BR>Mariana Islands ||| EUR, GBP, USD
NO | Norway ||| EUR, GBP, NOK, <BR>USD
OM | Oman ||| EUR, GBP, OMR, <BR>USD
PK | Pakistan ||| EUR, GBP, USD
PW | Palau ||| EUR, GBP, USD
PS | Palestine, <BR>State of ||| EUR, GBP, USD
PA | Panama ||| EUR, GBP, USD
PG | Papua New Guinea ||| EUR, GBP, USD
PY | Paraguay ||| EUR, GBP, USD
PE | Peru ||| EUR, GBP, USD
PH | Philippines ||| AUD, EUR, GBP, <BR>PHP, SGD, USD
PN | Pitcairn Island ||| EUR, GBP, NZD, <BR>USD
PL | Poland ||| AUD, EUR, GBP, <BR>PLN, USD
PT | Portugal ||| EUR, GBP, USD
PR | Puerto Rico ||| EUR, GBP, USD
QA | Qatar ||| EUR, GBP, QAR, <BR>USD
RE | Reunion ||| EUR, GBP, USD
RO | Romania ||| EUR, GBP, RON, <BR>USD
RW | Rwanda ||| EUR, GBP, USD
BL | Saint Barthelemy ||| EUR, GBP, USD
SH | Saint Helena, <BR>Ascension and <BR>Tristan da Cunha ||| EUR, GBP, USD
KN | Saint Kitts <BR>and Nevis ||| EUR, GBP, USD
LC | Saint Lucia ||| EUR, GBP, USD
MF | Saint Martin <BR>(French part) ||| EUR, GBP, USD
PM | Saint Pierre <BR>and Miquelon ||| EUR, GBP, USD
VC | Saint Vincent and <BR>the Grenadines ||| EUR, GBP, USD
WS | Samoa ||| EUR, GBP, USD
SM | San Marino ||| EUR, GBP, USD
ST | Sao Tome <BR>and Principe ||| EUR, GBP, USD
SA | Saudi Arabia ||| EUR, GBP, SAR, <BR>USD
SN | Senegal ||| EUR, GBP, USD
RS | Serbia ||| EUR, GBP, USD
SC | Seychelles ||| EUR, GBP, USD
SL | Sierra Leone ||| EUR, GBP, USD
SG | Singapore ||| AUD, EUR, GBP, <BR>SGD, USD
SX | Sint Maarten <BR>(Dutch part) ||| EUR, GBP, USD
SK | Slovakia ||| EUR, GBP, USD
SI | Slovenia ||| EUR, GBP, USD
SB | Solomon Islands ||| EUR, GBP, USD
ZA | South Africa ||| AUD, EUR, GBP, <BR>USD, ZAR
GS | South Georgia <BR>and the South <BR>Sandwich Islands ||| EUR, GBP, USD
ES | Spain ||| EUR, GBP, NZD, <BR>PLN, USD
LK | Sri Lanka ||| EUR, GBP, USD
SR | Suriname ||| EUR, GBP, USD
SJ | Svalbard and <BR>Jan Mayen Islands ||| EUR, GBP, USD
SZ | Swaziland ||| EUR, GBP, USD, <BR>ZAR
SE | Sweden ||| EUR, GBP, NOK, <BR>NZD, SEK, USD
CH | Switzerland ||| CAD, CHF, DKK, <BR>EUR, GBP, NZD, <BR>USD
TW | Taiwan ||| EUR, GBP, USD
TJ | Tajikistan ||| EUR, GBP, USD
TZ | Tanzania, <BR>United Republic of ||| EUR, GBP, USD
TH | Thailand ||| EUR, GBP, THB, <BR>USD
TL | Timor-Leste ||| EUR, GBP, USD
TG | Togo ||| EUR, GBP
TK | Tokelau ||| EUR, GBP, NZD, <BR>USD
TO | Tonga ||| EUR, GBP, USD
TT | Trinidad <BR>and Tobago ||| EUR, GBP, USD
TR | Turkey ||| CAD, EUR, GBP, <BR>TRY, USD
TM | Turkmenistan ||| EUR, GBP, USD
TC | Turks <BR>and Caicos Islands ||| EUR, GBP, USD
TV | Tuvalu ||| AUD, EUR, GBP, USD
UG | Uganda ||| EUR, GBP, USD
AE | United Arab Emirates ||| AED, EUR, GBP, USD
GB | United Kingdom ||| AUD, CAD, CHF, <BR>DKK, EUR, GBP, <BR>HKD, JPY, NZD, <BR>PLN, SEK, USD
UY | Uruguay ||| EUR, GBP, USD
US | USA | Alabama<BR>Alaska<BR>Arizona<BR>Arkansas<BR>California<BR>Colorado<BR>Connecticut<BR>Delaware<BR>District of Columbia<BR>Florida<BR>Georgia<BR>Hawaii<BR>Idaho<BR>Illinois<BR>Indiana<BR>Iowa<BR>Kansas<BR>Kentucky<BR>Louisiana<BR>Maine<BR>Maryland<BR>Massachusetts<BR>Michigan<BR>Minnesota<BR>Mississippi<BR>Missouri<BR>Montana<BR>Nebraska<BR>Nevada<BR>New Hampshire<BR>New Jersey<BR>New Mexico<BR>New York<BR>North Carolina<BR>North Dakota<BR>Ohio<BR>Oklahoma<BR>Oregon<BR>Pennsylvania<BR>Rhode Island<BR>South Carolina<BR>South Dakota<BR>Tennessee<BR>Texas<BR>Utah<BR>Vermont<BR>Virginia<BR>Washington<BR>West Virginia<BR>Wisconsin<BR>Wyoming | US-AL<BR>US-AK<BR>US-AZ<BR>US-AR<BR>US-CA<BR>US-CO<BR>US-CT<BR>US-DE<BR>US-DC<BR>US-FL<BR>US-GA<BR>US-HI<BR>US-ID<BR>US-IL<BR>US-IN<BR>US-IA<BR>US-KS<BR>US-KY<BR>US-LA<BR>US-ME<BR>US-MD<BR>US-MA<BR>US-MI<BR>US-MN<BR>US-MS<BR>US-MO<BR>US-MT<BR>US-NE<BR>US-NV<BR>US-NH<BR>US-NJ<BR>US-NM<BR>US-NY<BR>US-NC<BR>US-ND<BR>US-OH<BR>US-OK<BR>US-OR<BR>US-PA<BR>US-RI<BR>US-SC<BR>US-SD<BR>US-TN<BR>US-TX<BR>US-UT<BR>US-VT<BR>US-VA<BR>US-WA<BR>US-WV<BR>US-WI<BR>US-WY | AUD, CAD, EUR, <BR>GBP, NZD, USD
UM | US Minor <BR>Outlying Islands ||| EUR, GBP, USD
UZ | Uzbekistan ||| EUR, GBP, USD
VU | Vanuatu ||| EUR, GBP, USD
VA | Vatican City State <BR>(Holy See) ||| EUR, GBP, USD
VN | Vietnam ||| EUR, GBP, USD
VG | Virgin Islands, <BR>British ||| EUR, GBP, USD
VI | Virgin Islands, <BR>US ||| EUR, GBP, USD
WF | Wallis and <BR>Futuna Islands ||| EUR, GBP, USD 
EH | Western Sahara ||| EUR, GBP, USD
ZM | Zambia ||| EUR, GBP, USD
 | | | | 

## Currency Codes (ISO 4217)

Country | Currency | Code
--- | --- | ---
United Arab Emirates | Dirham | AED
Albania | Lek | ALL
Armenia | Dram | AMD
Argentina | Peso | ARS
Australian | Australian Dollars | AUD
Aruba | Aruban Florin | AWG
Azerbaijan | Azerbaijanian Manat | AZN
Bosnia and Herzegovina | Bosnia and Herzegovina convertible mark | BAM
Barbados | Barbadian Dollar | BBD
Bangladesh | Taka | BDT
Bulgaria | Lev | BGN
Bahrain | Bahraini Dinar | BHD
Burundi | Burundi Franc | BIF
Bermuda | Bermudian Dollar | BMD
Brunei Darussalam | Bruneian Dollar | BND
Bolivia | Boliviano | BOB
Brazil | Brazil | BRL
Bahamas | Bahamian Dollar | BSD
Botswana | Pula | BWP
Belarus | Belarus Ruble | BYR
Belize | Belizean Dollar | BZD
Canada | Canadian Dollar | CAD
Congo-Brazzaville | Congolese Frank | CDF
Liechtenstein | Swiss Franc | CHF
Chile | Chilean Peso | CLP
China | Yuan Renminbi (Mainland) | CNH
China | Yuan Renminbi | CNY
Colombia | Peso | COP
Costa Rica | Costa Rican Colon | CRC
Cuba | Cuban Peso | CUP
Cape Verde | Escudo | CVE
Czech Republic | Koruna | CZK
Djibouti | Djiboutian Franc | DJF
Denmark | Danish Krone | DKK
Dominican Republic | Dominican Peso | DOP
Algeria | Algerian Dinar | DZD
Egypt | Egyptian Pound | EGP
Eritrea | Ethiopian Birr | ETB
American Samoa | Euros | EUR
Fiji | Fijian Dollar | FJD
South Georgia and the <BR>South Sandwich Islands | Sterling | GBP
Georgia | Lari | GEL
Ghana | Ghana cedi | GHS
Gambia | Dalasi | GMD
Guinea | Guinean Franc | GNF
Guatemala | Quetzal | GTQ
Hong Kong | Hong Kong Dollar | HKD
Honduras | Lempira | HNL
Croatia (Hrvatska) | Croatian Dinar | HRK
Haiti | Gourde | HTG
Hungary | Forint | HUF
East Timor | Indonesian Rupiah | IDR
Israel | Shekel | ILS
Bhutan | Indian Rupee | INR
Iraq | Iraqi Dinar | IQD
Iran (Islamic Republic of) | Iranian Rial | IRR
Iceland | Icelandic Krona | ISK
Jamaica | Jamaican Dollar | JMD
Jordan | Jordanian Dinar | JOD
Japan | Japanese Yen | JPY
Kenya | Kenyan Shilling | KES
Cambodia | Riel | KHR
Comoros | Comoran Franc | KMF
Korea North | Won | KRW
Kuwait | Kuwaiti Dinar | KWD
Cayman Islands | Caymanian Dollar | KYD
Kazakhstan | Tenge | KZT
Lao People's Democratic Republic | Kip | LAK
Lebanon | Lebanese Pound | LBP
Sri Lanka | Rupee | LKR
Liberia | Liberian Dollar | LRD
Lesotho | Loti | LSL
Libyan Arab Jamahiriya | Libyan Dinar | LYD
Morocco | Dirham | MAD
Moldova Republic of | Leu | MDL
Madagascar | Malagasy Franc | MGA
Macedonia | Denar | MKD
Myanmar | Kyat | MMK
Macau | Pataca | MOP
Mauritania | Ouguiya | MRO
Mauritius | Mauritian Rupee | MUR
Maldives | Rufiyaa | MVR
Malawi | Malawian Kwacha | MWK
Mexico | Peso | MXN
Malaysia | Ringgit | MYR
Mozambique | Metical | MZN
Namibia | Dollar | NAD
Nigeria | Naira | NGN
Nicaragua | Cordoba Oro | NIO
Bouvet Island | Norwegian Krone | NOK
Nepal | Nepalese Rupee | NPR
New Zealand | New Zealand Dollars | NZD
Oman | Sul Rial | OMR
Panama | Balboa | PAB
Peru | Nuevo Sol | PEN
Papua New Guinea | Kina | PGK
Philippines | Peso | PHP
Pakistan | Rupee | PKR
Poland | Zloty | PLN
Paraguay | Guarani | PYG
Qatar | Rial | QAR
Romania | Leu | RON
Serbia | Serbian dinar | RSD
Russian Federation | Ruble | RUB
Rwanda | Rwanda Franc | RWF
Saudi Arabia | Riyal | SAR
Seychelles | Rupee | SCR
Sudan | Dinar | SDD
Sudan | Dinar | SDG
Sweden | Krona | SEK
Singapore | Dollar | SGD
Saint Helena, Ascension <BR>and Tristan De Cunha | Saint Helena Pound | SHP
Sierra Leone | Leone | SLL
Somalia | Shilling | SOS
Sao Tome and Principe | Dobra | STD
El Salvador | Salvadoran Colon | SVC
Syrian Arab Republic | Syrian Pound | SYP
Swaziland | Lilangeni | SZL
Thailand | Baht | THB
Tunisia | Tunisian Dinar | TND
Turkey | Lira | TRY
Trinidad and Tobago | Trinidad and Tobago Dollar | TTD
Taiwan | Dollar | TWD
Tanzania | Shilling | TZS
Ukraine | Hryvnia | UAH
Uganda | Shilling | UGX
United States | United States Dollar | USD
Uruguay | Peso | UYU
Uzbekistan | Som | UZS
Venezuela | Bolivar | VEF
Cameroon | CFA Franc BEAC | XAF
Anguilla | East Caribbean Dollar | XCD
Benin | CFA Franc BCEAO | XOF
French Polynesia | CFP Franc | XPF
Yemen | Rial | YER
South Africa | Rand | ZAR
Zambia | Zambian Kwacha | ZMW
 | | 


## Payment Method/Transaction Type Values

Value | Description 
:-:|---
1 | Direct Debit/ACH 
2 | Wire Transfer 
 | 

## Transit Code Values 

Value | Description | 
---|---|
- | Unavailable | 
aba_number_5 | ABA / Routing Number | 
bank_and_branch_code_12 | Bank Code | 
bsb_14 | BSB | 
ifsc_29 | IFSC | 
other_number_26 | Other Number | 
sort_code_2 | Sort Code | 
transit_code_19 | Transit Number & Institution Number |
 | | 


## Transfer Reasons

Value | Description 
:-:|---
7 | Bill payment 
8 | Business venture 
9 | Charitable donation 
10 | Estate settlement / Inheritance 
11 | Government related payment 
12 | Intercompany payment 
13 | Investment related payment 
23 | Medical fee refund 
24 | Paying Consultancy Fees 
14 | Payment for a loan or deposit 
15 | Payroll / Personnel payment 
16 | Professional fees payment 
17 | Purchase of Good(s) 
18 | Purchase of property / real estate 
19 | Purchase of Service(s) 
20 | Research and Development 
 | 

## Payment Status Codes

Value | Description  
:-:|---
1 | Awaiting Funds 
3 | Cancelled 
5 | Awaiting Authorization 
6 | Cancelled by approver 
 | 

## Country Reference by System ID

<aside class="notice">
Specifying a country using the system ID is deprecated in favor of using the ISO country codes. The following table shows the ISO 3166 country code that corresponds to each system ID.
</aside>

System ID | Country Code<BR>(ISO 3166) | Description | 
:-:|:-:|---
14 | AU | Australia 
16 | AT | Austria 
23 | BE | Belgium 
35 | BG | Bulgaria 
40 | CA | Canada 
56 | HR | Croatia 
58 | CY | Cyprus 
60 | CZ | Czechia 
61 | DK | Denmark 
72 | EE | Estonia 
77 | FI | Finland 
79 | FR | France 
81 | GF | French Guiana 
87 | DE | Germany 
91 | GR | Greece 
94 | GP | Guadeloupe 
107 | HU | Hungary 
113 | IE | Ireland 
116 | IT | Italy 
260 | JE | Jersey 
129 | LV | Latvia 
135 | LT | Lithuania 
136 | LU | Luxembourg 
144 | MT | Malta 
147 | MQ | Martinique 
102 | NL | Netherlands (Holland) 
164 | NZ | New Zealand 
181 | PL | Poland 
182 | PT | Portugal 
185 | RE | Reunion 
186 | RO | Romania 
264 | BL | Saint Barthelemy 
265 | MF | Saint Martin (French part) 
193 | PM | Saint Pierre and Miquelon 
204 | SK | Slovakia 
205 | SI | Slovenia 
211 | ES | Spain 
217 | SE | Sweden 
236 | GB | United Kingdom 
238 | US | USA 
 | | 

## Error Codes Reference

### **Error Code 403 - Incorrect Credentials**

<pre class="center-column">
{
    "stat" : "incorrect credentials. caution: multiple incorrect attempts"
}
</pre>

### **Error Code 403 - Too Many Incorrect Attempts**

<pre class="center-column">
{
    "stat" : "too many incorrect attempts. account is locked"
}
</pre>

### **Error Code 403 - Missing or Malformed Endpoint**

<pre class="center-column">
{
    "stat" : "missing or malformed endpoint"
}
</pre>

### **Error Code 403 - Session Not Available or Incorrect SSO Token**

<pre class="center-column">
{
    "stat" : "this session is not available, or the sso token is incorrect"
}
</pre>

### **Error Code 403 - Invalid Parameter Format**

`LB>>Need to confirm that the following is a 403 error code.`

<pre class="center-column">
{
    "stat" : "invalid parameter format"
}
</pre>


### **Error Code 500 - Invalid Parameter Format**

<pre class="center-column">
{
    "stat" : "invalid parameter format"
}
</pre>

### **Error Code 521 - Account Number Exists, Client ID Exists, or Unsuccessful Operation**

<pre class="center-column">
[
    {
        "ID": "iban_account_number",
        "description": "Already exists!"
    },
    {
        "ID": "client_id",
        "description": "Already exists!"
    },
    {
        "ID": "db_action",
        "description": "Operation is not successful! Please try again."
    }
]
</pre>

### **Error Code 521 - Name Already Exists**

<pre class="center-column">
[
    {
        "ID": "payee_name",
        "description": "Already exists!"
    }
]
</pre>

### **Error Code 522 - One or More JSON Attributes Are Missing or Malformed**

<pre class="center-column">
{
    "stat" : "One or more JSON attributes missing or malformed: country_id"
}
</pre>

### **Error Code 523 - Invalid Token or Missing Return Data**

<pre class="center-column">
{
    "stat" : "Invalid token or missing return data"
}
</pre>

### **Error Code 531 - Account Exists, Unknown Currency, or Operation Unsuccessful**

<pre class="center-column">
[
    {
        "ID": "account_number",
        "description": "Already exists!"
    },
    {
        "ID": "currency_id",
        "description": "Unknown currency"
    },
    {
        "ID": "db_action",
        "description": "Operation is not successful! Please try again."
    }
]
</pre>

### **Error Code 532 - One or More JSON Attributes Missing or Malformed**

<pre class="center-column">
{    
    "stat" : "One or more JSON attributes missing or malformed: country_id"
}
</pre>

### **Error Code 533 - Invalid Token or Missing Return Data**

<pre class="center-column">
{
    "stat" : "Invalid token or missing return data"
}
</pre>

### **Error Code 534 - Examples**

<pre class="center-column">
{
    "code": "1220",
    "message": "National Bank Code does not exist or the selected country does not provide National Bank Code!" 
}
{
    "code": "1020",
    "message": "Selected country and IBAN country do not match!"
}
</pre>

### **Error Code 535 - No Return Data**

<pre class="center-column">
{
    "stat" : "no return data. incorrect or inactive bank account"
}
</pre>

### **Error Code 540 - Invalid Parameter Format**

<pre class="center-column">
{
    "stat" : "invalid parameter format"
}
</pre>

### **Error Code 541 - Payment Type Invalid**

<pre class="center-column">
[
    {
        "ID": "bank_pay_type",
        "description": "Payment type is not valid!"
    },
    {
        "ID": "db_action",
        "description": "Operation is not successful! Please try again."
    }
]
</pre>

Field | Description | Type
----- | ----------- | ----
`ID` | The ID of the error message. | Varchar
`description` | A descirption of the error. | Varchar
| | 

### **Error Code 542 - Payment Blocked**

<pre class="center-column">
{
  "stat" : "forbidden",
  "message" : "payment is blocked and cannot be authorized",
  "reason_code" : 999
}
</pre>

Field | Description | Type
----- | ----------- | ----
`stat` | The status of the request. | Varchar
`message` | A message associated with the status. | Varchar
`reason_code` | The reason code associated with the satus. | Integer
| | 


### **Error Code 543 - Invalid Combination of Parameters or Payment Type Not Supported**

<pre class="center-column">
{
    "stat" : "invalid combination of bank/beneficiary currency parameters or payment type not supported for beneficiary"
}
</pre>

### **Error Code 544 - Terms and Conditions Not Agreed** 

<pre class="center-column">
{
    "stat" : "terms and conditions not signed"
}
</pre>

### **Error Code 545 - One or More JSON Attributes Missing or Malformed**

<pre class="center-column">
{
    "stat" : "one or more JSON attributes missing or malformed: currency_type"
}
</pre>

<aside class="notice">
In this error message structure, the <code>currency_type</code> parameter is just an example. Any of the request input parameters can be reported in an error structure like this.
</aside>


### **Error Code 546 - One or More JSON Attributes Failed Validation**

<pre class="center-column">
{
    "stat" : "contents of one or more JSON attributes failed validation"
}
</pre>

### **Error Code 547 - None of the Submitted Transaction IDs Are Recognized**

<pre class="center-column">
{
    "stat" : "no valid transactions ids submitted"
}
</pre>


