# CRM Application

A development-first B2B CRM for small sales teams. The product connects companies, contacts, opportunities, pipeline stages, activities, notes, tasks, timelines, search, and reporting into one realistic sales workflow.

The application is currently an early development scaffold. The approved product definition and implementation sequence are documented before feature work begins.

## Product workflow

1. Create a company.
2. Add contacts associated with the company.
3. Create and progress an opportunity through the sales pipeline.
4. Record calls, meetings, emails, and notes.
5. Create and complete follow-up tasks.
6. Review timelines, pipeline state, and dashboard reporting.

## Repository layout

```text
backend/       Express + TypeScript API
frontend/      React + Vite web application
docs/product/  Product requirements
docs/adr/      Important architecture decisions
docs/superpowers/plans/
               Implementation plans
CONTEXT.md     Shared domain terminology
```

## Documentation

- [Product requirements](docs/product/PRD.md) — what the CRM is
- [Architecture decisions](docs/adr/0001-workspace-as-business-boundary.md) — why key boundaries exist
- [Implementation plan](docs/superpowers/plans/2026-09-18-crm-foundation.md) — how the product will be built
- [Domain context](CONTEXT.md) — shared business terminology

## Local development

### Prerequisites

- Node.js and npm

### Start the backend

```bash
cd backend
npm install
npm run dev
```

The API runs on `http://localhost:5001`.

Health check:

```bash
curl http://localhost:5001/api/health
```

### Start the frontend

In a second terminal:

```bash
cd frontend
npm install
npm run dev
```

Vite will print the local frontend URL in the terminal.

## Available scripts

### Backend

```bash
npm run dev      # Start the API with watch mode
npm run build    # Compile TypeScript
npm start        # Run the compiled API
```

### Frontend

```bash
npm run dev      # Start Vite development server
npm run build    # Type-check and build the frontend
npm run lint     # Run ESLint
npm run preview  # Preview the production build locally
```

## Current scope

The first release targets local development and includes workspace users, companies, contacts, opportunities, a fixed pipeline, activities, tasks, timelines, search, filtering, dashboards, and basic reporting.

Leads, external integrations, imports/exports, custom fields, attachments, multi-currency, configurable pipelines, AI features, and production deployment are intentionally deferred.

## Development principles

- Keep CRM records workspace-scoped.
- Preserve business history through archiving rather than ordinary hard deletion.
- Keep domain rules explicit and testable.
- Enforce authorization at every workspace-scoped API operation.
- Build the non-AI workflow before adding the read-only AI Summary feature.
