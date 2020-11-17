# User Management API

## Get User

> Get User Response

```json
{
  "data": {
    "id": "test_user_id",
    "name": "Test USer",
    "email": "example@evocalize.com",
    "groups": [
      {
        "id": "seattle_office",
        "name": "Seattle Office",
        "role": "group_admin"
      },
      // ...
    ]
  }
}
```

Returns a single user and associated groups. 

### HTTP Request
```GET management/v1/user/{{ user_id }}```

### Request Path Params

Path Param | Required | Description
---------- | -------- | -----------
user_id | true | The ID of the user you are wanting to retrieve


__Response Codes__: 
  
- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the user.

 


## Get Users

> Get Users Response

```json
{
  "data": [
   {
      "id": "1234",
      "name": "Test User 1",
      "email": "test1@evocalize.com"
    },
    {
      "id": "5678",
      "name": "Test User 2",
      "email": "test2@evocalize.com"
    },
    {
      "id": "91011",
      "name": "Test User 2",
      "email": "test3@evocalize.com"
    },
    {
      "id": "1213",
      "name": "Test User 3",
      "email": "test3@evocalize.com"
    }
  ],
  "nextPageToken": "longtokenstringgoeshere", // only present when there are more pages
  "previousPageToken": "differenttokenstringgoeshere" // only present when 
}
```

Returns all users. This is a paginated response - returning 100 results per page.  

### HTTP Request
```GET management/v1/users?pageToken={{pageTokenHere}}```


### Request Params

Param | Required | Description
----- | -------- | -----------
pageToken | false | The page token (taken from either `nextPageToken`, or `previousPageToken` provided in the response) for the page you of results you want to view.


__Response Codes__: 
  
- `200 OK` - NOTE: will return an empty array and this status code if there are no users present.


## Create Or Update User

> Create Request Example

```json
{
	"id": "String", // Required
	"email": "String", // Required
	"name": "String", // Required
    // Groups is an optional property
	"groups": [
	  {
	   "userId": "String", 
		"groupId": "String",
		"role": "String"
	  },
    // ... 
	]
}
```

 > Update Request Example
 
```json
{
	"id": "String", 
	"email": "user@example.com", // Optional
	"name": "Test User", // Optional
  "replaceGroups": true/false // Optional, defaults to false
  // Groups is an optional property
	"groups": [
		{
			"userId": "56468", 
			"groupId": "8675309",
			"role": "group_admin"
		},
    // ... 
	]

}
```

> Create and Update Repsonse

```json
{
	"id":"test_user_id", 
	"email":"user@example.com", 
	"name":"Test User", 
	"groups": [
		{
			"userId": "56468", 
			"groupId": "8675309",
			"role": "group_admin"
		},
    // ... 
	]
}
```

This endpoint allows you to create or update a user and their group associations. You are able to call this endpoint and omit the groups property. In Create case the user would be created, but not be associated to any groups. In the case of update we would only update their name and or email.

When `completedGroups` is true, we will REMOVE all groups currently associated to the user, replacing them with the groups provided in the list. 

The group you want to associate with the user MUST exist prior to making this call.

### HTTP Request
`POST management/v1/user/`

__Valid group roles__: 

 - group_user
 - group_admin

__Response Codes__: 
  
 - `201 CREATED` - Returning the newly created / updated user.
 - `400 BAD REQUEST` - When the request is malformed or you attempt to associate to a non existant group.

## Create Or Update User Batch

 > Batch Create Request

``` json
[
  {
    "id": "String", // Required
    "email": "String", // Required
    "name": "String", // Required
      // Groups is an optional property
    "groups": [
      {
      "userId": "String", 
      "groupId": "String",
      "role": "String"
      },
        // ... 
    ]
  },
  // ...
]
```

> Batch Update Request

```json
[
  {
    "id":"String", 
    "email":"user@example.com", // Optional
    "name":"Test User", // Optional
      "replaceGroups": true/false // Optional, defaults to false
      // Groups is an optional property
    "groups": [
      {
        "userId": "56468", 
        "groupId": "8675309",
        "role": "group_admin"
      },
          // ... 
    ]
  },
  // ...
]
```

> Create and Update Batch Repsonse

```json
{
  "data": {
    "reportId": "HG3SrEndQch8dAZbjwBV"
  }
}
```

Endpoint that allows you to pass a JSON Array of User Request objects for creation or updating. Batches are limited to 1000 items at a time. The creation option is asynchronous - rather than returning the newly created items, you will receive a `report_id` which can be used to track status of the requests as they are processed in our system. Reports are stored for 30 days. 

### HTTP Request
```POST management/v1/users/```

__Response Codes__:

- `202 ACCEPTED` - List has been received and submitted for processing.
- `400 BAD REQUEST` - Malformed request.

## Deactivate User

> Deactivate User Response

```
No Body returned
```

Deactivates all active programs for a given user placing them in an inactive state.

### HTTP Request
```DELETE management/v1/user/{{ userId }}```

__Response Codes__:

 - `202 ACCEPTED`

# Group Management API

## Get Groups

> Get Groups Response

```json
{
  "data": [
   {
      "id": "1234",
      "name": "Test Group 1",
    },
    {
      "id": "5678",
      "name": "Test Group 2",
    },
    {
      "id": "91011",
      "name": "Test Group 3",
    },
    {
      "id": "1213",
      "name": "Test Group 4",
    }
  ],
  "nextPageToken": "longtokenstringgoeshere", // only present when there are more pages
  "previousPageToken": "differenttokenstringgoeshere" // only present when 
}
```

Returns all groups. This is a paginated response - returning 100 results per page.  

### HTTP Request
`GET management/v1/groups?pageToken={{pageTokenHere}}`

### Request Params

Param | Required | Description
----- | -------- | -----------
pageToken | false | The page token (taken from either `nextPageToken`, or `previousPageToken` provided in the response) for the page you of results you want to view.

__Response Codes__: 
  
- `200 OK` NOTE: will return an empty array and this status code if there are no groups present.

## Create Or Update Group

> Group Create/Update Request

```json
{
 "id": "String", 
 "name": "String",	
}
```

> Group Create/Update Response

```json 
{
  "data": {
    "id": "123",
    "name": "Test Group 1"
  }
}
```
### HTTP Request
```POST management/v1/group/```

__Response Codes__: 

- `201 CREATED`

## Create Or Update Group Batch

> Group Create Request

```json
[
	{
	 "id": "String", 
	 "name": "String",	
	},
  // ...
]
```

> Group Create Response

```json
{
  "data": {
    "reportId": "HG3SrEndQch8dAZbjwBV"
  }
}
```

Endpoint that allows you to pass a JSON Array of Group Request objects for creation. Batches are limited to 1000 items at a time. The creation option is asynchronous - rather than returning the newly created items, you will receive a `report_id` which can be used to track status of the requests as they are processed in our system. Reports are stored for 30 days. 

### HTTP Request
`POST management/v1/groups/`

__Response Codes__:

- `202 ACCEPTED` - List has been received and submitted for processing.
- `400 BAD REQUEST` - Malformed request.


# Batch Report API

## Get Report

> Get Report Response

```json
{
  "data": {
    "totalItems": 750,
    "remainingItems": 500,
    "completedItems": 250,
    "successfulItems": 250,
    "errorItems": 0,
    "isCompleted": false
  }
}
```

For batch endpoints we return a `report_id` string instead of the created objects. This endpoint allows you to view the status of a given batch. We retain this data for 30 days.

### HTTP Request
`GET management/v1/items/report/{{ reportId }}`

__Response Codes__:

`200 OK`
`404 NOT FOUND` - When we are unable to locate a report with that ID.

