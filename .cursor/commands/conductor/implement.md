# Implement

## Overview
Execute the tasks defined in the current track's plan. This command reads the plan file, works through tasks sequentially, follows the defined workflow (e.g., TDD), updates progress, and guides you through verification steps. The agent will check off tasks as they are completed and update the track status.

## Steps

1. **Identify Active Track**
   - Check `conductor/tracks.md` for tracks with status "Planning" or "In Progress"
   - If multiple tracks exist, ask user which track to implement, or use the most recent one
   - Read the track's `plan.md` file from `conductor/tracks/<track_id>/plan.md`

2. **Load Context**
   - Read project context files:
     - `conductor/product.md`
     - `conductor/product-guidelines.md`
     - `conductor/tech-stack.md`
     - `conductor/workflow.md`
   - Read track specification: `conductor/tracks/<track_id>/spec.md`
   - Read track plan: `conductor/tracks/<track_id>/plan.md`

3. **Update Track Status**
   - Update `conductor/tracks/<track_id>/metadata.json` status to "In Progress"
   - Update `conductor/tracks.md` status to "In Progress"

4. **Work Through Plan Sequentially**
   - For each phase in the plan:
     - Display the current phase name
     - For each task in the phase:
       - If task is already checked `- [x]`, skip it
       - If task is unchecked `- [ ]`:
         - Follow workflow preferences (e.g., if TDD: Write Test → Fail → Implement → Pass)
         - Implement the task according to:
           - Tech stack preferences
           - Code style guides
           - Product guidelines
         - Update `plan.md` to mark task as complete: `- [x]`
         - Commit changes with descriptive commit message (if workflow specifies)
     - After completing all tasks in a phase:
       - Update phase status in plan
       - **Pause for Manual Verification**: Ask user to verify the phase works as expected
       - Wait for user confirmation before proceeding to next phase

5. **Follow Workflow Preferences**
   - If TDD is specified in `workflow.md`:
     - Write tests first
     - Ensure tests fail initially
     - Implement code to make tests pass
     - Refactor if needed
   - If BDD is specified:
     - Write feature specifications
     - Implement step definitions
     - Implement features
   - Follow commit strategy from `workflow.md`

6. **Update Progress**
   - After each task completion, update `plan.md` with `- [x]`
   - After each phase completion, update phase status
   - Periodically update `conductor/tracks/<track_id>/metadata.json` updated timestamp
   - Update `conductor/tracks.md` with latest status

7. **Handle Errors and Edge Cases**
   - If implementation encounters issues:
     - Document the problem
     - Suggest solutions
     - Update plan if scope changes
     - Ask user for guidance if blocked

8. **Complete Track**
   - When all tasks are complete:
     - Update `conductor/tracks/<track_id>/metadata.json` status to "Completed"
     - Update `conductor/tracks.md` status to "Completed"
     - Synchronize any updates to project context files if needed
     - Provide summary of what was implemented

## Checklist

- [ ] Active track identified and plan.md loaded
- [ ] All context files read and understood
- [ ] Track status updated to "In Progress"
- [ ] Tasks implemented sequentially following workflow
- [ ] Plan.md updated with completed tasks (`- [x]`)
- [ ] Manual verification completed for each phase
- [ ] Code follows tech stack and style guidelines
- [ ] Track status updated to "Completed" when done
- [ ] Tracks.md updated with final status
