---
title: Taskmaster API Documentation

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - shell
  - python
  - javascript

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Taskmaster API
---

# Introduction

Welcome to the Taskmaster REST API. This API allows you to manage tasks and handle user authentication.

All responses are in JSON format.  
Base URL: `https://api.tm.noah-frank.de/`

# Authentication

> ℹ️ **Header vs. Cookie – Which one should I use?**  
> Using the `Authorization` header is recommended because it's a standard practice in REST APIs, works reliably across all clients (browser, mobile, CLI), and avoids issues with cookies like SameSite restrictions or automatic sending in cross-site requests.  
> Cookies are useful for browser-based sessions, especially when using `HttpOnly` and `Secure` flags to protect the token.

The Taskmaster API uses **JWT (JSON Web Tokens)** for authentication.

Protected endpoints require a valid token, which can be provided in one of two ways:

- **HTTP Header** (recommended):
  ```
  Authorization: Bearer your.jwt.token
  ```
- **HTTP Cookie**:  
  If the `Authorization` header is missing, the API will also check for a `jwt` cookie.

# User

## Register A New User

```shell
curl -X POST "https://api.tm.noah-frank.de/auth/register" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "user123",
    "password": "securepassword"
  }'
```

```javascript
const axios = require("axios");

axios.post("https://api.tm.noah-frank.de/auth/register", {
  username: "user123",
  password: "securepassword",
});
```

```python
import requests

response = requests.post("https://api.tm.noah-frank.de/auth/register", json={
  "username": "user123",
  "password": "securepassword"
})
```

> The above command returns JSON structured like this:

```json
{
  "message": "User registered successfully",
  "userId": "603dcd6e1c4a2f2f7c123456",
  "redirectUrl": "/dashboard"
}
```

This endpoint registers a new user.  
It also creates default tasks and logs the user in automatically.

### HTTP Request

`POST https://api.tm.noah-frank.de/auth/register`

### Request Body

| Field    | Type   | Required | Description          |
| -------- | ------ | -------- | -------------------- |
| username | string | ✅ Yes   | The desired username |
| password | string | ✅ Yes   | The desired password |

### Responses

| Status | Description                   |
|--------|-------------------------------|
| 200    | User registered successfully  |
| 400    | Username or password missing  |
| 500    | Internal server error         |

## Log In A User

```shell
curl -X POST "https://api.tm.noah-frank.de/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "user123",
    "password": "securepassword"
  }'
```

```javascript
const axios = require("axios");

axios.post("https://api.tm.noah-frank.de/auth/login", {
  username: "user123",
  password: "securepassword",
});
```

```python
import requests

response = requests.post("https://api.tm.noah-frank.de/auth/login", json={
  "username": "user123",
  "password": "securepassword"
})
```

> The above command returns JSON structured like this:

```json
{
  "message": "User logged in successfully",
  "userId": "603dcd6e1c4a2f2f7c123456",
  "redirectUrl": "/dashboard",
  "token": "your.jwt.token"
}
```

This endpoint logs in a user using username and password.  
Returns a JWT token and redirect URL.

### HTTP Request

`POST https://api.tm.noah-frank.de/auth/login`

### Request Body

| Field    | Type   | Required | Description         |
| -------- | ------ | -------- | ------------------- |
| username | string | ✅ Yes   | The user's username |
| password | string | ✅ Yes   | The user's password |

### Responses

| Status | Description                   |
|--------|-------------------------------|
| 200    | Login successful              |
| 400    | Username or password missing  |
| 401    | Invalid credentials           |
| 500    | Internal server error         |

# Tasks

All task-related endpoints require authentication via JWT.  
Use the `Authorization: Bearer <token>` header or a valid `jwt` cookie.

## Get All Tasks

```javascript
const axios = require("axios");

axios.get("https://api.tm.noah-frank.de/tasks", {
  headers: {
    Authorization: "Bearer your.jwt.token"
  }
}).then(response => {
  console.log(response.data);
});
```

```shell
curl "https://api.tm.noah-frank.de/tasks" \
  -H "Authorization: Bearer your.jwt.token"
```

```python
import requests

response = requests.get("https://api.tm.noah-frank.de/tasks", headers={
    "Authorization": "Bearer your.jwt.token"
})
tasks = response.json()
```

> The above command returns JSON structured like this:

```json
[
  {
    "_id": "123",
    "title": "Example Task",
    "description": "This is a task",
    "dueDate": "2024-12-31T23:59:59.999Z",
    "priority": "High",
    "completed": false,
    "user": "456"
  }
]
```

### HTTP Request

`GET https://api.tm.noah-frank.de/tasks`

### Responses

| Status | Description               |
|--------|---------------------------|
| 200    | Task list returned        |
| 500    | Internal server error     |

## Get a Task by ID

```javascript
const axios = require("axios");

axios.get("https://api.tm.noah-frank.de/tasks/<taskId>", {
  headers: {
    Authorization: "Bearer your.jwt.token"
  }
}).then(response => {
  console.log(response.data);
});
```

```shell
curl "https://api.tm.noah-frank.de/tasks/<taskId>" \
  -H "Authorization: Bearer your.jwt.token"
```

```python
import requests

response = requests.get("https://api.tm.noah-frank.de/tasks/<taskId>", headers={
    "Authorization": "Bearer your.jwt.token"
})
task = response.json()
```

> The above command returns JSON structured like this:

```json
{
  "_id": "123",
  "title": "Example Task",
  "description": "This is a task",
  "dueDate": "2024-12-31T23:59:59.999Z",
  "priority": "High",
  "completed": false,
  "user": "456"
}
```

### HTTP Request

`GET https://api.tm.noah-frank.de/tasks/<taskId>`

### Responses

| Status | Description           |
|--------|-----------------------|
| 200    | Task returned         |
| 404    | Task not found        |
| 500    | Internal server error |

## Create a New Task

```javascript
const axios = require("axios");

axios.post("https://api.tm.noah-frank.de/tasks", {
  title: "New Task",
  description: "Optional text",
  dueDate: "2024-12-31",
  priority: "High"
}, {
  headers: {
    Authorization: "Bearer your.jwt.token"
  }
}).then(response => {
  console.log(response.data);
});
```

```shell
curl -X POST "https://api.tm.noah-frank.de/tasks" \
  -H "Authorization: Bearer your.jwt.token" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "New Task",
    "description": "Optional text",
    "dueDate": "2024-12-31",
    "priority": "High"
  }'
```

```python
import requests

response = requests.post("https://api.tm.noah-frank.de/tasks", json={
    "title": "New Task",
    "description": "Optional text",
    "dueDate": "2024-12-31",
    "priority": "High"
}, headers={
    "Authorization": "Bearer your.jwt.token"
})
task = response.json()
```

> The above command returns JSON structured like this:

```json
{
  "_id": "123",
  "title": "New Task",
  "description": "Optional text",
  "dueDate": "2024-12-31T00:00:00.000Z",
  "priority": "High",
  "completed": false,
  "user": "456"
}
```

### HTTP Request

`POST https://api.tm.noah-frank.de/tasks`

### Request Body

| Field       | Type     | Required | Description                                              |
| ----------- | -------- | -------- | -------------------------------------------------------- |
| title       | string   | ✅ Yes   | Title of the task                                        |
| description | string   | No       | Description of the task                                  |
| dueDate     | ISO date | No       | When the task is due                                     |
| completed   | boolean  | No       | Whether the task is completed (default: `false`)         |
| priority    | string   | ✅ Yes   | One of: `"High"`, `"Medium"`, `"Low"` (default: `"Low"`) |
| user        | ObjectId | ⚠️ No    | Automatically set from the authenticated user            |

### Responses

| Status | Description                        |
|--------|------------------------------------|
| 201    | Task successfully created          |
| 400    | Title missing or invalid priority  |
| 400    | User identification is missing     |
| 500    | Internal server error              |

## Update a Task

```javascript
const axios = require("axios");

axios.put("https://api.tm.noah-frank.de/tasks/<taskId>", {
  title: "Updated Task",
  completed: true
}, {
  headers: {
    Authorization: "Bearer your.jwt.token"
  }
}).then(response => {
  console.log(response.data);
});
```

```shell
curl -X PUT "https://api.tm.noah-frank.de/tasks/<taskId>" \
  -H "Authorization: Bearer your.jwt.token" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Updated Task",
    "completed": true
  }'
```

```python
import requests

response = requests.put("https://api.tm.noah-frank.de/tasks/<taskId>", json={
    "title": "Updated Task",
    "completed": True
}, headers={
    "Authorization": "Bearer your.jwt.token"
})
updated_task = response.json()
```

### HTTP Request

`PUT https://api.tm.noah-frank.de/tasks/<taskId>`

### Responses

| Status | Description           |
|--------|-----------------------|
| 200    | Task updated          |
| 404    | Task not found        |
| 500    | Internal server error |

## Toggle Task Completion

```javascript
const axios = require("axios");

axios.patch("https://api.tm.noah-frank.de/tasks/<taskId>/toggle", {}, {
  headers: {
    Authorization: "Bearer your.jwt.token"
  }
}).then(response => {
  console.log(response.data);
});
```

```shell
curl -X PATCH "https://api.tm.noah-frank.de/tasks/<taskId>/toggle" \
  -H "Authorization: Bearer your.jwt.token"
```

```python
import requests

response = requests.patch("https://api.tm.noah-frank.de/tasks/<taskId>/toggle", headers={
    "Authorization": "Bearer your.jwt.token"
})
result = response.json()
```

> Returns:

```json
{
  "message": "Task toggled successfully",
  "task": {
    "_id": "123",
    "completed": true,
    ...
  }
}
```

### HTTP Request

`PATCH https://api.tm.noah-frank.de/tasks/<taskId>/toggle`

### Responses

| Status | Description                         |
|--------|-------------------------------------|
| 200    | Task toggled successfully           |
| 403    | Unauthorized access to this task    |
| 404    | Task not found                      |
| 500    | Internal server error               |

## Delete a Task

```javascript
const axios = require("axios");

axios.delete("https://api.tm.noah-frank.de/tasks/<taskId>", {
  headers: {
    Authorization: "Bearer your.jwt.token"
  }
}).then(response => {
  console.log(response.data);
});
```

```shell
curl -X DELETE "https://api.tm.noah-frank.de/tasks/<taskId>" \
  -H "Authorization: Bearer your.jwt.token"
```

```python
import requests

response = requests.delete("https://api.tm.noah-frank.de/tasks/<taskId>", headers={
    "Authorization": "Bearer your.jwt.token"
})
result = response.json()
```

> Returns:

```json
{
  "message": "Task successfully deleted",
  "taskId": "123"
}
```

### HTTP Request

`DELETE https://api.tm.noah-frank.de/tasks/<taskId>`
```

### Responses

| Status | Description               |
|--------|---------------------------|
| 200    | Task successfully deleted |
| 404    | Task not found            |
| 500    | Internal server error     |