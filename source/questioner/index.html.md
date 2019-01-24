---
title: Questioner Documentation

language_tabs:
 - shell

search: true
---

# Introduction

## Overview

Welcome to the Questioner documentation. Questioner helps meetup organizers to prioritize questions to be answered through crowd sourcing.

You can use the Questioner API to access Questioner endpoints which are categorized into:

- Users
- Meetups
- Questions
- Comments

You can view the request and response formats in the dark area to the right.

For the remainder of this document, **hostname** refers to the server you will be using to access the API. To access the API endpoints, replace every instance of **hostname** with `http://questioner-server.herokuapp.com`

## Response codes

The response codes for the API are:

CODE | MEANING
--- | ---
200 | `OK` - The request was successful
201 | `CREATED` - The resource was successfully created
400 | `BAD REQUEST` - The request could not be processed
401 | `UNAUTHORIZED` - The authentication failed for the request
403 | `FORBIDDEN` - The request was denied
404 | `NOT FOUND` - The requested resource was not found
405 | `METHOD NOT ALLOWED` - The http method for the request is not allowed
409 | `CONFLICT` - The request could not be completed due to a conflict with an existing resource

# Authentication

This contains the Endpoints that are used for authentication in the platform

## Register

> Request body

```shell
{
	"firstname" : "Vincent",
	"lastname" : "Tirgei",
	"othername" : "",
	"username" : "vinny",
	"email" : "vin@gmail.com",
	"password" : "asfD3#sdg",
	"phonenumber" : "0712345678"
}
```

Creates an account on the platform for a new user.

### HTTP request

To create an account send a **POST** request to:

`hostname/api/v2/auth/signup`

> The response you receive should be

```shell
{
  "data": {
    "id": 3,
    "firstname": "Chris",
    "othername": null,
    "email": "doe@gmail.com",
    "lastname": "Doe",
    "registered": "2019-01-24T09:56:09.174461+00:00",
    "username": "doe",
    "phonenumber": null
  },
  "status": 201,
  "message": "User created successfully"
}
```

> If there is invalid data

```shell
{
  "errors": {
    "password": [
      "Invalid password"
    ],
    "email": [
      "Not a valid email address.",
      "Invalid email format"
    ],
    "lastname": [
      "This parameter cannot be null"
    ]
  },
  "status": 400,
  "message": "Invalid data provided"
}
```

## Login

> Request body

```shell
{
	"username" : "vinny",
	"password" : "asfD3#sdg"
}
```

Logs in a registered user into the platform

### HTTP request

To login send a **POST** request to:

`hostname/api/v2/auth/login`

> The response you receive should be

```shell
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NDgzMTY4NzQsIm5iZiI6MTU0ODMxNjg3NCwianRpIjoiNWI2ZmUwMWMtNGYwNy00NTE0LTg4OTctMTY1ZWQxY2ExYmJkIiwiZXhwIjoxNTQ4MzE3Nzc0LCJpZGVudGl0eSI6MSwiZnJlc2giOmZhbHNlLCJ0eXBlIjoiYWNjZXNzIn0.Dd64OMbfES9RJUZfx7Hwht4e2AA2JndBzzn7J1Y3Mgo",
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NDgzMTY4NzQsIm5iZiI6MTU0ODMxNjg3NCwianRpIjoiZTYxZTZiOGUtZDBkZi00ZTBmLWJkODktZTU2MmJkYjY2NDcxIiwiZXhwIjoxNTUwOTA4ODc0LCJpZGVudGl0eSI6dHJ1ZSwidHlwZSI6InJlZnJlc2gifQ.B9_nN_8DRw-MV60NxBsfIGn0R5ZKBfRSoKEEAUMAdpM",
  "user_id": 1,
  "status": 200,
  "message": "User logged in successfully"
}
```

> If the user doesn't exist

```shell
{
  "status": 404,
  "message": "User not found"
}
```

## Refresh Token

Generates a new access token for a logged in user

### HTTP request

To refresh the access token send a **POST** request to:

`hostname/api/v2/auth/refresh-token`

> The response you receive should be

```shell
{
  "status": 200,
  "message": "Token refreshed successfully",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NDgzMjU2MjMsIm5iZiI6MTU0ODMyNTYyMywianRpIjoiYjY5NGVhMTMtMjdmNy00Y2E0LWFmMTQtM2VhODYwYzlkZGM3IiwiZXhwIjoxNTQ4MzI2NTIzLCJpZGVudGl0eSI6dHJ1ZSwiZnJlc2giOmZhbHNlLCJ0eXBlIjoiYWNjZXNzIn0.sj85Ro59_cZOHfqr0r6YKHhuXY4BNoq7duIbihIGHEQ"
}
```

> If you pass an access token

```shell
{
  "message": "Only refresh tokens are allowed",
  "status": 401
}
```

### Authentication

`Authorization: Bearer refresh-token`

## Profile

Fetches a users profile

### HTTP request

To get the profile send a **GET** request to:

`hostname/api/v2/profile/<int:user_id>`

> The response you receive should be

```shell
{
  "data": {
    "id": 1,
    "questions_commented": 0,
    "firstname": "Vincent",
    "lastname": "Tirgei",
    "questions_asked": 1,
    "username": "tirgei"
  },
  "status": 200
}
```

> If the user does not exist

```shell
{
  "message": "User not found",
  "status": 404
}
```

## Logout

Logs out a user from the platform

### HTTP request

To logout send a **POST** request to:

`hostname/api/v2/auth/logout`

> The response you receive should be

```shell
{
  "status": 200,
  "message": "Logged out successfully"
}
```

> If you try to logout again

```shell
{
  "message": "Token has been revoked",
  "status": 401
}
```

### Authentication

`Authorization: Bearer access-token`

# Meetups

This contains the Endpoints for meetups in the platform

## Create Meetup

> Request body

```shell
{
	"topic" : "Python",
	"description" : "Flask API",
	"happening_on" : "27/01/2019",
	"location" : "Andela",
	"tags": ["Python", "Andela", "Flask"]
}
```

Creates an new meetup

<aside class="notice">
Only an admin user can create meetup
</aside>

### HTTP request

To create a meetup send a **POST** request to:

`hostname/api/v2/meetups`

> The response you receive should be

```shell
{
  "data": {
    "id": 2,
    "created_at": "2019-01-24T08:01:59.791241+00:00",
    "happening_on": "27/01/2019",
    "topic": "Python",
    "description": "Flask API",
    "tags": [
      "Python",
      "Andela",
      "Flask"
    ],
    "location": "Andela",
    "modified_at": "2019-01-24T08:01:59.791241+00:00"
  },
  "status": 201,
  "message": "Meetup created successfully"
}
```

> If there is invalid data

```shell
{
  "errors": {
    "location": [
      "Missing data for required field."
    ],
    "topic": [
      "This parameter cannot be null"
    ]
  },
  "status": 400,
  "message": "Invalid data provided"
}
```

> If you aren't the admin user

```shell
{
  "status": 401,
  "message": "Only admin is authorized to perform this operation"
}
```

### Authentication

`Authorization: Bearer access-token`

## All Meetups

Fetch a list of all meetups in the platform

### HTTP request

To fetch all meetups send a **GET** request to:

`hostname/api/v2/meetups`

> The response you receive should be

```shell
{
  "status": 200,
  "data": [
    {
      "id": 2,
      "created_at": "2019-01-24T08:01:59.791241+00:00",
      "happening_on": "27/01/2019",
      "topic": "Python",
      "description": "Flask API",
      "tags": [
        "Python",
        "Andela",
        "Flask"
      ],
      "location": "Andela",
      "modified_at": "2019-01-24T08:01:59.791241+00:00"
    }
  ]
}
```

> If there are no meetups

```shell
{
  "status": 200,
  "data": []
}
```

## Upcoming Meetups

Fetch a list of upcoming meetups in the platform. The upcoming meetups are meetups that are happening in the next 7 days from the time of request.

### HTTP request

To fetch upcoming meetups send a **GET** request to:

`hostname/api/v2/meetups/upcoming`

> The response you receive should be

```shell
{
  "status": 200,
  "data": [
    {
      "id": 2,
      "created_at": "2019-01-24T08:01:59.791241+00:00",
      "happening_on": "27/01/2019",
      "topic": "Python",
      "description": "Flask API",
      "tags": [
        "Python",
        "Andela",
        "Flask"
      ],
      "location": "Andela",
      "modified_at": "2019-01-24T08:01:59.791241+00:00"
    }
  ]
}
```

> If there are no meetups

```shell
{
  "status": 200,
  "data": []
}
```

## Specific Meetup

Fetch a specific meetup

### HTTP request

To fetch a specific meetup send a **GET** request to:

`hostname/api/v2/meetups/<int:meetup_id>`

> The response you receive should be

```shell
{
  "data": {
    "id": 1,
    "attendees": 0,
    "created_at": "2019-01-24T07:59:15.921493+00:00",
    "happening_on": "10/10/2019",
    "topic": "JS",
    "description": "IoS",
    "tags": [
      "Python",
      "Andela",
      "Flask"
    ],
    "location": "Andela",
    "modified_at": "2019-01-24T07:59:15.921493+00:00"
  },
  "status": 200
}
```

> If the meetup does not exist

```shell
{
  "message": "Meetup not found",
  "status": 404
}
```

## Update Meetup Tags

> Request body

```shell
{
	"tags": ["Python", "Andela", "Flask"]
}
```

Updates the tags for a meetup

<aside class="notice">
Only an admin user can update the meetup tags
</aside>

### HTTP request

To update meetup tags send a **PATCH** request to:

`hostname/api/v2/meetups<int:meetup_id>/tags`

> The response you receive should be

```shell
{
  "data": {
    "happening_on": "05/05/2020",
    "description": "Python Microframework",
    "id": 1,
    "tags": [
      "Python",
      "Flask",
      "Andela",
      "Technology"
    ],
    "created_at": "2019-01-20T22:01:09.158737+00:00",
    "topic": "Flask",
    "location": "Andela HQ",
    "modified_at": "2019-01-20T22:01:09.158737+00:00"
  },
  "status": 200,
  "message": "Meetup tags updated successfully"
}
```

> If not tags is passed

```shell
{
  "status": 400,
  "message": "You need to pass atleast 1 tag for the meetup"
}
```

> If you aren't the admin user

```shell
{
  "status": 401,
  "message": "Only admin is authorized to perform this operation"
}
```

### Authentication

`Authorization: Bearer access-token`

## RSVP

Allows a user to rsvp to an upcoming meetup.

<aside class="notice">
The available options are yes, no and maybe
</aside>

### HTTP request

To rsvp to a meetup send a **POST** request to:

`hostname/api/v2/meetups/<int:meetup_id>/<string:rsvp>`

> The response you receive should be

```shell
{
  "data": {
    "response": "yes",
    "modified_at": "2019-01-24T11:02:39.962365+00:00",
    "user_id": 1,
    "created_at": "2019-01-24T11:02:39.962365+00:00",
    "meetup_id": 3
  },
  "status": 200,
  "message": "Meetup rsvp successfully"
}
```

> If an invalid rsvp is passed

```shell
{
  "status": 400,
  "message": "Invalid rsvp"
}
```

> If you had already responded to the meetup

```shell
{
  "status": 403,
  "message": "Meetup already responded"
}
```

### Authentication

`Authorization: Bearer access-token`

## Meetup Attendees

Fetch a list of users who are attending a meetup

### HTTP request

To fetch all meetups send a **GET** request to:

`hostname/api/v2/meetups/<int:meetup_id>/attendees`

> The response you receive should be

```shell
{
  "attendees": 1,
  "users": [
    {
      "firstname": "Vincent",
      "id": 1,
      "username": "tirgei",
      "lastname": "Tirgei"
    }
  ],
  "status": 200
}
```

> If there are no attendees

```shell
{
  "attendees": 0,
  "users": [],
  "status": 200
}
```

## Delete Meetup

Delete a new meetup

<aside class="notice">
Only an admin user can delete a meetup
</aside>

### HTTP request

To delete a meetup send a **DELETE** request to:

`hostname/api/v2/meetups/<int:meetup_id>`

> The response you receive should be

```shell
{
  "status": 200,
  "message": "Meetup deleted successfully"
}
```

> If the meetup does not exists

```shell
{
  "status": 404,
  "message": "Meetup not found"
}
```

> If you aren't the admin user

```shell
{
  "status": 401,
  "message": "Only admin is authorized to perform this operation"
}
```

### Authentication

`Authorization: Bearer access-token`

# Questions

This contains the Endpoints for Questions for meetups

## Post Question

> Request body

```shell
{
	"title" : "JS",
	"body" : "TDD",
	"meetup_id" : 3
}
```

Posts a new question to a meetup

### HTTP request

To post a question send a **POST** request to:

`hostname/api/v2/questions`

> The response you receive should be

```shell
{
  "data": {
    "meetup_id": 3,
    "body": "TDD",
    "title": "JS",
    "created_at": "2019-01-24T19:15:45.809437+00:00",
    "id": 6,
    "votes": 0,
    "modified_at": "2019-01-24T19:15:45.809437+00:00",
    "user_id": 4
  },
  "status": 201,
  "message": "Question posted successfully"
}
```

> If there is invalid data

```shell
{
  "errors": {
    "body": [
      "Missing data for required field."
    ]
  },
  "status": 400,
  "message": "Invalid data provided"
}
```

### Authentication

`Authorization: Bearer access-token`

## Fetch Questions

Fetch all questions posted for a specific meetup

### HTTP request

To fetch meetup questions send a **GET** request to

`hostname/api/v2/meetups/<int:meetup_id>/questions`

> The response you receive should be

```shell
{
  "data": [
    {
      "meetup_id": 3,
      "body": "Python for beginners?",
      "title": "Coding",
      "created_at": "2019-01-24T12:44:36.437292+00:00",
      "id": 2,
      "votes": 0,
      "modified_at": "2019-01-24T12:44:36.437292+00:00",
      "user_id": 1
    },
    {
      "meetup_id": 3,
      "body": "TDD",
      "title": "JS",
      "created_at": "2019-01-24T12:45:22.672153+00:00",
      "id": 3,
      "votes": 0,
      "modified_at": "2019-01-24T12:45:22.672153+00:00",
      "user_id": 1
    }
  ],
  "status": 200
}
```

> If there are no questions posted for the meetup

```shell
{
  "data": [],
  "status": 200
}
```

> If the meetup does not exist
```shell
{
  "status": 404,
  "message": "Meetup not found"
}
```

## Upvote Question

Upvotes a meetup question

### HTTP request

To upvote a question, send a **PATCH** request to:

`hostname/api/v2/questions/<int:question_id>/upvote`

> The response you receive should be

```shell
{
  "data": {
    "meetup_id": 3,
    "body": "TDD",
    "title": "JS",
    "created_at": "2019-01-24T13:38:58.716166+00:00",
    "id": 5,
    "votes": 1,
    "modified_at": "2019-01-24T13:38:58.716166+00:00",
    "user_id": 4
  },
  "status": 200,
  "message": "Question upvoted successfully"
}
```

> If you have already upvoted the question

```shell
{
  "status": 403,
  "message": "You have already voted for this question"
}
```

### Authentication

`Authorization: Bearer access-token`

## Downvote Question

Downvotes a meetup question

### HTTP request

To downvote a question, send a **PATCH** request to:

`hostname/api/v2/questions/<int:question_id>/downvote`

> The response you receive should be

```shell
{
  "data": {
    "meetup_id": 3,
    "body": "TDD",
    "title": "JS",
    "created_at": "2019-01-24T12:48:01.605004+00:00",
    "id": 4,
    "votes": -1,
    "modified_at": "2019-01-24T12:48:01.605004+00:00",
    "user_id": 4
  },
  "status": 200,
  "message": "Question downvoted successfully"
}
```

> If you have already downvoted the question

```shell
{
  "status": 403,
  "message": "You have already voted for this question"
}
```

### Authentication

`Authorization: Bearer access-token`

# Comments

This contains the Endpoints for Comments to meetup questions

## Post Comment

> Request body

```shell
{
	"body" : "What time is the meetup?"
}
```

Posts a new comment to a meetup question

### HTTP request

To post a comment send a **POST** request to:

`hostname/api/v2/questions/int:question_id>/comments`

> The response you receive should be

```shell
{
  "data": {
    "created_at": "2019-01-24T12:47:51.439767+00:00",
    "id": 1,
    "question_id": 3,
    "body": "What time is the meetup?",
    "user_id": 4,
    "modified_at": "2019-01-24T12:47:51.439767+00:00"
  },
  "status": 201,
  "message": "Comment posted successfully"
}
```

> If the question does not exist

```shell
{
  "message": "Token has expired",
  "status": 401
}
```

### Authentication

`Authorization: Bearer access-token`

## Fetch Comments

Fetch all comments posted for a specific meetup question

### HTTP request

To fetch comments for a meetup question send a **GET** request to:

`hostname/api/v2/questions/<int:question_id>/comments`

> The response you receive should be

```shell
{
  "data": [
    {
      "created_at": "2019-01-24T12:47:51.439767+00:00",
      "id": 1,
      "question_id": 3,
      "body": "What time is the meetup?",
      "user_id": 4,
      "modified_at": "2019-01-24T12:47:51.439767+00:00"
    }
  ],
  "status": 200
}
```

> If there are no comments posted for the meetup question

```shell
{
  "data": [],
  "status": 200
}
```

> If the meetup question does not exist
```shell
{
  "status": 404,
  "message": "Question not found"
}
```