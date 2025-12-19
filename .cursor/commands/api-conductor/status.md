# Status

## Overview
Display the current progress of all connectors in the project. This command reads the connectors tracking file and provides a high-level overview of active connectors, their status, and progress summary. Useful for getting a quick view of what's in progress, what's planned, and what's been completed.

## Steps

1. **Read Connectors File**
   - Read `api-conductor/tracks.md` to get list of all connectors
   - Parse connector entries to extract: ID, Title, Platform, Type, Status, Created Date, Updated Date

2. **Read Connector Details (if available)**
   - For each connector, check if `api-conductor/connectors/<connector_id>/plan.md` exists
   - Count completed tasks vs total tasks in plan.md
   - Calculate progress percentage for each connector
   - Read `api-conductor/connectors/<connector_id>/metadata.json` for additional details (API type, auth method)

3. **Organize Connectors by Status**
   - Group connectors by status:
     - **Planning**: Connectors in planning phase
     - **In Progress**: Connectors currently being implemented
     - **Completed**: Finished connectors
     - **Blocked**: Connectors that are blocked (if status exists)
     - **Cancelled**: Cancelled connectors (if status exists)

4. **Display Summary**
   - Show overall statistics:
     - Total connectors
     - Connectors by status (count)
     - Active connectors (In Progress)
     - Connectors by platform (if multiple)
   - Display each connector with:
     - Connector ID
     - Title
     - Platform name
     - API Type (REST, GraphQL, etc.)
     - Authentication method
     - Type (Connector)
     - Status
     - Progress (X/Y tasks completed, percentage if available)
     - Created date
     - Last updated date

5. **Highlight Active Connectors**
   - If there are connectors "In Progress", show them prominently
   - Display current phase being worked on
   - Show next pending tasks
   - Indicate which API standards are being applied

6. **Provide Recommendations**
   - If multiple connectors are "In Progress", suggest focusing on one
   - If connectors are stuck in "Planning", suggest moving to implementation
   - If no active connectors, suggest starting a new connector
   - If connectors lack tests, suggest adding test coverage

## Checklist

- [ ] `api-conductor/tracks.md` read successfully
- [ ] Connector details parsed correctly
- [ ] Progress calculated for connectors with plans
- [ ] Connectors organized by status
- [ ] Summary statistics displayed
- [ ] Individual connector details shown (including platform and API type)
- [ ] Active connectors highlighted
- [ ] Recommendations provided (if applicable)
