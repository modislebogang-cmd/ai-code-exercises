# Developer Guide for `POST /api/users/register`

## 1. Authentication
- No authentication is required for this endpoint.
- It is a public registration endpoint, so clients can call it without tokens or credentials.

## 2. Proper Request Formatting

### URL
- `POST /api/users/register`

### Headers
- `Content-Type: application/json`

### Body
Send JSON with these required fields:
- `username` (string)
- `email` (string)
- `password` (string)

Example:
```json
{
  "username": "jdoe",
  "email": "jdoe@example.com",
  "password": "SecurePass123"
}
```

## 3. Handling and Interpreting Responses

### Success: `201 Created`
Returns:
- `message`: success string
- `user`: object with user data excluding password

Example:
```json
{
  "message": "User registered successfully",
  "user": {
    "id": 123,
    "username": "jdoe",
    "email": "jdoe@example.com",
    "created_at": "2026-08-09T12:34:56.789000",
    "role": "user"
  }
}
```

### Client Errors: `400 Bad Request`
Occurs when input validation fails:
- missing required field
- invalid email format
- weak password

Example:
```json
{
  "error": "Missing required field",
  "message": "email is required"
}
```

### Conflict: `409 Conflict`
Occurs when:
- username already exists
- email already exists

Examples:
```json
{
  "error": "Username taken",
  "message": "Username is already in use"
}
```
```json
{
  "error": "Email exists",
  "message": "An account with this email already exists"
}
```

### Server Error: `500 Internal Server Error`
Returned on unexpected failures during user creation or commit:
```json
{
  "error": "Server error",
  "message": "Failed to register user"
}
```

## 4. Common Errors and Handling

### Missing fields
- Validate client-side before sending.
- Ensure `username`, `email`, and `password` are present.

### Invalid email
- The endpoint uses a simple regex to validate email format.
- It requires `@` and a domain with a dot after the `@`.

### Weak password
- Password must be at least 8 characters long.
- Enforce this rule before submitting the request.

### Duplicate account
- Handle `409` responses by prompting for a different username or email.

### Email delivery
- The endpoint attempts to send a confirmation email after registration.
- If email sending fails, the registration still succeeds and the failure is logged.
- Treat registration success as separate from email delivery success.

## 5. Example Python Flask Client Code

```python
import requests

API_URL = "https://example.com/api/users/register"


def register_user(username, email, password):
    payload = {
        "username": username,
        "email": email,
        "password": password
    }
    headers = {
        "Content-Type": "application/json"
    }

    response = requests.post(API_URL, json=payload, headers=headers)

    if response.status_code == 201:
        data = response.json()
        return {
            "success": True,
            "user": data["user"]
        }

    if response.status_code == 400:
        error = response.json()
        return {
            "success": False,
            "reason": "validation",
            "details": error
        }

    if response.status_code == 409:
        error = response.json()
        return {
            "success": False,
            "reason": "conflict",
            "details": error
        }

    return {
        "success": False,
        "reason": "server_error",
        "details": response.json()
    }


if __name__ == "__main__":
    result = register_user("jdoe", "jdoe@example.com", "SecurePass123")
    print(result)
```

## 6. Notes
- `email` is normalized to lowercase before persistence.
- Passwords are hashed before storing.
- The endpoint never returns password or password hash data.
- Because no authentication is required, production deployments should add rate limiting to prevent abuse.
