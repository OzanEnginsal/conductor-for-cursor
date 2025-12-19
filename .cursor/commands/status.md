# Status

## Overview
Display the current progress of all tracks in the project. This command reads the tracks tracking file and provides a high-level overview of active tracks, their status, and progress summary. Useful for getting a quick view of what's in progress, what's planned, and what's been completed.

## Steps

1. **Read Tracks File**
   - Read `conductor/tracks.md` to get list of all tracks
   - Parse track entries to extract: ID, Title, Type, Status, Created Date, Updated Date

2. **Read Track Details (if available)**
   - For each track, check if `conductor/tracks/<track_id>/plan.md` exists
   - Count completed tasks vs total tasks in plan.md
   - Calculate progress percentage for each track

3. **Organize Tracks by Status**
   - Group tracks by status:
     - **Planning**: Tracks in planning phase
     - **In Progress**: Tracks currently being implemented
     - **Completed**: Finished tracks
     - **Blocked**: Tracks that are blocked (if status exists)
     - **Cancelled**: Cancelled tracks (if status exists)

4. **Display Summary**
   - Show overall statistics:
     - Total tracks
     - Tracks by status (count)
     - Active tracks (In Progress)
   - Display each track with:
     - Track ID
     - Title
     - Type (Feature/Bug)
     - Status
     - Progress (X/Y tasks completed, percentage if available)
     - Created date
     - Last updated date

5. **Highlight Active Tracks**
   - If there are tracks "In Progress", show them prominently
   - Display current phase being worked on
   - Show next pending tasks

6. **Provide Recommendations**
   - If multiple tracks are "In Progress", suggest focusing on one
   - If tracks are stuck in "Planning", suggest moving to implementation
   - If no active tracks, suggest starting a new track

## Checklist

- [ ] `conductor/tracks.md` read successfully
- [ ] Track details parsed correctly
- [ ] Progress calculated for tracks with plans
- [ ] Tracks organized by status
- [ ] Summary statistics displayed
- [ ] Individual track details shown
- [ ] Active tracks highlighted
- [ ] Recommendations provided (if applicable)
