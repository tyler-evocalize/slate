---
title: Management API Reference

includes:
 - _user_and_group_management_api
 - errors
---

# Introduction

Welcome to the Evocalize management API. This API is designed for end users of the Evocalize Developer Tools platform.

# Request And Response Info

## Headers
> Example Headers

```
x-evocalize-client-key-id: a5646c38-fc29-11e9-8f0b-362b9e155667
x-evocalize-timestamp: 1604094273
x-evocalize-signature: 690a0ac5a5a219bb4a773f5bc116a32553be4e8380845d854f07b5e8471bc954
Content-Type: application/json
```

### Header info

Header | Required | Description
------ | -------- | -----------
X-Evocalize-Client-Key-Id | true | Client Key provided by Evocalize
X-Evocalize-Timestamp | true |Time Since Epoch in seconds. ex: 1604094273. Requests with a timestamp over a minute old will not be accepted.
X-Evocalize-Signature | true |Signature of the payload. See Signing Requests Below.
Content-Type | true | `application/json`


## Signing Requests: 
   
> Request signing example

```kotlin
// Concatenate all the parts
val toValidate = StringBuilder()
  .append(request.servletPath)
  .append("\n")
  // Omit this block if the request does not have a body
  .append(request.body())
  .append("\n")
  //
  .append(request.headers["X-Evocalize-Timestamp"])
  .append("\n")
  .append(myClientSecret)
  .toString()
                 
val expectedSignature = Hashing.sha256().hashString(toValidate, Charsets.UTF_8)

// expectedSignature will match request.headers["X-Evocalize-Signature"]
```

For security and identity purposes, we require all partners to sign all API requests. The methodology is straight forward. Join the following values with a new line character between each item

- UrlPath (including path params if present)
- Any POST data (leave this out if you are not POSTing a body)
- Value of the timestamp used in the X-Evocalize-Timestamp Header
- Client Secret (this will be provided by Evocalize)

<aside class="notice">All code examples below assume that you are passing the required headers described in this section</aside>

## Response Format

> JSON Response Schema

```json
{
  "metadata": {
    "<JSON Object>"
  },
  "data": {
    "<Request Specifc JSON Response>"
  },
  "errors": [
    {
      "message": "String",
      "code": "String",
      "field" : "String", // Omitted if null
      "details": "<JSON Object>" // Omitted if null
    }
  ],
  "nextPageToken": "String", 
  "previousPageToken": "String"
```

> Example Success Response: 

```json
{
  "data": {
    "<Request Specifc JSON Response>"
  }
}
```

> Example Error Response:

```json
{
  "errors": [
    {
      "message": "Unauthorized Request",
      "code": "EV_UNAUTHORIZED_MISSING_HEADERS"
    }
  ]
}
```

All requests are returned as JSON wrapped in a standard response format. The Schema section shows all the potential fields that we could return. Do note that we do not transmit null values. The most common responses will look like the examples provided.


Schema Field | Always Present | Description
------------ | -------------- | -----------
metadata | false | JSON object containing extra data around requests and responses. 
data | false | Returned for requests that result with 2xx responses.
errors | false | Returned for requests that result in non 2xx responses.
nextPageToken | false | Page token appears for result sets larger than 100 items. Not present on last page of results.
previousPageToken | false | Same logic as nextPageToken. Not present on first page of results.