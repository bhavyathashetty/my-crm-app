---
name: prd-to-linear
description: Use when the approved CRM PRD needs to be converted or synchronized into feature-level Linear issues for the CRM project.
---

# PRD to Linear

## Purpose

Convert the approved CRM Product Requirements Document into feature-level Linear issues.

This skill translates product requirements into manageable product work.

It does NOT perform implementation planning or software development.

---

# Fixed Linear Destination

Use exactly:

Team: `Anantha Subramanya`

Project: `CRM`

New issue status: `Backlog`

Do not select another team or project.

If the configured team or project cannot be found, stop and report the problem.

---

# Source of Truth

The authoritative product requirements are stored in:

`docs/product/PRD.md`

Always read the current PRD before creating or updating Linear issues.

The PRD is authoritative.

Linear issues are an execution representation of the PRD.

Do not modify the PRD based on Linear content.

---

# Core Principle

Create feature-level issues representing meaningful product capabilities.

Do not decompose features into implementation tasks or technical subtasks.

A feature issue should represent something valuable or meaningful from the product perspective.

Examples:

- Authentication & User Access
- Workspace Management
- Company Management
- Contact Management
- Opportunity Management
- Sales Pipeline
- Activities & Tasks
- Dashboard & Reporting
- Search & Filtering
- Audit History
- Observability Foundation

These are examples only.

The actual issues must be derived from `docs/product/PRD.md`.

---

# Preconditions

Before making any Linear changes:

1. Confirm `docs/product/PRD.md` exists.
2. Read the complete PRD.
3. Confirm Linear MCP is available.
4. Find the team exactly named:

   `Anantha Subramanya`

5. Find the project exactly named:

   `CRM`

6. Read existing project issues.
7. Identify issues previously managed by this skill.
8. Detect possible duplicates before creating anything.

If any prerequisite fails, stop.

Do not guess.

---

# Feature Identification

Analyze the PRD and identify major product capabilities.

Each issue should normally represent one cohesive capability that can later be refined independently.

A feature issue should:

- deliver recognizable product value
- have a clear objective
- have bounded scope
- map to one or more PRD requirements
- be understandable without implementation details
- be independently refinable later

Avoid issues that are either:

Too broad:

`Build CRM`

or too implementation-specific:

`Create POST /api/contacts endpoint`

Prefer:

`Contact Management`

---

# Issue Structure

Every managed feature issue should use this structure:

## Objective

Describe what capability this feature provides and why it exists.

## PRD Source

Identify the relevant PRD section or requirement.

## Scope

List the product behaviors included in this feature.

## High-Level Acceptance Criteria

Describe observable product outcomes.

Keep these at feature level.

Detailed implementation acceptance criteria will be added later by the `refine-story` skill.

## Dependencies

List other feature issues that must exist or be completed first.

Use Linear issue relationships when supported.

If no dependency exists, state:

`None`

## Out of Scope

Identify nearby functionality intentionally excluded from this feature.

## Source Metadata

Include:

`<!-- MANAGED_BY: prd-to-linear -->`

`<!-- PRD_SOURCE: <stable-source-key> -->`

The PRD source key must be stable across future synchronizations.

Example:

`<!-- PRD_SOURCE: contact-management -->`

---

# Managed Description Section

Where possible, structure managed issue descriptions using:

`<!-- PRD_TO_LINEAR_START -->`

[managed feature description]

`<!-- PRD_TO_LINEAR_END -->`

Only content inside these markers is controlled by this skill.

Preserve manually added content outside these markers.

---

# Creating New Issues

For every feature identified from the PRD:

1. Determine a clear feature title.
2. Generate a stable `PRD_SOURCE` key.
3. Search existing CRM project issues for that source key.
4. Also search for obvious title duplicates.
5. If no managed issue exists, create one.
6. Set:

   Team: `Anantha Subramanya`

   Project: `CRM`

   Status: `Backlog`

7. Add the structured managed description.
8. Add dependency relationships where appropriate.
9. Verify the issue after creation.

New issues MUST be created in `Backlog`.

---

# Updating Existing Managed Issues

If an issue already contains:

`<!-- MANAGED_BY: prd-to-linear -->`

and its `PRD_SOURCE` matches a current PRD feature:

1. Read the existing issue.
2. Compare its managed section with the current PRD.
3. Update the managed section only when requirements changed.
4. Preserve manual content outside the managed markers.
5. Preserve the current Linear status.
6. Preserve assignment.
7. Preserve priority unless explicitly requested.
8. Preserve comments.
9. Preserve implementation notes outside the managed section.
10. Verify the issue after updating it.

IMPORTANT:

Existing issues MUST NOT be moved back to `Backlog`.

Example:

If an issue is currently:

`In Progress`

and the PRD changes, update its requirements while keeping:

`In Progress`

---

# Status Rules

## New managed issue

Set status to:

`Backlog`

## Existing managed issue

Preserve its current status.

Never reset an existing issue to Backlog during synchronization.

This applies to statuses such as:

- Backlog
- Todo
- In Progress
- In Review
- Done
- Canceled

---

# Duplicate Prevention

Before creating an issue:

1. Search by `PRD_SOURCE`.
2. Search for the proposed issue title.
3. Inspect likely matches.

If exactly one managed issue matches:

Update it instead of creating a new issue.

If multiple possible matches exist:

Stop for that feature and report the ambiguity.

Never create another issue simply because the exact title differs slightly.

Example:

Existing:

`Contact Management`

Proposed:

`Contacts Management`

Do not automatically create a second issue.

Resolve the existing issue first.

---

# Dependencies

Identify obvious product-level dependencies.

Example:

`Contact Management`

may depend on:

`Company Management`

Use Linear dependency relationships if available.

Only create dependencies that are clearly supported by the PRD or product structure.

Do not invent unnecessary dependency chains.

Do not use dependencies to represent simple sequencing preferences.

---

# Removed Requirements

If a previously managed issue exists but its feature no longer appears in the current PRD:

DO NOT:

- delete the issue
- cancel the issue
- close the issue
- modify its status automatically

Instead, report it under:

`Potentially obsolete managed issues`

Human review is required before removing or canceling existing work.

---

# Issue Granularity

Create feature-level issues only.

Do NOT create:

- implementation tasks
- frontend tasks
- backend tasks
- database tasks
- API tasks
- testing subtasks
- Git tasks
- infrastructure subtasks
- individual acceptance-criterion issues

For example:

Correct:

`Company Management`

Incorrect:

- Create company table
- Build company API
- Build company form
- Add company validation
- Write company tests

Those belong to later refinement and implementation workflows.

---

# Architecture Boundary

The PRD defines WHAT the product must do.

This skill must not decide HOW the product will be implemented.

Do not introduce architecture-specific requirements such as:

- React components
- Express controllers
- REST endpoint paths
- PostgreSQL table names
- Supabase queries
- TypeScript interfaces
- Git branches
- worktree paths
- testing frameworks

unless they are explicitly stated as product constraints in the PRD.

Architecture and implementation details belong to later workflows.

---

# Acceptance Criteria Rules

High-level acceptance criteria should describe observable user or system behavior.

Good:

- Authenticated users can create a company record.
- Users can associate contacts with a company.
- Archived companies are distinguishable from active companies.

Avoid implementation details such as:

- POST `/api/company` returns HTTP 201.
- Prisma inserts the company.
- React refreshes the query cache.

Those belong to `refine-story` or implementation planning.

---

# No-Change Behavior

If all current PRD features already have matching managed Linear issues and their managed content is unchanged:

Do not make unnecessary Linear updates.

Report:

`Linear feature issues already match docs/product/PRD.md — no changes required.`

---

# Boundaries

This skill MUST NOT:

- modify `docs/product/PRD.md`
- modify the Linear PRD document
- rewrite product requirements
- invent product functionality
- create implementation subtasks
- create Git branches
- create Git worktrees
- write code
- generate architecture
- generate implementation plans
- assign developers
- estimate story points
- change existing issue statuses
- delete issues
- cancel issues
- close issues
- start development

This skill only creates and synchronizes feature-level Linear issues.

---

# Verification

After making changes:

1. Read the CRM project issues again.
2. Verify every newly created feature issue exists.
3. Verify every new issue is in `Backlog`.
4. Verify updated issues retained their previous status.
5. Verify every managed issue contains:

   `MANAGED_BY: prd-to-linear`

6. Verify every managed issue has a stable:

   `PRD_SOURCE`

7. Verify issues belong to:

   Team: `Anantha Subramanya`

   Project: `CRM`

8. Verify no duplicate issues were created.
9. Verify dependency relationships where applicable.

Do not report success until verification passes.

---

# Final Report

Return a concise summary:

## Linear Destination

Team: Anantha Subramanya

Project: CRM

Source: docs/product/PRD.md

## Created

- <issue identifier> — <title>
- <issue identifier> — <title>

## Updated

- <issue identifier> — <title>

## Unchanged

- <issue identifier> — <title>

## Dependencies Added

- <issue> depends on <issue>

## Potentially Obsolete Managed Issues

- <issue identifier> — <title>

Do not modify these automatically.

## Ambiguities

- None

or describe anything requiring human review.

## Verification

Passed | Failed