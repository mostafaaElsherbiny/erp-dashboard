# ERP Dashboard — Level 2

Level 2 extends the ERP foundation in [LEVEL1.md](LEVEL1.md) with project management, task tracking, task discussions, and a complete JWT authentication contract. Choose the same stack as Level 1; the API behavior below is framework-agnostic.

Complete Level 1 first, then add these routes without breaking existing API contracts. Level 1 setup and technology guidance are in [LEVEL1.md](LEVEL1.md).

## Projects

Projects group related work and have an owner, dates, and a lifecycle status. A project cannot be deleted while it has active tasks; archive it or complete/reassign its tasks first.

### Frontend Routes

| Action | Route |
|--------|-------|
| List projects | `/projects` |
| Create project | `/projects/new` |
| View project and tasks | `/projects/:id` |
| Edit project | `/projects/:id/edit` |

### API Endpoints

```http
GET    /api/projects             — List projects (supports ?status=&ownerId=&search=&page=&pageSize=)
GET    /api/projects/{id}        — Get project and summary
POST   /api/projects             — Create project
PUT    /api/projects/{id}        — Replace project details
DELETE /api/projects/{id}        — Delete project when it has no active tasks
PATCH  /api/projects/{id}/status — Change project status
```

Project statuses are `Planned`, `Active`, `OnHold`, and `Completed`. The creator or a user with the `Admin` role may create a project. The project owner or an `Admin` may edit or delete it; project members may view it and manage assigned tasks.

### Sample Request: Create Project

```http
POST /api/projects
Authorization: Bearer <access-token>
Content-Type: application/json

{
  "name": "Warehouse Upgrade",
  "description": "Modernize inventory receiving and tracking.",
  "ownerId": 7,
  "startDate": "2026-07-01",
  "dueDate": "2026-10-30",
  "status": "Planned"
}
```

---

## Tasks

Tasks belong to a project and can be assigned to an employee. Each task has a title, description, priority, due date, and status. A task's project must exist, and its assignee must be an active employee or user.

### Frontend Routes

| Action | Route |
|--------|-------|
| List and filter tasks | `/tasks` |
| Create task | `/tasks/new` |
| View task and comments | `/tasks/:id` |
| Edit task | `/tasks/:id/edit` |

### API Endpoints

```http
GET    /api/tasks                  — List tasks (supports ?projectId=&assigneeId=&status=&priority=&dueBefore=&page=&pageSize=)
GET    /api/tasks/{id}             — Get task and comments
POST   /api/tasks                  — Create task
PUT    /api/tasks/{id}             — Replace task details
DELETE /api/tasks/{id}             — Delete task
PATCH  /api/tasks/{id}/status      — Update task status
```

Task statuses are `ToDo`, `InProgress`, `Blocked`, and `Done`; priorities are `Low`, `Medium`, `High`, and `Urgent`. Project owners and `Admins` may create, assign, edit, or delete project tasks. An assignee may view and update the status of their own tasks. Reject invalid project, assignee, status, or priority values with `400 Bad Request`; return `404 Not Found` for unknown IDs.

### Sample Request: Create Task

```http
POST /api/tasks
Authorization: Bearer <access-token>
Content-Type: application/json

{
  "projectId": 24,
  "title": "Add barcode scanning",
  "description": "Scan item barcodes during receiving.",
  "assigneeId": 12,
  "priority": "High",
  "dueDate": "2026-08-15",
  "status": "ToDo"
}
```

---

## Task Comments

Comments provide a discussion history on a task. Authors may edit or delete their own comments; project owners and `Admins` may moderate comments in their projects.

### API Endpoints

```http
GET    /api/tasks/{taskId}/comments             — List comments for a task
POST   /api/tasks/{taskId}/comments             — Add a comment
PUT    /api/tasks/{taskId}/comments/{commentId} — Edit an authored comment
DELETE /api/tasks/{taskId}/comments/{commentId} — Delete an authored or moderated comment
```

### Sample Request: Add Comment

```http
POST /api/tasks/42/comments
Authorization: Bearer <access-token>
Content-Type: application/json

{
  "body": "The scanner is ready for testing in the staging environment."
}
```

---

## API Reference and JWT Authentication

All Level 1 and Level 2 CRUD APIs require a JWT bearer access token. Only login and refresh are public.

```http
POST /api/auth/login   — Public: validate credentials and issue tokens
POST /api/auth/refresh — Public: rotate a refresh token and issue a new access token
POST /api/auth/logout  — Authenticated: revoke the current refresh-token session
GET  /api/auth/me      — Authenticated: return the current user's profile and roles
```

### Login Request

```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "admin@company.com",
  "password": "Admin@1234"
}
```

### Login Response

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "dGhpcyBpcy...",
  "tokenType": "Bearer",
  "expiresAt": "2026-06-14T10:00:00Z"
}
```

### Refresh Request

```http
POST /api/auth/refresh
Content-Type: application/json

{ "refreshToken": "<refresh-token>" }
```

The refresh response uses the same shape as the login response and returns a newly rotated refresh token.

### Logout Request

Logout requires a valid access token and revokes the supplied refresh-token session.

```http
POST /api/auth/logout
Authorization: Bearer <access-token>
Content-Type: application/json

{ "refreshToken": "<refresh-token>" }
```

Send the short-lived access token with each protected request:

```http
Authorization: Bearer <access-token>
```

### JWT Requirements

- Hash passwords with a modern password-hashing algorithm; never store or return plaintext passwords.
- Sign access tokens with a server-side secret or private key supplied through environment-based configuration. Never commit signing keys.
- Include the user ID (`sub`), roles, issued-at (`iat`), and expiry (`exp`) claims. Validate signature, issuer, audience, and expiry on every protected request.
- Use short-lived access tokens (for example, 15 minutes) and longer-lived refresh tokens. Store refresh tokens securely as hashes, rotate them on refresh, and revoke the used token and its token family if reuse is detected.
- Require authentication by default. Return `401 Unauthorized` for missing, invalid, or expired credentials and `403 Forbidden` when an authenticated user lacks permission.
- Enforce role and resource ownership rules on the server, not only in the frontend. Do not expose password hashes or refresh tokens in API responses.
- Logout revokes the current refresh-token session. Clients discard both tokens on logout; access tokens remain valid only until their short expiry unless access-token revocation is implemented.

## Standard API Behavior

- Use JSON request and response bodies except file downloads. Return `201 Created` on creation, `204 No Content` on successful deletion, `400 Bad Request` for validation errors, and `404 Not Found` for missing resources.
- Return list results in a consistent envelope: `{ "items": [], "page": 1, "pageSize": 20, "total": 0 }`. Enforce a maximum `pageSize` of 100.
- Document all routes, request/response schemas, authentication requirements, and error responses in OpenAPI/Swagger.

## Level 2 Acceptance Criteria

- Users can create, list, view, edit, and delete projects, tasks, and task comments through the documented APIs.
- Task lists can be filtered by project, assignee, status, priority, and due date, and support pagination.
- Project membership, task assignment, and comment ownership/moderation permissions are checked by the API for every relevant operation.
- Login issues a signed JWT access token and a refresh token; refresh rotates the refresh token; logout revokes the refresh session.
- Protected routes reject missing, invalid, expired, or revoked credentials with `401`; valid users without the required role or ownership receive `403`.
- Automated tests cover successful CRUD flows, validation failures, unauthenticated access, forbidden access, refresh rotation, and logout revocation.
- OpenAPI documentation describes all Level 2 endpoints and JWT behavior.

## Level 2 Schema Additions

```text
Projects       — id, name, description, ownerId, startDate, dueDate, status, createdAt
ProjectMembers — projectId, userId, role
Tasks          — id, projectId, title, description, assigneeId, priority, dueDate, status, createdAt
TaskComments   — id, taskId, authorId, body, createdAt, updatedAt
RefreshTokens  — id, userId, tokenHash, familyId, expiresAt, revokedAt
```
