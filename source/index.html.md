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
    content: Documentation for the Kittn API
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

- `200 OK` – User registered successfully
- `400 Bad Request` – Username or password missing
- `500 Internal Server Error` – Something went wrong on the server

---

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

- `200 OK` – Login successful
- `400 Bad Request` – Username or password missing
- `401 Unauthorized` – Invalid credentials
- `500 Internal Server Error` – Something went wrong on the server
