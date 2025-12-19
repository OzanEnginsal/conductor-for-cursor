# Revert

## Overview
Revert a connector, phase, or task by analyzing git history to identify logical units of work. This command understands the API Conductor workflow and can revert entire connectors, specific phases, or individual tasks rather than just commit hashes. It analyzes git commits, file changes, and commit messages to determine what to revert.

## Steps

1. **Identify What to Revert**
   - Ask user what they want to revert:
     - Entire connector
     - Specific phase within a connector
     - Specific task within a connector
   - Get connector ID from user or list available connectors

2. **Load Connector Information**
   - Read `api-conductor/connectors/<connector_id>/metadata.json` to get connector details
   - Read `api-conductor/connectors/<connector_id>/plan.md` to understand phases and tasks
   - Read `api-conductor/connectors/<connector_id>/spec.md` to understand what was built

3. **Analyze Git History**
   - Use `git log` to find commits related to the connector
   - Look for commit messages containing:
     - Connector ID
     - Phase names
     - Task descriptions
     - Platform name
   - Analyze file changes to identify which files were modified for this connector
   - Map commits to phases/tasks based on:
     - Commit timestamps vs connector creation date
     - Commit messages
     - Files changed
     - Commit grouping patterns

4. **Determine Revert Strategy**
   - **For entire connector**: Revert all commits associated with the connector
   - **For phase**: Identify commits for that phase and revert them
   - **For task**: Identify commits for that specific task and revert them
   - Consider dependencies: if reverting a phase, may need to revert later phases
   - Consider API-specific dependencies (e.g., authentication must come before endpoints)

5. **Preview Changes**
   - Show user what will be reverted:
     - List of commits to revert
     - Files that will be affected
     - Impact on other connectors (if any)
     - Impact on shared API utilities (if any)
   - Ask for confirmation before proceeding

6. **Execute Revert**
   - If reverting entire connector:
     - Use `git revert` for each commit (in reverse order) or `git reset` if appropriate
     - Remove connector directory: `api-conductor/connectors/<connector_id>/`
     - Update `api-conductor/tracks.md` to remove or mark connector as "Reverted"
   - If reverting phase:
     - Revert commits associated with that phase
     - Update `plan.md` to uncheck tasks in that phase
     - Update phase status in plan
   - If reverting task:
     - Revert commits associated with that task
     - Update `plan.md` to uncheck that specific task

7. **Update Connector Status**
   - Update `api-conductor/connectors/<connector_id>/metadata.json`:
     - If entire connector reverted: remove file or mark as "Reverted"
     - If phase/task reverted: update status to "In Progress" or "Planning"
   - Update `api-conductor/tracks.md` with new status

8. **Handle Conflicts**
   - If git revert encounters conflicts:
     - Show conflict details
     - Guide user through resolution
     - Or abort revert if conflicts are too complex
   - Pay special attention to conflicts in:
     - Shared API utilities
     - Authentication code
     - Rate limiting logic

9. **Verify Revert**
   - After revert, verify:
     - Code changes are reverted correctly
     - Connector files are updated appropriately
     - No unintended side effects on other connectors
     - Mock API tests still work (if applicable)

## Checklist

- [ ] Connector/phase/task to revert identified
- [ ] Connector information loaded
- [ ] Git history analyzed for related commits
- [ ] Revert strategy determined
- [ ] Changes previewed and user confirmed
- [ ] Git revert executed successfully
- [ ] Connector files updated (plan.md, metadata.json, tracks.md)
- [ ] Conflicts resolved (if any)
- [ ] Revert verified and complete
