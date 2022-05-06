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

```javascript
const form = new FormData();
form.append("email", "testuser@sample.com");
form.append("project_id", "1427");
form.append("username", "test_user");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/users/', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```
> Example of JSON output

```json
{
  "id": 24,
  "username": "test_user",
  "name": "test_user",
  "first_name": "",
  "last_name": "",
  "email": "testuser@sample.com",
  "url": "",
  "description": "",
  "link": "https://example.org/author/test_user/",
  "locale": "en_US",
  "nickname": "test_user",
  "slug": "test_user",
  "roles": [
    "project_client"
  ],
  "user_role": "Project Client",
  "registered_date": "2022-04-22T08:15:29+00:00",
  "capabilities": {
    "read": true,
    "login_with_access_token": true,
    "publish_ph_comment_locations": true,
    "edit_ph_comment_locations": true,
    "edit_others_ph_comment_locations": true,
    "read_private_ph_comment_locations": true,
    "delete_ph_comment_locations": true,
    "delete_private_ph_comment_locations": true,
    "delete_published_ph_comment_locations": true,
    "edit_private_ph_comment_locations": true,
    "edit_published_ph_comment_locations": true,
    "publish_ph-webpages": true,
    "edit_ph-webpages": true,
    "edit_others_ph-webpages": true,
    "read_private_ph-webpages": true,
    "delete_ph-webpages": true,
    "delete_private_ph-webpages": true,
    "delete_published_ph-webpages": true,
    "edit_private_ph-webpages": true,
    "edit_published_ph-webpages": true,
    "publish_phw_comment_locs": true,
    "edit_phw_comment_locs": true,
    "edit_others_phw_comment_locs": true,
    "read_private_phw_comment_locs": true,
    "delete_phw_comment_locs": true,
    "delete_private_phw_comment_locs": true,
    "delete_published_phw_comment_locs": true,
    "edit_private_phw_comment_locs": true,
    "edit_published_phw_comment_locs": true,
    "read_ph-project": true,
    "read_ph-projects": true,
    "read_private_ph-projects": true,
    "read_ph_version": true,
    "read_ph_versions": true,
    "read_private_ph_versions": true,
    "read_ph-website": true,
    "read_ph-websites": true,
    "read_private_ph-websites": true,
    "project_client": true
  },
  "extra_capabilities": {
    "project_client": true
  },
  "avatar_urls": {
    "24": "https://secure.gravatar.com/avatar/9d37d03a914bc3a2696a3da0dbc2b040?s=24&d=mm&r=g",
    "48": "https://secure.gravatar.com/avatar/9d37d03a914bc3a2696a3da0dbc2b040?s=48&d=mm&r=g",
    "96": "https://secure.gravatar.com/avatar/9d37d03a914bc3a2696a3da0dbc2b040?s=96&d=mm&r=g"
  },
  "meta": [

  ],
  "refresh_token": "YOUR_REFRESH_ACCESS_TOKEN",
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/users/24"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/users"
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET'
};

fetch('https://example.org/wp-json/projecthuddle/v2/users/20', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 20,
  "name": "Tom",
  "url": "",
  "description": "",
  "link": "https://example.org/author/tom/",
  "slug": "tom",
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
}
```

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

```javascript
const form = new FormData();
form.append("first_name", "Test");
form.append("last_name", "User");

const options = {
  method: 'PATCH',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/users/24', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 24,
  "username": "test_user",
  "name": "test_user",
  "first_name": "Test",
  "last_name": "User",
  "email": "testuser@sample.com",
  "url": "",
  "description": "",
  "link": "https://example.org/author/test_user/",
  "locale": "en_US",
  "nickname": "test_user",
  "slug": "test_user",
  "roles": [
    "project_client"
  ],
  "user_role": "Project Client",
  "registered_date": "2022-04-22T08:15:29+00:00",
  "capabilities": {
    "read": true,
    "login_with_access_token": true,
    "publish_ph_comment_locations": true,
    "edit_ph_comment_locations": true,
    "edit_others_ph_comment_locations": true,
    "read_private_ph_comment_locations": true,
    "delete_ph_comment_locations": true,
    "delete_private_ph_comment_locations": true,
    "delete_published_ph_comment_locations": true,
    "edit_private_ph_comment_locations": true,
    "edit_published_ph_comment_locations": true,
    "publish_ph-webpages": true,
    "edit_ph-webpages": true,
    "edit_others_ph-webpages": true,
    "read_private_ph-webpages": true,
    "delete_ph-webpages": true,
    "delete_private_ph-webpages": true,
    "delete_published_ph-webpages": true,
    "edit_private_ph-webpages": true,
    "edit_published_ph-webpages": true,
    "publish_phw_comment_locs": true,
    "edit_phw_comment_locs": true,
    "edit_others_phw_comment_locs": true,
    "read_private_phw_comment_locs": true,
    "delete_phw_comment_locs": true,
    "delete_private_phw_comment_locs": true,
    "delete_published_phw_comment_locs": true,
    "edit_private_phw_comment_locs": true,
    "edit_published_phw_comment_locs": true,
    "read_ph-project": true,
    "read_ph-projects": true,
    "read_private_ph-projects": true,
    "read_ph_version": true,
    "read_ph_versions": true,
    "read_private_ph_versions": true,
    "read_ph-website": true,
    "read_ph-websites": true,
    "read_private_ph-websites": true,
    "project_client": true
  },
  "extra_capabilities": {
    "project_client": true
  },
  "avatar_urls": {
    "24": "https://secure.gravatar.com/avatar/9d37d03a914bc3a2696a3da0dbc2b040?s=24&d=mm&r=g",
    "48": "https://secure.gravatar.com/avatar/9d37d03a914bc3a2696a3da0dbc2b040?s=48&d=mm&r=g",
    "96": "https://secure.gravatar.com/avatar/9d37d03a914bc3a2696a3da0dbc2b040?s=96&d=mm&r=g"
  },
  "meta": [

  ],
  "refresh_token": "YOUR_REFRESH_ACCESS_TOKEN",
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/users/24"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/users"
      }
    ]
  }
}
```

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

```javascript

const options = {
  method: 'DELETE',
  body: {
    reassign: 21
    force: true
  },
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/users/24', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of output JSON

```json
{
  "deleted": true,
  "previous": {
    "id": 24,
    "username": "test_user",
    "name": "test_user",
    "first_name": "",
    "last_name": "",
    "email": "testuser@sample.com",
    "url": "",
    "description": "",
    "link": "https://example.org/author/test_user/",
    "locale": "en_US",
    "nickname": "test_user",
    "slug": "test_user",
    "roles": [
      "project_client"
    ],
    "user_role": "Project Client",
    "registered_date": "2022-04-12T10:18:07+00:00",
    "capabilities": {
      "read": true,
      "moderate_comments": false,
      "edit_comment_meta": true,
      "login_with_access_token": true,
      "publish_ph_comment_locations": true,
      "edit_ph_comment_locations": true,
      "edit_others_ph_comment_locations": true,
      "read_private_ph_comment_locations": true,
      "delete_ph_comment_locations": true,
      "delete_private_ph_comment_locations": true,
      "delete_published_ph_comment_locations": true,
      "edit_private_ph_comment_locations": true,
      "edit_published_ph_comment_locations": true,
      "publish_ph-webpages": true,
      "edit_ph-webpages": true,
      "edit_others_ph-webpages": true,
      "read_private_ph-webpages": true,
      "delete_ph-webpages": true,
      "delete_private_ph-webpages": true,
      "delete_published_ph-webpages": true,
      "edit_private_ph-webpages": true,
      "edit_published_ph-webpages": true,
      "publish_phw_comment_locs": true,
      "edit_phw_comment_locs": true,
      "edit_others_phw_comment_locs": true,
      "read_private_phw_comment_locs": true,
      "delete_phw_comment_locs": true,
      "delete_private_phw_comment_locs": true,
      "delete_published_phw_comment_locs": true,
      "edit_private_phw_comment_locs": true,
      "edit_published_phw_comment_locs": true,
      "read_ph-project": true,
      "read_ph-projects": true,
      "read_private_ph-projects": true,
      "read_ph_version": true,
      "read_ph_versions": true,
      "read_private_ph_versions": true,
      "read_ph-website": true,
      "read_ph-websites": true,
      "read_private_ph-websites": true,
      "project_client": true
    },
    "extra_capabilities": {
      "project_client": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=96&d=mm&r=g"
    },
    "meta": [

    ],
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/users/me', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 16,
  "name": "john",
  "url": "",
  "description": "",
  "link": "https://example.org/author/John/",
  "slug": "John",
  "user_role": "Administrator",
  "avatar_urls": {
    "24": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=24&d=mm&r=g",
    "48": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=48&d=mm&r=g",
    "96": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=96&d=mm&r=g"
  },
  "meta": [

  ],
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/users"
      }
    ]
  }
}
```

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

```javascript
const form = new FormData();
form.append("first_name", "John");
form.append("last_name", "Doe");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/users/me', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 16,
  "username": "john",
  "name": "john",
  "first_name": "John",
  "last_name": "Doe",
  "email": "johndoe@example.com",
  "url": "",
  "description": "",
  "link": "https://example.org/author/John/",
  "locale": "en_US",
  "nickname": "john",
  "slug": "john",
  "roles": [
    "administrator"
  ],
  "user_role": "Administrator",
  "registered_date": "2022-04-10T16:45:42+00:00",
  "capabilities": {
    "read": true,
    "login_with_access_token": true,
    "publish_ph_comment_locations": true,
    "edit_ph_comment_locations": true,
    "edit_others_ph_comment_locations": true,
    "read_private_ph_comment_locations": true,
    "delete_ph_comment_locations": true,
    "delete_private_ph_comment_locations": true,
    "delete_published_ph_comment_locations": true,
    "edit_private_ph_comment_locations": true,
    "edit_published_ph_comment_locations": true,
    "publish_ph-webpages": true,
    "edit_ph-webpages": true,
    "edit_others_ph-webpages": true,
    "read_private_ph-webpages": true,
    "delete_ph-webpages": true,
    "delete_private_ph-webpages": true,
    "delete_published_ph-webpages": true,
    "edit_private_ph-webpages": true,
    "edit_published_ph-webpages": true,
    "publish_phw_comment_locs": true,
    "edit_phw_comment_locs": true,
    "edit_others_phw_comment_locs": true,
    "read_private_phw_comment_locs": true,
    "delete_phw_comment_locs": true,
    "delete_private_phw_comment_locs": true,
    "delete_published_phw_comment_locs": true,
    "edit_private_phw_comment_locs": true,
    "edit_published_phw_comment_locs": true,
    "read_ph-project": true,
    "read_ph-projects": true,
    "read_private_ph-projects": true,
    "read_ph_version": true,
    "read_ph_versions": true,
    "read_private_ph_versions": true,
    "read_ph-website": true,
    "read_ph-websites": true,
    "read_private_ph-websites": true,
    "project_client": true
  },
  "extra_capabilities": {
    "administrator": true
  },
  "avatar_urls": {
    "24": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=24&d=mm&r=g",
    "48": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=48&d=mm&r=g",
    "96": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=96&d=mm&r=g"
  },
  "meta": [

  ],
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/users"
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'DELETE',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/users/me', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "deleted": true,
  "previous": {
    "id": 24,
    "username": "test_user",
    "name": "test_user",
    "first_name": "",
    "last_name": "",
    "email": "testuser@sample.com",
    "url": "",
    "description": "",
    "link": "https://example.org/author/test_user/",
    "locale": "en_US",
    "nickname": "test_user",
    "slug": "test_user",
    "roles": [
      "project_client"
    ],
    "user_role": "Project Client",
    "registered_date": "2022-04-12T10:18:07+00:00",
    "capabilities": {
      "read": true,
      "moderate_comments": false,
      "edit_comment_meta": true,
      "login_with_access_token": true,
      "publish_ph_comment_locations": true,
      "edit_ph_comment_locations": true,
      "edit_others_ph_comment_locations": true,
      "read_private_ph_comment_locations": true,
      "delete_ph_comment_locations": true,
      "delete_private_ph_comment_locations": true,
      "delete_published_ph_comment_locations": true,
      "edit_private_ph_comment_locations": true,
      "edit_published_ph_comment_locations": true,
      "publish_ph-webpages": true,
      "edit_ph-webpages": true,
      "edit_others_ph-webpages": true,
      "read_private_ph-webpages": true,
      "delete_ph-webpages": true,
      "delete_private_ph-webpages": true,
      "delete_published_ph-webpages": true,
      "edit_private_ph-webpages": true,
      "edit_published_ph-webpages": true,
      "publish_phw_comment_locs": true,
      "edit_phw_comment_locs": true,
      "edit_others_phw_comment_locs": true,
      "read_private_phw_comment_locs": true,
      "delete_phw_comment_locs": true,
      "delete_private_phw_comment_locs": true,
      "delete_published_phw_comment_locs": true,
      "edit_private_phw_comment_locs": true,
      "edit_published_phw_comment_locs": true,
      "read_ph-project": true,
      "read_ph-projects": true,
      "read_private_ph-projects": true,
      "read_ph_version": true,
      "read_ph_versions": true,
      "read_private_ph_versions": true,
      "read_ph-website": true,
      "read_ph-websites": true,
      "read_private_ph-websites": true,
      "project_client": true
    },
    "extra_capabilities": {
      "project_client": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/16ffd871facc7fd6cb4939703dd90d2b?s=96&d=mm&r=g"
    },
    "meta": [

    ],
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/media', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
[
  {
    "id": 1419,
    "date": "2022-04-19T16:53:10",
    "date_gmt": "2022-04-19T11:23:10",
    "modified": "2022-04-19T16:53:10",
    "modified_gmt": "2022-04-19T11:23:10",
    "slug": "edit-mockup-project-test-mock-bsf-wordpress",
    "status": "inherit",
    "type": "attachment",
    "link": "https://example.org/edit-mockup-project-test-mock-bsf-wordpress/",
    "title": {
      "rendered": "Edit Mockup Project “Test mock” ‹ BSF — WordPress"
    },
    "author": 16,
    "parent": 0,
    "comment_status": "open",
    "ping_status": "closed",
    "meta": {
      "site-sidebar-layout": "default",
      "site-content-layout": "default",
      "ast-main-header-display": "",
      "ast-hfb-above-header-display": "",
      "ast-hfb-below-header-display": "",
      "ast-hfb-mobile-header-display": "",
      "site-post-title": "",
      "ast-breadcrumbs-content": "",
      "ast-featured-img": "",
      "footer-sml-layout": "",
      "theme-transparent-header-meta": "",
      "adv-header-id-meta": "",
      "stick-header-meta": "",
      "header-above-stick-meta": "",
      "header-main-stick-meta": "",
      "header-below-stick-meta": ""
    },
    "model_type": null,
    "description": {
      "rendered": "<p class=\"attachment\"><a href='https://example.org/wp-content/uploads/2022/04/Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress.pdf'><img width=\"212\" height=\"300\" src=\"https://example.org/wp-content/uploads/2022/04/Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf-212x300.jpg\" class=\"attachment-medium size-medium\" alt=\"\" loading=\"lazy\" /></a></p>\n"
    },
    "caption": {
      "rendered": ""
    },
    "alt_text": "",
    "media_type": "file",
    "mime_type": "application/pdf",
    "media_details": {
      "sizes": {
        "full": {
          "file": "Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf.jpg",
          "width": 1058,
          "height": 1497,
          "mime_type": "application/pdf",
          "source_url": "https://example.org/wp-content/uploads/2022/04/Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf.jpg"
        },
        "medium": {
          "file": "Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf-212x300.jpg",
          "width": 212,
          "height": 300,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf-212x300.jpg"
        },
        "large": {
          "file": "Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf-724x1024.jpg",
          "width": 724,
          "height": 1024,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf-724x1024.jpg"
        },
        "thumbnail": {
          "file": "Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf-106x150.jpg",
          "width": 106,
          "height": 150,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress-pdf-106x150.jpg"
        }
      }
    },
    "post": null,
    "source_url": "https://example.org/wp-content/uploads/2022/04/Edit-Mockup-Project-Test-mock-‹-BSF-—-WordPress.pdf",
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/media/1419"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/media"
        }
      ],
      "about": [
        {
          "href": "https://example.org/wp-json/wp/v2/types/attachment"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "replies": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1419"
        }
      ],
      "comments": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1419"
        }
      ]
    }
  },
  {
    "id": 1384,
    "date": "2022-04-14T15:41:05",
    "date_gmt": "2022-04-14T10:11:05",
    "modified": "2022-04-14T15:41:05",
    "modified_gmt": "2022-04-14T10:11:05",
    "slug": "b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf",
    "status": "inherit",
    "type": "attachment",
    "link": "https://example.org/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf/",
    "title": {
      "rendered": "b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf"
    },
    "author": 16,
    "parent": 0,
    "comment_status": "open",
    "ping_status": "closed",
    "meta": {
      "site-sidebar-layout": "default",
      "site-content-layout": "default",
      "ast-main-header-display": "",
      "ast-hfb-above-header-display": "",
      "ast-hfb-below-header-display": "",
      "ast-hfb-mobile-header-display": "",
      "site-post-title": "",
      "ast-breadcrumbs-content": "",
      "ast-featured-img": "",
      "footer-sml-layout": "",
      "theme-transparent-header-meta": "",
      "adv-header-id-meta": "",
      "stick-header-meta": "",
      "header-above-stick-meta": "",
      "header-main-stick-meta": "",
      "header-below-stick-meta": ""
    },
    "model_type": null,
    "description": {
      "rendered": "<p class=\"attachment\"><a href='https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf.jpg'><img width=\"300\" height=\"200\" src=\"https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-300x200.jpg\" class=\"attachment-medium size-medium\" alt=\"\" loading=\"lazy\" srcset=\"https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-300x200.jpg 300w, https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-1024x682.jpg 1024w, https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-768x512.jpg 768w, https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf.jpg 1246w\" sizes=\"(max-width: 300px) 100vw, 300px\" /></a></p>\n"
    },
    "caption": {
      "rendered": ""
    },
    "alt_text": "",
    "media_type": "image",
    "mime_type": "image/jpeg",
    "media_details": {
      "width": 1246,
      "height": 830,
      "file": "2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf.jpg",
      "sizes": {
        "medium": {
          "file": "b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-300x200.jpg",
          "width": 300,
          "height": 200,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-300x200.jpg"
        },
        "large": {
          "file": "b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-1024x682.jpg",
          "width": 1024,
          "height": 682,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-1024x682.jpg"
        },
        "thumbnail": {
          "file": "b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-150x150.jpg",
          "width": 150,
          "height": 150,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-150x150.jpg"
        },
        "medium_large": {
          "file": "b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-768x512.jpg",
          "width": 768,
          "height": 512,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf-768x512.jpg"
        },
        "full": {
          "file": "b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf.jpg",
          "width": 1246,
          "height": 830,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf.jpg"
        }
      },
      "image_meta": {
        "aperture": "0",
        "credit": "",
        "camera": "",
        "caption": "",
        "created_timestamp": "0",
        "copyright": "",
        "focal_length": "0",
        "iso": "0",
        "shutter_speed": "0",
        "title": "",
        "orientation": "1",
        "keywords": [

        ]
      }
    },
    "post": null,
    "source_url": "https://example.org/wp-content/uploads/2022/04/b23d5a7f-607a-3a07-a9a4-e6910c4ffeaf.jpg",
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/media/1384"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/media"
        }
      ],
      "about": [
        {
          "href": "https://example.org/wp-json/wp/v2/types/attachment"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "replies": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1384"
        }
      ],
      "comments": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1384"
        }
      ]
    }
  },
]
```

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

```javascript
const form = new FormData();
form.append("title", "Mockup Design");
form.append("description", "Mockup design");
form.append("post", "127");
form.append("author", "21");
form.append("file", "image.jpg");
form.append("status", "publish");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/media', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1467,
  "date": "2022-04-25T16:33:30",
  "date_gmt": "2022-04-25T11:03:30",
  "modified": "2022-04-25T16:33:30",
  "modified_gmt": "2022-04-25T11:03:30",
  "slug": "mockup-design-10",
  "status": "inherit",
  "type": "attachment",
  "link": "https://example.org/?attachment_id=1467",
  "title": {
    "raw": "mockup-design",
    "rendered": "mockup-design"
  },
  "author": 21,
  "parent": 127,
  "comment_status": "open",
  "ping_status": "closed",
  "meta": {
    "site-sidebar-layout": "default",
    "site-content-layout": "default",
    "ast-main-header-display": "",
    "ast-hfb-above-header-display": "",
    "ast-hfb-below-header-display": "",
    "ast-hfb-mobile-header-display": "",
    "site-post-title": "",
    "ast-breadcrumbs-content": "",
    "ast-featured-img": "",
    "footer-sml-layout": "",
    "theme-transparent-header-meta": "",
    "adv-header-id-meta": "",
    "stick-header-meta": "",
    "header-above-stick-meta": "",
    "header-main-stick-meta": "",
    "header-below-stick-meta": ""
  },
  "model_type": null,
  "description": {
    "raw": "Mockup design",
    "rendered": "<p class=\"attachment\"><a href='https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg'><img width=\"300\" height=\"200\" src=\"https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg\" class=\"attachment-medium size-medium\" alt=\"\" loading=\"lazy\" srcset=\"https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg 300w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1024x683.jpg 1024w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-768x512.jpg 768w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1536x1024.jpg 1536w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg 1920w\" sizes=\"(max-width: 300px) 100vw, 300px\" /></a></p>\n<p>Mockup design</p>\n"
  },
  "caption": {
    "raw": "",
    "rendered": "<p>Mockup design</p>\n"
  },
  "alt_text": "",
  "media_type": "image",
  "mime_type": "image/jpeg",
  "media_details": {
    "width": 1920,
    "height": 1280,
    "file": "2022/04/road-g32889351c_1920-15.jpg",
    "sizes": {
      "medium": {
        "file": "road-g32889351c_1920-15-300x200.jpg",
        "width": 300,
        "height": 200,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg"
      },
      "large": {
        "file": "road-g32889351c_1920-15-1024x683.jpg",
        "width": 1024,
        "height": 683,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1024x683.jpg"
      },
      "thumbnail": {
        "file": "road-g32889351c_1920-15-150x150.jpg",
        "width": 150,
        "height": 150,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-150x150.jpg"
      },
      "medium_large": {
        "file": "road-g32889351c_1920-15-768x512.jpg",
        "width": 768,
        "height": 512,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-768x512.jpg"
      },
      "1536x1536": {
        "file": "road-g32889351c_1920-15-1536x1024.jpg",
        "width": 1536,
        "height": 1024,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1536x1024.jpg"
      },
      "full": {
        "file": "road-g32889351c_1920-15.jpg",
        "width": 1920,
        "height": 1280,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg"
      }
    },
    "image_meta": {
      "aperture": "0",
      "credit": "",
      "camera": "",
      "caption": "",
      "created_timestamp": "0",
      "copyright": "",
      "focal_length": "0",
      "iso": "0",
      "shutter_speed": "0",
      "title": "",
      "orientation": "0",
      "keywords": [

      ]
    }
  },
  "post": 127,
  "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg",
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/media/1467"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/media"
      }
    ],
    "about": [
      {
        "href": "https://example.org/wp-json/wp/v2/types/attachment"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
      }
    ],
    "replies": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1467"
      }
    ],
    "comments": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1467"
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/media/1467', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1467,
  "date": "2022-04-25T16:33:30",
  "date_gmt": "2022-04-25T11:03:30",
  "modified": "2022-04-25T16:33:30",
  "modified_gmt": "2022-04-25T11:03:30",
  "slug": "mockup-design-10",
  "status": "inherit",
  "type": "attachment",
  "link": "https://example.org/?attachment_id=1467",
  "title": {
    "rendered": "mockup-design"
  },
  "author": 21,
  "parent": 127,
  "comment_status": "open",
  "ping_status": "closed",
  "meta": {
    "site-sidebar-layout": "default",
    "site-content-layout": "default",
    "ast-main-header-display": "",
    "ast-hfb-above-header-display": "",
    "ast-hfb-below-header-display": "",
    "ast-hfb-mobile-header-display": "",
    "site-post-title": "",
    "ast-breadcrumbs-content": "",
    "ast-featured-img": "",
    "footer-sml-layout": "",
    "theme-transparent-header-meta": "",
    "adv-header-id-meta": "",
    "stick-header-meta": "",
    "header-above-stick-meta": "",
    "header-main-stick-meta": "",
    "header-below-stick-meta": ""
  },
  "model_type": null,
  "description": {
    "rendered": "<p class=\"attachment\"><a href='https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg'><img width=\"300\" height=\"200\" src=\"https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg\" class=\"attachment-medium size-medium\" alt=\"\" loading=\"lazy\" srcset=\"https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg 300w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1024x683.jpg 1024w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-768x512.jpg 768w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1536x1024.jpg 1536w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg 1920w\" sizes=\"(max-width: 300px) 100vw, 300px\" /></a></p>\n<p>Mockup design</p>\n"
  },
  "caption": {
    "rendered": "<p>Mockup design</p>\n"
  },
  "alt_text": "",
  "media_type": "image",
  "mime_type": "image/jpeg",
  "media_details": {
    "width": 1920,
    "height": 1280,
    "file": "2022/04/road-g32889351c_1920-15.jpg",
    "sizes": {
      "medium": {
        "file": "road-g32889351c_1920-15-300x200.jpg",
        "width": 300,
        "height": 200,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg"
      },
      "large": {
        "file": "road-g32889351c_1920-15-1024x683.jpg",
        "width": 1024,
        "height": 683,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1024x683.jpg"
      },
      "thumbnail": {
        "file": "road-g32889351c_1920-15-150x150.jpg",
        "width": 150,
        "height": 150,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-150x150.jpg"
      },
      "medium_large": {
        "file": "road-g32889351c_1920-15-768x512.jpg",
        "width": 768,
        "height": 512,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-768x512.jpg"
      },
      "1536x1536": {
        "file": "road-g32889351c_1920-15-1536x1024.jpg",
        "width": 1536,
        "height": 1024,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1536x1024.jpg"
      },
      "full": {
        "file": "road-g32889351c_1920-15.jpg",
        "width": 1920,
        "height": 1280,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg"
      }
    },
    "image_meta": {
      "aperture": "0",
      "credit": "",
      "camera": "",
      "caption": "",
      "created_timestamp": "0",
      "copyright": "",
      "focal_length": "0",
      "iso": "0",
      "shutter_speed": "0",
      "title": "",
      "orientation": "0",
      "keywords": [

      ]
    }
  },
  "post": 127,
  "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg",
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/media/1467"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/media"
      }
    ],
    "about": [
      {
        "href": "https://example.org/wp-json/wp/v2/types/attachment"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
      }
    ],
    "replies": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1467"
      }
    ],
    "comments": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1467"
      }
    ]
  }
}
```

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

```javascript
const form = new FormData();
form.append("title", "Changed mockup title");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/media/1467', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON ouptut

```json
{
  "id": 1467,
  "date": "2022-04-25T16:33:30",
  "date_gmt": "2022-04-25T11:03:30",
  "modified": "2022-04-25T16:33:30",
  "modified_gmt": "2022-04-25T11:03:30",
  "slug": "mockup-design-10",
  "status": "inherit",
  "type": "attachment",
  "link": "https://example.org/?attachment_id=1467",
  "title": {
    "rendered": "changed-mockup-design-title"
  },
  "author": 21,
  "parent": 127,
  "comment_status": "open",
  "ping_status": "closed",
  "meta": {
    "site-sidebar-layout": "default",
    "site-content-layout": "default",
    "ast-main-header-display": "",
    "ast-hfb-above-header-display": "",
    "ast-hfb-below-header-display": "",
    "ast-hfb-mobile-header-display": "",
    "site-post-title": "",
    "ast-breadcrumbs-content": "",
    "ast-featured-img": "",
    "footer-sml-layout": "",
    "theme-transparent-header-meta": "",
    "adv-header-id-meta": "",
    "stick-header-meta": "",
    "header-above-stick-meta": "",
    "header-main-stick-meta": "",
    "header-below-stick-meta": ""
  },
  "model_type": null,
  "description": {
    "rendered": "<p class=\"attachment\"><a href='https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg'><img width=\"300\" height=\"200\" src=\"https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg\" class=\"attachment-medium size-medium\" alt=\"\" loading=\"lazy\" srcset=\"https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg 300w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1024x683.jpg 1024w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-768x512.jpg 768w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1536x1024.jpg 1536w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg 1920w\" sizes=\"(max-width: 300px) 100vw, 300px\" /></a></p>\n<p>Mockup design</p>\n"
  },
  "caption": {
    "rendered": "<p>Mockup design</p>\n"
  },
  "alt_text": "",
  "media_type": "image",
  "mime_type": "image/jpeg",
  "media_details": {
    "width": 1920,
    "height": 1280,
    "file": "2022/04/road-g32889351c_1920-15.jpg",
    "sizes": {
      "medium": {
        "file": "road-g32889351c_1920-15-300x200.jpg",
        "width": 300,
        "height": 200,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-300x200.jpg"
      },
      "large": {
        "file": "road-g32889351c_1920-15-1024x683.jpg",
        "width": 1024,
        "height": 683,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1024x683.jpg"
      },
      "thumbnail": {
        "file": "road-g32889351c_1920-15-150x150.jpg",
        "width": 150,
        "height": 150,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-150x150.jpg"
      },
      "medium_large": {
        "file": "road-g32889351c_1920-15-768x512.jpg",
        "width": 768,
        "height": 512,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-768x512.jpg"
      },
      "1536x1536": {
        "file": "road-g32889351c_1920-15-1536x1024.jpg",
        "width": 1536,
        "height": 1024,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15-1536x1024.jpg"
      },
      "full": {
        "file": "road-g32889351c_1920-15.jpg",
        "width": 1920,
        "height": 1280,
        "mime_type": "image/jpeg",
        "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg"
      }
    },
    "image_meta": {
      "aperture": "0",
      "credit": "",
      "camera": "",
      "caption": "",
      "created_timestamp": "0",
      "copyright": "",
      "focal_length": "0",
      "iso": "0",
      "shutter_speed": "0",
      "title": "",
      "orientation": "0",
      "keywords": [

      ]
    }
  },
  "post": 127,
  "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-15.jpg",
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/media/1467"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/media"
      }
    ],
    "about": [
      {
        "href": "https://example.org/wp-json/wp/v2/types/attachment"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
      }
    ],
    "replies": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1467"
      }
    ],
    "comments": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1467"
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'DELETE',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/media/1467?force=true', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example JSON output

```json
{
  "deleted": true,
  "previous": {
    "id": 1462,
    "date": "2022-04-25T16:29:47",
    "date_gmt": "2022-04-25T10:59:47",
    "modified": "2022-04-25T16:29:47",
    "modified_gmt": "2022-04-25T10:59:47",
    "slug": "mockup-design-6",
    "status": "inherit",
    "type": "attachment",
    "link": "https://example.org/mockup/huddle-mock/mockup-design-6/",
    "title": {
      "raw": "mockup-design",
      "rendered": "mockup-design"
    },
    "author": 21,
    "parent": 180,
    "comment_status": "open",
    "ping_status": "closed",
    "meta": {
      "site-sidebar-layout": "default",
      "site-content-layout": "default",
      "ast-main-header-display": "",
      "ast-hfb-above-header-display": "",
      "ast-hfb-below-header-display": "",
      "ast-hfb-mobile-header-display": "",
      "site-post-title": "",
      "ast-breadcrumbs-content": "",
      "ast-featured-img": "",
      "footer-sml-layout": "",
      "theme-transparent-header-meta": "",
      "adv-header-id-meta": "",
      "stick-header-meta": "",
      "header-above-stick-meta": "",
      "header-main-stick-meta": "",
      "header-below-stick-meta": ""
    },
    "model_type": null,
    "description": {
      "raw": "Mockup design",
      "rendered": "<p class=\"attachment\"><a href='https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11.jpg'><img width=\"300\" height=\"200\" src=\"https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-300x200.jpg\" class=\"attachment-medium size-medium\" alt=\"\" loading=\"lazy\" srcset=\"https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-300x200.jpg 300w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-1024x683.jpg 1024w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-768x512.jpg 768w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-1536x1024.jpg 1536w, https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11.jpg 1920w\" sizes=\"(max-width: 300px) 100vw, 300px\" /></a></p>\n<p>Mockup design</p>\n"
    },
    "caption": {
      "raw": "",
      "rendered": "<p>Mockup design</p>\n"
    },
    "alt_text": "",
    "media_type": "image",
    "mime_type": "image/jpeg",
    "media_details": {
      "width": 1920,
      "height": 1280,
      "file": "2022/04/road-g32889351c_1920-11.jpg",
      "sizes": {
        "medium": {
          "file": "road-g32889351c_1920-11-300x200.jpg",
          "width": 300,
          "height": 200,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-300x200.jpg"
        },
        "large": {
          "file": "road-g32889351c_1920-11-1024x683.jpg",
          "width": 1024,
          "height": 683,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-1024x683.jpg"
        },
        "thumbnail": {
          "file": "road-g32889351c_1920-11-150x150.jpg",
          "width": 150,
          "height": 150,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-150x150.jpg"
        },
        "medium_large": {
          "file": "road-g32889351c_1920-11-768x512.jpg",
          "width": 768,
          "height": 512,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-768x512.jpg"
        },
        "1536x1536": {
          "file": "road-g32889351c_1920-11-1536x1024.jpg",
          "width": 1536,
          "height": 1024,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11-1536x1024.jpg"
        },
        "full": {
          "file": "road-g32889351c_1920-11.jpg",
          "width": 1920,
          "height": 1280,
          "mime_type": "image/jpeg",
          "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11.jpg"
        }
      },
      "image_meta": {
        "aperture": "0",
        "credit": "",
        "camera": "",
        "caption": "",
        "created_timestamp": "0",
        "copyright": "",
        "focal_length": "0",
        "iso": "0",
        "shutter_speed": "0",
        "title": "",
        "orientation": "0",
        "keywords": [

        ]
      }
    },
    "post": 180,
    "source_url": "https://example.org/wp-content/uploads/2022/04/road-g32889351c_1920-11.jpg"
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/comments', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
    "id": 163,
    "post": 1431,
    "parent": 0,
    "author": 17,
    "author_name": "John",
    "author_url": "",
    "date": "2022-04-20T14:27:38",
    "date_gmt": "2022-04-20T08:57:38",
    "content": {
      "rendered": "<p>Test comment content</p>"
    },
    "link": "https://example.org/website-thread/1431/",
    "status": "approved",
    "type": "ph_comment",
    "project_id": 1427,
    "item_id": 1429,
    "author_avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/3ce8a94b6c1e73122d0850929abc507b?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/3ce8a94b6c1e73122d0850929abc507b?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/3ce8a94b6c1e73122d0850929abc507b?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "comment_post_type": "thread",
    "approval": false,
    "is_private": false,
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/comments/163"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/comments"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/wp/v2/users/17"
        }
      ],
      "up": [
        {
          "embeddable": true,
          "post_type": "phw_comment_loc",
          "href": "https://example.org/wp-json/projecthuddle/v2/website-thread/1431"
        }
      ],
      "item": [
        {
          "embeddable": true,
          "post_type": "website-page",
          "href": "https://example.org/wp-json/projecthuddle/v2/website-page/1429"
        }
      ],
      "project": [
        {
          "embeddable": true,
          "post_type": "website",
          "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
        }
      ]
    }
  },
  {
    "id": 162,
    "post": 1430,
    "parent": 0,
    "author": 17,
    "author_name": "John",
    "author_url": "",
    "date": "2022-04-20T14:21:04",
    "date_gmt": "2022-04-20T08:51:04",
    "content": {
      "rendered": "<p>Test comment response</p>"
    },
    "link": "https://example.org/website-thread/1430/",
    "status": "approved",
    "type": "ph_comment",
    "project_id": 1427,
    "item_id": 1429,
    "author_avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/3ce8a94b6c1e73122d0850929abc507b?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/3ce8a94b6c1e73122d0850929abc507b?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/3ce8a94b6c1e73122d0850929abc507b?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "comment_post_type": "thread",
    "approval": false,
    "is_private": false,
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/comments/162"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/comments"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/wp/v2/users/17"
        }
      ],
      "up": [
        {
          "embeddable": true,
          "post_type": "phw_comment_loc",
          "href": "https://example.org/wp-json/projecthuddle/v2/website-thread/1430"
        }
      ],
      "item": [
        {
          "embeddable": true,
          "post_type": "website-page",
          "href": "https://example.org/wp-json/projecthuddle/v2/website-page/1429"
        }
      ],
      "project": [
        {
          "embeddable": true,
          "post_type": "website",
          "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
        }
      ]
    }
  },
```

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

```javascript
const form = new FormData();
form.append("content", "Comment content");
form.append("status", "approved");
form.append("post", "1427");
form.append("author", "21");
form.append("parent", "0");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/comments', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON ouput

```json
{
  "id": 171,
  "post": 1427,
  "parent": 0,
  "author": 21,
  "author_name": "ph-admin",
  "author_email": "ph-admin@ph.com",
  "author_url": "",
  "author_ip": "172.29.0.6",
  "date": "2022-04-25T17:20:06",
  "date_gmt": "2022-04-25T11:50:06",
  "content": {
    "rendered": "Comment content",
    "raw": "Comment content"
  },
  "link": "https://example.org/website/1427/",
  "status": "approved",
  "type": "ph_comment",
  "project_id": 1427,
  "item_id": 0,
  "author_avatar_urls": {
    "24": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=24&d=mm&r=g",
    "48": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=48&d=mm&r=g",
    "96": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=96&d=mm&r=g"
  },
  "meta": [

  ],
  "comment_post_type": "thread",
  "approval": false,
  "is_private": false,
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments/171"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/wp/v2/users/21"
      }
    ],
    "up": [
      {
        "embeddable": true,
        "post_type": "ph-website",
        "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
      }
    ],
    "project": [
      {
        "embeddable": true,
        "post_type": "website",
        "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/comments/171', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 171,
  "post": 1427,
  "parent": 0,
  "author": 21,
  "author_name": "ph-admin",
  "author_url": "",
  "date": "2022-04-25T17:20:06",
  "date_gmt": "2022-04-25T11:50:06",
  "content": {
    "rendered": "Comment content"
  },
  "link": "https://example.org/website/1427/",
  "status": "approved",
  "type": "ph_comment",
  "project_id": 1427,
  "item_id": 0,
  "author_avatar_urls": {
    "24": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=24&d=mm&r=g",
    "48": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=48&d=mm&r=g",
    "96": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=96&d=mm&r=g"
  },
  "meta": [

  ],
  "comment_post_type": "thread",
  "approval": false,
  "is_private": false,
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments/171"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/wp/v2/users/21"
      }
    ],
    "up": [
      {
        "embeddable": true,
        "post_type": "ph-website",
        "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
      }
    ],
    "project": [
      {
        "embeddable": true,
        "post_type": "website",
        "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
      }
    ]
  }
}
```

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

```javascript
const form = new FormData();
form.append("content", "Changed content");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/comments/171', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 171,
  "post": 1427,
  "parent": 0,
  "author": 21,
  "author_name": "ph-admin",
  "author_email": "ph-admin@ph.com",
  "author_url": "",
  "author_ip": "172.29.0.6",
  "author_user_agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.88 Safari/537.36",
  "date": "2022-04-25T17:09:10",
  "date_gmt": "2022-04-25T11:39:10",
  "content": {
    "rendered": "Changed content",
    "raw": "Changed content"
  },
  "link": "https://example.org/website/1427/",
  "status": "approved",
  "type": "ph_comment",
  "project_id": 1427,
  "item_id": 0,
  "author_avatar_urls": {
    "24": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=24&d=mm&r=g",
    "48": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=48&d=mm&r=g",
    "96": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=96&d=mm&r=g"
  },
  "meta": [

  ],
  "comment_post_type": "thread",
  "approval": false,
  "is_private": false,
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments/170"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/wp/v2/users/21"
      }
    ],
    "up": [
      {
        "embeddable": true,
        "post_type": "ph-website",
        "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
      }
    ],
    "project": [
      {
        "embeddable": true,
        "post_type": "website",
        "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'DELETE',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/comments/170', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 170,
  "post": 1427,
  "parent": 0,
  "author": 21,
  "author_name": "ph-admin",
  "author_email": "ph-admin@ph.com",
  "author_url": "",
  "author_ip": "172.29.0.6",
  "date_gmt": "2022-04-25T11:39:10",
  "content": {
    "rendered": "Changed content",
    "raw": "Changed content"
  },
  "link": "https://example.org/website/1427/",
  "status": "trash",
  "type": "ph_comment",
  "project_id": 1427,
  "item_id": 0,
  "author_avatar_urls": {
    "24": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=24&d=mm&r=g",
    "48": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=48&d=mm&r=g",
    "96": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=96&d=mm&r=g"
  },
  "meta": [

  ],
  "comment_post_type": "project",
  "approval": false,
  "is_private": false,
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments/170"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/comments"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/wp/v2/users/21"
      }
    ],
    "up": [
      {
        "embeddable": true,
        "post_type": "ph-website",
        "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
      }
    ],
    "project": [
      {
        "embeddable": true,
        "post_type": "website",
        "href": "https://example.org/wp-json/projecthuddle/v2/website/1427"
      }
    ]
  }
}
```

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

```javascript
const options = {method: 'GET'};

fetch('https://example.org/wp-json/projecthuddle/v2/mockup', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON ouput

```json
[
  {
    "id": 180,
    "date": "2022-03-29T09:24:23",
    "date_gmt": "2022-03-29T03:54:23",
    "modified": "2022-04-26T10:28:25",
    "modified_gmt": "2022-04-26T04:58:25",
    "slug": "huddle-mock",
    "status": "publish",
    "type": "ph-project",
    "link": "https://example.org/mockup/huddle-mock/",
    "title": {
      "rendered": "Huddle Mock"
    },
    "content": {
      "rendered": "",
      "protected": false
    },
    "author": 16,
    "parent": 0,
    "meta": {
    },
    "model_type": "mockup",
    "ph_short_link": "https://example.org/?p=180",
    "project_access": "login",
    "thread_subscribers": "all",
    "retina": false,
    "sharing": true,
    "zoom": true,
    "tooltip": true,
    "allow_guests": true,
    "force_login": true,
    "project_download": false,
    "project_comments": false,
    "project_approval": true,
    "project_unapproval": true,
    "access_token": "",
    "project_members": [
      17,
      22
    ],
    "resolve_status": {
      "total": 0,
      "resolved": 0,
      "by": false,
      "on": false
    },
    "items_status": {
      "total": 1,
      "approved": 0,
      "by": false,
      "on": false
    },
    "approved": false,
    "me": {
      "code": "rest_not_logged_in",
      "message": "You are not currently logged in.",
      "data": {
        "status": 401
      }
    },
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup/180"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup"
        }
      ],
      "about": [
        {
          "href": "https://example.org/wp-json/wp/v2/types/ph-project"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "members": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/users?include=17,22&per_page=100"
        }
      ],
      "approval_history": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=180&per_page=5&type=approval"
        }
      ],
      "images": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image?parent_id=180"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://example.org/wp-json/wp/v2/media?parent=180"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
  {
    "id": 97,
    "date": "2022-03-24T15:27:22",
    "date_gmt": "2022-03-24T09:57:22",
    "modified": "2022-04-25T16:30:29",
    "modified_gmt": "2022-04-25T11:00:29",
    "slug": "test-mock",
    "status": "publish",
    "type": "ph-project",
    "link": "https://example.org/mockup/test-mock/",
    "title": {
      "rendered": "Test mock"
    },
    "content": {
      "rendered": "",
      "protected": true
    },
    "author": 16,
    "parent": 0,
    "meta": {
      "site-sidebar-layout": "default",
      "site-content-layout": "default",
      "ast-main-header-display": "",
      "ast-hfb-above-header-display": "",
      "ast-hfb-below-header-display": "",
      "ast-hfb-mobile-header-display": "",
      "site-post-title": "",
      "ast-breadcrumbs-content": "",
      "ast-featured-img": "",
      "footer-sml-layout": "",
      "theme-transparent-header-meta": "default",
      "adv-header-id-meta": "",
      "stick-header-meta": "",
      "header-above-stick-meta": "",
      "header-main-stick-meta": "",
      "header-below-stick-meta": ""
    },
    "model_type": "mockup",
    "ph_short_link": "https://example.org/?p=97",
    "project_access": "login",
    "thread_subscribers": "all",
    "retina": false,
    "sharing": true,
    "zoom": true,
    "tooltip": true,
    "allow_guests": true,
    "force_login": true,
    "project_download": false,
    "project_comments": false,
    "project_approval": true,
    "project_unapproval": true,
    "access_token": "",
    "project_members": [

    ],
    "resolve_status": {
      "total": 0,
      "resolved": 0,
      "by": false,
      "on": false
    },
    "items_status": {
      "total": 0,
      "approved": 0,
      "by": false,
      "on": false
    },
    "approved": false,
    "me": {
      "code": "rest_not_logged_in",
      "message": "You are not currently logged in.",
      "data": {
        "status": 401
      }
    },
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup/97"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup"
        }
      ],
      "about": [
        {
          "href": "https://example.org/wp-json/wp/v2/types/ph-project"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "members": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/users?include=&per_page=100"
        }
      ],
      "approval_history": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=97&per_page=5&type=approval"
        }
      ],
      "images": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image?parent_id=97"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://example.org/wp-json/wp/v2/media?parent=97"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  }
]
```

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

```javascript
const form = new FormData();
form.append("title", "New mockup");
form.append("author", "21");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/mockup', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1471,
  "date": "2022-04-26T14:53:29",
  "date_gmt": "2022-04-26T09:23:29",
  "modified": "2022-04-26T14:53:29",
  "modified_gmt": "2022-04-26T09:23:29",
  "password": "",
  "slug": "new-mockup",
  "status": "publish",
  "type": "ph-project",
  "link": "https://example.org/mockup/new-mockup/?access_token=43c38077aaf19758c4f7d0f26cad8281",
  "title": {
    "raw": "New mockup",
    "rendered": "New mockup"
  },
  "content": {
    "raw": "",
    "rendered": "",
    "protected": false
  },
  "author": 21,
  "parent": 0,
  "meta": {
  },
  "model_type": "mockup",
  "project_access": "login",
  "thread_subscribers": "all",
  "retina": false,
  "sharing": true,
  "zoom": true,
  "tooltip": true,
  "allow_guests": false,
  "force_login": false,
  "project_download": false,
  "project_comments": false,
  "project_approval": true,
  "project_unapproval": true,
  "access_token": "43c38077aaf19758c4f7d0f26cad8281",
  "project_members": [

  ],
  "resolve_status": {
    "total": 0,
    "resolved": 0,
    "by": false,
    "on": false
  },
  "items_status": {
    "total": 0,
    "approved": 0,
    "by": false,
    "on": false
  },
  "approved": false,
  "me": {
    "id": 21,
    "username": "ph-admin",
    "name": "ph-admin",
    "first_name": "",
    "last_name": "",
    "email": "ph-admin@ph.com",
    "url": "",
    "description": "",
    "link": "https://example.org/author/ph-admin/",
    "locale": "en_US",
    "nickname": "ph-admin",
    "slug": "ph-admin",
    "roles": [
      "project_admin"
    ],
    "user_role": "Project Administrator",
    "registered_date": "2022-04-18T10:03:45+00:00",
    "capabilities": {
      "read": true,
      "moderate_comments": true,
      "edit_comment_meta": true,
      "upload_files": true,
      "list_users": true,
      "manage_ph_settings": true,
      "publish_ph_comment_locations": true,
      "edit_ph_comment_locations": true,
      "edit_others_ph_comment_locations": true,
      "read_private_ph_comment_locations": true,
      "delete_ph_comment_locations": true,
      "delete_private_ph_comment_locations": true,
      "delete_published_ph_comment_locations": true,
      "edit_private_ph_comment_locations": true,
      "edit_published_ph_comment_locations": true,
      "publish_project_images": true,
      "publish_ph-webpages": true,
      "edit_ph-webpages": true,
      "edit_others_ph-webpages": true,
      "read_private_ph-webpages": true,
      "delete_ph-webpages": true,
      "delete_private_ph-webpages": true,
      "delete_published_ph-webpages": true,
      "edit_private_ph-webpages": true,
      "edit_published_ph-webpages": true,
      "publish_phw_comment_locs": true,
      "edit_phw_comment_locs": true,
      "edit_others_phw_comment_locs": true,
      "read_private_phw_comment_locs": true,
      "delete_phw_comment_locs": true,
      "delete_private_phw_comment_locs": true,
      "delete_published_phw_comment_locs": true,
      "edit_private_phw_comment_locs": true,
      "edit_published_phw_comment_locs": true,
      "read_ph-project": true,
      "read_ph-projects": true,
      "read_private_ph-projects": true,
      "read_ph_version": true,
      "read_ph_versions": true,
      "read_private_ph_versions": true,
      "read_ph-website": true,
      "read_ph-websites": true,
      "read_private_ph-websites": true,
      "create_ph-projects": true,
      "delete_ph-project": true,
      "edit_ph-projects": true,
      "edit_others_ph-projects": true,
      "publish_ph-projects": true,
      "delete_ph-projects": true,
      "delete_private_ph-projects": true,
      "delete_published_ph-projects": true,
      "delete_others_ph-projects": true,
      "edit_private_ph-projects": true,
      "edit_published_ph-projects": true,
      "view_unsubscribed_ph-projects": true,
      "manage_ph-project_terms": true,
      "edit_ph-project_terms": true,
      "delete_ph-project_terms": true,
      "assign_ph-project_terms": true,
      "create_ph_versions": true,
      "delete_ph_version": true,
      "edit_ph_versions": true,
      "edit_others_ph_versions": true,
      "publish_ph_versions": true,
      "delete_ph_versions": true,
      "delete_private_ph_versions": true,
      "delete_published_ph_versions": true,
      "delete_others_ph_versions": true,
      "edit_private_ph_versions": true,
      "edit_published_ph_versions": true,
      "view_unsubscribed_ph_versions": true,
      "manage_ph_version_terms": true,
      "edit_ph_version_terms": true,
      "delete_ph_version_terms": true,
      "assign_ph_version_terms": true,
      "create_ph-websites": true,
      "delete_ph-website": true,
      "edit_ph-websites": true,
      "edit_others_ph-websites": true,
      "publish_ph-websites": true,
      "delete_ph-websites": true,
      "delete_private_ph-websites": true,
      "delete_published_ph-websites": true,
      "delete_others_ph-websites": true,
      "edit_private_ph-websites": true,
      "edit_published_ph-websites": true,
      "view_unsubscribed_ph-websites": true,
      "manage_ph-website_terms": true,
      "edit_ph-website_terms": true,
      "delete_ph-website_terms": true,
      "assign_ph-website_terms": true,
      "delete_others_ph_comment_locations": true,
      "delete_others_project_images": true,
      "delete_others_ph-webpages": true,
      "delete_others_phw_comment_locs": true,
      "project_admin": true
    },
    "extra_capabilities": {
      "project_admin": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup/1471"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup"
      }
    ],
    "about": [
      {
        "href": "https://example.org/wp-json/wp/v2/types/ph-project"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
      }
    ],
    "members": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users?include=&per_page=100"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1471&per_page=5&type=approval"
      }
    ],
    "images": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image?parent_id=1471"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://example.org/wp-json/wp/v2/media?parent=1471"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/mockup/180', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON ouput

```json
{
  "id": 180,
  "date": "2022-03-29T09:24:23",
  "date_gmt": "2022-03-29T03:54:23",
  "modified": "2022-04-26T10:28:25",
  "modified_gmt": "2022-04-26T04:58:25",
  "slug": "huddle-mock",
  "status": "publish",
  "type": "ph-project",
  "link": "https://example.org/mockup/huddle-mock/?access_token=ce635f81fd0b4c77decf759a9919deec",
  "title": {
    "rendered": "Huddle Mock"
  },
  "content": {
    "rendered": "",
    "protected": false
  },
  "author": 16,
  "parent": 0,
  "meta": {
    "site-sidebar-layout": "default",
    "site-content-layout": "default",
    "ast-main-header-display": "",
    "ast-hfb-above-header-display": "",
    "ast-hfb-below-header-display": "",
    "ast-hfb-mobile-header-display": "",
    "site-post-title": "",
    "ast-breadcrumbs-content": "",
    "ast-featured-img": "",
    "footer-sml-layout": "",
    "theme-transparent-header-meta": "default",
    "adv-header-id-meta": "",
    "stick-header-meta": "",
    "header-above-stick-meta": "",
    "header-main-stick-meta": "",
    "header-below-stick-meta": ""
  },
  "model_type": "mockup",
  "ph_short_link": "https://example.org/?p=180",
  "project_access": "login",
  "thread_subscribers": "all",
  "retina": false,
  "sharing": true,
  "zoom": true,
  "tooltip": true,
  "allow_guests": true,
  "force_login": true,
  "project_download": false,
  "project_comments": false,
  "project_approval": true,
  "project_unapproval": true,
  "access_token": "ce635f81fd0b4c77decf759a9919deec",
  "project_members": [
    17,
    22
  ],
  "resolve_status": {
    "total": 0,
    "resolved": 0,
    "by": false,
    "on": false
  },
  "items_status": {
    "total": 1,
    "approved": 0,
    "by": false,
    "on": false
  },
  "approved": false,
  "me": {
    "id": 21,
    "username": "ph-admin",
    "name": "ph-admin",
    "first_name": "",
    "last_name": "",
    "email": "ph-admin@ph.com",
    "url": "",
    "description": "",
    "link": "https://example.org/author/ph-admin/",
    "locale": "en_US",
    "nickname": "ph-admin",
    "slug": "ph-admin",
    "roles": [
      "project_admin"
    ],
    "user_role": "Project Administrator",
    "registered_date": "2022-04-18T10:03:45+00:00",
    "capabilities": {
      "read": true,
      "moderate_comments": true,
      "edit_comment_meta": true,
      "upload_files": true,
      "list_users": true,
      "manage_ph_settings": true,
      "publish_ph_comment_locations": true,
      "edit_ph_comment_locations": true,
      "edit_others_ph_comment_locations": true,
      "read_private_ph_comment_locations": true,
      "delete_ph_comment_locations": true,
      "delete_private_ph_comment_locations": true,
      "delete_published_ph_comment_locations": true,
      "edit_private_ph_comment_locations": true,
      "edit_published_ph_comment_locations": true,
      "publish_ph-webpages": true,
      "edit_ph-webpages": true,
      "edit_others_ph-webpages": true,
      "read_private_ph-webpages": true,
      "delete_ph-webpages": true,
      "delete_private_ph-webpages": true,
      "delete_published_ph-webpages": true,
      "edit_private_ph-webpages": true,
      "edit_published_ph-webpages": true,
      "publish_phw_comment_locs": true,
      "edit_phw_comment_locs": true,
      "edit_others_phw_comment_locs": true,
      "read_private_phw_comment_locs": true,
      "delete_phw_comment_locs": true,
      "delete_private_phw_comment_locs": true,
      "delete_published_phw_comment_locs": true,
      "edit_private_phw_comment_locs": true,
      "edit_published_phw_comment_locs": true,
      "read_ph-project": true,
      "read_ph-projects": true,
      "read_private_ph-projects": true,
      "read_ph_version": true,
      "read_ph_versions": true,
      "read_private_ph_versions": true,
      "read_ph-website": true,
      "read_ph-websites": true,
      "read_private_ph-websites": true,
      "create_ph-projects": true,
      "delete_ph-project": true,
      "edit_ph-projects": true,
      "edit_others_ph-projects": true,
      "publish_ph-projects": true,
      "delete_ph-projects": true,
      "delete_private_ph-projects": true,
      "delete_published_ph-projects": true,
      "delete_others_ph-projects": true,
      "edit_private_ph-projects": true,
      "edit_published_ph-projects": true,
      "view_unsubscribed_ph-projects": true,
      "manage_ph-project_terms": true,
      "edit_ph-project_terms": true,
      "delete_ph-project_terms": true,
      "assign_ph-project_terms": true,
      "create_ph_versions": true,
      "delete_ph_version": true,
      "edit_ph_versions": true,
      "edit_others_ph_versions": true,
      "publish_ph_versions": true,
      "delete_ph_versions": true,
      "delete_private_ph_versions": true,
      "delete_published_ph_versions": true,
      "delete_others_ph_versions": true,
      "edit_private_ph_versions": true,
      "edit_published_ph_versions": true,
      "view_unsubscribed_ph_versions": true,
      "manage_ph_version_terms": true,
      "edit_ph_version_terms": true,
      "delete_ph_version_terms": true,
      "assign_ph_version_terms": true,
      "create_ph-websites": true,
      "delete_ph-website": true,
      "edit_ph-websites": true,
      "edit_others_ph-websites": true,
      "publish_ph-websites": true,
      "delete_ph-websites": true,
      "delete_private_ph-websites": true,
      "delete_published_ph-websites": true,
      "delete_others_ph-websites": true,
      "edit_private_ph-websites": true,
      "edit_published_ph-websites": true,
      "view_unsubscribed_ph-websites": true,
      "manage_ph-website_terms": true,
      "edit_ph-website_terms": true,
      "delete_ph-website_terms": true,
      "assign_ph-website_terms": true,
      "delete_others_ph_comment_locations": true,
      "delete_others_project_images": true,
      "delete_others_ph-webpages": true,
      "delete_others_phw_comment_locs": true,
      "project_admin": true
    },
    "extra_capabilities": {
      "project_admin": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup/180"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup"
      }
    ],
    "about": [
      {
        "href": "https://example.org/wp-json/wp/v2/types/ph-project"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "members": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users?include=17,22&per_page=100"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=180&per_page=5&type=approval"
      }
    ],
    "images": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image?parent_id=180"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://example.org/wp-json/wp/v2/media?parent=180"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const form = new FormData();
form.append("access_link_login", "true");
form.append("sharing", "true");
form.append("project_comments", "true");
form.append("retina", "true");
form.append("project_members", "16, 18, 21");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/mockup/1471', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1471,
  "date": "2022-04-26T14:53:29",
  "date_gmt": "2022-04-26T09:23:29",
  "modified": "2022-04-26T15:11:19",
  "modified_gmt": "2022-04-26T09:41:19",
  "password": "",
  "slug": "new-mockup",
  "status": "publish",
  "type": "ph-project",
  "link": "https://example.org/mockup/new-mockup/?access_token=43c38077aaf19758c4f7d0f26cad8281",
  "title": {
    "raw": "New mockup",
    "rendered": "New mockup"
  },
  "content": {
    "raw": "",
    "rendered": "",
    "protected": false
  },
  "author": 21,
  "parent": 0,
  "meta": {
  },
  "model_type": "mockup",
  "project_access": "",
  "thread_subscribers": "all",
  "retina": true,
  "sharing": true,
  "zoom": true,
  "tooltip": true,
  "allow_guests": false,
  "force_login": false,
  "project_download": false,
  "project_comments": true,
  "project_approval": true,
  "project_unapproval": true,
  "access_token": "43c38077aaf19758c4f7d0f26cad8281",
  "project_members": [
    16,
    18,
    20,
    21
  ],
  "resolve_status": {
    "total": 0,
    "resolved": 0,
    "by": false,
    "on": false
  },
  "items_status": {
    "total": 0,
    "approved": 0,
    "by": false,
    "on": false
  },
  "approved": false,
  "me": {
    "id": 21,
    "username": "ph-admin",
    "name": "ph-admin",
    "first_name": "",
    "last_name": "",
    "email": "ph-admin@ph.com",
    "url": "",
    "description": "",
    "link": "https://example.org/author/ph-admin/",
    "locale": "en_US",
    "nickname": "ph-admin",
    "slug": "ph-admin",
    "roles": [
      "project_admin"
    ],
    "user_role": "Project Administrator",
    "registered_date": "2022-04-18T10:03:45+00:00",
    "capabilities": {
      "read": true,
      "moderate_comments": true,
      "edit_comment_meta": true,
      "upload_files": true,
      "list_users": true,
      "manage_ph_settings": true,
      "publish_ph_comment_locations": true,
      "edit_ph_comment_locations": true,
      "edit_others_ph_comment_locations": true,
      "read_private_ph_comment_locations": true,
      "delete_ph_comment_locations": true,
      "delete_private_ph_comment_locations": true,
      "delete_published_ph_comment_locations": true,
      "edit_private_ph_comment_locations": true,
      "edit_published_ph_comment_locations": true,
      "publish_ph-webpages": true,
      "edit_ph-webpages": true,
      "edit_others_ph-webpages": true,
      "read_private_ph-webpages": true,
      "delete_ph-webpages": true,
      "delete_private_ph-webpages": true,
      "delete_published_ph-webpages": true,
      "edit_private_ph-webpages": true,
      "edit_published_ph-webpages": true,
      "publish_phw_comment_locs": true,
      "edit_phw_comment_locs": true,
      "edit_others_phw_comment_locs": true,
      "read_private_phw_comment_locs": true,
      "delete_phw_comment_locs": true,
      "delete_private_phw_comment_locs": true,
      "delete_published_phw_comment_locs": true,
      "edit_private_phw_comment_locs": true,
      "edit_published_phw_comment_locs": true,
      "read_ph-project": true,
      "read_ph-projects": true,
      "read_private_ph-projects": true,
      "read_ph_version": true,
      "read_ph_versions": true,
      "read_private_ph_versions": true,
      "read_ph-website": true,
      "read_ph-websites": true,
      "read_private_ph-websites": true,
      "create_ph-projects": true,
      "delete_ph-project": true,
      "edit_ph-projects": true,
      "edit_others_ph-projects": true,
      "publish_ph-projects": true,
      "delete_ph-projects": true,
      "delete_private_ph-projects": true,
      "delete_published_ph-projects": true,
      "delete_others_ph-projects": true,
      "edit_private_ph-projects": true,
      "edit_published_ph-projects": true,
      "view_unsubscribed_ph-projects": true,
      "manage_ph-project_terms": true,
      "edit_ph-project_terms": true,
      "delete_ph-project_terms": true,
      "assign_ph-project_terms": true,
      "create_ph_versions": true,
      "delete_ph_version": true,
      "edit_ph_versions": true,
      "edit_others_ph_versions": true,
      "publish_ph_versions": true,
      "delete_ph_versions": true,
      "delete_private_ph_versions": true,
      "delete_published_ph_versions": true,
      "delete_others_ph_versions": true,
      "edit_private_ph_versions": true,
      "edit_published_ph_versions": true,
      "view_unsubscribed_ph_versions": true,
      "manage_ph_version_terms": true,
      "edit_ph_version_terms": true,
      "delete_ph_version_terms": true,
      "assign_ph_version_terms": true,
      "create_ph-websites": true,
      "delete_ph-website": true,
      "edit_ph-websites": true,
      "edit_others_ph-websites": true,
      "publish_ph-websites": true,
      "delete_ph-websites": true,
      "delete_private_ph-websites": true,
      "delete_published_ph-websites": true,
      "delete_others_ph-websites": true,
      "edit_private_ph-websites": true,
      "edit_published_ph-websites": true,
      "view_unsubscribed_ph-websites": true,
      "manage_ph-website_terms": true,
      "edit_ph-website_terms": true,
      "delete_ph-website_terms": true,
      "assign_ph-website_terms": true,
      "delete_others_ph_comment_locations": true,
      "delete_others_project_images": true,
      "delete_others_ph-webpages": true,
      "delete_others_phw_comment_locs": true,
      "project_admin": true
    },
    "extra_capabilities": {
      "project_admin": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup/1471"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup"
      }
    ],
    "about": [
      {
        "href": "https://example.org/wp-json/wp/v2/types/ph-project"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
      }
    ],
    "members": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users?include=16,18,20,21&per_page=100"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1471&per_page=5&type=approval"
      }
    ],
    "images": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image?parent_id=1471"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://example.org/wp-json/wp/v2/media?parent=1471"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'DELETE',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOEKN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/mockup/1471', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1471,
  "date": "2022-04-26T14:53:29",
  "date_gmt": "2022-04-26T09:23:29",
  "modified": "2022-04-26T15:13:08",
  "modified_gmt": "2022-04-26T09:43:08",
  "password": "",
  "slug": "new-mockup__trashed",
  "status": "trash",
  "type": "ph-project",
  "link": "https://example.org/?post_type=ph-project&p=1471&access_token=43c38077aaf19758c4f7d0f26cad8281",
  "title": {
    "raw": "New mockup",
    "rendered": "New mockup"
  },
  "content": {
    "raw": "",
    "rendered": "",
    "protected": false
  },
  "author": 21,
  "parent": 0,
  "meta": {
    "site-sidebar-layout": "default",
    "site-content-layout": "default",
    "ast-main-header-display": "",
    "ast-hfb-above-header-display": "",
    "ast-hfb-below-header-display": "",
    "ast-hfb-mobile-header-display": "",
    "site-post-title": "",
    "ast-breadcrumbs-content": "",
    "ast-featured-img": "",
    "footer-sml-layout": "",
    "theme-transparent-header-meta": "",
    "adv-header-id-meta": "",
    "stick-header-meta": "",
    "header-above-stick-meta": "",
    "header-main-stick-meta": "",
    "header-below-stick-meta": ""
  },
  "model_type": "mockup",
  "project_access": "",
  "thread_subscribers": "all",
  "retina": true,
  "sharing": true,
  "zoom": true,
  "tooltip": true,
  "allow_guests": false,
  "force_login": false,
  "project_download": false,
  "project_comments": true,
  "project_approval": true,
  "project_unapproval": true,
  "access_token": "43c38077aaf19758c4f7d0f26cad8281",
  "project_members": [
    16,
    18,
    20,
    21
  ],
  "resolve_status": {
    "total": 0,
    "resolved": 0,
    "by": false,
    "on": false
  },
  "items_status": {
    "total": 0,
    "approved": 0,
    "by": false,
    "on": false
  },
  "approved": false,
  "me": {
    "id": 21,
    "username": "ph-admin",
    "name": "ph-admin",
    "first_name": "",
    "last_name": "",
    "email": "ph-admin@ph.com",
    "url": "",
    "description": "",
    "link": "https://example.org/author/ph-admin/",
    "locale": "en_US",
    "nickname": "ph-admin",
    "slug": "ph-admin",
    "roles": [
      "project_admin"
    ],
    "user_role": "Project Administrator",
    "registered_date": "2022-04-18T10:03:45+00:00",
    "capabilities": {
      "read": true,
      "moderate_comments": true,
      "edit_comment_meta": true,
      "upload_files": true,
      "list_users": true,
      "manage_ph_settings": true,
      "publish_ph_comment_locations": true,
      "edit_ph_comment_locations": true,
      "edit_others_ph_comment_locations": true,
      "read_private_ph_comment_locations": true,
      "delete_ph_comment_locations": true,
      "delete_private_ph_comment_locations": true,
      "delete_published_ph_comment_locations": true,
      "edit_private_ph_comment_locations": true,
      "edit_published_ph_comment_locations": true,
      "publish_ph-webpages": true,
      "edit_ph-webpages": true,
      "edit_others_ph-webpages": true,
      "read_private_ph-webpages": true,
      "delete_ph-webpages": true,
      "delete_private_ph-webpages": true,
      "delete_published_ph-webpages": true,
      "edit_private_ph-webpages": true,
      "edit_published_ph-webpages": true,
      "publish_phw_comment_locs": true,
      "edit_phw_comment_locs": true,
      "edit_others_phw_comment_locs": true,
      "read_private_phw_comment_locs": true,
      "delete_phw_comment_locs": true,
      "delete_private_phw_comment_locs": true,
      "delete_published_phw_comment_locs": true,
      "edit_private_phw_comment_locs": true,
      "edit_published_phw_comment_locs": true,
      "read_ph-project": true,
      "read_ph-projects": true,
      "read_private_ph-projects": true,
      "read_ph_version": true,
      "read_ph_versions": true,
      "read_private_ph_versions": true,
      "read_ph-website": true,
      "read_ph-websites": true,
      "read_private_ph-websites": true,
      "create_ph-projects": true,
      "delete_ph-project": true,
      "edit_ph-projects": true,
      "edit_others_ph-projects": true,
      "publish_ph-projects": true,
      "delete_ph-projects": true,
      "delete_private_ph-projects": true,
      "delete_published_ph-projects": true,
      "delete_others_ph-projects": true,
      "edit_private_ph-projects": true,
      "edit_published_ph-projects": true,
      "view_unsubscribed_ph-projects": true,
      "manage_ph-project_terms": true,
      "edit_ph-project_terms": true,
      "delete_ph-project_terms": true,
      "assign_ph-project_terms": true,
      "create_ph_versions": true,
      "delete_ph_version": true,
      "edit_ph_versions": true,
      "edit_others_ph_versions": true,
      "publish_ph_versions": true,
      "delete_ph_versions": true,
      "delete_private_ph_versions": true,
      "delete_published_ph_versions": true,
      "delete_others_ph_versions": true,
      "edit_private_ph_versions": true,
      "edit_published_ph_versions": true,
      "view_unsubscribed_ph_versions": true,
      "manage_ph_version_terms": true,
      "edit_ph_version_terms": true,
      "delete_ph_version_terms": true,
      "assign_ph_version_terms": true,
      "create_ph-websites": true,
      "delete_ph-website": true,
      "edit_ph-websites": true,
      "edit_others_ph-websites": true,
      "publish_ph-websites": true,
      "delete_ph-websites": true,
      "delete_private_ph-websites": true,
      "delete_published_ph-websites": true,
      "delete_others_ph-websites": true,
      "edit_private_ph-websites": true,
      "edit_published_ph-websites": true,
      "view_unsubscribed_ph-websites": true,
      "manage_ph-website_terms": true,
      "edit_ph-website_terms": true,
      "delete_ph-website_terms": true,
      "assign_ph-website_terms": true,
      "delete_others_ph_comment_locations": true,
      "delete_others_project_images": true,
      "delete_others_ph-webpages": true,
      "delete_others_phw_comment_locs": true,
      "project_admin": true
    },
    "extra_capabilities": {
      "project_admin": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/aa9dbc0b2851fc84bfda834edadff893?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup/1471"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup"
      }
    ],
    "about": [
      {
        "href": "https://example.org/wp-json/wp/v2/types/ph-project"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users/21"
      }
    ],
    "members": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/users?include=16,18,20,21&per_page=100"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1471&per_page=5&type=approval"
      }
    ],
    "images": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image?parent_id=1471"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://example.org/wp-json/wp/v2/media?parent=1471"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://example.org/wp-json/projecthuddle/v2/mockup-image', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
[
  {
    "id": 1469,
    "date": "2022-04-26T15:18:23",
    "date_gmt": "2022-04-26T09:48:23",
    "modified": "2022-04-26T15:18:23",
    "modified_gmt": "2022-04-26T09:48:23",
    "slug": "18a1425e-ea0c-3eda-8685-0070273be067",
    "status": "publish",
    "type": "image",
    "link": "https://example.org/mockup/test-mock/?access_token=52e76d9fcd0c7b7783156bfcf38174d9#18a1425e-ea0c-3eda-8685-0070273be067",
    "title": {
      "rendered": "4b86a172-87cf-30b3-b6fa-2d53a1ebcabf"
    },
    "excerpt": {
      "rendered": "",
      "protected": false
    },
    "featured_media": 1300,
    "parent": 0,
    "menu_order": 0,
    "meta": {
    },
    "parent_id": 97,
    "model_type": "image",
    "current_version": "2",
    "sketch_id": "",
    "ph_short_link": "https://example.org/?p=1469",
    "options": {
      "alignment": "center",
      "size": "scale",
      "background_color": "#15181C",
      "background_image": "",
      "background_image_position": "http://center"
    },
    "approved": false,
    "approval_data": {
      "by": false,
      "on": false
    },
    "resolve_status": {
      "total": 0,
      "approved": 0,
      "resolved": 0,
      "by": false,
      "on": false
    },
    "is_private": false,
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image/1469"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image"
        }
      ],
      "about": [
        {
          "href": "https://example.org/wp-json/wp/v2/types/project_image"
        }
      ],
      "versions": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image/1469/versions"
        }
      ],
      "approval_history": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1469&per_page=5&type=approval"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup/97"
        }
      ],
      "wp:featuredmedia": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/media/1300"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://example.org/wp-json/wp/v2/media?parent=1469"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
  {
    "id": 1468,
    "date": "2022-04-26T15:17:44",
    "date_gmt": "2022-04-26T09:47:44",
    "modified": "2022-04-26T15:17:44",
    "modified_gmt": "2022-04-26T09:47:44",
    "slug": "2b69413a-9d67-3b07-a5d2-96a772fe4779",
    "status": "publish",
    "type": "image",
    "link": "https://example.org/mockup/huddle-mock/?access_token=ce635f81fd0b4c77decf759a9919deec#2b69413a-9d67-3b07-a5d2-96a772fe4779",
    "title": {
      "rendered": "b023d4a0-e453-329b-b55a-0dc4bd4c938e"
    },
    "excerpt": {
      "rendered": "",
      "protected": false
    },
    "featured_media": 1379,
    "parent": 0,
    "menu_order": 0,
    "meta": {
    },
    "parent_id": 180,
    "model_type": "image",
    "current_version": "2",
    "sketch_id": "",
    "ph_short_link": "https://example.org/?p=1468",
    "options": {
      "alignment": "center",
      "size": "scale",
      "background_color": "#15181C",
      "background_image": "",
      "background_image_position": "http://center"
    },
    "approved": false,
    "approval_data": {
      "by": false,
      "on": false
    },
    "resolve_status": {
      "total": 0,
      "approved": 0,
      "resolved": 0,
      "by": false,
      "on": false
    },
    "is_private": false,
    "_links": {
      "self": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image/1468"
        }
      ],
      "collection": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image"
        }
      ],
      "about": [
        {
          "href": "https://example.org/wp-json/wp/v2/types/project_image"
        }
      ],
      "versions": [
        {
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image/1468/versions"
        }
      ],
      "approval_history": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1468&per_page=5&type=approval"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://example.org/wp-json/projecthuddle/v2/mockup/180"
        }
      ],
      "wp:featuredmedia": [
        {
          "embeddable": true,
          "href": "https://example.org/wp-json/projecthuddle/v2/media/1379"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://example.org/wp-json/wp/v2/media?parent=1468"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  }
]
```

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

```javascript
const form = new FormData();
form.append("title", "A new title for new mockup image");
form.append("type", "image");
form.append("parent_id", "180");
form.append("file", "image.jpg");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://example.org/wp-json/projecthuddle/v2/mockup-image', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1478,
  "date": "2022-04-26T15:38:59",
  "date_gmt": "2022-04-26T10:08:59",
  "modified": "2022-04-26T15:38:59",
  "modified_gmt": "2022-04-26T10:08:59",
  "password": "",
  "slug": "a-new-title-for-new-mockup-image-2",
  "status": "publish",
  "type": "image",
  "link": "https://example.org/mockup/huddle-mock/?access_token=ce635f81fd0b4c77decf759a9919deec#a-new-title-for-new-mockup-image-2",
  "title": {
    "raw": "A new title for new mockup image",
    "rendered": "A new title for new mockup image"
  },
  "excerpt": {
    "raw": "",
    "rendered": "",
    "protected": false
  },
  "featured_media": 0,
  "parent": 0,
  "menu_order": 0,
  "meta": {
  },
  "parent_id": 180,
  "model_type": "image",
  "current_version": 1,
  "sketch_id": "",
  "options": {
    "alignment": "center",
    "size": "normal",
    "background_color": "#222",
    "background_image": "",
    "background_image_position": "http://center"
  },
  "approved": false,
  "approval_data": {
    "by": false,
    "on": false
  },
  "resolve_status": {
    "total": 0,
    "approved": 0,
    "resolved": 0,
    "by": false,
    "on": false
  },
  "is_private": false,
  "_links": {
    "self": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image/1478"
      }
    ],
    "collection": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image"
      }
    ],
    "about": [
      {
        "href": "https://example.org/wp-json/wp/v2/types/project_image"
      }
    ],
    "versions": [
      {
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup-image/1478/versions"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/comments?post=1478&per_page=5&type=approval"
      }
    ],
    "up": [
      {
        "embeddable": true,
        "href": "https://example.org/wp-json/projecthuddle/v2/mockup/180"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://example.org/wp-json/wp/v2/media?parent=1478"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1468,
  "date": "2022-04-26T15:17:44",
  "date_gmt": "2022-04-26T09:47:44",
  "modified": "2022-04-26T15:41:16",
  "modified_gmt": "2022-04-26T10:11:16",
  "slug": "2b69413a-9d67-3b07-a5d2-96a772fe4779",
  "status": "publish",
  "type": "image",
  "link": "https://bsf.ddev.site/mockup/huddle-mock/?access_token=ce635f81fd0b4c77decf759a9919deec#2b69413a-9d67-3b07-a5d2-96a772fe4779",
  "title": {
    "rendered": "b023d4a0-e453-329b-b55a-0dc4bd4c938e"
  },
  "excerpt": {
    "rendered": "",
    "protected": false
  },
  "featured_media": 1379,
  "parent": 0,
  "menu_order": 0,
  "meta": {
  },
  "parent_id": 180,
  "model_type": "image",
  "current_version": "2",
  "sketch_id": "",
  "ph_short_link": "https://bsf.ddev.site/?p=1468",
  "options": {
    "alignment": "center",
    "size": "scale",
    "background_color": "#15181C",
    "background_image": "",
    "background_image_position": "http://center"
  },
  "approved": false,
  "approval_data": {
    "by": false,
    "on": false
  },
  "resolve_status": {
    "total": 1,
    "approved": 0,
    "resolved": 0,
    "by": false,
    "on": false
  },
  "is_private": false,
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/project_image"
      }
    ],
    "versions": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468/versions"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1468&per_page=5&type=approval"
      }
    ],
    "up": [
      {
        "embeddable": false,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup/180"
      }
    ],
    "wp:featuredmedia": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/media/1379"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1468"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const form = new FormData();
form.append("title", "Changed the title");
form.append("parent_id", "180");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1468,
  "date": "2022-04-26T15:17:44",
  "date_gmt": "2022-04-26T09:47:44",
  "modified": "2022-05-01T18:14:06",
  "modified_gmt": "2022-05-01T12:44:06",
  "password": "",
  "slug": "2b69413a-9d67-3b07-a5d2-96a772fe4779",
  "status": "publish",
  "type": "image",
  "link": "https://bsf.ddev.site/mockup/huddle-mock/?access_token=ce635f81fd0b4c77decf759a9919deec#2b69413a-9d67-3b07-a5d2-96a772fe4779",
  "title": {
    "raw": "Changed the title",
    "rendered": "Changed the title"
  },
  "excerpt": {
    "raw": "",
    "rendered": "",
    "protected": false
  },
  "featured_media": 1379,
  "parent": 0,
  "menu_order": 0,
  "meta": {
  },
  "parent_id": 180,
  "model_type": "image",
  "current_version": "2",
  "sketch_id": "",
  "options": {
    "alignment": "center",
    "size": "scale",
    "background_color": "#15181C",
    "background_image": "",
    "background_image_position": "http://center"
  },
  "approved": false,
  "approval_data": {
    "by": false,
    "on": false
  },
  "resolve_status": {
    "total": 1,
    "approved": 0,
    "resolved": 0,
    "by": false,
    "on": false
  },
  "is_private": false,
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/project_image"
      }
    ],
    "versions": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468/versions"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1468&per_page=5&type=approval"
      }
    ],
    "up": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup/180"
      }
    ],
    "wp:featuredmedia": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/media/1379"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1468"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'DELETE',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1478', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1478,
  "date": "2022-04-26T15:38:59",
  "date_gmt": "2022-04-26T10:08:59",
  "modified": "2022-05-01T18:18:53",
  "modified_gmt": "2022-05-01T12:48:53",
  "password": "",
  "slug": "a-new-title-for-new-mockup-image-2__trashed",
  "status": "trash",
  "type": "image",
  "link": "https://bsf.ddev.site/?post_type=project_image&p=1478&access_token=ce635f81fd0b4c77decf759a9919deec",
  "title": {
    "raw": "A new title for new mockup image",
    "rendered": "A new title for new mockup image"
  },
  "excerpt": {
    "raw": "",
    "rendered": "",
    "protected": false
  },
  "featured_media": 0,
  "parent": 0,
  "menu_order": 1,
  "meta": {
  },
  "parent_id": 180,
  "model_type": "image",
  "current_version": "1",
  "sketch_id": "",
  "options": {
    "alignment": "center",
    "size": "normal",
    "background_color": "#222",
    "background_image": "",
    "background_image_position": "http://center"
  },
  "approved": false,
  "approval_data": {
    "by": false,
    "on": false
  },
  "resolve_status": {
    "total": 0,
    "approved": 0,
    "resolved": 0,
    "by": false,
    "on": false
  },
  "is_private": false,
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1478"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/project_image"
      }
    ],
    "versions": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1478/versions"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1478&per_page=5&type=approval"
      }
    ],
    "up": [
      {
        "embeddable": false,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup/180"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1478"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468/versions', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
[
  {
    "id": 1474,
    "date": "2022-04-26T10:28:25",
    "date_gmt": "2022-04-26T04:58:25",
    "modified": "2022-04-26T10:28:25",
    "modified_gmt": "2022-04-26T04:58:25",
    "slug": "2b69413a-9d67-3b07-a5d2-96a772fe4779",
    "status": "publish",
    "type": "image",
    "link": "https://bsf.ddev.site/?post_type=ph_version&p=1474&access_token=b0f06bae8adc6cd0e18adf74b67b7977",
    "title": {
      "rendered": "b023d4a0-e453-329b-b55a-0dc4bd4c938e"
    },
    "excerpt": {
      "rendered": "",
      "protected": false
    },
    "featured_media": 1335,
    "parent": 1468,
    "menu_order": 0,
    "meta": {
    },
    "parent_id": 180,
    "model_type": "image_version",
    "current_version": "1",
    "sketch_id": "",
    "ph_short_link": "https://bsf.ddev.site/?p=1474",
    "options": {
      "alignment": "center",
      "size": "scale",
      "background_color": "#15181C",
      "background_image": "",
      "background_image_position": "http://center"
    },
    "approved": false,
    "approval_data": {
      "by": false,
      "on": false
    },
    "resolve_status": {
      "total": 0,
      "approved": 0,
      "resolved": 0,
      "by": false,
      "on": false
    },
    "is_private": false,
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1474"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/project_image"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "replies": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1474"
        }
      ],
      "comments": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1474"
        }
      ],
      "version-history": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/1474/revisions"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup/180"
        }
      ],
      "wp:featuredmedia": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/media/1335"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1474"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  }
]
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468/versions/1474', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "author": 16,
  "featured_media": 1335,
  "date": "2022-04-26T10:28:25",
  "date_gmt": "2022-04-26T04:58:25",
  "id": 1474,
  "modified": "2022-04-26T10:28:25",
  "modified_gmt": "2022-04-26T04:58:25",
  "parent": 1468,
  "slug": "2b69413a-9d67-3b07-a5d2-96a772fe4779",
  "title": {
    "rendered": "b023d4a0-e453-329b-b55a-0dc4bd4c938e"
  },
  "excerpt": {
    "rendered": ""
  },
  "_links": {
    "parent": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'DELETE',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468/versions/1474?force=true', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "deleted": true,
  "previous": {
    "author": 16,
    "featured_media": 1335,
    "date": "2022-04-26T10:28:25",
    "date_gmt": "2022-04-26T04:58:25",
    "id": 1474,
    "modified": "2022-04-26T10:28:25",
    "modified_gmt": "2022-04-26T04:58:25",
    "parent": 1468,
    "slug": "2b69413a-9d67-3b07-a5d2-96a772fe4779",
    "title": {
      "rendered": "b023d4a0-e453-329b-b55a-0dc4bd4c938e"
    },
    "excerpt": {
      "rendered": ""
    }
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
[
  {
    "id": 1510,
    "date": "2022-05-01T19:24:21",
    "date_gmt": "2022-05-01T13:54:21",
    "modified": "2022-05-01T19:24:21",
    "modified_gmt": "2022-05-01T13:54:21",
    "slug": "content-is-static",
    "status": "publish",
    "type": "ph_comment_location",
    "link": "https://bsf.ddev.site/mockup-comment/1510/?access_token=ce635f81fd0b4c77decf759a9919deec",
    "title": {
      "rendered": "content is static"
    },
    "content": {
      "rendered": "<p>content is static</p>",
      "protected": false
    },
    "author": 16,
    "parent": 0,
    "comment_status": "open",
    "ping_status": "closed",
    "meta": {
    },
    "parent_id": 1469,
    "project_id": 180,
    "model_type": "mockup-thread",
    "members": [
      16,
      17,
      22
    ],
    "relativeX": 0.40198321891686001,
    "relativeY": 0.14302059496568001,
    "resolved": false,
    "assigned": 0,
    "comments": [

    ],
    "comments_count": 1,
    "activity_count": 1,
    "ph_short_link": "https://bsf.ddev.site/?p=1510",
    "project_name": "Huddle Mock",
    "item_name": "Fruits basket",
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1510"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph_comment_location"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "replies": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1510"
        }
      ],
      "comments": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1510"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1469"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1510"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
  {
    "id": 1509,
    "date": "2022-05-01T19:24:17",
    "date_gmt": "2022-05-01T13:54:17",
    "modified": "2022-05-01T19:24:17",
    "modified_gmt": "2022-05-01T13:54:17",
    "slug": "comment-threads-are-comments",
    "status": "publish",
    "type": "ph_comment_location",
    "link": "https://bsf.ddev.site/mockup-comment/1509/?access_token=ce635f81fd0b4c77decf759a9919deec",
    "title": {
      "rendered": "comment threads are comments"
    },
    "content": {
      "rendered": "<p>comment threads are comments</p>",
      "protected": false
    },
    "author": 16,
    "parent": 0,
    "comment_status": "open",
    "ping_status": "closed",
    "meta": {
    },
    "parent_id": 1469,
    "project_id": 180,
    "model_type": "mockup-thread",
    "members": [
      16,
      17,
      22
    ],
    "relativeX": 0.24485125858123999,
    "relativeY": 0.27231121281464998,
    "resolved": false,
    "assigned": 0,
    "comments": [

    ],
    "comments_count": 1,
    "activity_count": 1,
    "ph_short_link": "https://bsf.ddev.site/?p=1509",
    "project_name": "Huddle Mock",
    "item_name": "Fruits basket",
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1509"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph_comment_location"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "replies": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1509"
        }
      ],
      "comments": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1509"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1469"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1509"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
  {
    "id": 1507,
    "date": "2022-05-01T19:23:41",
    "date_gmt": "2022-05-01T13:53:41",
    "modified": "2022-05-01T19:23:41",
    "modified_gmt": "2022-05-01T13:53:41",
    "slug": "just-another-comment",
    "status": "publish",
    "type": "ph_comment_location",
    "link": "https://bsf.ddev.site/mockup-comment/1507/?access_token=ce635f81fd0b4c77decf759a9919deec",
    "title": {
      "rendered": "just another comment"
    },
    "content": {
      "rendered": "<p>just another comment</p>",
      "protected": false
    },
    "author": 16,
    "parent": 0,
    "comment_status": "open",
    "ping_status": "closed",
    "meta": {
    },
    "parent_id": 1468,
    "project_id": 180,
    "model_type": "mockup-thread",
    "members": [
      16,
      17,
      22
    ],
    "relativeX": 0.38374291115311998,
    "relativeY": 0.12907801418439999,
    "resolved": false,
    "assigned": 0,
    "comments": [

    ],
    "comments_count": 1,
    "activity_count": 1,
    "ph_short_link": "https://bsf.ddev.site/?p=1507",
    "project_name": "Huddle Mock",
    "item_name": "The city view",
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1507"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph_comment_location"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "replies": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1507"
        }
      ],
      "comments": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1507"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1507"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
  {
    "id": 1480,
    "date": "2022-04-26T17:56:03",
    "date_gmt": "2022-04-26T12:26:03",
    "modified": "2022-04-26T17:56:03",
    "modified_gmt": "2022-04-26T12:26:03",
    "slug": "another-one-comment",
    "status": "publish",
    "type": "ph_comment_location",
    "link": "https://bsf.ddev.site/mockup-comment/1480/?access_token=ce635f81fd0b4c77decf759a9919deec",
    "title": {
      "rendered": "another one comment"
    },
    "content": {
      "rendered": "<p>another one comment</p>",
      "protected": false
    },
    "author": 16,
    "parent": 0,
    "comment_status": "open",
    "ping_status": "closed",
    "meta": {
    },
    "parent_id": 1468,
    "project_id": 180,
    "model_type": "mockup-thread",
    "members": [
      16,
      17,
      22
    ],
    "relativeX": 0.22306238185255001,
    "relativeY": 0.14042553191488999,
    "resolved": false,
    "assigned": 0,
    "comments": [

    ],
    "comments_count": 1,
    "activity_count": 1,
    "ph_short_link": "https://bsf.ddev.site/?p=1480",
    "project_name": "Huddle Mock",
    "item_name": "b023d4a0-e453-329b-b55a-0dc4bd4c938e",
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1480"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph_comment_location"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "replies": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1480"
        }
      ],
      "comments": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1480"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1480"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  }
]
```

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

```javascript
const form = new FormData();
form.append("title", "API comment");
form.append("content", "comment from api");
form.append("parent_id", "1468");
form.append("author", "16");
form.append("relativeX", "0.2");
form.append("relativeY", "0.5");
form.append("comment_status", "open");
form.append("status", "publish");
form.append("assigned", "20");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example

```json
{
  "id": 1516,
  "date": "2022-05-01T19:52:35",
  "date_gmt": "2022-05-01T14:22:35",
  "modified": "2022-05-01T19:52:35",
  "modified_gmt": "2022-05-01T14:22:35",
  "password": "",
  "slug": "api-comment-4",
  "status": "publish",
  "type": "ph_comment_location",
  "link": "https://bsf.ddev.site/mockup-comment/1516/?access_token=ce635f81fd0b4c77decf759a9919deec",
  "title": {
    "raw": "API comment",
    "rendered": "API comment"
  },
  "content": {
    "raw": "comment from api",
    "rendered": "No comment text",
    "protected": false
  },
  "author": 16,
  "parent": 0,
  "comment_status": "open",
  "ping_status": "closed",
  "meta": {
  },
  "parent_id": 1468,
  "project_id": 180,
  "model_type": "mockup-thread",
  "members": [

  ],
  "relativeX": 0.20000000000000001,
  "relativeY": 0.5,
  "resolved": false,
  "assigned": 20,
  "comments": [

  ],
  "comments_count": 0,
  "activity_count": 0,
  "project_name": "Huddle Mock",
  "item_name": "The city view",
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1516"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph_comment_location"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "replies": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1516"
      }
    ],
    "assigned": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/20"
      }
    ],
    "comments": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1516"
      }
    ],
    "up": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1516"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1480', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1480,
  "date": "2022-04-26T17:56:03",
  "date_gmt": "2022-04-26T12:26:03",
  "modified": "2022-04-26T17:56:03",
  "modified_gmt": "2022-04-26T12:26:03",
  "slug": "jus-a-comment",
  "status": "publish",
  "type": "ph_comment_location",
  "link": "https://bsf.ddev.site/mockup-comment/1480/",
  "title": {
    "rendered": "jus a comment"
  },
  "content": {
    "rendered": "<p>jus a comment</p>",
    "protected": false
  },
  "author": 16,
  "parent": 0,
  "comment_status": "open",
  "ping_status": "closed",
  "meta": {
    "site-sidebar-layout": "default",
    "site-content-layout": "default",
    "ast-main-header-display": "",
    "ast-hfb-above-header-display": "",
    "ast-hfb-below-header-display": "",
    "ast-hfb-mobile-header-display": "",
    "site-post-title": "",
    "ast-breadcrumbs-content": "",
    "ast-featured-img": "",
    "footer-sml-layout": "",
    "theme-transparent-header-meta": "",
    "adv-header-id-meta": "",
    "stick-header-meta": "",
    "header-above-stick-meta": "",
    "header-main-stick-meta": "",
    "header-below-stick-meta": ""
  },
  "parent_id": 1468,
  "project_id": 180,
  "model_type": "mockup-thread",
  "members": [

  ],
  "relativeX": 0.22306238185255001,
  "relativeY": 0.14042553191488999,
  "resolved": false,
  "assigned": 0,
  "comments": [

  ],
  "comments_count": 1,
  "activity_count": 1,
  "ph_short_link": "https://bsf.ddev.site/?p=1480",
  "project_name": "Huddle Mock",
  "item_name": "b023d4a0-e453-329b-b55a-0dc4bd4c938e",
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1480"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph_comment_location"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "replies": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1480"
      }
    ],
    "comments": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1480"
      }
    ],
    "up": [
      {
        "embeddable": false,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1480"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const form = new FormData();
form.append("content", "just changed the content");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1480', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1480,
  "date": "2022-04-26T17:56:03",
  "date_gmt": "2022-04-26T12:26:03",
  "modified": "2022-05-01T20:13:34",
  "modified_gmt": "2022-05-01T14:43:34",
  "password": "",
  "slug": "just-a-comment",
  "status": "publish",
  "type": "ph_comment_location",
  "link": "https://bsf.ddev.site/mockup-comment/1480/?access_token=ce635f81fd0b4c77decf759a9919deec",
  "title": {
    "raw": "just changed the content",
    "rendered": "just changed the content"
  },
  "content": {
    "raw": "just changed the content",
    "rendered": "<p>just changed the content</p>",
    "protected": false
  },
  "author": 16,
  "parent": 0,
  "comment_status": "open",
  "ping_status": "closed",
  "meta": {
  },
  "parent_id": 1468,
  "project_id": 180,
  "model_type": "mockup-thread",
  "members": [
    16,
    17,
    22
  ],
  "relativeX": 0.22306238185255001,
  "relativeY": 0.14042553191488999,
  "resolved": false,
  "assigned": 0,
  "comments": [
    {
      "id": 172,
      "post": 1480,
      "parent": 0,
      "author": 16,
      "author_name": "john",
      "author_url": "",
      "date": "2022-04-26T17:56:04",
      "date_gmt": "2022-04-26T12:26:04",
      "content": {
        "rendered": "<p>just changed the content</p>"
      },
      "link": "https://bsf.ddev.site/mockup-comment/1480/?access_token=ce635f81fd0b4c77decf759a9919deec/",
      "status": "approved",
      "type": "ph_comment",
      "project_id": 180,
      "item_id": 1468,
      "author_avatar_urls": {
        "24": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=24&d=mm&r=g",
        "48": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=48&d=mm&r=g",
        "96": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=96&d=mm&r=g"
      },
      "meta": [

      ],
      "comment_post_type": "thread",
      "approval": false,
      "is_private": false,
      "_links": {
        "self": [
          {
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments/172"
          }
        ],
        "collection": [
          {
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments"
          }
        ],
        "author": [
          {
            "embeddable": true,
            "href": "https://bsf.ddev.site/wp-json/wp/v2/users/16"
          }
        ],
        "up": [
          {
            "embeddable": true,
            "post_type": "ph_comment_location",
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1480"
          }
        ],
        "item": [
          {
            "embeddable": true,
            "post_type": "mockup-image",
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
          }
        ],
        "project": [
          {
            "embeddable": true,
            "post_type": "mockup",
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup/180"
          }
        ]
      }
    }
  ],
  "comments_count": 1,
  "activity_count": 1,
  "project_name": "Huddle Mock",
  "item_name": "b023d4a0-e453-329b-b55a-0dc4bd4c938e",
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1480"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph_comment_location"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "replies": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1480"
      }
    ],
    "comments": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1480"
      }
    ],
    "up": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1480"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'DELETE',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1480', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1480,
  "date": "2022-04-26T17:56:03",
  "date_gmt": "2022-04-26T12:26:03",
  "modified": "2022-05-01T20:17:11",
  "modified_gmt": "2022-05-01T14:47:11",
  "password": "",
  "slug": "just-a-comment__trashed",
  "status": "trash",
  "type": "ph_comment_location",
  "link": "https://bsf.?access_token=ce635f81fd0b4c77decf759a9919deec",
  "title": {
    "raw": "just changed the content",
    "rendered": "just changed the content"
  },
  "content": {
    "raw": "just changed the content",
    "rendered": "just changed the content",
    "protected": false
  },
  "author": 16,
  "parent": 0,
  "comment_status": "open",
  "ping_status": "closed",
  "meta": {
  },
  "parent_id": 1468,
  "project_id": 180,
  "model_type": "mockup-thread",
  "members": [
    16,
    17,
    22
  ],
  "relativeX": 0.22306238185255001,
  "relativeY": 0.14042553191488999,
  "resolved": false,
  "assigned": 0,
  "comments": [

  ],
  "comments_count": 0,
  "activity_count": 0,
  "project_name": "Huddle Mock",
  "item_name": "b023d4a0-e453-329b-b55a-0dc4bd4c938e",
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread/1480"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-thread"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph_comment_location"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "replies": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1480"
      }
    ],
    "comments": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1480"
      }
    ],
    "up": [
      {
        "embeddable": false,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/mockup-image/1468"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1480"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/website', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
[
  {
    "id": 1503,
    "date": "2022-04-28T19:12:29",
    "date_gmt": "2022-04-28T13:42:29",
    "modified": "2022-04-28T19:12:37",
    "modified_gmt": "2022-04-28T13:42:37",
    "slug": "https-curlslope-s2-tastewp-com-wp-admin",
    "status": "publish",
    "type": "ph-website",
    "link": "https://bsf.ddev.site/website/https-curlslope-s2-tastewp-com-wp-admin/?access_token=dd1e54352aec6e3728ea71f7008c0c2c",
    "title": {
      "rendered": "https://curlslope.s2-tastewp.com/wp-admin"
    },
    "author": 16,
    "parent": 0,
    "meta": {

    },
    "model_type": "website",
    "me": {
      "id": 16,
      "username": "john",
      "name": "john",
      "first_name": "John",
      "last_name": "Doe",
      "email": "johndoe@gmail.com",
      "url": "",
      "description": "",
      "link": "https://bsf.ddev.site/author/john/",
      "locale": "en_US",
      "nickname": "john",
      "slug": "john",
      "roles": [
        "administrator"
      ],
      "user_role": "Administrator",
      "registered_date": "2022-04-10T16:45:42+00:00",
      "capabilities": {

      },
      "extra_capabilities": {
        "administrator": true
      },
      "avatar_urls": {
        "24": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=24&d=mm&r=g",
        "48": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=48&d=mm&r=g",
        "96": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=96&d=mm&r=g"
      },
      "meta": [

      ],
      "_links": {
        "self": [
          {
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
          }
        ],
        "collection": [
          {
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users"
          }
        ]
      }
    },
    "ph_short_link": "https://bsf.ddev.site/?p=1503",
    "website_url": "https://curlslope.s2-tastewp.com",
    "pages": [

    ],
    "allow_guests": false,
    "force_login": false,
    "thread_subscribers": "all",
    "project_approval": false,
    "project_unapproval": true,
    "webhook": "",
    "ph_installed": true,
    "child_site": "",
    "child_plugin_installed": "",
    "access_token": "dd1e54352aec6e3728ea71f7008c0c2c",
    "project_members": [
      16
    ],
    "resolve_status": {
      "total": 0,
      "resolved": 0
    },
    "items_status": {
      "total": 0,
      "approved": 0,
      "by": false,
      "on": false
    },
    "approved": false,
    "comment_scroll": false,
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1503"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph-website"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "members": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users?include=16&per_page=100"
        }
      ],
      "approval_history": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1503&per_page=5&type=approval"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1503"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
  {
    "id": 1427,
    "date": "2022-04-19T21:15:49",
    "date_gmt": "2022-04-19T15:45:49",
    "modified": "2022-04-27T12:31:22",
    "modified_gmt": "2022-04-27T07:01:22",
    "slug": "1427",
    "status": "publish",
    "type": "ph-website",
    "link": "https://bsf.ddev.site/website/1427/?access_token=87e63af5cb8f4262a8f44e8842341b99",
    "title": {
      "rendered": "https://funola.com/"
    },
    "author": 16,
    "parent": 0,
    "meta": {

    },
    "model_type": "website",
    "me": {
      "id": 16,
      "username": "john",
      "name": "john",
      "first_name": "John",
      "last_name": "Doe",
      "email": "johndoe@gmail.com",
      "url": "",
      "description": "",
      "link": "https://bsf.ddev.site/author/john/",
      "locale": "en_US",
      "nickname": "john",
      "slug": "john",
      "roles": [
        "administrator"
      ],
      "user_role": "Administrator",
      "registered_date": "2022-04-10T16:45:42+00:00",
      "capabilities": {

      },
      "extra_capabilities": {
        "administrator": true
      },
      "avatar_urls": {
        "24": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=24&d=mm&r=g",
        "48": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=48&d=mm&r=g",
        "96": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=96&d=mm&r=g"
      },
      "meta": [

      ],
      "_links": {
        "self": [
          {
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
          }
        ],
        "collection": [
          {
            "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users"
          }
        ]
      }
    },
    "ph_short_link": "https://bsf.ddev.site/?p=1427",
    "website_url": "https://funola.com",
    "pages": [

    ],
    "allow_guests": false,
    "force_login": false,
    "thread_subscribers": "all",
    "project_approval": false,
    "project_unapproval": true,
    "webhook": "",
    "ph_installed": true,
    "child_site": "",
    "child_plugin_installed": "",
    "access_token": "87e63af5cb8f4262a8f44e8842341b99",
    "project_members": [
      16,
      17,
      18,
      21,
      23,
      24
    ],
    "resolve_status": {
      "total": 6,
      "resolved": 0
    },
    "items_status": {
      "total": 0,
      "approved": 0,
      "by": false,
      "on": false
    },
    "approved": false,
    "comment_scroll": false,
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1427"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph-website"
        }
      ],
      "author": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "members": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users?include=16,17,18,21,23,24&per_page=100"
        }
      ],
      "approval_history": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1427&per_page=5&type=approval"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1427"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
]
```

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

```javascript
const form = new FormData();
form.append("website_url", "https://rapidblade.com");
form.append("title", "Rest API website");
form.append("access_link_login", "true");
form.append("project_members", "16, 20");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/website', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1596,
  "date": "2022-05-03T17:10:46",
  "date_gmt": "2022-05-03T11:40:46",
  "modified": "2022-05-03T17:10:46",
  "modified_gmt": "2022-05-03T11:40:46",
  "password": "",
  "slug": "rest-api-website",
  "status": "publish",
  "type": "ph-website",
  "link": "https://bsf.ddev.site/website/rest-api-website/?access_token=75867dcfe8b32bf84a969e0c0411c7b8",
  "title": {
    "raw": "Rest API website",
    "rendered": "Rest API website"
  },
  "author": 16,
  "parent": 0,
  "meta": {

  },
  "model_type": "website",
  "me": {
    "id": 16,
    "username": "john",
    "name": "john",
    "first_name": "John",
    "last_name": "Doe",
    "email": "johndoe@gmail.com",
    "url": "",
    "description": "",
    "link": "https://bsf.ddev.site/author/john/",
    "locale": "en_US",
    "nickname": "john",
    "slug": "john",
    "roles": [
      "administrator"
    ],
    "user_role": "Administrator",
    "registered_date": "2022-04-10T16:45:42+00:00",
    "capabilities": {

    },
    "extra_capabilities": {
      "administrator": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
  "website_url": "https://rapidblade.com",
  "pages": [

  ],
  "allow_guests": false,
  "force_login": false,
  "thread_subscribers": "all",
  "project_approval": false,
  "project_unapproval": true,
  "webhook": "",
  "ph_installed": false,
  "child_site": "",
  "child_plugin_installed": "",
  "access_token": "75867dcfe8b32bf84a969e0c0411c7b8",
  "project_members": [
    16,
    20
  ],
  "resolve_status": {
    "total": 0,
    "resolved": 0
  },
  "items_status": {
    "total": 0,
    "approved": 0,
    "by": false,
    "on": false
  },
  "approved": false,
  "comment_scroll": null,
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1596"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph-website"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "members": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users?include=16,20&per_page=100"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1596&per_page=5&type=approval"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1596"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1427', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1427,
  "date": "2022-04-19T21:15:49",
  "date_gmt": "2022-04-19T15:45:49",
  "modified": "2022-04-27T12:31:22",
  "modified_gmt": "2022-04-27T07:01:22",
  "slug": "1427",
  "status": "publish",
  "type": "ph-website",
  "link": "https://bsf.ddev.site/website/1427/",
  "title": {
    "rendered": "https://funola.com/"
  },
  "author": 16,
  "parent": 0,
  "meta": {

  },
  "model_type": "website",
  "me": {
    "code": "rest_not_logged_in",
    "message": "You are not currently logged in.",
    "data": {
      "status": 401
    }
  },
  "ph_short_link": "https://bsf.ddev.site/?p=1427",
  "website_url": "https://funola.com",
  "pages": [

  ],
  "allow_guests": false,
  "force_login": false,
  "thread_subscribers": "all",
  "project_approval": false,
  "project_unapproval": true,
  "webhook": "",
  "ph_installed": true,
  "child_site": "",
  "child_plugin_installed": "",
  "access_token": "",
  "project_members": [
    16,
    17,
    18,
    21,
    23,
    24
  ],
  "resolve_status": {
    "total": 6,
    "resolved": 0
  },
  "items_status": {
    "total": 0,
    "approved": 0,
    "by": false,
    "on": false
  },
  "approved": false,
  "comment_scroll": null,
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1427"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph-website"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "members": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users?include=16,17,18,21,23,24&per_page=100"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1427&per_page=5&type=approval"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1427"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const form = new FormData();
form.append("project_members", "20, 22");

const options = {
  method: 'POST',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN',
    'content-type': 'multipart/form-data'
  }
};

options.body = form;

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1427', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1427,
  "date": "2022-04-19T21:15:49",
  "date_gmt": "2022-04-19T15:45:49",
  "modified": "2022-05-03T18:31:36",
  "modified_gmt": "2022-05-03T13:01:36",
  "password": "",
  "slug": "1427",
  "status": "publish",
  "type": "ph-website",
  "link": "https://bsf.ddev.site/website/1427/?access_token=87e63af5cb8f4262a8f44e8842341b99",
  "title": {
    "raw": "https://funola.com/",
    "rendered": "https://funola.com/"
  },
  "author": 16,
  "parent": 0,
  "meta": {

  },
  "model_type": "website",
  "me": {
    "id": 16,
    "username": "john",
    "name": "john",
    "first_name": "John",
    "last_name": "Doe",
    "email": "johndoe@gmail.com",
    "url": "",
    "description": "",
    "link": "https://bsf.ddev.site/author/john/",
    "locale": "en_US",
    "nickname": "john",
    "slug": "john",
    "roles": [
      "administrator"
    ],
    "user_role": "Administrator",
    "registered_date": "2022-04-10T16:45:42+00:00",
    "capabilities": {

    },
    "extra_capabilities": {
      "administrator": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
  "website_url": "https://funola.com",
  "pages": [

  ],
  "allow_guests": false,
  "force_login": false,
  "thread_subscribers": "all",
  "project_approval": false,
  "project_unapproval": true,
  "webhook": "",
  "ph_installed": true,
  "child_site": "",
  "child_plugin_installed": "",
  "access_token": "87e63af5cb8f4262a8f44e8842341b99",
  "project_members": [
    20,
    22
  ],
  "resolve_status": {
    "total": 6,
    "resolved": 0
  },
  "items_status": {
    "total": 0,
    "approved": 0,
    "by": false,
    "on": false
  },
  "approved": false,
  "comment_scroll": null,
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1427"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph-website"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "members": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users?include=20,22&per_page=100"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1427&per_page=5&type=approval"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1427"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'DELETE',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1596', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
{
  "id": 1596,
  "date": "2022-05-03T17:10:46",
  "date_gmt": "2022-05-03T11:40:46",
  "modified": "2022-05-03T18:50:03",
  "modified_gmt": "2022-05-03T13:20:03",
  "password": "",
  "slug": "rest-api-website__trashed",
  "status": "trash",
  "type": "ph-website",
  "link": "https://bsf.ddev.site/?post_type=ph-website&p=1596&access_token=75867dcfe8b32bf84a969e0c0411c7b8",
  "title": {
    "raw": "Rest API website",
    "rendered": "Rest API website"
  },
  "author": 16,
  "parent": 0,
  "meta": {

  },
  "model_type": "website",
  "me": {
    "id": 16,
    "username": "john",
    "name": "john",
    "first_name": "John",
    "last_name": "Doe",
    "email": "johndoe@gmail.com",
    "url": "",
    "description": "",
    "link": "https://bsf.ddev.site/author/john/",
    "locale": "en_US",
    "nickname": "john",
    "slug": "john",
    "roles": [
      "administrator"
    ],
    "user_role": "Administrator",
    "registered_date": "2022-04-10T16:45:42+00:00",
    "capabilities": {

    },
    "extra_capabilities": {
      "administrator": true
    },
    "avatar_urls": {
      "24": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=24&d=mm&r=g",
      "48": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=48&d=mm&r=g",
      "96": "https://secure.gravatar.com/avatar/884e294ade6d1ad5a6d8f95e9fae37dd?s=96&d=mm&r=g"
    },
    "meta": [

    ],
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users"
        }
      ]
    }
  },
  "website_url": "https://rapidblade.com",
  "pages": [

  ],
  "allow_guests": false,
  "force_login": false,
  "thread_subscribers": "all",
  "project_approval": false,
  "project_unapproval": true,
  "webhook": "",
  "ph_installed": false,
  "child_site": "",
  "child_plugin_installed": "",
  "access_token": "75867dcfe8b32bf84a969e0c0411c7b8",
  "project_members": [
    16,
    20
  ],
  "resolve_status": {
    "total": 0,
    "resolved": 0
  },
  "items_status": {
    "total": 0,
    "approved": 0,
    "by": false,
    "on": false
  },
  "approved": false,
  "comment_scroll": null,
  "_links": {
    "self": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1596"
      }
    ],
    "collection": [
      {
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website"
      }
    ],
    "about": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph-website"
      }
    ],
    "author": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users/16"
      }
    ],
    "members": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/users?include=16,20&per_page=100"
      }
    ],
    "approval_history": [
      {
        "embeddable": true,
        "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1596&per_page=5&type=approval"
      }
    ],
    "wp:attachment": [
      {
        "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1596"
      }
    ],
    "curies": [
      {
        "name": "wp",
        "href": "https://api.w.org/{rel}",
        "templated": true
      }
    ]
  }
}
```

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

```javascript
const options = {
  method: 'GET',
  headers: {
    Authorization: 'Bearer YOUR_ACCESS_TOKEN'
  }
};

fetch('https://bsf.ddev.site/wp-json/projecthuddle/v2/website-page', options)
  .then(response => response.json())
  .then(response => console.log(response))
  .catch(err => console.error(err));
```

> Example of JSON output

```json
[
    {
    "id": 1481,
    "date": "2022-04-27T09:50:54",
    "date_gmt": "2022-04-27T04:20:54",
    "modified": "2022-04-27T09:50:54",
    "modified_gmt": "2022-04-27T04:20:54",
    "slug": "huddle-just-another-wordpress-site-2",
    "status": "publish",
    "type": "ph-webpage",
    "link": "https://bsf.dev.site/website-page/huddle-just-another-wordpress-site-2/?access_token=87e63af5cb8f4262a8f44e8842341b99",
    "title": {
      "rendered": "Huddle – Just another WordPress site"
    },
    "parent": 0,
    "menu_order": 0,
    "meta": {

    },
    "parent_id": 1427,
    "model_type": "page",
    "user_order": [

    ],
    "ph_short_link": "https://bsf.dev.site/?p=1481",
    "page_url": "https://huddle.dev.site/",
    "resolve_status": {
      "total": 2,
      "approved": 0,
      "resolved": 0,
      "by": false,
      "on": false
    },
    "approved": false,
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website-page/1481"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website-page"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph-webpage"
        }
      ],
      "approval_history": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1481&per_page=5&type=approval"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1427"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1481"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
  {
    "id": 1429,
    "date": "2022-04-20T14:21:02",
    "date_gmt": "2022-04-20T08:51:02",
    "modified": "2022-04-20T14:21:02",
    "modified_gmt": "2022-04-20T08:51:02",
    "slug": "huddle-just-another-wordpress-site",
    "status": "publish",
    "type": "ph-webpage",
    "link": "https://bsf.ddev.site/website-page/huddle-just-another-wordpress-site/?access_token=87e63af5cb8f4262a8f44e8842341b99",
    "title": {
      "rendered": "Huddle – Just another WordPress site"
    },
    "parent": 0,
    "menu_order": 0,
    "meta": {

    },
    "parent_id": 1427,
    "model_type": "page",
    "user_order": [

    ],
    "ph_short_link": "https://bsf.ddev.site/?p=1429",
    "page_url": "https://huddle.ddev.site/",
    "resolve_status": {
      "total": 4,
      "approved": 0,
      "resolved": 0,
      "by": false,
      "on": false
    },
    "approved": false,
    "_links": {
      "self": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website-page/1429"
        }
      ],
      "collection": [
        {
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website-page"
        }
      ],
      "about": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/types/ph-webpage"
        }
      ],
      "approval_history": [
        {
          "embeddable": true,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/comments?post=1429&per_page=5&type=approval"
        }
      ],
      "up": [
        {
          "embeddable": false,
          "href": "https://bsf.ddev.site/wp-json/projecthuddle/v2/website/1427"
        }
      ],
      "wp:attachment": [
        {
          "href": "https://bsf.ddev.site/wp-json/wp/v2/media?parent=1429"
        }
      ],
      "curies": [
        {
          "name": "wp",
          "href": "https://api.w.org/{rel}",
          "templated": true
        }
      ]
    }
  },
]
```

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
