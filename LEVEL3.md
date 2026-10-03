# ERP Dashboard — Level 3

Level 3 adds operational insight and automation to the ERP foundation in [LEVEL1.md](LEVEL1.md) and the secure project operations in [LEVEL2.md](LEVEL2.md). It remains framework-agnostic; use the same stack and API conventions as the earlier levels.

Complete Levels 1 and 2 first. Level 3 introduces reporting, background processing, notifications, immutable audit history, and outbound webhooks without changing existing API contracts.

## Analytics and Reports

Provide a dashboard and report endpoints for sales, inventory, invoices, and task progress. Reports are computed from existing records; do not create duplicate report data as the source of truth.

### API Endpoints

```http
GET  /api/reports/sales       — Sales totals and order counts (supports ?from=&to=&groupBy=day|week|month)
GET  /api/reports/inventory   — Stock levels and low-stock products (supports ?categoryId=&lowStock=)
GET  /api/reports/invoices    — Invoice totals by status and aging (supports ?from=&to=&status=)
GET  /api/reports/tasks       — Task totals by status, project, and assignee (supports ?projectId=&assigneeId=&from=&to=)
POST /api/reports/exports     — Queue a CSV export and return a job ID
GET  /api/jobs/{id}           — Get export job status and download link when complete
```

Reports accept ISO 8601 dates and use the configured base currency. Exports are generated asynchronously; download links expire and are available only to the requesting user or an authorized administrator. Restrict finance reports to users with the appropriate role and scope task reports to projects the caller may access.

### Sample Request: Queue Sales Export

```http
POST /api/reports/exports
Authorization: Bearer <access-token>
Content-Type: application/json

{
  "report": "sales",
  "format": "csv",
  "filters": {
    "from": "2026-01-01",
    "to": "2026-03-31",
    "groupBy": "month"
  }
}
```

---

## Workflow Automation

Allow authorized managers to configure rules for routine business events. Start with `InventoryBelowReorderLevel` and `InvoiceOverdue` triggers and `NotifyRole` or `NotifyUser` actions. Rules can be enabled or disabled and must be validated against supported triggers and actions.

### API Endpoints

```http
GET    /api/automation-rules       — List rules visible to the caller
GET    /api/automation-rules/{id}  — Get a rule
POST   /api/automation-rules       — Create a rule (Admin or Manager)
PUT    /api/automation-rules/{id}  — Update a rule (Admin or Manager)
DELETE /api/automation-rules/{id} — Delete a rule (Admin or Manager)
PATCH  /api/automation-rules/{id}/enabled — Enable or disable a rule
GET    /api/automation-runs       — List execution history (supports ?ruleId=&status=&from=&to=)
GET    /api/automation-runs/{id}  — Get execution result
```

Evaluate rules asynchronously and make event processing idempotent so retries do not create duplicate actions. Record each run's trigger, outcome, timestamps, and safe error details. Do not allow a rule to bypass the acting user's permissions.

### Sample Rule

```json
{
  "name": "Alert when stock is low",
  "trigger": "InventoryBelowReorderLevel",
  "action": {
    "type": "NotifyRole",
    "role": "InventoryManager"
  },
  "enabled": true
}
```

---

## Notifications

Support in-app notifications and optional email delivery. Users can view notifications, check their unread count, mark one or more as read, and manage delivery preferences.

### API Endpoints

```http
GET   /api/notifications                 — List the current user's notifications (supports ?unreadOnly=&page=&pageSize=)
GET   /api/notifications/unread-count    — Get unread count
PATCH /api/notifications/{id}/read      — Mark one notification as read
PATCH /api/notifications/read           — Mark selected notifications as read
GET   /api/notification-preferences      — Get current user's delivery preferences
PUT   /api/notification-preferences      — Update current user's delivery preferences
```

Users may access only their own notifications and preferences, except administrators with an explicitly granted support permission. Notification delivery failures must not roll back the business operation that triggered the notification; retry delivery asynchronously and expose a safe delivery status.

---

## Audit History

Record security-relevant and business-critical changes, including user and role changes, inventory adjustments, order and invoice status changes, project/task updates, automation rule changes, and webhook configuration changes. Audit records are append-only and cannot be edited or deleted through the API.

### API Endpoints

```http
GET /api/audit-logs — Search audit history (supports ?actorId=&entityType=&entityId=&action=&from=&to=&page=&pageSize=)
GET /api/audit-logs/{id} — Get one audit event
```

Each event includes its actor, action, entity type and ID, timestamp, and a redacted before/after change summary when applicable. Never record passwords, access or refresh tokens, webhook secrets, or other credentials. Restrict access by role and resource scope, and paginate all results.

---

## Webhooks

Allow authorized administrators to register HTTPS endpoints and subscribe to supported events: `order.created`, `invoice.paid`, `inventory.low_stock`, and `task.status_changed`. Sign each delivery with an HMAC-SHA256 signature and a timestamp header so receivers can verify authenticity and reject stale replays.

### API Endpoints

```http
GET    /api/webhook-endpoints                    — List configured endpoints
GET    /api/webhook-endpoints/{id}               — Get endpoint configuration (never return its secret)
POST   /api/webhook-endpoints                    — Register an endpoint and reveal its secret once
PUT    /api/webhook-endpoints/{id}               — Update endpoint URL or event subscriptions
DELETE /api/webhook-endpoints/{id}               — Disable and remove an endpoint
POST   /api/webhook-endpoints/{id}/test          — Queue a signed test delivery
GET    /api/webhook-endpoints/{id}/deliveries    — List delivery attempts and statuses
POST   /api/webhook-deliveries/{id}/retry        — Retry a failed delivery
```

Validate endpoint URLs and reject non-HTTPS destinations in production. Protect against server-side request forgery by blocking loopback, private, link-local, and metadata-service destinations, including after DNS resolution and redirects. Encrypt stored signing secrets, redact them from logs and responses, and support secret rotation.

Deliveries include a unique event ID and use bounded exponential-backoff retries. Send a stable idempotency key so consumers can safely handle repeated deliveries. Disable or pause endpoints after repeated permanent failures and expose delivery status to administrators.

---

## Level 3 Acceptance Criteria

- Authorized users can view and filter the four report types; CSV exports run asynchronously, enforce access checks, and expire after a configured period.
- Managers can create, edit, enable, disable, and delete supported automation rules; execution is recorded and retry-safe.
- Users can view and manage their own notifications and preferences; failed email delivery does not undo business transactions.
- Audit events are append-only, paginated, access-controlled, and redact credentials and other sensitive values.
- Webhook deliveries are HTTPS-only, signed, replay-resistant, idempotent, retried with bounded backoff, and protected against SSRF.
- Tests cover report authorization and filters, automation idempotency, notification ownership, audit immutability/redaction, webhook signature verification, retries, and URL validation.
- OpenAPI documents Level 3 APIs, request and response shapes, role requirements, and error behavior.

## Level 3 Schema Additions

```text
AutomationRules       — id, name, trigger, action, enabled, createdBy, createdAt, updatedAt
AutomationRuns        — id, ruleId, eventId, status, startedAt, completedAt, errorSummary
Notifications         — id, userId, type, title, body, readAt, createdAt
NotificationSettings  — userId, channel, eventType, enabled
AuditLogs             — id, actorId, action, entityType, entityId, changes, occurredAt, correlationId
WebhookEndpoints      — id, url, eventTypes, encryptedSecret, enabled, createdBy, createdAt
WebhookDeliveries     — id, endpointId, eventId, status, attemptCount, nextAttemptAt, lastResponseCode
BackgroundJobs        — id, type, requestedBy, status, resultLocation, expiresAt, createdAt, completedAt
```
