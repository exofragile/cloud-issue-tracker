# API Test Plan

Run these tests against the Lab 4 API implementation. Reset or prepare the JSON data file as needed so each test has the stated starting conditions. For request-body tests, send `Content-Type: application/json` unless the test specifically covers media type handling.

| Test ID | Scenario | Starting condition / request | Expected result |
|---|---|---|---|
| T01 | View all issues | `GET /api/issues` with one or more issues in the data file. | `200 OK`; response has `Content-Type: application/json` and the body is an array of complete issue objects. |
| T02 | Create a valid issue | `POST /api/issues` with a valid title, description, and priority. | `201 Created`; response is a complete issue with a generated unique `id`, trimmed client fields, `status` equal to `Open`, and an ISO 8601 `createdAt`. The new issue is persisted. |
| T03 | Create without title | `POST /api/issues` with title omitted and other valid client fields. | `400 Bad Request`; error code is `VALIDATION_ERROR`; no issue is added. |
| T04 | Create with invalid priority | `POST /api/issues` with a title and description meeting their rules and priority set to `Urgent`. | `400 Bad Request`; error code is `VALIDATION_ERROR`; no issue is added. |
| T05 | Change existing issue status | `PATCH /api/issues/{id}/status` for an existing issue with body `{"status":"Resolved"}`. | `200 OK`; response contains the complete issue with status `Resolved`; the change is persisted. |
| T06 | Change to invalid status | `PATCH /api/issues/{id}/status` for an existing issue with body `{"status":"Closed"}`. | `400 Bad Request`; error code is `VALIDATION_ERROR`; stored status remains unchanged. |
| T07 | Change status for unknown ID | `PATCH /api/issues/{unknown-id}/status` with a valid status. | `404 Not Found`; error code is `ISSUE_NOT_FOUND`. |
| T08 | View when there are no issues | Start with an empty issue list or a missing data file; send `GET /api/issues`. | `200 OK`; response body is the empty JSON array `[]`. |
| T09 | Create with server-owned fields | `POST /api/issues` with valid client fields plus client-supplied `id`, `status`, and `createdAt`. | Per this contract, the extra server-owned fields are ignored. `201 Created`; response has a server-generated ID and timestamp and status `Open`, not the supplied values. |
| T10 | Reject unsupported media type | Send a valid `POST` or `PATCH` body with a non-JSON `Content-Type`. | `415 Unsupported Media Type`; error code is `UNSUPPORTED_MEDIA_TYPE`; no data is changed. |
| T11 | Handle persistence failure | Make the data file unavailable or unwritable and send a valid create request. | `500 Internal Server Error`; error code is `INTERNAL_ERROR`; the API does not return a success-shaped response. |
