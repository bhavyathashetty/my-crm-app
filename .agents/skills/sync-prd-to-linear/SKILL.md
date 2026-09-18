---

name: sync-prd-to-linear
description: Use when the approved CRM PRD in docs/product/PRD.md needs to be synchronized with the configured Linear project.
------------------------------------------------------------------------------------------------------------------------------

# Sync PRD to Linear

## Purpose

Synchronize the approved local CRM Product Requirements Document with the configured Linear project using Linear MCP.

The local PRD is the source of truth.

This skill only synchronizes the PRD.

It does not create stories, tasks, architecture, code, branches, or implementation plans.

---

# Linear Destination

Use the following Linear destination:

Team: Anantha Subramanya

Project: CRM

Source: docs/product/PRD.md

These values are fixed for this project.

Do not select another Linear team or project based on name similarity.

Do not create a replacement team or project automatically.

If the configured team or project cannot be found, stop and report the problem.

---

# PRD Source

The authoritative PRD is:

`docs/product/PRD.md`

The local PRD is always the source of truth.

Linear contains a synchronized representation of the PRD.

Do not modify `docs/product/PRD.md` based on information found in Linear.

---

# Preconditions

Before making any Linear changes:

1. Confirm that `docs/product/PRD.md` exists.

2. Read the complete PRD.

3. Confirm that the Linear MCP server is available.

4. Locate the Linear team exactly named:

   `Anantha Subramanya`

5. Within that team, locate the Linear project exactly named:

   `CRM`

6. Read the existing CRM project before making changes.

If either the team or project cannot be found, stop.

Do not guess.

Do not create a new project unless explicitly instructed by the user.

---

# Workflow

When this skill is invoked:

1. Read `docs/product/PRD.md`.
2. Connect to Linear through Linear MCP.
3. Locate team `Anantha Subramanya`.
4. Locate project `CRM`.
5. Read the current CRM project description or PRD content.
6. Locate the managed PRD section.
7. Compare the Linear PRD with the local PRD.
8. Determine whether synchronization is required.
9. If the contents already match, perform no update.
10. If the contents differ, update only the managed PRD section.
11. Preserve all Linear content outside the managed section.
12. Read the Linear project again after the update.
13. Verify that the synchronization succeeded.
14. Report the result.

---

# Managed PRD Section

When storing the PRD in the Linear project description, use the following markers:

`<!-- PRD_SYNC_START -->`

# Product Requirements Document

[Contents synchronized from docs/product/PRD.md]

`<!-- PRD_SYNC_END -->`

Only content between these markers is controlled by this skill.

Content outside these markers belongs to Linear users or other workflows and must not be modified.

---

# First Synchronization

If the CRM project exists but does not yet contain PRD sync markers:

1. Read the complete existing project description.
2. Preserve all existing content.
3. Append the following section:

`<!-- PRD_SYNC_START -->`

# Product Requirements Document

[Contents of docs/product/PRD.md]

`<!-- PRD_SYNC_END -->`

4. Update the CRM project.
5. Read the project again.
6. Verify that the PRD was added.
7. Verify that the previous project content was preserved.

Never replace the entire project description unnecessarily.

---

# Existing PRD Synchronization

If the PRD sync markers already exist:

1. Read the content between `PRD_SYNC_START` and `PRD_SYNC_END`.
2. Compare it with `docs/product/PRD.md`.
3. If the content matches, do nothing.
4. If the content differs, replace only the content inside the managed section.
5. Preserve everything outside the markers.
6. Verify the update afterward.

---

# No-Change Behavior

If the local PRD and Linear PRD already match:

Do not make an unnecessary Linear update.

Report:

`Linear PRD already matches docs/product/PRD.md — no synchronization required.`

---

# Linear Destination Safety

Before every write operation, confirm:

Team:

`Anantha Subramanya`

Project:

`CRM`

The skill must never write to another team or project.

If multiple projects named `CRM` are returned:

1. Restrict the search to team `Anantha Subramanya`.
2. Confirm the exact project within that team.
3. If ambiguity remains, stop and report the ambiguity.

Never resolve ambiguity by guessing.

---

# Boundaries

This skill MUST NOT:

* modify `docs/product/PRD.md`
* rewrite the PRD
* improve the PRD
* shorten the PRD
* expand the PRD
* reinterpret requirements
* invent requirements
* create Linear implementation stories
* create Linear development tasks
* create milestones
* change issue statuses
* assign issues
* make architecture decisions
* create Git branches
* create Git worktrees
* write application code
* run application development
* modify another Linear project
* modify another Linear team

This skill performs PRD synchronization only.

---

# Fidelity Rules

When synchronizing the PRD:

Preserve, where supported:

* headings
* numbered lists
* bullet lists
* acceptance criteria
* requirements
* user stories
* tables
* code blocks
* scope definitions
* out-of-scope definitions
* product constraints

Do not summarize sections merely to make them shorter.

The Linear copy should represent the approved PRD as faithfully as possible.

---

# Verification

After any Linear update:

1. Read team `Anantha Subramanya`.
2. Read project `CRM`.
3. Retrieve the updated PRD section.
4. Confirm the expected PRD content exists.
5. Confirm the content matches `docs/product/PRD.md`.
6. Confirm content outside the PRD sync markers remains intact.

Do not report success until verification passes.

---

# Failure Handling

If Linear MCP is unavailable:

Stop and report:

`Linear MCP is not available. PRD synchronization was not performed.`

If team `Anantha Subramanya` cannot be found:

Stop and report:

`Configured Linear team 'Anantha Subramanya' could not be found.`

If project `CRM` cannot be found:

Stop and report:

`Configured Linear project 'CRM' could not be found under team 'Anantha Subramanya'.`

If verification fails:

Report the failure.

Do not claim that synchronization succeeded.

---

# Final Response

After execution, return a concise report using this structure:

Linear Team:
Anantha Subramanya

Linear Project:
CRM

Source:
docs/product/PRD.md

Action:
Updated | No changes required | Failed

Changes:

* Brief summary of what changed

Preserved:

* Existing Linear content outside the managed PRD section

Verification:
Passed | Failed
