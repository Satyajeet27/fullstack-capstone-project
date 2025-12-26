---
name: User Story Epic
about: Aggregated user stories for MVP based on backlog screenshot
title: 'Epic: MVP Gift App — Backlog Stories'
labels: ['epic', 'new']
assignees: ['Satayajeet27']
---

# Epic: MVP Gift App — Backlog Stories

**As a** Product Team  
**I need** a clear set of user stories derived from the current backlog  
**So that** the team can implement the MVP features with testable outcomes


## Non-Functional Assumptions
- Tech stack: React (SPA), Node.js/Express API, MongoDB, Docker, CI/CD (e.g., GitHub Actions).
- Environments: `dev` and `prod`.
- Authentication for protected flows (comments, profile).
- Accessibility (WCAG basics), security (JWT, CSRF, XSS), and baseline performance are in scope.

---

## Story 1 — Run skeleton application
**As a** developer  
**I need** to run a working skeleton app (frontend + backend)  
**So that** I can iterate quickly with a live baseline

### Details & Assumptions
- Repo includes React (frontend) and Express (backend).
- Basic health endpoint `/health` and a landing route `/`.

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Skeleton Application
  Scenario: Start frontend and backend successfully
    Given the repository is cloned
    And dependencies are installed
    When I run the documented setup commands for frontend and backend
    Then the frontend should start at the configured port
    And the backend /health endpoint should return 200 with { "status": "ok" }
    And both apps log startup without errors
```

---

## Story 2 — Initialize and populate MongoDB
**As a** developer  
**I need** a seeded MongoDB with baseline collections  
**So that** I can test features end-to-end

### Details & Assumptions
- Collections: `users`, `gifts`, `comments`.
- Seed script is idempotent.

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Database Initialization and Seeding
  Scenario: Seed baseline collections and data
    Given MongoDB is running and the connection string is configured
    When I execute the seed script
    Then the collections users, gifts, and comments exist
    And at least 3 gifts exist with name, price, and tags
    And at least 1 user and 2 comments are present
```

---

## Story 3 — Implement a landing page and navigation
**As a** visitor  
**I need** a landing page with clear navigation  
**So that** I can discover features easily

### Details & Assumptions
- React Router for routes.
- Navigation: Home, Gifts, Search, Login/Register (Profile when authenticated).

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Landing Page and Navigation
  Scenario: See landing page and navigate between routes
    Given I am on the root URL
    When the app loads
    Then I see a landing page with branding and a CTA to view gifts
    And the top nav shows Home, Gifts, Search, Login/Register
    And the nav highlights the active route
```

---

## Story 4 — Add authentication components and logic
**As a** user  
**I need** to sign up, sign in, and sign out  
**So that** I can access protected features

### Details & Assumptions
- JWT-based auth (httpOnly cookie preferred), optional refresh tokens.
- Forms: Login, Register with basic validation.
- Protected routes gated by auth state.

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Authentication
  Scenario: Register a new user
    Given I am a new user
    When I register with valid details
    Then I see a success message and can log in

  Scenario: Log in successfully
    Given I am a registered user
    When I log in with valid credentials
    Then I receive a session (token/cookie)
    And I see my profile dropdown

  Scenario: Sign out
    Given I am logged in
    When I click "Sign out"
    Then my session is cleared
    And I return to the landing page
```

---

## Story 5 — Implement Gifts details page
**As a** user  
**I need** to view a gift’s detailed information  
**So that** I can evaluate it before taking an action

### Details & Assumptions
- Route: `/gifts/:id`.
- Fields: title, description, price, images, tags.

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Gift Details Page
  Scenario: View a gift’s details
    Given gifts exist in the database
    When I navigate to /gifts/:id
    Then I see the gift’s title, description, price, tags, and images
    And I see a back link to the gifts list

  Scenario: Handle non-existent gift
    Given I enter a non-existent gift ID
    When I navigate to /gifts/unknown-id
    Then I see a 404-friendly page
```

---

## Story 6 — Implement a search component
**As a** user  
**I need** to search gifts by name and tags  
**So that** I can quickly find relevant items

### Details & Assumptions
- Search box with debounced queries (~500ms).
- Backend supports `q` and `tags` filters.

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Gift Search
  Scenario: Search by keyword
    Given I am on the Gifts or Search page
    When I type a keyword
    Then the list updates within 500ms after I stop typing
    And results include items where name or tags match (case-insensitive)

  Scenario: No results
    Given I typed a keyword that matches nothing
    When the results are empty
    Then I see an empty state with "No items found"
```

---

## Story 7 — Design and implement the comments feature
**As a** logged-in user  
**I need** to add and read comments on a gift  
**So that** I can discuss with others

### Details & Assumptions
- Comments available at `/gifts/:id`.
- CRUD: Create (auth required), Read (public), Delete (author/admin only).

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Comments on Gifts
  Scenario: Read comments without login
    Given I am on a gift detail page
    And I am not logged in
    Then I can read comments but cannot post

  Scenario: Post a comment when logged in
    Given I am logged in
    When I submit a valid comment
    Then my comment appears with my display name and timestamp

  Scenario: Delete my own comment
    Given I am the author of a comment
    When I click delete and confirm
    Then the comment is removed
```

---

## Story 8 — Containerize the services and applications
**As a** DevOps engineer  
**I need** Dockerized frontend, backend, and database  
**So that** the app runs consistently across environments

### Details & Assumptions
- `Dockerfile` for frontend and backend.
- `docker-compose.yml` orchestrates services and network.
- Environment variables via `.env` or Compose.

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Containerization
  Scenario: Run services via docker-compose
    Given Docker is installed
    When I run "docker-compose up"
    Then the frontend, backend, and MongoDB start successfully
    And the app is reachable at the configured host/port
    And logs show healthy startup without crash loops
```

---

## Story 9 — Deploy backend and frontend
**As a** stakeholder  
**I need** the app deployed to a live environment  
**So that** I can review progress and provide feedback

### Details & Assumptions
- CI/CD (e.g., GitHub Actions) builds, tests, and deploys.
- Environment variables injected securely.
- Health checks for both services.

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Deployment
  Scenario: Auto-deploy on main branch
    Given changes are pushed to main
    When CI/CD completes successfully
    Then a new build is deployed to the target environment
    And the backend /health endpoint returns 200
    And the frontend is reachable and shows the latest commit hash in the footer/about
```

---

## Story 10 — Research authentication in React and Express (technical debt)
**As a** developer  
**I need** a documented auth approach  
**So that** we adopt secure, maintainable practices

### Details & Assumptions
- Compare cookie-based vs localStorage token handling.
- Consider refresh tokens, CSRF, and route guards.
- Produce an ADR (Architecture Decision Record).

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Auth Research and ADR
  Scenario: Document chosen auth approach
    Given the research task is complete
    When I open the ADR
    Then I see tradeoffs, the chosen approach, and code snippets for client and server
    And security considerations (CSRF, XSS, token rotation) are documented
```

---

## Story 11 — Finish user stories
**As a** product owner  
**I need** the backlog stories fully written with acceptance criteria  
**So that** delivery is predictable and testable

### Details & Assumptions
- All stories are linked to this epic.
- Each story has at least one AC block and DoD (lint, tests, review).

### Acceptance Criteria (Gherkin)
```gherkin
Feature: Backlog User Stories Completion
  Scenario: All backlog items have full stories
    Given the backlog screenshot items
    When I open each corresponding issue
    Then I see a clear user story with role, need, and benefit
    And acceptance criteria in Gherkin format
    And labels and assignees are applied
```

---

## Definition of Done (applies to all stories)
- Linted code and unit tests for business logic.
- Basic e2e coverage for critical flows.
- Accessibility pass for new UI.
- Security checks for auth and API endpoints.
- Documentation updated (README, ENV samples).
- Linked issue closed with PR reference.

---

