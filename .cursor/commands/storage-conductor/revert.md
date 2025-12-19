# Revert

## Overview
Revert a storage component, phase, or task by analyzing git history to identify logical units of work. This command understands the Storage Conductor workflow and can revert entire storage components, specific phases, or individual tasks rather than just commit hashes. It analyzes git commits, file changes, and commit messages to determine what to revert. Special attention is paid to storage-specific concerns like schema migrations, data model changes, and backup configurations.

## Steps

1. **Identify What to Revert**
   - Ask user what they want to revert:
     - Entire storage component
     - Specific phase within a storage component
     - Specific task within a storage component
   - Get storage component ID from user or list available storage components

2. **Load Storage Component Information**
   - Read `storage-conductor/storages/<storage_id>/metadata.json` to get storage component details
   - Read `storage-conductor/storages/<storage_id>/plan.md` to understand phases and tasks
   - Read `storage-conductor/storages/<storage_id>/spec.md` to understand what was built

3. **Analyze Git History**
   - Use `git log` to find commits related to the storage component
   - Look for commit messages containing:
     - Storage component ID
     - Phase names
     - Task descriptions
     - Storage type and backend
   - Analyze file changes to identify which files were modified for this storage component
   - Map commits to phases/tasks based on:
     - Commit timestamps vs storage component creation date
     - Commit messages
     - Files changed (migrations, schemas, models)
     - Commit grouping patterns

4. **Determine Revert Strategy**
   - **For entire storage component**: Revert all commits associated with the storage component
   - **For phase**: Identify commits for that phase and revert them
   - **For task**: Identify commits for that specific task and revert them
   - Consider dependencies: if reverting a phase, may need to revert later phases
   - Consider storage-specific dependencies (e.g., schema must come before queries, migrations must be sequential)
   - **Special handling for migrations**:
     - Identify migration files to revert
     - Determine if forward or backward migration is needed
     - Check for data dependencies in migrations

5. **Preview Changes**
   - Show user what will be reverted:
     - List of commits to revert
     - Files that will be affected (schema files, migrations, models, etc.)
     - Impact on other storage components (if any)
     - Impact on shared storage utilities (if any)
     - Migration files that will be reverted
   - Ask for confirmation before proceeding
   - **Warning**: Alert user if reverting migrations that have been applied to production/staging databases

6. **Execute Revert**
   - If reverting entire storage component:
     - Use `git revert` for each commit (in reverse order) or `git reset` if appropriate
     - Remove storage component directory: `storage-conductor/storages/<storage_id>/`
     - Update `storage-conductor/tracks.md` to remove or mark storage component as "Reverted"
     - **Note**: May need to manually revert database migrations if they were applied
   - If reverting phase:
     - Revert commits associated with that phase
     - Update `plan.md` to uncheck tasks in that phase
     - Update phase status in plan
     - **Handle migrations**: If reverting migration phase, may need to create rollback migrations
   - If reverting task:
     - Revert commits associated with that task
     - Update `plan.md` to uncheck that specific task
     - **Handle schema changes**: If reverting schema task, ensure related code is also reverted

7. **Update Storage Component Status**
   - Update `storage-conductor/storages/<storage_id>/metadata.json`:
     - If entire storage component reverted: remove file or mark as "Reverted"
     - If phase/task reverted: update status to "In Progress" or "Planning"
   - Update `storage-conductor/tracks.md` with new status

8. **Handle Conflicts**
   - If git revert encounters conflicts:
     - Show conflict details
     - Guide user through resolution
     - Or abort revert if conflicts are too complex
   - Pay special attention to conflicts in:
     - Shared storage utilities
     - Migration files
     - Schema definitions
     - Model files

9. **Handle Database Migrations**
   - **If reverting migrations**:
     - Alert user that database state may need manual intervention
     - Check if migrations were applied to any environments
     - Provide guidance on creating rollback migrations
     - Document which migrations need to be rolled back manually
   - **If reverting schema changes**:
     - Check for dependent queries or code
     - Ensure data model changes are properly reverted

10. **Verify Revert**
    - After revert, verify:
      - Code changes are reverted correctly
      - Storage component files are updated appropriately
      - No unintended side effects on other storage components
      - Migration files are properly handled
      - Schema definitions are consistent

## Checklist

- [ ] Storage component/phase/task to revert identified
- [ ] Storage component information loaded
- [ ] Git history analyzed for related commits
- [ ] Revert strategy determined (with migration considerations)
- [ ] Changes previewed and user confirmed
- [ ] Git revert executed successfully
- [ ] Storage component files updated (plan.md, metadata.json, tracks.md)
- [ ] Migrations handled appropriately (with warnings if needed)
- [ ] Conflicts resolved (if any)
- [ ] Revert verified and complete

