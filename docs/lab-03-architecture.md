# Lab 3 — Application Architecture

## Application goal

The Cloud Issue Tracker allows users to record issues, view the current issue list, and mark an issue as resolved. Lab 3 defines the application architecture and REST API contract and prepares a Node.js data service that persists issues in a JSON file.

## Functional scope

The application supports these three operations:

1. **Create an issue** with a title, description, and priority.
2. **View all issues**.
3. **Change an issue's status** from `Open` to `Resolved` (and, where permitted by the API contract, between the supported statuses).

The browser frontend was built in Lab 2. The API routes and request-validation middleware are not implemented in this lab; they are planned for Lab 4.

## Three-layer architecture

```text
+--------------------------+
| Browser / Frontend       |
| User interface           |
+------------+-------------+
             | HTTP requests and JSON responses
=============|=================================== Trust boundary
             v
+--------------------------+
| REST API                 |
| Node.js + Express        |
| Routes and validation   |
+------------+-------------+
             | Calls data-service functions
             v
+--------------------------+
| Local Data Layer         |
| issueService.js          |
| data/issues.json         |
+--------------------------+
```

The frontend is responsible for displaying the interface and sending requests. The REST API will receive requests, validate and interpret client input, call the data service, and return HTTP responses. The local data layer reads and writes the JSON file and does not implement HTTP behavior.

## Request flows

### Create Issue

1. The user enters a title, description, and priority in the browser.
2. The frontend sends `POST /api/issues` with those client-owned fields as JSON.
3. The API checks the request media type and validates the body, including required fields, lengths, and allowed priority.
4. The API calls the data service to create the issue.
5. The service reads the existing issue array, generates a unique ID and creation timestamp, sets the initial status to `Open`, and writes the updated array to `backend/data/issues.json`.
6. The API responds with `201 Created` and the complete issue, including its server-owned fields.
7. The frontend displays the created issue or reports an error.

### View Issues

1. The user opens or refreshes the issue list in the browser.
2. The frontend sends `GET /api/issues`.
3. The API calls the data service to read the issue array.
4. The service reads and parses `backend/data/issues.json`; if the file does not exist yet, it returns an empty array.
5. The API responds with `200 OK` and a JSON array (which may be empty).
6. The frontend renders the returned issues.

### Change Issue Status

1. The user selects an issue and requests a status change.
2. The frontend sends `PATCH /api/issues/{id}/status` with the requested status as JSON.
3. The API checks the request media type and validates the status value.
4. The API asks the data service to find the issue by ID and update its status.
5. The service returns the updated issue after saving it, or returns `null` if the ID does not exist.
6. The API responds with `200 OK` and the updated issue, or `404 Not Found` when no issue has that ID.
7. The frontend updates the displayed issue or reports the error.

## Trust boundary and rules

The trust boundary is between the browser and the REST API. Data received from a browser must be treated as untrusted, even when it was entered through the application's own interface.

- The frontend may validate input to give users quick feedback, but **frontend validation is not enough; the API must validate every request** because clients can bypass the interface or send modified requests.
- The API owns validation, error responses, and authorization decisions when those are added.
- Clients may submit only `title`, `description`, and `priority` when creating an issue. The API/service determines `id`, `status`, and `createdAt`; client attempts to set server-owned fields are ignored by the service.
- For a status change, the client supplies only the requested `status`. The API must validate that it is an allowed value before calling the service.
- The data service performs persistence only. It does not validate input or trust client-provided server-owned fields.
- The JSON file is local application storage and should only be changed through the backend data service during normal application operation.
