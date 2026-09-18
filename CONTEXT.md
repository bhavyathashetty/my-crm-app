# CRM Domain

This glossary defines the business concepts used by the CRM. It is intentionally separate from implementation details and product planning.

## Workspace and people

**Workspace**:
A business boundary containing its CRM records and users. The first release may serve one deployed business, but the domain keeps the business boundary explicit.
_Avoid_: Tenant, account

**User**:
A person who can sign in to a workspace and use the CRM.
_Avoid_: Member, employee

**Owner**:
The user responsible for a CRM record or follow-up, without restricting other workspace users from seeing the record in the first release.
_Avoid_: Assignee when referring to record responsibility

## CRM records

**Company**:
An organization that the business may sell to or work with.
_Avoid_: Account, customer (unless the company has actually become a customer)

A Company may be Prospect, Customer, or Inactive. Customer is a Company status, not a separate kind of record.

Companies are independent records in the initial product; parent/subsidiary hierarchies are deferred.

**Company Status**:
The business relationship state of a Company: Prospect, Customer, or Inactive.
_Avoid_: Lifecycle stage (to keep it distinct from opportunity Pipeline Stage)

Company status changes are user-controlled; a Won Opportunity does not automatically change its Company's status.

**Contact**:
A person associated with a company and involved in the business relationship.
_Avoid_: Lead (when the person is already part of the active CRM relationship)

**Opportunity**:
A qualified potential sale associated with one primary company and potentially multiple contacts.
_Avoid_: Deal, prospect (unless referring to an external business term)

An Opportunity has an amount, workspace currency, Pipeline Stage, probability, expected close date, and owner. Its probability defaults from the stage and may be overridden.

A Company may have multiple simultaneous Opportunities. An open Opportunity past its expected close date remains open and is reported as overdue.

**Forecast Probability**:
The estimated likelihood that an Opportunity will be Won. It normally comes from its Pipeline Stage but may be overridden by the owner or an authorized user.
_Avoid_: Confidence (when reporting the probability of an opportunity outcome)

An Opportunity may be created without an amount in New, but must have an amount before entering Qualified.

**Workspace Currency**:
The currency used for opportunity amounts and pipeline reporting in a Workspace.
_Avoid_: Account currency, reporting currency

**Lead**:
An unqualified person or organization that has not yet entered the active company/contact relationship model. Leads are deferred from the initial release.
_Avoid_: Prospect, contact (until qualification/conversion)

## Work and history

**Pipeline**:
The ordered set of stages through which an opportunity progresses toward a won or lost outcome.
_Avoid_: Funnel (when referring to the opportunity workflow)

**Activity**:
A record of something that happened, such as a call, meeting, email, or note, retained in the CRM history.
_Avoid_: Task, event (when referring to a completed interaction)

An Activity has one primary subject and may include contact participants. Notes are an Activity type rather than a separate business record.

**Activity Type**:
The kind of interaction recorded in an Activity. The initial types are Call, Meeting, Email, Note, and Other.
_Avoid_: Channel (when referring to the kind of recorded interaction)

**Task**:
An actionable follow-up assigned to a user, with a status and due date. Completed tasks remain part of the historical timeline.
_Avoid_: Activity, reminder

Tasks may be Open, In Progress, Completed, or Canceled.

Open Tasks require a due date; completed and canceled tasks retain their due-date history when present.

Each Task has one Primary Subject: a Company, Contact, or Opportunity. It may optionally include a contact participant.

**Archive**:
The reversible state used when a business record should leave normal workflows without losing its history or relationships.
_Avoid_: Delete (for ordinary record removal)

**Record History**:
The business-visible history of important changes to a record, including opportunity stage and outcome changes, ownership changes, task completion, and archival or restoration.
_Avoid_: Audit log (when referring only to the initial product's user-facing history)

**Timeline**:
The chronological view of activities and tasks associated with a CRM record, including relevant related-record history according to that record's context.
_Avoid_: Feed, audit log

Archived records are excluded from normal timelines and views but remain available through an explicit archived-record view.

**Deactivated User**:
A user who can no longer sign in or receive new work but whose historical ownership and activity records remain intact.
_Avoid_: Deleted user, removed user

## Product boundaries

**Task Reminder**:
An in-app indication that a task is due or overdue. The initial product does not send email or push notifications.
_Avoid_: Notification (when referring to the initial in-app task indication)

**AI Summary**:
A read-only, user-requested summary generated from selected CRM timeline history. It is a suggestion and cannot change records, send messages, move opportunities, or assign work.
_Avoid_: AI Agent, automation

An AI Summary may use only records the requesting user can view and must identify its source records as well as its AI-generated nature.

**Workspace Time Zone**:
The time zone used for date-based reporting and task due-date interpretation within a Workspace. Event timestamps are displayed in each user's local time.
_Avoid_: Server time, account time zone

**Fixed Schema**:
The intentionally defined set of CRM fields available in the initial release. Workspace-specific custom fields are outside the initial domain.
_Avoid_: Dynamic schema, custom attributes

**Pipeline Stage**:
One ordered step in the opportunity pipeline. The initial stages are New, Qualified, Proposal, Negotiation, Won, and Lost.
_Avoid_: Status (when referring to an opportunity's position in the pipeline)

**Primary Subject**:
The main CRM record that an activity describes. An activity has one primary subject and may also include related contacts as participants.
_Avoid_: Parent (when referring to the business meaning of an activity relationship)

**Participant**:
A contact involved in an activity in addition to its primary subject.
_Avoid_: Attendee (unless the activity is specifically a meeting)

**Outcome**:
The Won or Lost result of an opportunity, including the reason recorded for that result. Outcomes are terminal for normal reporting but an opportunity may be reopened with a reason.
_Avoid_: Resolution (when referring to an opportunity result)

An Outcome reason is required for both Won and Lost.

## Workspace roles

**Admin**:
A workspace user who can manage the workspace and edit any CRM record.
_Avoid_: Owner (an admin may own records, but the concepts are different)

**Manager**:
A workspace user who can edit any CRM record while remaining accountable for their own records and follow-ups.
_Avoid_: Admin (unless the user also has workspace administration authority)

**Contributor**:
A workspace user who can view workspace records and normally edits records they own.
_Avoid_: Representative, salesperson
