---
title: Management API Reference

includes:
 - _user_and_group_management_api
 - errors
---

# Introduction

Welcome to the Evocalize management API. This API allows you to manage users, groups and user group associations.

# Request And Response Info

## Headers
> Example Headers

```http
x-evocalize-client-key-id: a5646c38-fc29-11e9-8f0b-362b9e155667
x-evocalize-timestamp: 1604094273
x-evocalize-signature: 690a0ac5a5a219bb4a773f5bc116a32553be4e8380845d854f07b5e8471bc954
Content-Type: application/json
```

- X-Evocalize-Client-Key-Id: Provided by Evocalize. 
- X-Evocalize-Timestamp: Time Since Epoch in seconds. ex: 1604094273. Requests with a timestamp over a minute old will not be accepted.
- X-Evocalize-Signature: Signature of the payload. See Signing Requests Below.
- Content-Type: application/json


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
  // Omitted if null
  "metaData": {
    "<JSON Object>"
  },
  // Omitted in the case of an error response
  "data": {
    "<Request Specifc JSON Response>"
  },
  // Omitted in the case of a success response
  "errors": [
    {
      "message": "String", // Always prsent in null case
      "field" : "String", // Omitted if null
      "code": "String", // Omitted if null
      "details": <JSON Object> // Omitted if null
    }
  ],
  // Only present when results exceed 100 items.
  "nextPageToken": "String", // Appears on all pages but the last
  "previousPageToken": "String" // Appears on all pages but the first
}
```

> Success Response Example: 

```json
{
  "data": {
    "<Request Specifc JSON Response>"
  }
}
```

> Error Response:

```json
{
  "errors": [
    {
      "message": "Unauthorized Request"
    }
  ]
}
```

All requests are returned as JSON wrapped in a standard response format. The Schema section shows all the potential fields that we could. By convention, we do not transmit null values. Most responses will follow the Examples provided. 
