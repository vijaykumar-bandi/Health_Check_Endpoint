# Requirements brief — Health Check Endpoint
## Profile
A minimal local Node.js app with a single health-check endpoint for developers to verify it is running. · API · software development · size: small · depth: quick
- **Has a user interface?** No (The description says there is no user interface.)
- **Runs locally?** Yes (It is a Node.js API that serves GET /health on a local port, so Greenfield can start it on the person's machine.)
## The app
A minimal local Node.js app with a single GET /health endpoint that answers {"status":"ok"}, for a developer to confirm the service is running. It has no user interface, database, login, deployment or records, and keeps nothing between calls.
## Goals and success
- G1 Provide a minimal local Node.js app with one GET /health endpoint returning {"status":"ok"}. (description)
## Workflows
### W1 Check the app is running (release 1) — actors: developer; trigger: The developer wants to confirm the service is up.; outcome: The developer knows the service is up and continues with their work.; how often: about once a day; module: health check (Q4, Q12, Q13)
1. Start the app on the local machine.
2. Call GET /health from a browser or a terminal.
3. See {"status":"ok"} come back.
4. Stop the app when done.
## Decisions
- **Stack:** Node.js with the built-in http module (the user's choice; suggested: The app is one endpoint with no database or login, so the built-in module keeps it minimal with nothing to install.) (Q7)
- **Deploy target:** No deploy for now (the user's choice; suggested: The description says the app runs locally with no deployment, so nothing needs hosting yet.) (Q8)
## Assumptions
- A1 The health endpoint is used by developers and automated checks to confirm the service is up. — The description says it is for developers to verify it is running. [assumed] (description)
- A2 The app is used by the developer who runs it locally to confirm the service is up. — The description says it is for developers to verify it is running, and no other users were named. [assumed] (assumption, Q1, Q5)
- A3 The app keeps no records of its own and remembers nothing between calls; it only answers the health check. — The description says no database, and the person confirmed the app keeps no records and no history. [assumed] (assumption, Q6, Q14)
- A4 Success is the health endpoint answering {"status":"ok"} within 1 second, every time, from the day it first runs. — The description gives no measure; a health check is only useful if it answers fast and reliably. [assumed] (assumption)
- A5 Today the developer confirms the service is up by starting it and looking at the console or calling the endpoint by hand; there is no tooling and no data to move. — The description says the app is new and minimal; no current tool was named. [assumed] (assumption)
- A6 The closest products are the built-in health checks of frameworks such as Express or Fastify; copy their plain {"status":"ok"} answer, avoid adding metrics or dependencies. — The description asks for a minimal health endpoint and no similar product was named. [assumed] (assumption)
- A7 The only business event is a request to GET /health; the app answers it immediately and nothing is timed. — The description names one endpoint and no timed events. [assumed] (assumption)
- A8 If the app is not running, the developer sees a connection error and starts it again; there is no other exception. — The only workflow is checking the app is running, and its failure is the app being down. [assumed] (assumption)
- A9 There are no permissions: anyone who can reach the local port may call GET /health, and no one signs in. — The description says no login and the app runs locally. [assumed] (assumption)
- A10 There are no business rules beyond answering GET /health with {"status":"ok"} and answering other paths with a not-found response. — The description gives one endpoint and no rules. [assumed] (assumption)
- A11 There are no notifications: the developer sees the answer in the browser or terminal and no one is told anything. — The description names no notifications and the app runs locally. [assumed] (assumption)
- A12 There are no reports: the health answer itself is the only output. — The description asks for one endpoint and no reporting. [assumed] (assumption)
- A13 There is no administration: nothing is configured in the app and changing the answer means changing the code. — The description says minimal and no database or settings. [assumed] (assumption)
- A14 There is no money in the app: no pricing, payments, refunds, tax or invoices. — The description names no money and the app is a local health check. [assumed] (assumption)
- A15 There are no files: the app uploads, stores and serves nothing. — The description says no database and one endpoint. [assumed] (assumption)
- A16 At the busiest moment one developer calls GET /health, about once a day; there are no records in year 1 or year 3. — The description and answers name one developer and no records. [assumed] (assumption, Q12, Q15)
- A17 GET /health answers in under 1 second on the local machine. — A health check is only useful if it answers quickly. [assumed] (assumption)
- A18 An hour of outage costs nothing beyond the developer not being able to check; there is no data to lose and no recovery needed. — The app runs locally, keeps no records and has no users beyond the developer. [assumed] (assumption, Q15)
- A19 There is no sign-in and no security beyond the local machine: the worst misuse is someone on the same machine calling the endpoint, which reveals nothing. — The description says no login and the app runs locally. [assumed] (assumption)
- A20 The app holds no personal data, serves users in no particular country, keeps nothing and needs no consent or deletion. — The app keeps no records and has no users beyond the developer. [assumed] (assumption, Q15)
- A21 There is no audit trail: nothing is recorded, so nothing can be proved beyond the answer itself. — The app keeps no history. [assumed] (assumption, Q11, Q15)
- A22 The app is English only, left-to-right, uses the machine's time zone and no currency; accessibility is basic because there is no interface. — The description is in English and the app has no user interface. [assumed] (assumption)
- A23 The app is used on a desktop or laptop in a terminal or browser; it needs no offline mode because it runs locally. — The description says local and no user interface. [assumed] (assumption)
- A24 Support is the developer who runs the app: they see the console output and restart it if it stops. — The app runs locally and has one user. [assumed] (assumption)
- A25 There is no existing data to move: the app starts empty and nothing runs side by side. — The app keeps no records and is new. [assumed] (assumption, Q15)
- A26 There is one organisation only: the developer's own machine, with no separation, branding or sign-up. — The app runs locally for one developer. [assumed] (assumption, Q5)
- A27 There are no integrations: no monitoring tool or other system calls the endpoint; if one does later, it reads the answer and nothing is sent back. — The person was not sure who calls the endpoint and named no other system. [assumed] (assumption, Q2)
- A28 The first release is the whole app: the one workflow of checking the app is running. — The description asks for a minimal app with one endpoint. [assumed] (assumption)
- A29 A year after launch the app most likely failed because nobody used it: a health check with no monitoring tool behind it adds nothing. — The person was not sure who calls the endpoint, so adoption is the main risk. [assumed] (assumption, Q2)
## Other facts
- F1 The app is built with Node.js. [stack] (description)
- F2 The app runs locally. [hosting] (description)
- F3 The person does not know who will call the health endpoint or what they will do with the answer. [stakeholders] (Q2)
- F4 The person is not sure what records the app needs to keep track of. [entities] (Q6)
- F5 The app is built with Node.js using the built-in http module. (Q7)
- F6 There is no deployment for now; the app runs only on the local machine. (Q8)
- F7 No one is worse off because the health-check app exists; it only helps developers confirm the service is running. [stakeholders] (Q9)
- F8 No one is affected by the app but would never sign in; only the developer running it is affected. [stakeholders] (Q10)
- F9 The app remembers nothing between calls: it only answers each health check and keeps no history. [entities] (Q11)
- F10 A developer checks the health endpoint about once a day. [workflows] (Q12)
- F11 After seeing the health check answer, the developer confirms the service is up and continues with their work. [workflows] (Q13)
- F12 The app keeps no records at all: nothing to create, change or delete. [entities] (Q15)
## Out of scope and later releases
- S1 No user interface, database, login, or deployment; the app is only the health endpoint. [out of scope] (description, Q3)
- S2 No mock-ups are needed. [out of scope] (description)
## The interview
- Q1 [stakeholders · owner] Who will use the app, and about how many people of each kind? → Not sure (settled: The app is used by the developer who runs it locally to confirm the service is up.)
- Q2 [stakeholders · owner] Who will call the health endpoint, and what will they do with the answer? → Not sure
- Q3 [scope · owner] Which of these comes closest to what 'minimal' means for this app? → Only the health endpoint
- Q4 [workflows · owner] Walk me through how someone will check the app is running, step by step. → 1. Start the app on the local machine. 2. Open a browser or a terminal and call GET /health. 3. See {"status":"ok"} come back. 4. Stop the app when done.
- Q5 [stakeholders · admin] Who is most likely to call the health endpoint? → Only the developer running it
- Q6 [entities · admin] What things does the app need to keep track of? → Not sure
- Q7 [stack · owner] Which stack should the health-check app be built with? → Node.js with the built-in http module
- Q8 [hosting · owner] Where should this health-check app be deployed? → No deploy for now
- Q9 [stakeholders · negative] Who, if anyone, would be worse off because this health-check app exists? → No one — the app only helps developers confirm the service is running. (Greenfield's draft, sent unchanged)
- Q10 [stakeholders · negative] Who is affected by the app but would never sign in to it? → No one — only the developer running it is affected. (Greenfield's draft, sent unchanged)
- Q11 [entities · negative] What, if anything, should the app remember between calls, such as a count of health checks or the time of the last one? → Nothing — it only answers each health check and keeps no history. (Greenfield's draft, sent unchanged)
- Q12 [workflows · owner] How many times a day does a developer check the health endpoint? → 1 checks per day
- Q13 [workflows · owner] What does the developer do after seeing the health check answer? → They confirm the service is up and continue with their work. (Greenfield's draft, sent unchanged)
- Q14 [entities · admin] The app keeps no records of its own and remembers nothing between calls — is that right? → Yes, that's right
- Q15 [entities · admin] The app keeps no records at all — nothing to create, change or delete — is that right? → Yes, that's right
