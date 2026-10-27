# Design: Health Check Endpoint

## Overview

A minimal local Node.js app that answers one request: `GET /health` returns HTTP 200 with the body `{"status":"ok"}` and the header `Content-Type: application/json`. It has no user interface, no database, no login, no deployment and no state: every request is answered the same way and nothing is remembered between calls.

The app is built with Node.js and its built-in `http` module only, as the stack decision in the answers says. There are no runtime dependencies, so `npm install` installs only the development tools used by the tests.

The only actor is the developer who runs the app on their own machine. They start it, call `GET /health` from a browser or a terminal, see `{"status":"ok"}`, and stop it.

## Architecture

One process, one HTTP server, one request handler.

```
developer (browser or terminal)
        |
        |  GET /health
        v
+-------------------------------+
| node server.js                |
|   http.createServer(handler)  |
|   listen on process.env.PORT  |
+-------------------------------+
        |
        +-- path /health, method GET  -> 200 {"status":"ok"}
        +-- path /health, other method -> 405
        +-- any other path            -> 404
```

The handler is a pure function of the request's path and method. It reads no file, opens no connection and writes nothing, so it holds no state and answers the first request and the hundredth identically.

The server is created in a module that exports the handler and a `start` function, so tests can start it on an ephemeral port (`PORT=0`) and stop it without touching the developer's own port. The entry point `server.js` calls `start` with `process.env.PORT` when it is run directly.

## Components and Interfaces

### `src/handler.js`

Exports `handleRequest(req, res)`.

- Parses the request path with `new URL(req.url, 'http://localhost')` and compares `pathname` to `/health`, so a query string such as `/health?probe=1` still matches (criterion 1.5).
- `GET /health` → status 200, header `Content-Type: application/json`, body `{"status":"ok"}` (criteria 1.1, 1.2).
- `/health` with any other method → status 405, header `Allow: GET`, empty body (criterion 2.2).
- Any other path → status 404, empty body (criterion 2.1).
- Writes no log line that records the request, keeps no counter and no timestamp (criterion 2.3).
- Checks no header, cookie or credential (criterion 2.4).

The body is a constant string, so the answer time is the time to write a few bytes on the loopback interface, well under the 1 second of criterion 1.3.

### `src/server.js`

Exports `start(port)` and `stop(server)`.

- `start(port)` creates the server with `handleRequest`, listens on `port`, and resolves with the server once it is listening.
- On a listen error with code `EADDRINUSE`, it prints `Port <port> is already in use` to stderr and exits with code 1 (criterion 3.2).
- `stop(server)` closes the server and resolves once it has stopped listening (criterion 3.3).

### `server.js`

The entry point. Reads `process.env.PORT`, exits with code 1 and a message naming `PORT` when it is unset or not a number, otherwise calls `start(port)` and prints `Listening on port <port>` to stdout. It handles `SIGINT` and `SIGTERM` by calling `stop` and exiting with code 0 (criterion 3.3).

### `package.json`

Scripts:

- `start`: `node server.js`
- `dev`: `node server.js` (the port comes from `PORT`, never a fixed value)
- `test`: `node --test`

`node --test` runs every `*.test.js` file once and exits, and it leaves the `e2e/` folder alone because no file there matches `*.test.js`.

## Data Models

None. The app stores nothing, reads nothing and keeps no records between calls (criterion 2.3). The only data is the constant response body `{"status":"ok"}`.

## Error Handling

| Situation | Response |
| --- | --- |
| `GET /health` | 200, `{"status":"ok"}`, `Content-Type: application/json` |
| `/health` with another method | 405, `Allow: GET` |
| Any other path | 404 |
| `PORT` unset or not a number | stderr message naming `PORT`, exit code 1 |
| Port already in use | stderr message naming the port, exit code 1 |
| App not running | The browser or terminal shows its own connection error; the app does nothing (criterion 3.4) |

No error response carries a stack trace or a file path, and no error is logged with request details.

## Testing Strategy

Tests use the built-in `node:test` runner and `node:assert`, so there is nothing to install for them and no test framework to configure.

- **Handler tests** (`src/handler.test.js`) call `handleRequest` with a fake request and a fake response object, or start the server on port `0` and call it with `fetch`. They cover 1.1, 1.2, 1.5, 2.1, 2.2 and 2.4.
- **Server tests** (`src/server.test.js`) start the server on port `0`, send repeated requests and check that every answer is identical (1.4), check the answer arrives within 1 second (1.3), check that `stop` closes the port (3.3), and check that starting twice on the same port exits with code 1 and a message naming the port (3.2).
- **Entry point tests** (`server.test.js`) spawn `node server.js` with `PORT` set to a free port, wait for the listening line, call `GET /health`, then send `SIGINT` and check the exit code is 0 (3.1, 3.3). They spawn it with `PORT` unset and check the exit code is 1 (3.2).
- **End-to-end journeys** live in `e2e/` and are written by Greenfield's Tester against the running app; the unit runner does not pick them up.

Every test starts its own server on port `0` or a free port it picks, so tests run in parallel and need no `.env` file, no fixed port and no service started by hand.

## Correctness Properties

### Property P1: Every GET /health answer is identical
For any sequence of requests where each request is `GET /health`, optionally with a query string and any headers, every response has status 200, `Content-Type: application/json` and the body `{"status":"ok"}`.
- _Validates: 1.1, 1.2, 1.4, 1.5_
- _Kind: invariant_

### Property P2: Only GET /health succeeds
For any request where the path is not `/health` or the method is not `GET`, the response status is 404 when the path is not `/health` and 405 when the path is `/health` with another method, and the body is never `{"status":"ok"}`.
- _Validates: 2.1, 2.2_
- _Kind: error_

### Property P3: The answer does not depend on the request's headers
For any request to `GET /health` where the headers differ, including a `Cookie`, an `Authorization` header or none at all, the response is the same 200 with `{"status":"ok"}`.
- _Validates: 1.5, 2.4_
- _Kind: metamorphic_

### Property P4: The app answers within 1 second
For any request to `GET /health` on the local machine, the response arrives within 1 second.
- _Validates: 1.3_
- _Kind: invariant_

### Property P5: Starting and stopping leaves the port free
For any free port, starting the app on it and then stopping it leaves the port free, and the process exits with code 0.
- _Validates: 3.1, 3.3_
- _Kind: round-trip_

### Property P6: A port already in use is refused
For any port that another server already listens on, starting the app on that port exits with a non-zero code and prints a message naming the port.
- _Validates: 3.2_
- _Kind: error_

### Property P7: The app is not running
- _Validates: 3.4_
- _Not property-testable: the connection error comes from the developer's browser or terminal, not from the app, so there is no app output to generate inputs against._

## Build and run
Install: npm install
Test: npm test
Run locally: yes
Dev: npm run dev
Port variable: PORT
Health path: /health
Deploy: none

## Assumptions

- The app is a single Node.js package at the repository root, with no workspaces and no build step, since the brief asks for a minimal app.
- `PORT` is required: the app exits with code 1 when it is unset, rather than falling back to a fixed default, so Greenfield can always start it on a free port.
- The 405 response carries an `Allow: GET` header, which is the standard way to name the allowed method and costs nothing.
- Error responses (404, 405) have an empty body, since the requirements name no body for them.
- Tests use `node:test` and `node:assert` from Node.js itself, so the only dev dependency is `@playwright/test` for the Tester's journeys in `e2e/`.
- The app logs only its listening line and its errors; it never logs a request, so nothing is recorded between calls.
