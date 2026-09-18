# CRM Application — Product Requirements Document

## 1. Product summary

Build a development-only, production-style B2B CRM for small sales teams. The product helps users manage companies, contacts, opportunities, pipeline progress, follow-up work, interaction history, and business dashboards in one connected workflow.

The CRM is a normal business application. AI is intentionally outside the core workflow and may be added later as a small, read-only assistive feature.

## 2. Target users

The initial audience is a small B2B sales team of approximately 5–50 users.

The product supports three workspace roles:

- **Contributor**: views active workspace records and normally edits records they own; creates activities and tasks.
- **Manager**: views and edits all records and manages team work.
- **Admin**: manages workspace users and settings, views and edits all records, and archives or restores records.

All users can see active records in their workspace. Ownership establishes responsibility and normal editing scope; it is not a visibility boundary in v1.

## 3. Product goals

- Provide a coherent B2B sales workflow instead of disconnected CRUD pages.
- Model realistic relationships between companies, contacts, opportunities, activities, and tasks.
- Make pipeline state and follow-up work visible through a useful dashboard.
- Demonstrate production-style development practices locally: authentication, authorization, validation, testing, migrations, seed data, logging, and health checks.
- Establish a foundation for later observability and evaluation of a small AI feature without making the CRM autonomous.

## 4. Release boundary

### In scope for v1

- Workspace and user authentication
- Admin, Manager, and Contributor roles
- Companies
- Contacts
- Opportunities
- Fixed sales pipeline
- Activities and Notes
- Tasks and in-app due/overdue indicators
- Record timelines and history
- Search and list filtering
- Fixed-layout dashboard
- Basic pipeline, activity, and task reporting
- Archive and restore behavior
- Development seed and reset data
- Local testing, structured logs, correlation IDs, and health checks

### Deferred from v1

- Leads and lead conversion
- Email, calendar, and communication integrations
- CSV import/export
- Custom fields
- Configurable pipelines, stages, statuses, or activity types
- Attachments and rich-text notes
- Multi-currency and exchange-rate history
- Parent/subsidiary company hierarchies
- Email or push notifications
- Configurable dashboards or report builder
- Production and staging deployment
- AI Summary implementation

## 5. Domain model

### Workspace

A Workspace is the business boundary for users and CRM records. The first release may be operated for one business, but records remain workspace-scoped so the model can support future expansion.

### Company

An organization the business may sell to or work with. A Company has one of three statuses:

- Prospect
- Customer
- Inactive

Status changes are user-controlled. Winning an Opportunity does not automatically change the Company status. Companies are independent records in v1; parent/subsidiary hierarchies are deferred.

### Contact

A person associated with one Company. A name is required; email, phone, title, and job role are optional. A Contact cannot be created without a Company in v1.

### Opportunity

A qualified potential sale associated with one primary Company and zero or more Contacts. A Company may have multiple simultaneous Opportunities.

An Opportunity contains:

- Name
- Primary Company
- Optional Contacts
- Owner
- Amount
- Workspace currency
- Pipeline Stage
- Forecast Probability
- Expected close date
- Outcome reason when Won or Lost

An Opportunity may be created without an amount in New, but must have an amount before entering Qualified. Open Opportunities that pass their expected close date remain open and are reported as overdue.

### Pipeline

The initial fixed pipeline is:

`New → Qualified → Proposal → Negotiation → Won/Lost`

Users may move Opportunities forward or backward. Stage changes are recorded in history. Each stage provides a default probability, which authorized users may override.

Won and Lost require outcome reasons. They are terminal for normal reporting but may be reopened with a required reason.

### Activity

An Activity records something that happened and is retained in CRM history. Supported types are:

- Call
- Meeting
- Email
- Note
- Other

Every Activity has one primary subject: Company, Contact, or Opportunity. It may include Contact participants. Notes are an Activity type, not a separate business record. Notes use plain text in v1.

### Task

A Task is an actionable follow-up assigned to a User. Every Task has one primary subject: Company, Contact, or Opportunity. It may optionally include a Contact participant.

Task statuses are:

- Open
- In Progress
- Completed
- Canceled

Open Tasks require a due date. Completed and Canceled Tasks preserve their due-date history when present. The first release provides in-app due and overdue indicators, not email or push notifications.

### Timeline and history

A Timeline is the chronological view of relevant Activities and Tasks for a CRM record. Company timelines include direct history plus relevant Contact and Opportunity history. Opportunity timelines include their own history and explicitly related Contact activity without duplicating unrelated Company history.

Record History captures important business changes:

- Opportunity stage changes
- Opportunity outcomes and reopening
- Ownership changes
- Task completion
- Archive and restore actions

## 6. Core user workflows

### Create and qualify a sales relationship

1. A User creates a Company as a Prospect.
2. The User adds one or more Contacts to the Company.
3. The User creates an Opportunity for the Company.
4. The Opportunity progresses through pipeline stages.
5. The User records calls, meetings, emails, and notes.
6. The User creates follow-up Tasks and assigns ownership.
7. The dashboard reflects pipeline value, activity, and outstanding work.

### Manage an opportunity

1. Create an Opportunity with a primary Company and optional Contacts.
2. Keep the Opportunity in New without an amount if necessary.
3. Add an amount before moving it to Qualified.
4. Advance or move the Opportunity backward through the pipeline.
5. Mark it Won or Lost with an outcome reason.
6. Reopen it with a reason if circumstances change.

### Manage historical work

1. Log an Activity against a primary Company, Contact, or Opportunity.
2. Add Contact participants when relevant.
3. Create a Task with an owner and due date.
4. Complete, cancel, or reassign the Task.
5. Review the resulting Timeline and Record History.

### Manage users and records

1. The first User creates the Workspace and becomes Admin.
2. Admin invites additional Users.
3. Managers and Admins can edit any active record.
4. Contributors normally edit records they own.
5. A departing User is deactivated, not deleted.
6. Historical ownership and Activities remain intact; active Tasks and open Opportunities are reassigned.

## 7. Functional requirements

### Authentication and authorization

- Users can sign in and sign out.
- Workspace membership is required for CRM access.
- Every workspace-scoped operation enforces workspace authorization.
- User roles enforce the capabilities defined above.
- Deactivated Users cannot sign in or receive new work.

### Record lifecycle

- Business records are archived rather than hard-deleted.
- Archived records are excluded from normal lists, search, pipeline views, timelines, and dashboard metrics.
- Users with permission can explicitly view and restore archived records.
- Archiving a Company is blocked while it has open Opportunities.
- Archiving a Company with open Tasks warns the User; Tasks remain visible to their owners.

### Search and filtering

- Global search covers active Companies, Contacts, and Opportunities.
- Lists support filters for status, owner, stage, date, amount, Task status, and Activity type.
- Searching full Activity descriptions and Notes is deferred.

### Dashboard and reporting

The dashboard uses a fixed layout and defaults to the current quarter. It supports selectable month, quarter, year, and custom date ranges.

It displays:

- Pipeline value by stage
- Weighted pipeline
- Opportunities closing soon
- Won and Lost totals
- Overdue and upcoming Tasks
- Recent Activity

Amounts use the Workspace currency. Multi-currency conversion is deferred.

## 8. AI boundary

After the non-AI CRM timeline is stable, the product may add an **AI Summary** feature.

The AI Summary must:

- Be user-requested and read-only.
- Use only records the requesting User can already view.
- Use explicitly selected timeline history.
- Identify its source records.
- Display a clear AI-generated label.
- Never edit records, send messages, move Opportunities, assign Tasks, or make decisions for the User.

## 9. Quality and operational requirements

- Desktop-first responsive web experience, usable on tablets.
- Keyboard navigation, visible focus, semantic structure, readable contrast, and accessible form labels.
- Unit tests for domain rules.
- Integration tests for API and persistence behavior.
- End-to-end coverage of the core Company → Contact → Opportunity → Activity/Task → Dashboard workflow.
- Secure password handling, authenticated sessions, input validation, and authorization checks.
- Structured logs with request IDs and workspace context where available.
- Health checks that do not expose secrets.
- Repeatable development seed and reset workflows guarded against non-development environments.
- Database migration discipline and documented local restore/reset behavior.

## 10. Success criteria

The first release is useful when a User can:

1. Create a Company.
2. Add a Contact.
3. Create and advance an Opportunity.
4. Record a Call or Note.
5. Create and complete a follow-up Task.
6. Review the resulting Timeline and Record History.
7. See the updated pipeline and activity state on the Dashboard.

The product is ready for the next phase when this workflow is covered by automated tests, works with seeded data, respects workspace roles, and can be observed locally through logs and health checks.
