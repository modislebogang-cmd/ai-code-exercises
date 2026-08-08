# Findings for User Registration Endpoint

## Endpoint
- `POST /api/users/register`

## Purpose
Registers a new user by validating the request body, checking uniqueness of username and email, hashing the password, saving the user record, and sending a confirmation email.

## Request
### Path Parameters
- None

### Query Parameters
- None

### Body (JSON)
```json
{
  "username": "string",
  "email": "string",
  "password": "string"
}
```

### Body Fields
- `username` (string) — required
  - Desired account username.
- `email` (string) — required
  - User email address. Must be valid.
- `password` (string) — required
  - Password for the new account. Must be at least 8 characters.

## Responses
### Success `201 Created`
```json
{
  "message": "User registered successfully",
  "user": {
    "id": 123,
    "username": "jdoe",
    "email": "jdoe@example.com",
    "created_at": "2026-08-04T12:34:56.789000",
    "role": "user"
  }
}
```

### Validation Error `400 Bad Request`
Missing required field:
```json
{
  "error": "Missing required field",
  "message": "email is required"
}
```
Invalid email format:
```json
{
  "error": "Invalid email",
  "message": "Please provide a valid email address"
}
```
Weak password:
```json
{
  "error": "Weak password",
  "message": "Password must be at least 8 characters long"
}
```

### Conflict `409 Conflict`
Username taken:
```json
{
  "error": "Username taken",
  "message": "Username is already in use"
}
```
Email exists:
```json
{
  "error": "Email exists",
  "message": "An account with this email already exists"
}
```

### Server Error `500 Internal Server Error`
```json
{
  "error": "Server error",
  "message": "Failed to register user"
}
```

## Authentication
- Not required. This is a public registration endpoint.

## Potential Error Responses
- `400 Bad Request`
  - Required field missing
  - Invalid email format
  - Password too short
- `409 Conflict`
  - Username already taken
  - Email already registered
- `500 Internal Server Error`
  - Database save failure
  - Unexpected server exception
  - Email sending failure is logged but does not block registration

## Example Requests
### Example 1: Successful Registration
```bash
curl -X POST https://example.com/api/users/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "jdoe",
    "email": "jdoe@example.com",
    "password": "s3cur3P@ssw0rd"
  }'
```

Response:
```json
{
  "message": "User registered successfully",
  "user": {
    "id": 42,
    "username": "jdoe",
    "email": "jdoe@example.com",
    "created_at": "2026-08-04T12:34:56.789000",
    "role": "user"
  }
}
```

### Example 2: Email Already Exists
```bash
curl -X POST https://example.com/api/users/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "newuser",
    "email": "existing@example.com",
    "password": "password123"
  }'
```

Response:
```json
{
  "error": "Email exists",
  "message": "An account with this email already exists"
}
```

## Special Considerations
- Passwords are hashed before storage.
- Email is normalized to lowercase before saving.
- Confirmation email sending is attempted after registration. If email delivery fails, registration still succeeds, and the failure is logged.
- There is no rate limiting in the implementation shown, so adding request throttling is recommended to prevent abuse.
