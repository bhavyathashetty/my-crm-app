# CRM Foundation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a development-only, desktop-first B2B CRM that supports the complete core workflow from company creation through pipeline reporting and follow-up work.

**Architecture:** Keep the React client and Express API as separate applications. Model CRM rules in backend domain services with repository boundaries, and keep workspace authorization at the API boundary. Use the existing Supabase client dependency for development persistence/authentication while keeping database access behind repositories so domain rules do not depend on Supabase APIs.

**Tech Stack:** React 19, TypeScript, Vite, Express 5, Supabase JavaScript client, relational persistence, ESLint, and a browser end-to-end test runner.

**Spec:** `docs/product/PRD.md`, `CONTEXT.md`, and `docs/adr/0001-workspace-as-business-boundary.md`.

## Global Constraints

- Development-only delivery: local development, tests, migrations, seed data, logs, and health checks are in scope; staging and production deployment are deferred.
- Workspace is the business boundary for users and CRM records.
- v1 includes Companies, Contacts, Opportunities, Pipeline, Activities, Tasks, Users, Dashboard, Search, Filtering, and basic reporting.
- Leads, integrations, imports/exports, custom fields, attachments, multi-currency, configurable pipelines, and configurable activity types are deferred from v1.
- Contributors can view active workspace records and normally edit records they own; Managers and Admins can edit all records; Admins manage workspace users and archive/restore records.
- Business records are archived rather than hard-deleted.
- Every workspace-scoped API operation must enforce workspace authorization.
- Core tests must cover Company → Contact → Opportunity → Stage change → Activity/Task → Dashboard.

---

### Task 1: Establish testable application and persistence foundations

**Files:**
- Create: `backend/src/app.ts`
- Create: `backend/src/config/env.ts`
- Create: `backend/src/db/client.ts`
- Create: `backend/src/db/migrations/001_initial_schema.sql`
- Create: `backend/src/db/seed.ts`
- Modify: `backend/src/server.ts`
- Modify: `backend/package.json`
- Create: `backend/src/app.test.ts`
- Create: `frontend/src/test/setup.ts`
- Modify: `frontend/package.json`

**Interfaces:**
- `createApp(): Express` returns an app without listening, so API tests can invoke it directly.
- `getEnv(): { port: number; supabaseUrl: string; supabaseAnonKey: string }` validates development configuration.
- `seedDevelopmentData(): Promise<void>` creates repeatable workspace, users, companies, contacts, opportunities, activities, tasks, and archived records.

- [ ] **Step 1: Add failing health and configuration tests**

  Test that `createApp()` returns `200` from `GET /api/health` and that missing required development configuration produces a named configuration error.

- [ ] **Step 2: Run the backend test command and verify the new tests fail for missing app/config modules**

  Run: `npm test -- --runInBand`

- [ ] **Step 3: Extract the Express app and validated configuration**

  Move middleware and the health route into `backend/src/app.ts`; keep `server.ts` responsible only for loading configuration, creating the app, and listening.

- [ ] **Step 4: Add the initial relational migration**

  Create workspace-scoped tables for users, memberships, companies, contacts, opportunities, pipeline stages, activities, tasks, and record history. Include archive timestamps and foreign keys; do not add leads, attachments, custom fields, or multi-currency tables.

- [ ] **Step 5: Add repeatable development seed/reset commands**

  Add `db:seed` and `db:reset` scripts that are available only under an explicit development environment check.

- [ ] **Step 6: Run the tests and build**

  Run: `npm test`

  Run: `npm run build`

  Expected: health/configuration tests pass and TypeScript compilation succeeds.

---

### Task 2: Implement authentication, workspace membership, and roles

**Files:**
- Create: `backend/src/auth/auth-service.ts`
- Create: `backend/src/auth/auth-middleware.ts`
- Create: `backend/src/workspaces/workspace-service.ts`
- Create: `backend/src/users/user-service.ts`
- Create: `backend/src/routes/auth-routes.ts`
- Create: `backend/src/routes/workspace-routes.ts`
- Create: `backend/src/auth/auth-service.test.ts`
- Create: `frontend/src/auth/AuthProvider.tsx`
- Create: `frontend/src/pages/LoginPage.tsx`
- Create: `frontend/src/pages/WorkspaceInvitePage.tsx`
- Create: `frontend/src/auth/auth.test.tsx`

**Interfaces:**
- `requireWorkspaceUser(request): AuthenticatedWorkspaceUser` rejects unauthenticated or cross-workspace requests.
- `canEditRecord(user, record): boolean` implements Contributor, Manager, and Admin permissions.
- `deactivateUser(userId): Promise<void>` preserves history and prevents new sign-in/work assignment.

- [ ] **Step 1: Write failing authorization tests**

  Cover unauthenticated access, cross-workspace access, contributor editing an owned record, contributor editing another user’s record, manager editing any record, and admin archiving/restoring records.

- [ ] **Step 2: Run the authorization tests and verify failure**

  Run: `npm test -- auth-service.test.ts`

- [ ] **Step 3: Implement auth and membership boundaries**

  Use the existing Supabase client for authentication, map authenticated identities to workspace memberships, and attach the membership to each request context.

- [ ] **Step 4: Add login and invitation UI**

  Implement accessible login, invite acceptance, sign-out, and an authenticated application shell. Use visible error states and keyboard-accessible controls.

- [ ] **Step 5: Verify permission behavior end to end**

  Run: `npm test`

  Run: `npm run build`

  Expected: role tests pass and the authenticated shell builds.

---

### Task 3: Build Companies and Contacts

**Files:**
- Create: `backend/src/companies/company-service.ts`
- Create: `backend/src/companies/company-repository.ts`
- Create: `backend/src/contacts/contact-service.ts`
- Create: `backend/src/contacts/contact-repository.ts`
- Create: `backend/src/routes/company-routes.ts`
- Create: `backend/src/routes/contact-routes.ts`
- Create: `backend/src/companies/company-service.test.ts`
- Create: `frontend/src/pages/CompaniesPage.tsx`
- Create: `frontend/src/pages/CompanyDetailPage.tsx`
- Create: `frontend/src/components/ContactForm.tsx`
- Create: `frontend/src/companies/companies.test.tsx`

**Interfaces:**
- `createCompany(input, actor): Promise<Company>` creates Prospect, Customer, or Inactive companies.
- `updateCompanyStatus(companyId, status, actor): Promise<Company>` applies user-controlled status changes.
- `createContact(input, actor): Promise<Contact>` requires a company and a name.
- `archiveCompany(companyId, actor): Promise<void>` blocks archiving with open opportunities and does not cascade.

- [ ] **Step 1: Write failing domain tests**

  Cover required company name, default Prospect status, allowed status changes, required contact company/name, non-unique email, archive blocking for open opportunities, and warning-compatible archive behavior when only open tasks exist.

- [ ] **Step 2: Implement repositories and services**

  Keep validation and archive rules in services; repositories perform workspace-scoped persistence only.

- [ ] **Step 3: Add list/detail/form API routes**

  Support filtering by company status and owner, explicit archived filtering, and contact lookup by company.

- [ ] **Step 4: Build accessible company/contact UI**

  Add list, detail, create, edit, archive, restore, and contact-management flows with loading, empty, validation, and error states.

- [ ] **Step 5: Run unit, API, and client tests**

  Run: `npm test`

  Run: `npm run build`

---

### Task 4: Implement Opportunities, Pipeline, and History

**Files:**
- Create: `backend/src/opportunities/opportunity-service.ts`
- Create: `backend/src/opportunities/opportunity-repository.ts`
- Create: `backend/src/opportunities/pipeline.ts`
- Create: `backend/src/routes/opportunity-routes.ts`
- Create: `backend/src/opportunities/opportunity-service.test.ts`
- Create: `frontend/src/pages/OpportunitiesPage.tsx`
- Create: `frontend/src/components/OpportunityForm.tsx`
- Create: `frontend/src/components/PipelineBoard.tsx`
- Create: `frontend/src/opportunities/opportunities.test.tsx`

**Interfaces:**
- `createOpportunity(input, actor): Promise<Opportunity>` requires a primary company and permits no contacts or amount while New.
- `moveOpportunity(opportunityId, stage, actor, reason?): Promise<Opportunity>` records every stage change and requires an outcome reason for Won/Lost.
- `reopenOpportunity(opportunityId, actor, reason): Promise<Opportunity>` records the reopening reason.
- `getPipelineSummary(filters): Promise<PipelineSummary>` excludes archived records and groups by stage.

- [ ] **Step 1: Write failing pipeline tests**

  Cover multiple opportunities per company, required company, optional New amount/contact, amount required before Qualified, stage defaults, manual probability override, backward movement, outcome reasons, reopening, overdue open opportunities, and archived exclusion.

- [ ] **Step 2: Implement pipeline rules and history writes**

  Use the fixed stages New, Qualified, Proposal, Negotiation, Won, and Lost. Store stage changes and outcomes in Record History.

- [ ] **Step 3: Add opportunity API routes**

  Support list filters for stage, owner, close-date range, amount range, overdue state, and archived state.

- [ ] **Step 4: Build pipeline and opportunity detail UI**

  Add list/board views, stage movement, detail timeline placeholder, outcome dialogs, and clear overdue indicators.

- [ ] **Step 5: Run pipeline tests and build**

  Run: `npm test`

  Run: `npm run build`

---

### Task 5: Add Activities, Notes, Tasks, and Timelines

**Files:**
- Create: `backend/src/activities/activity-service.ts`
- Create: `backend/src/activities/activity-repository.ts`
- Create: `backend/src/tasks/task-service.ts`
- Create: `backend/src/tasks/task-repository.ts`
- Create: `backend/src/routes/activity-routes.ts`
- Create: `backend/src/routes/task-routes.ts`
- Create: `backend/src/timeline/timeline-service.ts`
- Create: `backend/src/activities/activity-service.test.ts`
- Create: `backend/src/tasks/task-service.test.ts`
- Create: `frontend/src/components/ActivityForm.tsx`
- Create: `frontend/src/components/TaskForm.tsx`
- Create: `frontend/src/components/Timeline.tsx`
- Create: `frontend/src/activities/activity-task.test.tsx`

**Interfaces:**
- `createActivity(input, actor): Promise<Activity>` requires one primary subject and supports Call, Meeting, Email, Note, and Other.
- `createTask(input, actor): Promise<Task>` requires one primary subject, owner, and due date while Open.
- `updateTaskStatus(taskId, status, actor): Promise<Task>` supports Open, In Progress, Completed, and Canceled.
- `getTimeline(subject, filters): Promise<TimelineEntry[]>` aggregates direct and contextually related history without unrelated company duplication.

- [ ] **Step 1: Write failing activity/task tests**

  Cover activity types, plain-text notes, primary subject and participant rules, task status transitions, required open-task due dates, owner assignment, archive instead of delete, and timeline aggregation.

- [ ] **Step 2: Implement services and repositories**

  Preserve completed tasks and activities in history; use archive timestamps for reversible removal.

- [ ] **Step 3: Add API routes and timeline queries**

  Enforce visibility and editing permissions through the authenticated workspace context.

- [ ] **Step 4: Build forms, timelines, and in-app due/overdue indicators**

  Keep notes plain text; show source record, creator, timestamp, owner, and status for each entry.

- [ ] **Step 5: Run tests and verify the core workflow**

  Run: `npm test`

  Verify manually: Company → Contact → Opportunity → Activity/Task → Timeline.

---

### Task 6: Implement Search, Filters, Dashboard, and Basic Reporting

**Files:**
- Create: `backend/src/search/search-service.ts`
- Create: `backend/src/dashboard/dashboard-service.ts`
- Create: `backend/src/routes/search-routes.ts`
- Create: `backend/src/routes/dashboard-routes.ts`
- Create: `backend/src/dashboard/dashboard-service.test.ts`
- Create: `frontend/src/pages/DashboardPage.tsx`
- Create: `frontend/src/components/GlobalSearch.tsx`
- Create: `frontend/src/components/FilterBar.tsx`
- Create: `frontend/src/dashboard/dashboard.test.tsx`

**Interfaces:**
- `searchActiveRecords(query, workspaceId): Promise<SearchResult[]>` searches Companies, Contacts, and Opportunities while excluding archived records by default.
- `getDashboard(filters): Promise<DashboardData>` returns pipeline by stage, weighted pipeline, closing-soon opportunities, won/lost totals, overdue/upcoming tasks, and recent activity.

- [ ] **Step 1: Write failing reporting tests**

  Cover current-quarter defaults, custom date filters, workspace currency totals, stage grouping, weighted pipeline, overdue tasks, archived exclusion, and global search result types.

- [ ] **Step 2: Implement query services**

  Keep reporting calculations in backend services and apply the workspace time zone to date-based filters.

- [ ] **Step 3: Build fixed-layout dashboard and search UI**

  Use accessible tables/cards with explicit labels; do not add widget customization or report-builder configuration.

- [ ] **Step 4: Run tests and verify dashboard data against seeded records**

  Run: `npm test`

  Expected: seeded dashboard totals match the fixture assertions.

---

### Task 7: Complete development operations and quality gates

**Files:**
- Modify: `backend/package.json`
- Modify: `frontend/package.json`
- Create: `backend/src/observability/logger.ts`
- Create: `backend/src/observability/request-context.ts`
- Create: `backend/src/routes/health-routes.ts`
- Create: `e2e/core-crm-workflow.spec.ts`
- Create: `e2e/auth-and-permissions.spec.ts`
- Create: `docs/development.md`
- Create: `.env.example`

**Interfaces:**
- Request logs include request ID, workspace ID when known, route, status, duration, and error classification.
- Health checks expose application readiness without leaking secrets.
- Development reset and seed commands require an explicit development environment.

- [ ] **Step 1: Add request correlation and structured logging tests**

  Verify every request receives or preserves a correlation ID and error responses do not expose secrets.

- [ ] **Step 2: Add end-to-end core workflow tests**

  Cover login, role restrictions, company/contact creation, opportunity progression, activity/task creation, dashboard reflection, archive behavior, and deactivated-user handling.

- [ ] **Step 3: Add accessibility checks to core pages**

  Verify keyboard navigation, visible focus, form labels, semantic headings, and accessible error messages.

- [ ] **Step 4: Document local setup and safe reset behavior**

  Document environment variables, migration order, seed data, test commands, health checks, and the development-only reset guard.

- [ ] **Step 5: Run the complete local verification suite**

  Run from `backend`: `npm test && npm run build`

  Run from `frontend`: `npm run lint && npm run build`

  Run from the repository root: `npm run e2e`

  Expected: unit, integration, client, build, accessibility, and core workflow checks pass locally.

---

## Deferred scope

The following are explicitly outside this implementation plan: Leads and lead conversion, production/staging deployment, email/calendar integrations, CSV import/export, attachments, custom fields, custom pipelines or activity types, multi-currency, parent/subsidiary company hierarchies, rich-text notes, email/push notifications, configurable dashboards, and AI Summary implementation.

AI Summary becomes a separate plan only after the non-AI timeline workflow is stable. That plan must keep summaries read-only, source-linked, user-visible as AI-generated, and limited to records the user can already view.
