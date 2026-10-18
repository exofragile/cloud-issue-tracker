# Cloud Issue Tracker

Starter application used across the Cloud Computing and Cloud Security labs.

## Lab 1

- Development environment verified
- Static HTML structure created
- CSS interface created
- Git repository prepared

## Lab 2

- JavaScript practice file added
- npm and http-server configured
- Interactive frontend with in-memory data
- Create, view and change-status behaviours implemented

## Lab 3 — Application and API Design

The Cloud Issue Tracker now has a documented three-layer architecture and a REST API contract for create, view and status-change operations. The backend project is initialised and its data service reads and writes issues in backend/data/issues.json. See the files in /docs before implementing the API routes.

## Lab 4: Building the REST API with Node.js and Express

The backend is now a working Express REST API with three layers: `routes/issues.js` (method + path), `controllers/issues.js` (validation and HTTP responses) and `services/issueService.js` (reads and writes `data/issues.json`). `server.js` loads settings from `.env`, logs every request, parses JSON, and answers errors in one consistent shape (400, 404, 415 and 500).

Endpoints:

- `GET /api/issues`
- `POST /api/issues`
- `PATCH /api/issues/:id/status`

Run it:

```bash
cd backend
npm install
cp .env.example .env   # then adjust the values for your machine
npm run dev
```

The API is tested with Postman (`evidence/lab-04-postman-collection.json`) and the results are recorded in `docs/api-test-plan.md`. The frontend is not connected to the API yet.
