# Revert

## Overview
Revert a track, phase, or task by analyzing git history to identify logical units of work. This command understands the Conductor workflow and can revert entire tracks, specific phases, or individual tasks rather than just commit hashes. It analyzes git commits, file changes, and commit messages to determine what to revert.

## Steps

1. **Identify What to Revert**
   - Ask user what they want to revert:
     - Entire track
     - Specific phase within a track
     - Specific task within a track
   - Get track ID from user or list available tracks

2. **Load Track Information**
   - Read `conductor/tracks/<track_id>/metadata.json` to get track details
   - Read `conductor/tracks/<track_id>/plan.md` to understand phases and tasks
   - Read `conductor/tracks/<track_id>/spec.md` to understand what was built

3. **Analyze Git History**
   - Use `git log` to find commits related to the track
   - Look for commit messages containing:
     - Track ID
     - Phase names
     - Task descriptions
   - Analyze file changes to identify which files were modified for this track
   - Map commits to phases/tasks based on:
     - Commit timestamps vs track creation date
     - Commit messages
     - Files changed
     - Commit grouping patterns

4. **Determine Revert Strategy**
   - **For entire track**: Revert all commits associated with the track
   - **For phase**: Identify commits for that phase and revert them
   - **For task**: Identify commits for that specific task and revert them
   - Consider dependencies: if reverting a phase, may need to revert later phases

5. **Preview Changes**
   - Show user what will be reverted:
     - List of commits to revert
     - Files that will be affected
     - Impact on other tracks (if any)
   - Ask for confirmation before proceeding

6. **Execute Revert**
   - If reverting entire track:
     - Use `git revert` for each commit (in reverse order) or `git reset` if appropriate
     - Remove track directory: `conductor/tracks/<track_id>/`
     - Update `conductor/tracks.md` to remove or mark track as "Reverted"
   - If reverting phase:
     - Revert commits associated with that phase
     - Update `plan.md` to uncheck tasks in that phase
     - Update phase status in plan
   - If reverting task:
     - Revert commits associated with that task
     - Update `plan.md` to uncheck that specific task

7. **Update Track Status**
   - Update `conductor/tracks/<track_id>/metadata.json`:
     - If entire track reverted: remove file or mark as "Reverted"
     - If phase/task reverted: update status to "In Progress" or "Planning"
   - Update `conductor/tracks.md` with new status

8. **Handle Conflicts**
   - If git revert encounters conflicts:
     - Show conflict details
     - Guide user through resolution
     - Or abort revert if conflicts are too complex

9. **Verify Revert**
   - After revert, verify:
     - Code changes are reverted correctly
     - Track files are updated appropriately
     - No unintended side effects on other tracks

## Checklist

- [ ] Track/phase/task to revert identified
- [ ] Track information loaded
- [ ] Git history analyzed for related commits
- [ ] Revert strategy determined
- [ ] Changes previewed and user confirmed
- [ ] Git revert executed successfully
- [ ] Track files updated (plan.md, metadata.json, tracks.md)
- [ ] Conflicts resolved (if any)
- [ ] Revert verified and complete
