# Implement

## Overview
Execute the tasks defined in the current connector's plan. This command reads the plan file, works through tasks sequentially, follows the defined workflow (e.g., TDD), updates progress, and guides you through verification steps. The agent will check off tasks as they are completed and update the connector status. This implementation emphasizes API-specific best practices including mock API testing, authentication flows, and rate limiting.

## Steps

1. **Identify Active Connector**
   - Check `api-conductor/tracks.md` for connectors with status "Planning" or "In Progress"
   - If multiple connectors exist, ask user which connector to implement, or use the most recent one
   - Read the connector's `plan.md` file from `api-conductor/connectors/<connector_id>/plan.md`

2. **Load Context**
   - Read project context files:
     - `api-conductor/product.md`
     - `api-conductor/tech-stack.md`
     - `api-conductor/workflow.md`
     - `api-conductor/api-standards.md`
     - `api-conductor/connector-template.md`
   - Read connector specification: `api-conductor/connectors/<connector_id>/spec.md`
   - Read connector plan: `api-conductor/connectors/<connector_id>/plan.md`

3. **Update Connector Status**
   - Update `api-conductor/connectors/<connector_id>/metadata.json` status to "In Progress"
   - Update `api-conductor/tracks.md` status to "In Progress"

4. **Work Through Plan Sequentially**
   - For each phase in the plan:
     - Display the current phase name
     - For each task in the phase:
       - If task is already checked `- [x]`, skip it
       - If task is unchecked `- [ ]`:
         - Follow workflow preferences (e.g., if TDD: Write Test → Fail → Implement → Pass)
         - Implement the task according to:
           - Tech stack preferences
           - API standards (authentication, rate limiting, error handling)
           - Code style guides
           - Connector template patterns
         - For API-specific tasks:
           - Set up mock API server if needed (for testing)
           - Implement authentication flow according to standards
           - Add rate limiting logic per API standards
           - Implement error handling per API standards
         - Update `plan.md` to mark task as complete: `- [x]`
         - Commit changes with descriptive commit message (if workflow specifies)
     - After completing all tasks in a phase:
       - Update phase status in plan
       - **Pause for Manual Verification**: Ask user to verify the phase works as expected
       - For authentication phase: Verify OAuth flow works
       - For API client phase: Verify requests work with mock API
       - For rate limiting phase: Verify rate limit handling
       - Wait for user confirmation before proceeding to next phase

5. **Follow Workflow Preferences**
   - If TDD is specified in `workflow.md`:
     - Write tests first (unit tests with mock API responses)
     - Ensure tests fail initially
     - Implement code to make tests pass
     - Refactor if needed
   - If BDD is specified:
     - Write feature specifications
     - Implement step definitions
     - Implement features
   - Follow commit strategy from `workflow.md`
   - Use mock API servers for development and testing

6. **Apply API Standards**
   - **Authentication**: Follow patterns from `api-standards.md`
     - Implement OAuth flows correctly
     - Handle token refresh automatically
     - Secure credential storage
   - **Rate Limiting**: Implement according to standards
     - Parse rate limit headers
     - Implement exponential backoff
     - Queue requests if needed
   - **Error Handling**: Follow error handling standards
     - Create custom error classes
     - Map API error codes
     - Implement retry logic for transient errors
   - **Data Models**: Follow data model conventions
     - Use schema validation
     - Proper type definitions
     - Serialization/deserialization

7. **Update Progress**
   - After each task completion, update `plan.md` with `- [x]`
   - After each phase completion, update phase status
   - Periodically update `api-conductor/connectors/<connector_id>/metadata.json` updated timestamp
   - Update `api-conductor/tracks.md` with latest status

8. **Handle Errors and Edge Cases**
   - If implementation encounters issues:
     - Document the problem
     - Suggest solutions based on API standards
     - Update plan if scope changes
     - Ask user for guidance if blocked
   - Handle API-specific issues:
     - Rate limit exceeded errors
     - Authentication failures
     - Network timeouts
     - Invalid API responses

9. **Complete Connector**
   - When all tasks are complete:
     - Update `api-conductor/connectors/<connector_id>/metadata.json` status to "Completed"
     - Update `api-conductor/tracks.md` status to "Completed"
     - Verify all API standards are followed
     - Ensure documentation is complete
     - Provide summary of what was implemented

## Checklist

- [ ] Active connector identified and plan.md loaded
- [ ] All context files read and understood (including API standards)
- [ ] Connector status updated to "In Progress"
- [ ] Tasks implemented sequentially following workflow
- [ ] API standards applied (auth, rate limiting, error handling)
- [ ] Mock API server set up for testing (if needed)
- [ ] Plan.md updated with completed tasks (`- [x]`)
- [ ] Manual verification completed for each phase
- [ ] Code follows tech stack, API standards, and style guidelines
- [ ] Connector status updated to "Completed" when done
- [ ] Tracks.md updated with final status
