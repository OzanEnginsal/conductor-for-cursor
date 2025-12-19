# Implement

## Overview
Execute the tasks defined in the current storage component's plan. This command reads the plan file, works through tasks sequentially, follows the defined workflow (e.g., schema-first, TDD), updates progress, and guides you through verification steps. The agent will check off tasks as they are completed and update the storage component status. This implementation emphasizes storage-specific best practices including schema design, migration management, data integrity, and backup strategies.

## Steps

1. **Identify Active Storage Component**
   - Check `storage-conductor/tracks.md` for storage components with status "Planning" or "In Progress"
   - If multiple storage components exist, ask user which storage component to implement, or use the most recent one
   - Read the storage component's `plan.md` file from `storage-conductor/storages/<storage_id>/plan.md`

2. **Load Context**
   - Read project context files:
     - `storage-conductor/product.md`
     - `storage-conductor/tech-stack.md`
     - `storage-conductor/workflow.md`
     - `storage-conductor/storage-standards.md`
     - `storage-conductor/storage-template.md`
   - Read storage component specification: `storage-conductor/storages/<storage_id>/spec.md`
   - Read storage component plan: `storage-conductor/storages/<storage_id>/plan.md`

3. **Update Storage Component Status**
   - Update `storage-conductor/storages/<storage_id>/metadata.json` status to "In Progress"
   - Update `storage-conductor/tracks.md` status to "In Progress"

4. **Work Through Plan Sequentially**
   - For each phase in the plan:
     - Display the current phase name
     - For each task in the phase:
       - If task is already checked `- [x]`, skip it
       - If task is unchecked `- [ ]`:
         - Follow workflow preferences (e.g., if schema-first: Design Schema → Create Migration → Implement; if TDD: Write Test → Fail → Implement → Pass)
         - Implement the task according to:
           - Tech stack preferences
           - Storage standards (data consistency, backups, indexing)
           - Code style guides
           - Storage template patterns
         - For storage-specific tasks:
           - Design schemas according to standards
           - Create migrations following migration strategy
           - Implement CRUD operations with proper transaction handling
           - Add indexes based on query patterns
           - Implement data validation and integrity checks
           - Set up backup and recovery procedures
         - Update `plan.md` to mark task as complete: `- [x]`
         - Commit changes with descriptive commit message (if workflow specifies)
     - After completing all tasks in a phase:
       - Update phase status in plan
       - **Pause for Manual Verification**: Ask user to verify the phase works as expected
       - For schema design phase: Verify schema design is correct
       - For migration phase: Verify migrations run successfully
       - For core implementation phase: Verify CRUD operations work correctly
       - For indexing phase: Verify query performance improvements
       - For data integrity phase: Verify constraints and validations work
       - For backup phase: Verify backup and restore procedures
       - Wait for user confirmation before proceeding to next phase

5. **Follow Workflow Preferences**
   - If schema-first is specified in `workflow.md`:
     - Design schema first
     - Create migrations before implementation
     - Implement code to match schema
   - If TDD is specified:
     - Write tests first (unit tests, integration tests)
     - Ensure tests fail initially
     - Implement code to make tests pass
     - Refactor if needed
   - Follow migration strategy from `workflow.md`:
     - Versioned migrations: Create migration files
     - Automatic migrations: Use migration tooling
   - Follow commit strategy from `workflow.md`

6. **Apply Storage Standards**
   - **Data Consistency**: Follow patterns from `storage-standards.md`
     - Implement ACID transactions where needed
     - Handle eventual consistency if applicable
     - Ensure data integrity
   - **Backup & Recovery**: Implement according to standards
     - Set up automated backups
     - Create recovery procedures
     - Test backup and restore
   - **Indexing**: Implement according to standards
     - Create indexes based on query patterns
     - Optimize slow queries
     - Monitor index usage
   - **Migrations**: Follow migration management practices
     - Create forward and backward migrations
     - Test migrations on sample data
     - Document migration changes
   - **Data Validation**: Follow validation standards
     - Implement constraints at database level
     - Add application-level validation
     - Validate data before storage
   - **Transaction Handling**: Follow transaction patterns
     - Use transactions for multi-step operations
     - Handle transaction failures gracefully
     - Set appropriate isolation levels

7. **Update Progress**
   - After each task completion, update `plan.md` with `- [x]`
   - After each phase completion, update phase status
   - Periodically update `storage-conductor/storages/<storage_id>/metadata.json` updated timestamp
   - Update `storage-conductor/tracks.md` with latest status

8. **Handle Errors and Edge Cases**
   - If implementation encounters issues:
     - Document the problem
     - Suggest solutions based on storage standards
     - Update plan if scope changes
     - Ask user for guidance if blocked
   - Handle storage-specific issues:
     - Migration failures
     - Schema conflicts
     - Data integrity violations
     - Performance problems
     - Backup failures

9. **Complete Storage Component**
   - When all tasks are complete:
     - Update `storage-conductor/storages/<storage_id>/metadata.json` status to "Completed"
     - Update `storage-conductor/tracks.md` status to "Completed"
     - Verify all storage standards are followed
     - Ensure documentation is complete
     - Verify migrations are tested and documented
     - Verify backup and recovery procedures are tested
     - Provide summary of what was implemented

## Checklist

- [ ] Active storage component identified and plan.md loaded
- [ ] All context files read and understood (including storage standards)
- [ ] Storage component status updated to "In Progress"
- [ ] Tasks implemented sequentially following workflow
- [ ] Storage standards applied (consistency, backups, indexing, migrations)
- [ ] Schema design completed and documented
- [ ] Migrations created and tested
- [ ] Plan.md updated with completed tasks (`- [x]`)
- [ ] Manual verification completed for each phase
- [ ] Code follows tech stack, storage standards, and style guidelines
- [ ] Storage component status updated to "Completed" when done
- [ ] Tracks.md updated with final status

