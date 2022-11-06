## Pretend we're building an issue tracker. Describe how you would design a REST API according to the following specification:

General notes before specifications:

Personally I would most likely attempt this with either a Python web framework, e.g. DRF or Flask, or I would set this up as a Golang backend service. Let's take flask as the example.

Using Flask:
Set up base models, models and Schemas for User, Bug, Comment via an SQL ORM library such as Peewee.

Schemas could be set up using marshmallow for validation on Request/Response body.
I'd set up Blueprints and then define the routes for each specified action below, e.g. function for get all bugs for example which will use the orm to select all bugs from the Db then dump them into the bug schema to be used as a response body.

Add auth to the api, e.g. add an api key. Depending on who needs this (users may need to be able to assign themselves or create bugs), you could either have authentication in the app they are using to perform these actions (E.g. login) with the login of the app gatekeeping the actions or JWT tokens could also be used.

### So in general for each way of implementing this:
1. Set up models and request / response schemas
2. Set up ORM to perform CRUD on chosen DB
3. Set up Routes of the API (that is uri's, params, methods etc for each request needed) as well as base uri, e.g. https://example-api.com
4. Add authentication to API, e.g. Headers, token, jwt token or similar.

## As an API user, I should be able to:

### Create a bug. A bug should have a title, a body, and a status (resolved/unresolved).

**Request**

`POST /bug`

```
{
    "title": "title",
    "body": "the issue is blah blah",
    "status": "unresolved"
}
```


**Response**

```
{
    "id": "00000000-0000-0000-0000-000000000000"
}
```

- 201 if successful creation
- 422 if missing a field
- 400 if malformed request

### Edit a bug.

Wasn't specified if we require to edit the whole bug or just specific fields without needing to update entire resource. Assumed specific fields in this case.

**Request**

`PATCH /bug/{id}`

```
{
    "title": "new title",
}
```


**Response**

```
{
    "id": "00000000-0000-0000-0000-000000000000"
    "title": "new title",
    "body": "the issue is blah blah",
    "status": "unresolved"
}
```

- 202 if successful
- 400 if malformed request

### Delete a bug.

**Request**

`DELETE /bug/{id}`

**Response**

- 204 if successful deletion (nothing to return)
- 404 if id doesnt exist

### View all bugs.

**Request**

`GET /bugs`

**Response**

```
[
    {
        "id": "00000000-0000-0000-0000-000000000000"
        "title": "title",
        "body": "the issue is blah blah",
        "status": "unresolved"
    },
    {
        "id": "00000000-0000-0000-0000-000000000000"
        "title": "title",
        "body": "the issue is blah blah",
        "status": "unresolved"
    }
]
```

- 200 if successful
- [] if no objects returned (still successful)

### View a specific bug.

**Request**

`GET /bug/{id}`

**Response**

```
{
    "id": "00000000-0000-0000-0000-000000000000"
    "title": "title",
    "body": "the issue is blah blah",
    "status": "unresolved"
}
```

- 200 if successful
- 404 if ID doesnt exist

### Add a comment to a bug. A comment should have a title, and a body.

Wasn't specified if required to allow multiple bugs (i.e. foreign key for Bug to comments), assumed required therefor allowing list of 'comments'

**Request**

`POST /bug/{id}/comment`

```
{
    "title": "title",
    "body": "the comment is blah blah",
}
```


**Response**

```
{
    "id": "00000000-0000-0000-0000-000000000000"
    "title": "title",
    "body": "the issue is blah blah",
    "status": "unresolved"
    "comments: [
        {
            "id": "00000000-0000-0000-0000-000000000000"
            "title": "title",
            "body": "the comment is blah blah",
        }
    ]
}
```

- 201 if successful creation
- 422 if missing a field
- 400 if malformed request

- Delete a comment from a bug.

**Request**

`DELETE /bug/{id}/comment/{id}`

**Response**

- 204 if successful deletion (nothing to return)
- 404 if eiter id doesnt exist

### Mark a bug as "resolved".

use the above endpoint for edit

`PATCH /bug/{id}`

### Mark a bug as "unresolved".

use the above endpoint for edit

`PATCH /bug/{id}`

### View all bugs marked as "resolved".

Update the Get all bugs endpoint to consider query parameters such that and any other required parameters:

`GET /bugs?status=resolved`

This will be passed as a parameter to the route which uses an ORM function to filter the query in the database.

### Assign the bug to a user. A user is identified by its ID.

Wasn't specified if we should allow multiple users or just one. Asssumed just one to one relationship in this case.

**Request**

`POST /bug/{id}/user/{user_id}`

**Response**

```
{
    "id": "00000000-0000-0000-0000-000000000000"
    "title": "title",
    "body": "the issue is blah blah",
    "status": "unresolved"
    "assigned_user_id: "00000000-0000-0000-0000-000000000000"
}
```

- 200 if successful
- 404 if an id doesnt exist.
