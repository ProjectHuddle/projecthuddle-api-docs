---
title: ProjectHuddle Rest API

language_tabs:
  - javascript

toc_footers:
  - <a href='http://projecthuddle.io'>About ProjectHuddle</a>

includes:
  - errors

search: true

logo:
  link: https://projecthuddle.io
  target: _blank
---

# Introduction

**Version:** 2.0

ProjectHuddle 3.4+ is fully integrated with the WordPress REST API. This allows ProjectHuddle data to be created, read, updated, and deleted using requests in JSON format and using WordPress REST API Authentication methods and standard HTTP verbs which are understood by most HTTP clients.

## Requirements

To use the latest version of the REST API you must be using:

- ProjectHuddle 3.4+.
- WordPress 4.7+.
- Pretty permalinks in **Settings > Permalinks** so that the custom endpoints are supported. Default permalinks will not work.
- Your site must be served over HTTPS.
- Your server must be set up to allow for the Authentication header.

# Authentication

## **Obtaining an API Key and API Secret**

You'll need to get an API and Secret key from your ProjectHuddle plugin WordPress installation in order to securely communicate with the REST API. To do this, login to your WordPress installation and navigate to your **Users > Your Profile**. You should see a ProjectHuddle API section. Type a description, then click "Add New". You can download your API Key and API Secret for future reference.

## **Generating a JSON Web Token**

ProjectHuddle uses JSON Web Token (JWT) to securely authenticate a valid user requesting access to ProjectHuddle REST API resources. JSON Web Tokens are an open, industry standard [RFC 7519](https://tools.ietf.org/html/rfc7519) method for representing claims securely between two parties.

### HTTP Request

`POST /projecthuddle/v2/token`

```javascript
fetch("https://example.org/wp-json/projecthuddle/v2/token", {
  method: "POST",
  body: {
    api_key: "12345ascde",
    api_secret: "54321edcba"
  },
  headers: {
    "Content-Type": "application/json"
  }
})
  .then(res => res.json())
  .then(response => console.log("Success:", JSON.stringify(response)))
  .catch(error => console.error("Error:", error));
```

> Example of JSON posted with the API Keys

```json
{
  "access_token": "YOUR_ACCESS_TOKEN",
  "data": {
    "user": {
      "id": 1,
      "type": "wp_user",
      "user_login": "admin",
      "user_email": "admin@sample.org",
      "api_key": "12345ascde"
    }
  },
  "exp": 604800,
  "refresh_token": "YOUR_REFRESH_TOKEN"
}
```

**Parameters**

| Name       | Located in | Description                                                  | Required | Type   |
| ---------- | ---------- | ------------------------------------------------------------ | -------- | ------ |
| api_key    | formData   | API Key, generated from your WordPress user profile page.    | Yes      | string |
| api_secret | formData   | API Secret, generated from your WordPress user profile page. | Yes      | string |

## **Authenticated Requests**

```javascript
fetch("https://example.org/wp-json/projecthuddle/v2/mockup/1", {
  method: "GET",
  headers: {
    Authorization: "Bearer YOUR_ACCESS_TOKEN"
  }
})
  .then(res => res.json())
  .then(response => console.log("Success:", JSON.stringify(response)))
  .catch(error => console.error("Error:", error));
```

The access_token field is what you'll use for subsequent requests, with `Bearer: YOUR_ACCESS_TOKEN`.

## **Refresh Token**

The refresh_token field is a special kind of token that can be used to obtain a renewed access token when it finally expires. By default, access tokens expire after 7 days.

> Obtain a new access token using the refresh token

```javascript
fetch("https://example.org/wp-json/projecthuddle/v2/token", {
  method: "POST",
  body: {
    refresh_token: "YOUR_REFRESH_TOKEN"
  }
})
  .then(res => res.json())
  .then(response => console.log("Success:", JSON.stringify(response)))
  .catch(error => console.error("Error:", error));
```

```json
{
  "access_token": "YOUR_ACCESS_TOKEN",
  "data": {
    "user": {
      "id": 1,
      "type": "wp_user",
      "user_login": "admin",
      "user_email": "admin@sample.org",
      "api_key": "12345ascde"
    }
  },
  "exp": 604800,
  "refresh_token": "YOUR_REFRESH_TOKEN"
}
```

## **Checking Expiration**

You can also check if the token is still valid and when it expires using the `token/validate` endpoint.

> Checking Expiration

```javascript
fetch("https://example.org/wp-json/projecthuddle/v2/token/validate", {
  method: "POST",
  headers: {
    Authorization: "Bearer YOUR_ACCESS_TOKEN"
  }
})
  .then(res => res.json())
  .then(response => console.log("Success:", JSON.stringify(response)))
  .catch(error => console.error("Error:", error));
```

```json
{
  "code": "rest_authentication_valid_access_token",
  "message": "Valid access token.",
  "data": {
    "status": 200,
    "exp": 604800
  }
}
```

## **Troubleshooting**

Most of the shared hosting has disabled the HTTP Authorization Header by default. To enable this option you’ll need to edit your .htaccess file adding the following:

`RewriteEngine on` <br>
`RewriteCond %{HTTP:Authorization} ^(.*)` <br>
`RewriteRule ^(.*) - [E=HTTP_AUTHORIZATION:%1]`

### WPEngine

To enable the Authorization header option you’ll need to edit your .htaccess file adding the following:

`SetEnvIf Authorization "(.*)" HTTP_AUTHORIZATION=$1`

# Users

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/users`

```javascript
fetch('https://example.org/wp-json/projecthuddle/v2/users', {
  method: "GET",
  headers: {
    "Content-Type": "application/json"
  }
})
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
[
  {
    "id": 19,
    "name": "John",
    "url": "",
    "description": "",
    "link": "https://example.org/author/john/",
    "slug": "john",
    "user_role": "Project Client",
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users/19"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
  {
    "id": 20,
    "name": "Sam",
    "url": "",
    "description": "",
    "link": "https://example.org/author/sam/",
    "slug": "sam",
    "user_role": "Project Client",
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/9eeca2d699a8dfdcd5c5ac9d1cee6e3f?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/9eeca2d699a8dfdcd5c5ac9d1cee6e3f?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/9eeca2d699a8dfdcd5c5ac9d1cee6e3f?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users/20"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
]
```

**Parameters**

| Name     | Located in | Description                                                                                              | Required | Type             |
| -------- | ---------- | -------------------------------------------------------------------------------------------------------- | -------- | ---------------- |
| context  | query      | Scope under which the request is made; determines fields present in response.                            | No       | string           |
| page     | query      | Current page of the collection.                                                                          | No       | integer (number) |
| per_page | query      | Maximum number of items to be returned in result set.                                                    | No       | integer (number) |
| search   | query      | Limit results to those matching a string.                                                                | No       | string           |
| exclude  | query      | Ensure result set excludes specific IDs.                                                                 | No       | array            |
| include  | query      | Limit result set to specific IDs.                                                                        | No       | array            |
| offset   | query      | Offset the result set by a specific number of items.                                                     | No       | integer          |
| order    | query      | Order sort attribute ascending or descending.                                                            | No       | string           |
| orderby  | query      | Sort collection by object attribute.                                                                     | No       | string           |
| slug     | query      | Limit result set to users with one or more specific slugs.                                               | No       | array            |
| roles    | query      | Limit result set to users matching at least one specific role provided. Accepts csv list or single role. | No       | array            |
| project  | query      | Limit result set to users who are members of a project.                                                  | No       | integer          |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/users`

**Parameters**

| Name        | Located in | Description                              | Required | Type           |
| ----------- | ---------- | ---------------------------------------- | -------- | -------------- |
| username    | formData   | Login name for the user.                 | No       | string         |
| name        | formData   | Display name for the user.               | No       | string         |
| first_name  | formData   | First name for the user.                 | No       | string         |
| last_name   | formData   | Last name for the user.                  | No       | string         |
| email       | formData   | The email address for the user.          | Yes      | string (email) |
| url         | formData   | URL of the user.                         | No       | string (uri)   |
| description | formData   | Description of the user.                 | No       | string         |
| locale      | formData   | Locale for the user.                     | No       | string         |
| nickname    | formData   | The nickname for the user.               | No       | string         |
| slug        | formData   | An alphanumeric identifier for the user. | No       | string         |
| roles       | formData   | Roles assigned to the user.              | No       | array          |
| password    | formData   | Password for the user (never included).  | No       | string         |
| meta        | formData   | Meta fields.                             | No       | string         |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# User

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/users/{id}`

**Parameters**

| Name    | Located in | Description                                                                   | Required | Type    |
| ------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| id      | path       |                                                                               | Yes      | string  |
| id      | query      | Unique identifier for the user.                                               | No       | integer |
| context | query      | Scope under which the request is made; determines fields present in response. | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_PUT/PATCH_**

### HTTP Request

`PUT/PATCH /projecthuddle/v2/users/{id}`

**Parameters**

| Name        | Located in | Description                              | Required | Type           |
| ----------- | ---------- | ---------------------------------------- | -------- | -------------- |
| id          | path       |                                          | Yes      | string         |
| id          | formData   | Unique identifier for the user.          | No       | integer        |
| username    | formData   | Login name for the user.                 | No       | string         |
| name        | formData   | Display name for the user.               | No       | string         |
| first_name  | formData   | First name for the user.                 | No       | string         |
| last_name   | formData   | Last name for the user.                  | No       | string         |
| email       | formData   | The email address for the user.          | No       | string (email) |
| url         | formData   | URL of the user.                         | No       | string (uri)   |
| description | formData   | Description of the user.                 | No       | string         |
| locale      | formData   | Locale for the user.                     | No       | string         |
| nickname    | formData   | The nickname for the user.               | No       | string         |
| slug        | formData   | An alphanumeric identifier for the user. | No       | string         |
| roles       | formData   | Roles assigned to the user.              | No       | array          |
| password    | formData   | Password for the user (never included).  | No       | string         |
| meta        | formData   | Meta fields.                             | No       | string         |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/users/{id}`

**Parameters**

| Name     | Located in | Description                                                  | Required | Type    |
| -------- | ---------- | ------------------------------------------------------------ | -------- | ------- |
| id       | path       |                                                              | Yes      | string  |
| id       | query      | Unique identifier for the user.                              | No       | integer |
| force    | query      | Required to be true, as users do not support trashing.       | No       | boolean |
| reassign | query      | Reassign the deleted user's posts and links to this user ID. | Yes      | integer |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Me

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/users/me`

**Parameters**

| Name    | Located in | Description                                                                   | Required | Type   |
| ------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------ |
| context | query      | Scope under which the request is made; determines fields present in response. | No       | string |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/users/me`

**Parameters**

| Name        | Located in | Description                              | Required | Type           |
| ----------- | ---------- | ---------------------------------------- | -------- | -------------- |
| username    | formData   | Login name for the user.                 | No       | string         |
| name        | formData   | Display name for the user.               | No       | string         |
| first_name  | formData   | First name for the user.                 | No       | string         |
| last_name   | formData   | Last name for the user.                  | No       | string         |
| email       | formData   | The email address for the user.          | No       | string (email) |
| url         | formData   | URL of the user.                         | No       | string (uri)   |
| description | formData   | Description of the user.                 | No       | string         |
| locale      | formData   | Locale for the user.                     | No       | string         |
| nickname    | formData   | The nickname for the user.               | No       | string         |
| slug        | formData   | An alphanumeric identifier for the user. | No       | string         |
| roles       | formData   | Roles assigned to the user.              | No       | array          |
| password    | formData   | Password for the user (never included).  | No       | string         |
| meta        | formData   | Meta fields.                             | No       | string         |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/users/me`

**Parameters**

| Name     | Located in | Description                                                  | Required | Type    |
| -------- | ---------- | ------------------------------------------------------------ | -------- | ------- |
| force    | query      | Required to be true, as users do not support trashing.       | No       | boolean |
| reassign | query      | Reassign the deleted user's posts and links to this user ID. | Yes      | integer |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# All Media

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/media`

**Parameters**

| Name           | Located in | Description                                                                   | Required | Type             |
| -------------- | ---------- | ----------------------------------------------------------------------------- | -------- | ---------------- |
| context        | query      | Scope under which the request is made; determines fields present in response. | No       | string           |
| page           | query      | Current page of the collection.                                               | No       | integer (number) |
| per_page       | query      | Maximum number of items to be returned in result set.                         | No       | integer (number) |
| search         | query      | Limit results to those matching a string.                                     | No       | string           |
| after          | query      | Limit response to posts published after a given ISO8601 compliant date.       | No       | dateTime         |
| author         | query      | Limit result set to posts assigned to specific authors.                       | No       | array            |
| author_exclude | query      | Ensure result set excludes posts assigned to specific authors.                | No       | array            |
| before         | query      | Limit response to posts published before a given ISO8601 compliant date.      | No       | dateTime         |
| exclude        | query      | Ensure result set excludes specific IDs.                                      | No       | array            |
| include        | query      | Limit result set to specific IDs.                                             | No       | array            |
| offset         | query      | Offset the result set by a specific number of items.                          | No       | integer          |
| order          | query      | Order sort attribute ascending or descending.                                 | No       | string           |
| orderby        | query      | Sort collection by object attribute.                                          | No       | string           |
| parent         | query      | Limit result set to items with particular parent IDs.                         | No       | array            |
| parent_exclude | query      | Limit result set to all items except those of a particular parent ID.         | No       | array            |
| slug           | query      | Limit result set to posts with one or more specific slugs.                    | No       | array            |
| status         | query      | Limit result set to posts assigned one or more statuses.                      | No       | array            |
| parent_id      | query      | Limit result set to items that have a parent model.                           | No       | integer          |
| project        | query      | Limit result set to comments of specific project IDs.                         | No       | array            |
| media_type     | query      | Limit result set to attachments of a particular media type.                   | No       | string           |
| mime_type      | query      | Limit result set to attachments of a particular MIME type.                    | No       | string           |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/media`

**Parameters**

| Name           | Located in | Description                                                   | Required | Type     |
| -------------- | ---------- | ------------------------------------------------------------- | -------- | -------- |
| date           | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt       | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug           | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status         | formData   | A named status for the object.                                | No       | string   |
| title          | formData   | The title for the object.                                     | No       | string   |
| author         | formData   | The ID for the author of the object.                          | No       | integer  |
| comment_status | formData   | Whether or not comments are open on the object.               | No       | string   |
| ping_status    | formData   | Whether or not the object can be pinged.                      | No       | string   |
| meta           | formData   | Meta fields.                                                  | No       | string   |
| alt_text       | formData   | Alternative text to display when attachment is not displayed. | No       | string   |
| caption        | formData   | The attachment caption.                                       | No       | string   |
| description    | formData   | The attachment description.                                   | No       | string   |
| post           | formData   | The ID for the associated post of the attachment.             | No       | integer  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# Single Media

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/media/{id}`

**Parameters**

| Name    | Located in | Description                                                                   | Required | Type    |
| ------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| id      | path       |                                                                               | Yes      | string  |
| id      | query      | Unique identifier for the object.                                             | No       | integer |
| context | query      | Scope under which the request is made; determines fields present in response. | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/media/{id}`

**Parameters**

| Name           | Located in | Description                                                   | Required | Type     |
| -------------- | ---------- | ------------------------------------------------------------- | -------- | -------- |
| id             | path       |                                                               | Yes      | string   |
| id             | formData   | Unique identifier for the object.                             | No       | integer  |
| date           | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt       | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug           | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status         | formData   | A named status for the object.                                | No       | string   |
| title          | formData   | The title for the object.                                     | No       | string   |
| author         | formData   | The ID for the author of the object.                          | No       | integer  |
| comment_status | formData   | Whether or not comments are open on the object.               | No       | string   |
| ping_status    | formData   | Whether or not the object can be pinged.                      | No       | string   |
| meta           | formData   | Meta fields.                                                  | No       | string   |
| alt_text       | formData   | Alternative text to display when attachment is not displayed. | No       | string   |
| caption        | formData   | The attachment caption.                                       | No       | string   |
| description    | formData   | The attachment description.                                   | No       | string   |
| post           | formData   | The ID for the associated post of the attachment.             | No       | integer  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/media/{id}`

**Parameters**

| Name  | Located in | Description                                 | Required | Type    |
| ----- | ---------- | ------------------------------------------- | -------- | ------- |
| id    | path       |                                             | Yes      | string  |
| id    | query      | Unique identifier for the object.           | No       | integer |
| force | query      | Whether to bypass trash and force deletion. | No       | boolean |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Comments

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/comments`

**Parameters**

| Name           | Located in | Description                                                                                | Required | Type             |
| -------------- | ---------- | ------------------------------------------------------------------------------------------ | -------- | ---------------- |
| context        | query      | Scope under which the request is made; determines fields present in response.              | No       | string           |
| page           | query      | Current page of the collection.                                                            | No       | integer (number) |
| per_page       | query      | Maximum number of items to be returned in result set.                                      | No       | integer (number) |
| search         | query      | Limit results to those matching a string.                                                  | No       | string           |
| after          | query      | Limit response to comments published after a given ISO8601 compliant date.                 | No       | dateTime         |
| author         | query      | Limit result set to comments assigned to specific user IDs. Requires authorization.        | No       | array            |
| author_exclude | query      | Ensure result set excludes comments assigned to specific user IDs. Requires authorization. | No       | array            |
| author_email   | query      | Limit result set to that from a specific author email. Requires authorization.             | No       | string (email)   |
| before         | query      | Limit response to comments published before a given ISO8601 compliant date.                | No       | dateTime         |
| exclude        | query      | Ensure result set excludes specific IDs.                                                   | No       | array            |
| include        | query      | Limit result set to specific IDs.                                                          | No       | array            |
| offset         | query      | Offset the result set by a specific number of items.                                       | No       | integer          |
| order          | query      | Order sort attribute ascending or descending.                                              | No       | string           |
| orderby        | query      | Sort collection by object attribute.                                                       | No       | string           |
| parent         | query      | Limit result set to comments of specific parent IDs.                                       | No       | array            |
| project        | query      | Limit result set to comments of specific project IDs.                                      | No       | array            |
| item           | query      | Limit result set to comments of specific item IDs.                                         | No       | array            |
| parent_exclude | query      | Ensure result set excludes specific parent IDs.                                            | No       | array            |
| post           | query      | Limit result set to comments assigned to specific post IDs.                                | No       | array            |
| status         | query      | Limit result set to comments assigned a specific status. Requires authorization.           | No       | string           |
| type           | query      | Limit result set to comments assigned a specific type. Requires authorization.             | No       | string           |
| password       | query      | The password for the post if it is password protected.                                     | No       | string           |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/comments`

**Parameters**

| Name              | Located in | Description                                                | Required | Type           |
| ----------------- | ---------- | ---------------------------------------------------------- | -------- | -------------- |
| author            | formData   | The ID of the user object, if author was a user.           | No       | integer        |
| author_email      | formData   | Email address for the object author.                       | No       | string (email) |
| author_ip         | formData   | IP address for the object author.                          | No       | string (ip)    |
| author_name       | formData   | Display name for the object author.                        | No       | string         |
| author_url        | formData   | URL for the object author.                                 | No       | string (uri)   |
| author_user_agent | formData   | User agent for the object author.                          | No       | string         |
| content           | formData   | The content for the object.                                | No       | string         |
| date              | formData   | The date the object was published, in the site's timezone. | No       | dateTime       |
| date_gmt          | formData   | The date the object was published, as GMT.                 | No       | dateTime       |
| parent            | formData   | The ID for the parent of the object.                       | No       | integer        |
| post              | formData   | The ID of the associated post object.                      | No       | integer        |
| status            | formData   | State of the object.                                       | No       | string         |
| meta              | formData   | Meta fields.                                               | No       | string         |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# Comment

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/comments/{id}`

**Parameters**

| Name     | Located in | Description                                                                          | Required | Type    |
| -------- | ---------- | ------------------------------------------------------------------------------------ | -------- | ------- |
| id       | path       |                                                                                      | Yes      | string  |
| id       | query      | Unique identifier for the object.                                                    | No       | integer |
| context  | query      | Scope under which the request is made; determines fields present in response.        | No       | string  |
| password | query      | The password for the parent post of the comment (if the post is password protected). | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/comments/{id}`

**Parameters**

| Name              | Located in | Description                                                | Required | Type           |
| ----------------- | ---------- | ---------------------------------------------------------- | -------- | -------------- |
| id                | path       |                                                            | Yes      | string         |
| id                | formData   | Unique identifier for the object.                          | No       | integer        |
| author            | formData   | The ID of the user object, if author was a user.           | No       | integer        |
| author_email      | formData   | Email address for the object author.                       | No       | string (email) |
| author_ip         | formData   | IP address for the object author.                          | No       | string (ip)    |
| author_name       | formData   | Display name for the object author.                        | No       | string         |
| author_url        | formData   | URL for the object author.                                 | No       | string (uri)   |
| author_user_agent | formData   | User agent for the object author.                          | No       | string         |
| content           | formData   | The content for the object.                                | No       | string         |
| date              | formData   | The date the object was published, in the site's timezone. | No       | dateTime       |
| date_gmt          | formData   | The date the object was published, as GMT.                 | No       | dateTime       |
| parent            | formData   | The ID for the parent of the object.                       | No       | integer        |
| post              | formData   | The ID of the associated post object.                      | No       | integer        |
| status            | formData   | State of the object.                                       | No       | string         |
| meta              | formData   | Meta fields.                                               | No       | string         |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/comments/{id}`

**Parameters**

| Name     | Located in | Description                                                                          | Required | Type    |
| -------- | ---------- | ------------------------------------------------------------------------------------ | -------- | ------- |
| id       | path       |                                                                                      | Yes      | string  |
| id       | query      | Unique identifier for the object.                                                    | No       | integer |
| force    | query      | Whether to bypass trash and force deletion.                                          | No       | boolean |
| password | query      | The password for the parent post of the comment (if the post is password protected). | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Mockups

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/mockup`

**Parameters**

| Name           | Located in | Description                                                                   | Required | Type             |
| -------------- | ---------- | ----------------------------------------------------------------------------- | -------- | ---------------- |
| context        | query      | Scope under which the request is made; determines fields present in response. | No       | string           |
| page           | query      | Current page of the collection.                                               | No       | integer (number) |
| per_page       | query      | Maximum number of items to be returned in result set.                         | No       | integer (number) |
| search         | query      | Limit results to those matching a string.                                     | No       | string           |
| after          | query      | Limit response to posts published after a given ISO8601 compliant date.       | No       | dateTime         |
| author         | query      | Limit result set to posts assigned to specific authors.                       | No       | array            |
| author_exclude | query      | Ensure result set excludes posts assigned to specific authors.                | No       | array            |
| before         | query      | Limit response to posts published before a given ISO8601 compliant date.      | No       | dateTime         |
| exclude        | query      | Ensure result set excludes specific IDs.                                      | No       | array            |
| include        | query      | Limit result set to specific IDs.                                             | No       | array            |
| offset         | query      | Offset the result set by a specific number of items.                          | No       | integer          |
| order          | query      | Order sort attribute ascending or descending.                                 | No       | string           |
| orderby        | query      | Sort collection by object attribute.                                          | No       | string           |
| slug           | query      | Limit result set to posts with one or more specific slugs.                    | No       | array            |
| status         | query      | Limit result set to posts assigned one or more statuses.                      | No       | array            |
| parent_id      | query      | Limit result set to items that have a parent model.                           | No       | integer          |
| project        | query      | Limit result set to comments of specific project IDs.                         | No       | array            |
| project_member | query      | Limit results to only those you are a member of.                              | No       | boolean          |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/mockup`

**Parameters**

| Name               | Located in | Description                                                   | Required | Type     |
| ------------------ | ---------- | ------------------------------------------------------------- | -------- | -------- |
| date               | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt           | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug               | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status             | formData   | A named status for the object.                                | No       | string   |
| password           | formData   | A password to protect access to the content and excerpt.      | No       | string   |
| title              | formData   | The title for the object.                                     | No       | string   |
| author             | formData   | The ID for the author of the object.                          | No       | integer  |
| access_link_login  | formData   | Whether to allow logging in through the access link.          | No       | boolean  |
| project_members    | formData   | A list of user IDs that have access to the project.           | No       | array    |
| project_access     | formData   | Access options for the project                                | No       | string   |
| retina             | formData   | Whether to serve the images as retina                         | No       | boolean  |
| sharing            | formData   | Is the project sharing UI enabled?                            | No       | boolean  |
| zoom               | formData   | Is the project zoom functionality enabled                     | No       | boolean  |
| tooltip            | formData   | Show the Leave A Comment tooltip.                             | No       | boolean  |
| project_comments   | formData   | Allow non-users to make comments                              | No       | boolean  |
| project_approval   | formData   | Approvals allowed?                                            | No       | boolean  |
| project_unapproval | formData   | Unapprovals allowed?                                          | No       | boolean  |
| me                 | formData   | Current user data.                                            | No       | string   |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# Mockup

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/mockup/{id}`

**Parameters**

| Name     | Located in | Description                                                                   | Required | Type    |
| -------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| id       | path       |                                                                               | Yes      | string  |
| id       | query      | Unique identifier for the object.                                             | No       | integer |
| context  | query      | Scope under which the request is made; determines fields present in response. | No       | string  |
| password | query      | The password for the post if it is password protected.                        | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/mockup/{id}`

**Parameters**

| Name               | Located in | Description                                                   | Required | Type     |
| ------------------ | ---------- | ------------------------------------------------------------- | -------- | -------- |
| id                 | path       |                                                               | Yes      | string   |
| id                 | formData   | Unique identifier for the object.                             | No       | integer  |
| date               | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt           | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug               | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status             | formData   | A named status for the object.                                | No       | string   |
| password           | formData   | A password to protect access to the content and excerpt.      | No       | string   |
| title              | formData   | The title for the object.                                     | No       | string   |
| author             | formData   | The ID for the author of the object.                          | No       | integer  |
| access_link_login  | formData   | Whether to allow logging in through the access link.          | No       | boolean  |
| project_members    | formData   | A list of user IDs that have access to the project.           | No       | array    |
| project_access     | formData   | Access options for the project                                | No       | string   |
| retina             | formData   | Whether to serve the images as retina                         | No       | boolean  |
| sharing            | formData   | Is the project sharing UI enabled?                            | No       | boolean  |
| zoom               | formData   | Is the project zoom functionality enabled                     | No       | boolean  |
| tooltip            | formData   | Show the Leave A Comment tooltip.                             | No       | boolean  |
| project_comments   | formData   | Allow non-users to make comments                              | No       | boolean  |
| project_approval   | formData   | Approvals allowed?                                            | No       | boolean  |
| project_unapproval | formData   | Unapprovals allowed?                                          | No       | boolean  |
| me                 | formData   | Current user data.                                            | No       | string   |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/mockup/{id}`

**Parameters**

| Name  | Located in | Description                                 | Required | Type    |
| ----- | ---------- | ------------------------------------------- | -------- | ------- |
| id    | path       |                                             | Yes      | string  |
| id    | query      | Unique identifier for the object.           | No       | integer |
| force | query      | Whether to bypass trash and force deletion. | No       | boolean |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Mockup Images

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/mockup-image`

**Parameters**

| Name       | Located in | Description                                                                   | Required | Type             |
| ---------- | ---------- | ----------------------------------------------------------------------------- | -------- | ---------------- |
| context    | query      | Scope under which the request is made; determines fields present in response. | No       | string           |
| page       | query      | Current page of the collection.                                               | No       | integer (number) |
| per_page   | query      | Maximum number of items to be returned in result set.                         | No       | integer (number) |
| search     | query      | Limit results to those matching a string.                                     | No       | string           |
| after      | query      | Limit response to posts published after a given ISO8601 compliant date.       | No       | dateTime         |
| before     | query      | Limit response to posts published before a given ISO8601 compliant date.      | No       | dateTime         |
| exclude    | query      | Ensure result set excludes specific IDs.                                      | No       | array            |
| include    | query      | Limit result set to specific IDs.                                             | No       | array            |
| menu_order | query      | Limit result set to posts with a specific menu_order value.                   | No       | integer          |
| offset     | query      | Offset the result set by a specific number of items.                          | No       | integer          |
| order      | query      | Order sort attribute ascending or descending.                                 | No       | string           |
| orderby    | query      | Sort collection by object attribute.                                          | No       | string           |
| slug       | query      | Limit result set to posts with one or more specific slugs.                    | No       | array            |
| status     | query      | Limit result set to posts assigned one or more statuses.                      | No       | array            |
| parent_id  | query      | Limit result set to items that have a parent model.                           | No       | integer          |
| project    | query      | Limit result set to comments of specific project IDs.                         | No       | array            |
| sketch_id  | query      | Limit results by sketch id.                                                   | No       | string           |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/mockup-image`

**Parameters**

| Name           | Located in | Description                                                   | Required | Type     |
| -------------- | ---------- | ------------------------------------------------------------- | -------- | -------- |
| date           | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt       | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug           | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status         | formData   | A named status for the object.                                | No       | string   |
| type           | formData   | Media type.                                                   | No       | string   |
| password       | formData   | A password to protect access to the content and excerpt.      | No       | string   |
| parent_id      | formData   | The ID for the parent of the object.                          | No       | integer  |
| title          | formData   | The title for the object.                                     | No       | string   |
| excerpt        | formData   | The excerpt for the object.                                   | No       | string   |
| featured_media | formData   | The ID of the featured media for the object.                  | No       | integer  |
| menu_order     | formData   | Order of image in project.                                    | No       | number   |
| meta           | formData   | Meta fields.                                                  | No       | string   |
| sketch_id      | formData   | ID of sketch artboard                                         | No       | string   |
| version        | formData   | Save previous version history for this change.                | No       | boolean  |
| options        | formData   |                                                               | No       | string   |
| approval       | formData   | Approval.                                                     | No       | boolean  |
| approval_data  | formData   | Array of approval data for the image.                         | No       | array    |
| resolve_status | formData   | Array of comment data for the image.                          | No       | array    |
| resolved       | formData   | Whether to resolve all threads in this image.                 | No       | boolean  |
| pdf_page       | formData   | Page of the pdf document.                                     | No       | integer  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# Mockup Image

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/mockup-image/{id}`

**Parameters**

| Name     | Located in | Description                                                                   | Required | Type    |
| -------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| id       | path       |                                                                               | Yes      | string  |
| id       | query      | Unique identifier for the object.                                             | No       | integer |
| context  | query      | Scope under which the request is made; determines fields present in response. | No       | string  |
| password | query      | The password for the post if it is password protected.                        | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/mockup-image/{id}`

**Parameters**

| Name           | Located in | Description                                                   | Required | Type     |
| -------------- | ---------- | ------------------------------------------------------------- | -------- | -------- |
| id             | path       |                                                               | Yes      | string   |
| id             | formData   | Unique identifier for the object.                             | No       | integer  |
| date           | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt       | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug           | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status         | formData   | A named status for the object.                                | No       | string   |
| type           | formData   | Media type.                                                   | No       | string   |
| password       | formData   | A password to protect access to the content and excerpt.      | No       | string   |
| parent_id      | formData   | The ID for the parent of the object.                          | No       | integer  |
| title          | formData   | The title for the object.                                     | No       | string   |
| excerpt        | formData   | The excerpt for the object.                                   | No       | string   |
| featured_media | formData   | The ID of the featured media for the object.                  | No       | integer  |
| menu_order     | formData   | Order of image in project.                                    | No       | number   |
| meta           | formData   | Meta fields.                                                  | No       | string   |
| sketch_id      | formData   | ID of sketch artboard                                         | No       | string   |
| version        | formData   | Save previous version history for this change.                | No       | boolean  |
| options        | formData   |                                                               | No       | string   |
| approval       | formData   | Approval.                                                     | No       | boolean  |
| approval_data  | formData   | Array of approval data for the image.                         | No       | array    |
| resolve_status | formData   | Array of comment data for the image.                          | No       | array    |
| resolved       | formData   | Whether to resolve all threads in this image.                 | No       | boolean  |
| pdf_page       | formData   | Page of the pdf document.                                     | No       | integer  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/mockup-image/{id}`

**Parameters**

| Name  | Located in | Description                                 | Required | Type    |
| ----- | ---------- | ------------------------------------------- | -------- | ------- |
| id    | path       |                                             | Yes      | string  |
| id    | query      | Unique identifier for the object.           | No       | integer |
| force | query      | Whether to bypass trash and force deletion. | No       | boolean |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Mockup Image Versions

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/mockup-image/{parent}/versions`

**Parameters**

| Name    | Located in | Description                                                                   | Required | Type    |
| ------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| parent  | path       |                                                                               | Yes      | string  |
| parent  | query      | The ID for the parent of the object.                                          | No       | integer |
| context | query      | Scope under which the request is made; determines fields present in response. | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Mockup Image Version

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/mockup-image/{parent}/versions/{id}`

**Parameters**

| Name    | Located in | Description                                                                   | Required | Type    |
| ------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| parent  | path       |                                                                               | Yes      | string  |
| id      | path       |                                                                               | Yes      | string  |
| parent  | query      | The ID for the parent of the object.                                          | No       | integer |
| id      | query      | Unique identifier for the object.                                             | No       | integer |
| context | query      | Scope under which the request is made; determines fields present in response. | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/mockup-image/{parent}/versions/{id}`

**Parameters**

| Name   | Located in | Description                                               | Required | Type    |
| ------ | ---------- | --------------------------------------------------------- | -------- | ------- |
| parent | path       |                                                           | Yes      | string  |
| id     | path       |                                                           | Yes      | string  |
| parent | query      | The ID for the parent of the object.                      | No       | integer |
| id     | query      | Unique identifier for the object.                         | No       | integer |
| force  | query      | Required to be true, as versions do not support trashing. | No       | boolean |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Mockup Comment Threads

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/mockup-thread`

**Parameters**

| Name           | Located in | Description                                                                   | Required | Type             |
| -------------- | ---------- | ----------------------------------------------------------------------------- | -------- | ---------------- |
| context        | query      | Scope under which the request is made; determines fields present in response. | No       | string           |
| page           | query      | Current page of the collection.                                               | No       | integer (number) |
| per_page       | query      | Maximum number of items to be returned in result set.                         | No       | integer (number) |
| search         | query      | Limit results to those matching a string.                                     | No       | string           |
| after          | query      | Limit response to posts published after a given ISO8601 compliant date.       | No       | dateTime         |
| author         | query      | Limit result set to posts assigned to specific authors.                       | No       | array            |
| author_exclude | query      | Ensure result set excludes posts assigned to specific authors.                | No       | array            |
| before         | query      | Limit response to posts published before a given ISO8601 compliant date.      | No       | dateTime         |
| exclude        | query      | Ensure result set excludes specific IDs.                                      | No       | array            |
| include        | query      | Limit result set to specific IDs.                                             | No       | array            |
| offset         | query      | Offset the result set by a specific number of items.                          | No       | integer          |
| order          | query      | Order sort attribute ascending or descending.                                 | No       | string           |
| orderby        | query      | Sort collection by object attribute.                                          | No       | string           |
| slug           | query      | Limit result set to posts with one or more specific slugs.                    | No       | array            |
| status         | query      | Limit result set to posts assigned one or more statuses.                      | No       | array            |
| parent_id      | query      | Limit result set to items that have a parent model.                           | No       | integer          |
| project        | query      | Limit result set to comments of specific project IDs.                         | No       | array            |
| resolved       | query      | Limit results by resolved status.                                             | No       | boolean          |
| assigned       | query      | Limit by ID of user who is assigned.                                          | No       | integer          |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/mockup-thread`

**Parameters**

| Name           | Located in | Description                                                   | Required | Type     |
| -------------- | ---------- | ------------------------------------------------------------- | -------- | -------- |
| date           | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt       | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug           | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status         | formData   | A named status for the object.                                | No       | string   |
| password       | formData   | A password to protect access to the content and excerpt.      | No       | string   |
| parent_id      | formData   | The ID for the parent of the object.                          | No       | integer  |
| title          | formData   | The title for the object.                                     | No       | string   |
| content        | formData   | The content for the object.                                   | No       | string   |
| author         | formData   | The ID for the author of the object.                          | No       | integer  |
| comment_status | formData   | Whether or not comments are open on the object.               | No       | string   |
| ping_status    | formData   | Whether or not the object can be pinged.                      | No       | string   |
| meta           | formData   | Meta fields.                                                  | No       | string   |
| relativeX      | formData   | Relative horizontal click position of the element.            | No       | number   |
| relativeY      | formData   | Relative vertical click position of the element.              | No       | number   |
| resolved       | formData   | Issue resolve status.                                         | No       | boolean  |
| assigned       | formData   | ID of user who is assigned.                                   | No       | integer  |
| comments       | formData   | Array of comment objects in thread.                           | No       | array    |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# Mockup Comment Thread

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/mockup-thread/{id}`

**Parameters**

| Name     | Located in | Description                                                                   | Required | Type    |
| -------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| id       | path       |                                                                               | Yes      | string  |
| id       | query      | Unique identifier for the object.                                             | No       | integer |
| context  | query      | Scope under which the request is made; determines fields present in response. | No       | string  |
| password | query      | The password for the post if it is password protected.                        | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/mockup-thread/{id}`

**Parameters**

| Name           | Located in | Description                                                   | Required | Type     |
| -------------- | ---------- | ------------------------------------------------------------- | -------- | -------- |
| id             | path       |                                                               | Yes      | string   |
| id             | formData   | Unique identifier for the object.                             | No       | integer  |
| date           | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt       | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug           | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status         | formData   | A named status for the object.                                | No       | string   |
| password       | formData   | A password to protect access to the content and excerpt.      | No       | string   |
| parent_id      | formData   | The ID for the parent of the object.                          | No       | integer  |
| title          | formData   | The title for the object.                                     | No       | string   |
| content        | formData   | The content for the object.                                   | No       | string   |
| author         | formData   | The ID for the author of the object.                          | No       | integer  |
| comment_status | formData   | Whether or not comments are open on the object.               | No       | string   |
| ping_status    | formData   | Whether or not the object can be pinged.                      | No       | string   |
| meta           | formData   | Meta fields.                                                  | No       | string   |
| relativeX      | formData   | Relative horizontal click position of the element.            | No       | number   |
| relativeY      | formData   | Relative vertical click position of the element.              | No       | number   |
| resolved       | formData   | Issue resolve status.                                         | No       | boolean  |
| assigned       | formData   | ID of user who is assigned.                                   | No       | integer  |
| comments       | formData   | Array of comment objects in thread.                           | No       | array    |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/mockup-thread/{id}`

**Parameters**

| Name  | Located in | Description                                 | Required | Type    |
| ----- | ---------- | ------------------------------------------- | -------- | ------- |
| id    | path       |                                             | Yes      | string  |
| id    | query      | Unique identifier for the object.           | No       | integer |
| force | query      | Whether to bypass trash and force deletion. | No       | boolean |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Websites

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/website`

**Parameters**

| Name           | Located in | Description                                                                   | Required | Type             |
| -------------- | ---------- | ----------------------------------------------------------------------------- | -------- | ---------------- |
| context        | query      | Scope under which the request is made; determines fields present in response. | No       | string           |
| page           | query      | Current page of the collection.                                               | No       | integer (number) |
| per_page       | query      | Maximum number of items to be returned in result set.                         | No       | integer (number) |
| search         | query      | Limit results to those matching a string.                                     | No       | string           |
| after          | query      | Limit response to posts published after a given ISO8601 compliant date.       | No       | dateTime         |
| author         | query      | Limit result set to posts assigned to specific authors.                       | No       | array            |
| author_exclude | query      | Ensure result set excludes posts assigned to specific authors.                | No       | array            |
| before         | query      | Limit response to posts published before a given ISO8601 compliant date.      | No       | dateTime         |
| exclude        | query      | Ensure result set excludes specific IDs.                                      | No       | array            |
| include        | query      | Limit result set to specific IDs.                                             | No       | array            |
| offset         | query      | Offset the result set by a specific number of items.                          | No       | integer          |
| order          | query      | Order sort attribute ascending or descending.                                 | No       | string           |
| orderby        | query      | Sort collection by object attribute.                                          | No       | string           |
| slug           | query      | Limit result set to posts with one or more specific slugs.                    | No       | array            |
| status         | query      | Limit result set to posts assigned one or more statuses.                      | No       | array            |
| parent_id      | query      | Limit result set to items that have a parent model.                           | No       | integer          |
| project        | query      | Limit result set to comments of specific project IDs.                         | No       | array            |
| website_url    | query      | URL for the website.                                                          | No       | url              |
| project_member | query      | Limit results to only those you are a member of.                              | No       | boolean          |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/website`

**Parameters**

| Name              | Located in | Description                                                   | Required | Type     |
| ----------------- | ---------- | ------------------------------------------------------------- | -------- | -------- |
| date              | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt          | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug              | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status            | formData   | A named status for the object.                                | No       | string   |
| password          | formData   | A password to protect access to the content and excerpt.      | No       | string   |
| title             | formData   | The title for the object.                                     | No       | string   |
| author            | formData   | The ID for the author of the object.                          | No       | integer  |
| meta              | formData   | Meta fields.                                                  | No       | string   |
| me                | formData   | Current user data.                                            | No       | string   |
| access_link_login | formData   | Whether to allow logging in through the access link.          | No       | boolean  |
| resolve_status    | formData   | Array of comment data for the image.                          | No       | array    |
| project_members   | formData   | A list of user IDs that have access to the project.           | No       | array    |
| website_url       | formData   | URL for the website.                                          | Yes      | url      |
| project_access    | formData   | Access options for the project                                | No       | string   |
| toolbar_location  | formData   | Toolbar location for project                                  | No       | string   |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# Website

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/website/{id}`

**Parameters**

| Name     | Located in | Description                                                                   | Required | Type    |
| -------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| id       | path       |                                                                               | Yes      | string  |
| id       | query      | Unique identifier for the object.                                             | No       | integer |
| context  | query      | Scope under which the request is made; determines fields present in response. | No       | string  |
| password | query      | The password for the post if it is password protected.                        | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/website/{id}`

**Parameters**

| Name              | Located in | Description                                                   | Required | Type     |
| ----------------- | ---------- | ------------------------------------------------------------- | -------- | -------- |
| id                | path       |                                                               | Yes      | string   |
| id                | formData   | Unique identifier for the object.                             | No       | integer  |
| date              | formData   | The date the object was published, in the site's timezone.    | No       | dateTime |
| date_gmt          | formData   | The date the object was published, as GMT.                    | No       | dateTime |
| slug              | formData   | An alphanumeric identifier for the object unique to its type. | No       | string   |
| status            | formData   | A named status for the object.                                | No       | string   |
| password          | formData   | A password to protect access to the content and excerpt.      | No       | string   |
| title             | formData   | The title for the object.                                     | No       | string   |
| author            | formData   | The ID for the author of the object.                          | No       | integer  |
| meta              | formData   | Meta fields.                                                  | No       | string   |
| me                | formData   | Current user data.                                            | No       | string   |
| access_link_login | formData   | Whether to allow logging in through the access link.          | No       | boolean  |
| resolve_status    | formData   | Array of comment data for the image.                          | No       | array    |
| project_members   | formData   | A list of user IDs that have access to the project.           | No       | array    |
| website_url       | formData   | URL for the website.                                          | No       | url      |
| project_access    | formData   | Access options for the project                                | No       | string   |
| toolbar_location  | formData   | Toolbar location for project                                  | No       | string   |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/website/{id}`

**Parameters**

| Name  | Located in | Description                                 | Required | Type    |
| ----- | ---------- | ------------------------------------------- | -------- | ------- |
| id    | path       |                                             | Yes      | string  |
| id    | query      | Unique identifier for the object.           | No       | integer |
| force | query      | Whether to bypass trash and force deletion. | No       | boolean |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Website Pages

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/website-page`

**Parameters**

| Name       | Located in | Description                                                                   | Required | Type             |
| ---------- | ---------- | ----------------------------------------------------------------------------- | -------- | ---------------- |
| context    | query      | Scope under which the request is made; determines fields present in response. | No       | string           |
| page       | query      | Current page of the collection.                                               | No       | integer (number) |
| per_page   | query      | Maximum number of items to be returned in result set.                         | No       | integer (number) |
| search     | query      | Limit results to those matching a string.                                     | No       | string           |
| after      | query      | Limit response to posts published after a given ISO8601 compliant date.       | No       | dateTime         |
| before     | query      | Limit response to posts published before a given ISO8601 compliant date.      | No       | dateTime         |
| exclude    | query      | Ensure result set excludes specific IDs.                                      | No       | array            |
| include    | query      | Limit result set to specific IDs.                                             | No       | array            |
| menu_order | query      | Limit result set to posts with a specific menu_order value.                   | No       | integer          |
| offset     | query      | Offset the result set by a specific number of items.                          | No       | integer          |
| order      | query      | Order sort attribute ascending or descending.                                 | No       | string           |
| orderby    | query      | Sort collection by object attribute.                                          | No       | string           |
| slug       | query      | Limit result set to posts with one or more specific slugs.                    | No       | array            |
| status     | query      | Limit result set to posts assigned one or more statuses.                      | No       | array            |
| parent_id  | query      | Limit result set to items that have a parent model.                           | No       | integer          |
| project    | query      | Limit result set to comments of specific project IDs.                         | No       | array            |
| page_url   | query      | Url for the page.                                                             | No       | string           |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/website-page`

**Parameters**

| Name           | Located in | Description                                                      | Required | Type         |
| -------------- | ---------- | ---------------------------------------------------------------- | -------- | ------------ |
| date           | formData   | The date the object was published, in the site's timezone.       | No       | dateTime     |
| date_gmt       | formData   | The date the object was published, as GMT.                       | No       | dateTime     |
| slug           | formData   | An alphanumeric identifier for the object unique to its type.    | No       | string       |
| status         | formData   | A named status for the object.                                   | No       | string       |
| password       | formData   | A password to protect access to the content and excerpt.         | No       | string       |
| parent_id      | formData   | The ID for the parent of the object.                             | No       | integer      |
| title          | formData   | The title for the object.                                        | No       | string       |
| menu_order     | formData   | The order of the object in relation to other object of its type. | No       | integer      |
| meta           | formData   | Meta fields.                                                     | No       | string       |
| page_url       | formData   | Full url for the page.                                           | Yes      | string (uri) |
| resolve_status | formData   | Array of comment data for the image.                             | No       | array        |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# Website Page

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/website-page/{id}`

**Parameters**

| Name     | Located in | Description                                                                   | Required | Type    |
| -------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| id       | path       |                                                                               | Yes      | string  |
| id       | query      | Unique identifier for the object.                                             | No       | integer |
| context  | query      | Scope under which the request is made; determines fields present in response. | No       | string  |
| password | query      | The password for the post if it is password protected.                        | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/website-page/{id}`

**Parameters**

| Name           | Located in | Description                                                      | Required | Type         |
| -------------- | ---------- | ---------------------------------------------------------------- | -------- | ------------ |
| id             | path       |                                                                  | Yes      | string       |
| id             | formData   | Unique identifier for the object.                                | No       | integer      |
| date           | formData   | The date the object was published, in the site's timezone.       | No       | dateTime     |
| date_gmt       | formData   | The date the object was published, as GMT.                       | No       | dateTime     |
| slug           | formData   | An alphanumeric identifier for the object unique to its type.    | No       | string       |
| status         | formData   | A named status for the object.                                   | No       | string       |
| password       | formData   | A password to protect access to the content and excerpt.         | No       | string       |
| parent_id      | formData   | The ID for the parent of the object.                             | No       | integer      |
| title          | formData   | The title for the object.                                        | No       | string       |
| menu_order     | formData   | The order of the object in relation to other object of its type. | No       | integer      |
| meta           | formData   | Meta fields.                                                     | No       | string       |
| page_url       | formData   | Full url for the page.                                           | No       | string (uri) |
| resolve_status | formData   | Array of comment data for the image.                             | No       | array        |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/website-page/{id}`

**Parameters**

| Name  | Located in | Description                                 | Required | Type    |
| ----- | ---------- | ------------------------------------------- | -------- | ------- |
| id    | path       |                                             | Yes      | string  |
| id    | query      | Unique identifier for the object.           | No       | integer |
| force | query      | Whether to bypass trash and force deletion. | No       | boolean |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

# Website Comment Threads

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/website-thread`

**Parameters**

| Name           | Located in | Description                                                                   | Required | Type             |
| -------------- | ---------- | ----------------------------------------------------------------------------- | -------- | ---------------- |
| context        | query      | Scope under which the request is made; determines fields present in response. | No       | string           |
| page           | query      | Current page of the collection.                                               | No       | integer (number) |
| per_page       | query      | Maximum number of items to be returned in result set.                         | No       | integer (number) |
| search         | query      | Limit results to those matching a string.                                     | No       | string           |
| after          | query      | Limit response to posts published after a given ISO8601 compliant date.       | No       | dateTime         |
| author         | query      | Limit result set to posts assigned to specific authors.                       | No       | array            |
| author_exclude | query      | Ensure result set excludes posts assigned to specific authors.                | No       | array            |
| before         | query      | Limit response to posts published before a given ISO8601 compliant date.      | No       | dateTime         |
| exclude        | query      | Ensure result set excludes specific IDs.                                      | No       | array            |
| include        | query      | Limit result set to specific IDs.                                             | No       | array            |
| menu_order     | query      | Limit result set to posts with a specific menu_order value.                   | No       | integer          |
| offset         | query      | Offset the result set by a specific number of items.                          | No       | integer          |
| order          | query      | Order sort attribute ascending or descending.                                 | No       | string           |
| orderby        | query      | Sort collection by object attribute.                                          | No       | string           |
| slug           | query      | Limit result set to posts with one or more specific slugs.                    | No       | array            |
| status         | query      | Limit result set to posts assigned one or more statuses.                      | No       | array            |
| parent_id      | query      | Limit result set to items that have a parent model.                           | No       | integer          |
| project        | query      | Limit result set to comments of specific project IDs.                         | No       | array            |
| resolved       | query      | Limit results by resolved status.                                             | No       | boolean          |
| assigned       | query      | Limit by ID of user who is assigned.                                          | No       | integer          |
| project_id     | query      | Limit results by project id status.                                           | No       | integer          |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/website-thread`

**Parameters**

| Name           | Located in | Description                                                      | Required | Type     |
| -------------- | ---------- | ---------------------------------------------------------------- | -------- | -------- |
| date           | formData   | The date the object was published, in the site's timezone.       | No       | dateTime |
| date_gmt       | formData   | The date the object was published, as GMT.                       | No       | dateTime |
| slug           | formData   | An alphanumeric identifier for the object unique to its type.    | No       | string   |
| status         | formData   | A named status for the object.                                   | No       | string   |
| password       | formData   | A password to protect access to the content and excerpt.         | No       | string   |
| parent_id      | formData   | The ID for the parent of the object.                             | No       | integer  |
| title          | formData   | The title for the object.                                        | No       | string   |
| content        | formData   | The content for the object.                                      | No       | string   |
| author         | formData   | The ID for the author of the object.                             | No       | integer  |
| comment_status | formData   | Whether or not comments are open on the object.                  | No       | string   |
| ping_status    | formData   | Whether or not the object can be pinged.                         | No       | string   |
| menu_order     | formData   | The order of the object in relation to other object of its type. | No       | integer  |
| meta           | formData   | Meta fields.                                                     | No       | string   |
| path           | formData   | CSS path to the clicked element.                                 | No       | string   |
| xPath          | formData   | xPath to the element.                                            | No       | string   |
| relativeX      | formData   | Relative horizontal click position of the element.               | No       | number   |
| relativeY      | formData   | Relative vertical click position of the element.                 | No       | number   |
| pageX          | formData   | Page relative horizontal click position of the element.          | No       | number   |
| pageY          | formData   | Page relative vertical click position of the element.            | No       | number   |
| html           | formData   | Inner html of the clicked element.                               | No       | string   |
| resX           | formData   | Horizontal resolution of user who reported issue.                | No       | number   |
| resY           | formData   | Vertical resolution of user who reported issue.                  | No       | number   |
| resolved       | formData   | Issue resolve status.                                            | No       | boolean  |
| browser        | formData   | Browser information.                                             | No       | string   |
| browserVersion | formData   | Browser version information.                                     | No       | number   |
| browserOS      | formData   | Operating system information.                                    | No       | string   |
| page_url       | formData   | Url for the page.                                                | No       | url      |
| page_title     | formData   | Title for the new page.                                          | No       | string   |
| website_id     | formData   | Website project id for the thread.                               | No       | integer  |
| assigned       | formData   | ID of user who is assigned.                                      | No       | integer  |
| comments       | formData   | Array of comment objects in thread.                              | No       | array    |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| 201     | successful operation |
| default | error                |

# Website Comment Thread

## **_GET_**

### HTTP Request

`GET /projecthuddle/v2/website-thread/{id}`

**Parameters**

| Name     | Located in | Description                                                                   | Required | Type    |
| -------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| id       | path       |                                                                               | Yes      | string  |
| id       | query      | Unique identifier for the object.                                             | No       | integer |
| context  | query      | Scope under which the request is made; determines fields present in response. | No       | string  |
| password | query      | The password for the post if it is password protected.                        | No       | string  |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_POST_**

### HTTP Request

`POST /projecthuddle/v2/website-thread/{id}`

**Parameters**

| Name           | Located in | Description                                                      | Required | Type     |
| -------------- | ---------- | ---------------------------------------------------------------- | -------- | -------- |
| id             | path       |                                                                  | Yes      | string   |
| id             | formData   | Unique identifier for the object.                                | No       | integer  |
| date           | formData   | The date the object was published, in the site's timezone.       | No       | dateTime |
| date_gmt       | formData   | The date the object was published, as GMT.                       | No       | dateTime |
| slug           | formData   | An alphanumeric identifier for the object unique to its type.    | No       | string   |
| status         | formData   | A named status for the object.                                   | No       | string   |
| password       | formData   | A password to protect access to the content and excerpt.         | No       | string   |
| parent_id      | formData   | The ID for the parent of the object.                             | No       | integer  |
| title          | formData   | The title for the object.                                        | No       | string   |
| content        | formData   | The content for the object.                                      | No       | string   |
| author         | formData   | The ID for the author of the object.                             | No       | integer  |
| comment_status | formData   | Whether or not comments are open on the object.                  | No       | string   |
| ping_status    | formData   | Whether or not the object can be pinged.                         | No       | string   |
| menu_order     | formData   | The order of the object in relation to other object of its type. | No       | integer  |
| meta           | formData   | Meta fields.                                                     | No       | string   |
| path           | formData   | CSS path to the clicked element.                                 | No       | string   |
| xPath          | formData   | xPath to the element.                                            | No       | string   |
| relativeX      | formData   | Relative horizontal click position of the element.               | No       | number   |
| relativeY      | formData   | Relative vertical click position of the element.                 | No       | number   |
| pageX          | formData   | Page relative horizontal click position of the element.          | No       | number   |
| pageY          | formData   | Page relative vertical click position of the element.            | No       | number   |
| html           | formData   | Inner html of the clicked element.                               | No       | string   |
| resX           | formData   | Horizontal resolution of user who reported issue.                | No       | number   |
| resY           | formData   | Vertical resolution of user who reported issue.                  | No       | number   |
| resolved       | formData   | Issue resolve status.                                            | No       | boolean  |
| browser        | formData   | Browser information.                                             | No       | string   |
| browserVersion | formData   | Browser version information.                                     | No       | number   |
| browserOS      | formData   | Operating system information.                                    | No       | string   |
| page_url       | formData   | Url for the page.                                                | No       | url      |
| page_title     | formData   | Title for the new page.                                          | No       | string   |
| website_id     | formData   | Website project id for the thread.                               | No       | integer  |
| assigned       | formData   | ID of user who is assigned.                                      | No       | integer  |
| comments       | formData   | Array of comment objects in thread.                              | No       | array    |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

## **_DELETE_**

### HTTP Request

`DELETE /projecthuddle/v2/website-thread/{id}`

**Parameters**

| Name  | Located in | Description                                 | Required | Type    |
| ----- | ---------- | ------------------------------------------- | -------- | ------- |
| id    | path       |                                             | Yes      | string  |
| id    | query      | Unique identifier for the object.           | No       | integer |
| force | query      | Whether to bypass trash and force deletion. | No       | boolean |

**Responses**

| Code    | Description          |
| ------- | -------------------- |
| 200     | successful operation |
| default | error                |

<!-- Converted with the swagger-to-slate https://github.com/lavkumarv/swagger-to-slate -->
