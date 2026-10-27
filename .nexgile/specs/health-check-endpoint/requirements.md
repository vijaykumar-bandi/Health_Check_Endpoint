# Requirements: Health Check Endpoint

## Requirement 1: Answer the health check

**User Story:** As a developer, I want to call GET /health on the local app and see {"status":"ok"} come back, so that I know the service is up and can continue with my work.

_Source: G1, W1, A4_

### Acceptance Criteria

1. WHEN the app is running and a developer sends GET /health to the local port THE SYSTEM SHALL answer with HTTP status 200.
2. WHEN the app answers GET /health THE SYSTEM SHALL return the body {"status":"ok"} as JSON, with the Content-Type header set to application/json.
3. WHEN the app answers GET /health THE SYSTEM SHALL answer within 1 second.
4. WHEN the app answers GET /health THE SYSTEM SHALL answer every request the same way, whether it is the first request since the app started or the hundredth.
5. WHEN a developer sends GET /health with a query string or other request headers THE SYSTEM SHALL still answer with HTTP status 200 and the body {"status":"ok"}.

## Requirement 2: Answer only the health check

**User Story:** As a developer, I want the app to answer only GET /health and nothing else, so that it stays a minimal health check with no other surface.

_Source: W1, A10, S1_

### Acceptance Criteria

1. WHEN a developer sends a request for any path other than /health THE SYSTEM SHALL answer with HTTP status 404.
2. WHEN a developer sends a request to /health with a method other than GET THE SYSTEM SHALL answer with HTTP status 405.
3. WHEN the app answers a request THE SYSTEM SHALL keep no record of it and remember nothing between calls.
4. WHEN the app answers a request THE SYSTEM SHALL require no login, session or credential.

## Requirement 3: Start and stop on the local machine

**User Story:** As a developer, I want to start the app on my local machine on a port I choose and stop it when I am done, so that I can check the service whenever I need to.

_Source: W1, A23, A24_

### Acceptance Criteria

1. WHEN a developer starts the app with the port set in the environment variable PORT THE SYSTEM SHALL listen for HTTP requests on that port.
2. IF the port is already in use THEN THE SYSTEM SHALL exit with a non-zero exit code and print an error message naming the port.
3. WHEN a developer stops the app THE SYSTEM SHALL stop listening on the port and exit with exit code 0.
4. WHILE the app is not running THE SYSTEM SHALL leave the developer with a connection error from their browser or terminal, and nothing else.

## Assumptions

- The app answers on the port given in the environment variable `PORT`, since the brief names no port and Greenfield sets one for each run.
- The JSON body is exactly `{"status":"ok"}` with no other fields, as the description writes it.
- A request to `/health` with a method other than GET gets 405 rather than 404, so the developer can tell a wrong method from a wrong path.
- The 1-second answer time is measured on the local machine, as A4 and A17 assume.
- The app is English only and has no user interface, so no screen, role or start page applies.
- Nothing is deployed: the app runs only on the local machine, as F6 and the Deploy target decision say.

## Later releases

- S1 No user interface, database, login, or deployment; the app is only the health endpoint.
- S2 No mock-ups are needed.
