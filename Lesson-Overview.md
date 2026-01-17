# Full-Stack Web Development — 26×1hr Lesson Schedule

This schedule breaks the basics of full-stack web development into 26 one-hour tutoring sessions. The final capstone is a simple CRUD Task/Notes web app (optional user accounts), with persistent storage and a basic deployed instance.

## How to use this schedule
- Each lesson ≈ 60 minutes: 30–40m guided instruction, 15–25m hands-on exercise, short homework.
- Materials: short links provided in the References section. Use a minimal stack: TypeScript + Vite frontend (bundled) + Bootstrap for styling, and ASP.NET Core (C#) backend running under WSL2 + Entity Framework Core with SQLite (or Postgres) DB.

---

## Week 1 — Front-end Foundations

### Lesson 1 — Orientation & Tooling
- Objective: Set expectations; install `git`, VS Code, Node.js; create repository skeleton.
- Materials: VS Code, GitHub account, Node.js installer.
- In-class: Initialize repo, create README, basic `.gitignore`.
- Exercise: Make initial commit with goals and app idea.
- Homework: Read a short primer on HTTP and client/server.

### Lesson 2 — HTML Basics
- Objective: Build semantic page structure for the app.
- Materials: `index.html` template.
- In-class: Create header, form, list placeholders for tasks/notes.
- Exercise: Mark up sample tasks and forms.
- Homework: Add additional content pages (about/help).

### Lesson 3 — Styling with Bootstrap (and CSS fundamentals)
- Objective: Build a polished UI using Bootstrap components and utilities; reinforce CSS fundamentals (box model, flexbox, responsive basics).
- Materials: Bootstrap CSS (CDN) and optional `styles.css` starter for custom overrides.
- In-class: Use Bootstrap grid, forms, buttons, and utilities to style the task list and form; demonstrate customizing with a small CSS override.
- Exercise: Implement a mobile-friendly layout using Bootstrap classes and utilities.
- Homework: Read up on core CSS, flexbox, and responsive design basics (links in References / Homework section).

### Lesson 4 — DOM Manipulation & Events
- Objective: Learn DOM manipulation and event handling with JavaScript.
- Materials: `app.js` starter with basic JavaScript.
- In-class: Select elements, handle form submit and click events, dynamically add/remove tasks.
- Exercise: Implement task add, delete, and re-render functionality using vanilla JavaScript.
- Homework: Persist tasks to `localStorage`; prepare for TypeScript transition.

### Lesson 26 — TypeScript Advanced & Type-Safe DOM
- Objective: Set up TypeScript; use types and interfaces for type-safe DOM manipulation and data management.
- Materials: `tsconfig.json`, TypeScript compiler setup, `app.ts` refactor.
- In-class: Set up TypeScript project; convert `app.js` to typed `app.ts` with interfaces.
- Exercise: Compile TypeScript and verify type safety with DOM element assertions.
- Homework: Practice optional properties in interfaces; set up watch mode.

### Lesson 26 — Debugging & DevTools
- Objective: Use browser DevTools, console, and basic debugging flows.
- In-class: Demonstrate breakpoints, network panel, and performance hints.
- Exercise: Fix an intentional bug and profile a slow operation.
- Homework: Refactor TS into modules.

---

## Week 2 — Modern JS & Build Tools

### Lesson 26 — Modern TypeScript
- Objective: Use modern TypeScript features: `let/const`, arrow functions, async/await, interfaces/types, and modules.
- In-class: Rewrite parts of the app with TypeScript types; demonstrate interfaces for the task model and `fetch` with typed responses.
- Exercise: Fetch data from a public API and create TypeScript interfaces for the response.
- Homework: Read about TypeScript basics and `tsconfig` options.

### Lesson 26 — Package Management & Vite (TypeScript)
- Objective: Initialize `package.json`, set up Vite with TypeScript, and run the local dev server with hot module replacement.
- In-class: `npm init`, `npm create vite@latest` with the `vanilla-ts` or `react-ts` template; add `npm start` and `npm run build` scripts.
- Exercise: Start the Vite dev server and confirm TypeScript compilation and HMR.
- Homework: Explore a beginner React/TypeScript tutorial (optional).

### Lesson 26 — Intro to Component Thinking / SPA concepts (optional)
- Objective: Understand componentization and simple routing ideas using TypeScript modules or a framework.
- In-class: Implement view switching for All/Completed tasks without page reload using TypeScript modules (or a minimal React + TypeScript example if using a framework).
- Exercise: Build a small component structure with TypeScript (or with React+TS) and simple client-side routing.
- Homework: Read about single-page apps, component patterns, and progressive enhancement.

---

## Week 3 — Back-end Basics

### Lesson 26 — ASP.NET Core Intro
- Objective: Create a minimal ASP.NET Core Web API that returns JSON.
- In-class: Scaffold a Web API with `dotnet new webapi`, add a `TasksController` with a GET `/api/tasks` endpoint, run under WSL2 using the `dotnet` CLI.
- Exercise: Start the API and `curl` the endpoint from WSL2 and from the browser.
- Homework: Read HTTP methods and status codes.

### Lesson 26 — Build RESTful CRUD Endpoints (ASP.NET Core)
- Objective: Implement POST, PUT/PATCH, DELETE alongside GET using controllers and model binding.
- In-class: Implement create and delete actions in `TasksController`; test with `curl`, Postman, or Insomnia.
- Exercise: Add model validation attributes and return appropriate status codes.
- Homework: Add simple integration/unit tests for endpoints (see xUnit notes later).

### Lesson 26 — Persistence with SQLite (EF Core)
- Objective: Add a persistent SQLite database for tasks using Entity Framework Core.
- In-class: Scaffold `DbContext`, create `Task` entity, add EF Core migrations, apply to local SQLite DB.
- Exercise: Migrate endpoints to use EF Core; implement timestamps and simple queries (finished/unfinished).
- Homework: Explore using a desktop SQLite viewer and `dotnet ef` commands.

### Lesson 26 — Entity Framework Core Patterns
- Objective: Introduce EF Core patterns: migrations, LINQ queries, and repository patterns (optional).
- In-class: Replace raw SQL or direct DB access with EF Core LINQ queries for one endpoint.
- Exercise: Add filtering (e.g., by status, by date) with LINQ and EF Core.
- Homework: Read about parameterized queries, SQL injection, and EF Core change tracking.

### Lesson 26 — Advanced SQL: Queries, Joins, Views & Indexes
- Objective: Deepen SQL knowledge: SELECT variants, JOINs, GROUP BY, window functions, views, indexes, and EXPLAIN plans.
- In-class: Demonstrate inner/outer joins, aggregate queries, create a view, explain query plans, and discuss indexing strategies.
- Exercise: Write SQL queries that join tasks with users, aggregate counts, and create a view for active tasks; run `EXPLAIN` and interpret costs.
- Homework: Read a concise SQL tuning primer and a guide to views and indexes.

---

## Week 4 — Authentication & Integration

### Lesson 26 — User Accounts & Passwords (ASP.NET Identity / EF Core)
- Objective: Add user model and hashed passwords using ASP.NET Core Identity backed by EF Core.
- In-class: Scaffold Identity, register and login endpoints or pages, and store hashed passwords via Identity.
- Exercise: Implement a simple registration flow and inspect stored hashed passwords.
- Homework: Read sessions vs JWT basics and how Identity manages security.

### Lesson 26 — Protecting Routes / Middleware
- Objective: Require auth for create/update/delete operations using ASP.NET Core authentication/authorization.
- In-class: Configure authentication (cookie or JWT) and `[Authorize]` on controllers/actions.
- Exercise: Protect task endpoints and test unauthorized responses.
- Homework: Add a “current user” state in the front-end.

### Lesson 26 — Frontend-Backend Integration
- Objective: Replace `localStorage` persistence with real API calls to the ASP.NET Core API using TypeScript client code.
- In-class: Implement typed `fetch` calls or a small API client module in TypeScript for CRUD operations; handle CORS configuration in the API.
- Exercise: Complete end-to-end create/read/update/delete flow from UI using the TypeScript client.
- Homework: Add loading states, error displays, and refine TypeScript types for API responses.

- ### Lesson 26 — Validation & Error Handling
- Objective: Validate input client- and server-side (use Data Annotations and FluentValidation if desired); map errors to friendly UI messages.
- In-class: Add model validation attributes and global error handling middleware in ASP.NET Core.
- Exercise: Add client-side form validation and edge-case tests.
- Homework: Harden endpoints against malformed input.

---

## Week 5 — Workflow, Testing & Production Prep

### Lesson 26 — Git Workflow & Collaboration
- Objective: Branching, PR basics, and meaningful commit messages.
- In-class: Create a feature branch and open a PR simulation.
- Exercise: Practice resolving a merge conflict.
- Homework: Improve the README with setup steps.

### Lesson 26 — Basic Testing for Backend
- Objective: Unit and integration tests with xUnit and Microsoft.AspNetCore.Mvc.Testing (or NUnit/MSTest as preferred).
- In-class: Write tests for GET and POST endpoints using `WebApplicationFactory` to host the API in-memory.
- Exercise: Add one failing test and fix the code to pass.
- Homework: Extend tests to cover error cases.

### Lesson 26 — Environment & Secrets
- Objective: Use environment variables and `dotnet user-secrets` (for local development) and configuration providers for production.
- In-class: Configure `appsettings.Development.json`, use `dotnet user-secrets` for local secrets, and switch DB connection strings per environment.
- Exercise: Run the app in test vs dev mode (different DBs).
- Homework: Document how to set env vars for deployment.

### Lesson 26 — Production Build & Logging
- Objective: Produce a production-ready frontend build (Vite) and publish the ASP.NET Core app (`dotnet publish`) and configure logging (built-in logging or Serilog).
- In-class: Run frontend build (`npm run build`) and set up ASP.NET Core to serve static assets; run `dotnet publish` and test the published output.
- Exercise: Create a Dockerfile suitable for the chosen host.
- Homework: Perform a local production smoke-test of the published app.

---

## Week 6 — Deployment & Wrap-up

### Lesson 26 — Deploy Frontend (Netlify/Vercel)
- Objective: Deploy static frontend to Netlify or Vercel.
- In-class: Connect repo, set build command, deploy preview.
- Exercise: Configure environment variables and redirects if needed.
- Homework: Document the frontend deploy steps.

### Lesson 26 — Dockerize Frontend, Backend & Database
- Objective: Create Dockerfiles for the TypeScript frontend (Vite), the ASP.NET Core backend, and run a Postgres database container.
- In-class: Write a minimal Dockerfile for the frontend (`node` base + `vite build`), a Dockerfile for ASP.NET Core (multi-stage: SDK build, runtime image), and run a Postgres container for local persistence.
- Exercise: Build each image locally and run containers; verify the backend can reach the database (connection string set via env var).
- Homework: Read Dockerfile best practices and multi-stage builds.

### Lesson 26 — Compose the Stack and Local Deployment
- Objective: Combine services with `docker-compose` (or Docker Compose v2) and orchestrate frontend, backend, and DB with volumes, networks, and env files.
- In-class: Create `docker-compose.yml` with services: `frontend`, `backend`, `db`, optional `reverse-proxy`/`traefik` or `caddy` for local routing; add volumes and healthchecks.
- Exercise: Run `docker-compose up --build`, inspect logs, and test end-to-end functionality; practice restarting a single service and persisting DB files.
- Homework: Read `docker-compose` basics, volumes, and networking.

### Lesson 26 — Local HTTPS, Reverse Proxy & Monitoring
- Objective: Add local TLS using `mkcert` or a reverse-proxy container, configure HTTPS for local development, and add simple monitoring (container logs, `docker stats`, optional Uptime Kuma container).
- In-class: Generate local certs with `mkcert`, configure Kestrel or reverse proxy to use them, or deploy a Traefik/Caddy container to terminate TLS and route to services; add a monitoring container and health endpoints.
- Exercise: Serve the app locally over `https://local.example` (add `/etc/hosts` entry), view logs, and check container health.
- Homework: Write a short ops checklist and read about local TLS tooling and container monitoring.

### Lesson 26 — Final Demo, Feedback & Next Steps
- Objective: Student demo and code walkthrough; plan next learning steps.
- In-class: Final demo, feedback, discuss extensions (search, tags, pagination, web sockets).
- Exercise: Student presents the app; instructor records rubric scores.
- Homework: Create a 3-month roadmap for deeper learning.

---

## Capstone Project — Minimum & Stretch
- Minimum: Deployed CRUD app where tasks persist and can be created, edited, deleted, and listed.
- Good: Auth-protected endpoints, tests, continuous deploy from repo.
- Great: Responsive UI, search/filter, pagination, tags, and documented setup + CI.

---

## References & Resources
- MDN Web Docs — HTML/CSS/JS: https://developer.mozilla.org/
- ASP.NET Core guide: https://learn.microsoft.com/aspnet/core/
- .NET docs: https://learn.microsoft.com/dotnet/
- Entity Framework Core: https://learn.microsoft.com/ef/core/
- SQLite quickstart: https://www.sqlite.org/quickstart.html
- ASP.NET Core Identity: https://learn.microsoft.com/aspnet/core/security/authentication/identity
- dotnet user-secrets: https://learn.microsoft.com/aspnet/core/security/app-secrets
- xUnit: https://xunit.net/
- Deploy: Azure App Service, Render (Linux), or Docker-based hosts
- Docker: https://docs.docker.com/
- Docker Compose: https://docs.docker.com/compose/
- Dockerfile best practices: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
- Local TLS (mkcert): https://github.com/FiloSottile/mkcert
- Local monitoring (Uptime Kuma): https://github.com/louislam/uptime-kuma

### Suggested short readings / videos
- HTTP overview (MDN): https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview
- REST API basics (tutorial): https://restfulapi.net/
- Git branching (Atlassian guide): https://www.atlassian.com/git/tutorials/using-branches

---

_Footnotes:_

[1] Choose SQLite for local simplicity; migrate to Postgres on hosted DB for production. See SQLite vs Postgres tradeoffs in the references.

---

## Per-lesson References (quick links)
1. Lesson 1 — Git & tooling: https://git-scm.com/book/en/v2 | https://docs.github.com/en/get-started/quickstart
2. Lesson 2 — HTML basics: https://developer.mozilla.org/en-US/docs/Web/HTML
3. Lesson 3 — Bootstrap & CSS fundamentals: https://getbootstrap.com/docs/5.3/getting-started/introduction/ | https://developer.mozilla.org/en-US/docs/Web/CSS
4. Lesson 4 — DOM & events: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model
5. Lesson 5 — DevTools: https://developer.chrome.com/docs/devtools/
6. Lesson 6 — Modern TypeScript: https://www.typescriptlang.org/docs/ | https://developer.mozilla.org/en-US/docs/Web/JavaScript
7. Lesson 7 — npm & dev servers: https://docs.npmjs.com/ | https://vitejs.dev/guide/
8. Lesson 8 — SPA concepts (optional): https://reactjs.org/docs/getting-started.html
9. Lesson 9 — ASP.NET Core & C#: https://learn.microsoft.com/aspnet/core/ | https://learn.microsoft.com/dotnet/
10. Lesson 10 — REST API design: https://restfulapi.net/
11. Lesson 11 — SQLite: https://www.sqlite.org/quickstart.html
12. Lesson 12 — EF Core patterns & LINQ: https://learn.microsoft.com/ef/core/
13. Lesson 13 — Advanced SQL & indexing: https://www.postgresql.org/docs/current/tutorial.html | https://use-the-index-luke.com/
14. Lesson 14 — Password hashing & Identity: https://learn.microsoft.com/aspnet/core/security/authentication/identity
15. Lesson 15 — Auth basics (JWT): https://jwt.io/introduction/
16. Lesson 16 — Fetch & client integration: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
17. Lesson 17 — Validation patterns: https://docs.microsoft.com/aspnet/core/mvc/models/validation
18. Lesson 18 — Branching & PRs: https://www.atlassian.com/git/tutorials/using-branches
19. Lesson 19 — Testing (xUnit & WebApplicationFactory): https://xunit.net/ | https://learn.microsoft.com/aspnet/core/test/integration-tests
20. Lesson 20 — Environment & secrets: https://learn.microsoft.com/aspnet/core/security/app-secrets
21. Lesson 21 — Production builds & logging: https://learn.microsoft.com/aspnet/core/host-and-deploy/
22. Lesson 22 — Netlify & Vercel docs: https://docs.netlify.com/ | https://vercel.com/docs
23. Lesson 23 — Deploy backend (Render / Azure / Docker): https://render.com/docs | https://learn.microsoft.com/azure/app-service/
24. Lesson 24 — DNS, HTTPS & Let's Encrypt: https://letsencrypt.org/ | https://developers.cloudflare.com/
25. Lesson 25 — Next steps & advanced paths: https://www.freecodecamp.org/learn/ | https://frontendmasters.com/

---

## Homework reading (per lesson)
Use these 15–40 minute readings or short tutorials as homework for each lesson.

1. Lesson 1 — HTTP overview: https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview
2. Lesson 2 — HTML basics: https://developer.mozilla.org/en-US/docs/Web/HTML
3. Lesson 3 — CSS, Flexbox & Responsive basics: https://developer.mozilla.org/en-US/docs/Web/CSS | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox | https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Responsive_Design
4. Lesson 4 — DOM & events: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model
5. Lesson 5 — DevTools intro: https://developer.chrome.com/docs/devtools/
6. Lesson 6 — Modern TypeScript guide: https://www.typescriptlang.org/docs/ | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide
7. Lesson 7 — npm & package.json: https://docs.npmjs.com/cli/v9/commands/npm-init | Vite quickstart: https://vitejs.dev/guide/
8. Lesson 8 — Component thinking /SPA primer: https://reactjs.org/docs/getting-started.html
9. Lesson 9 — ASP.NET Core getting started: https://learn.microsoft.com/aspnet/core/getting-started/
10. Lesson 10 — REST API design: https://restfulapi.net/
11. Lesson 11 — SQLite quickstart: https://www.sqlite.org/quickstart.html
12. Lesson 12 — EF Core patterns & LINQ: https://learn.microsoft.com/ef/core/
13. Lesson 13 — Advanced SQL (joins, views, indexes): https://www.postgresql.org/docs/current/tutorial.html | https://use-the-index-luke.com/
14. Lesson 14 — ASP.NET Identity & password hashing: https://learn.microsoft.com/aspnet/core/security/authentication/identity
15. Lesson 15 — JWT introduction: https://jwt.io/introduction/
16. Lesson 16 — Fetch API and promises: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
17. Lesson 17 — Validation patterns (Data Annotations / FluentValidation): https://docs.microsoft.com/aspnet/core/mvc/models/validation | https://fluentvalidation.net/
18. Lesson 18 — Git branching & PRs: https://www.atlassian.com/git/tutorials/using-branches
19. Lesson 19 — Testing (xUnit & WebApplicationFactory): https://xunit.net/ | https://learn.microsoft.com/aspnet/core/test/integration-tests
20. Lesson 20 — Environment & secrets (`dotnet user-secrets`): https://learn.microsoft.com/aspnet/core/security/app-secrets
21. Lesson 21 — Production publish & logging: https://learn.microsoft.com/aspnet/core/host-and-deploy/
22. Lesson 22 — Deploy frontends (Netlify/Vercel guides): https://docs.netlify.com/get-started/ | https://vercel.com/docs
23. Lesson 23 — Deploy backend (Render / Azure / Docker): https://render.com/docs | https://learn.microsoft.com/azure/app-service/
24. Lesson 24 — DNS & HTTPS basics: https://letsencrypt.org/getting-started/ | Cloudflare docs: https://developers.cloudflare.com/
25. Lesson 25 — Next-step learning paths: freeCodeCamp: https://www.freecodecamp.org/learn/ | Frontend Masters: https://frontendmasters.com/

