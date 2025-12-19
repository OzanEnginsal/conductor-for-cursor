# New Track

## Overview
Start a new feature or bug fix track. This command generates a unique track ID, creates the track directory structure, and helps you create detailed specifications and an actionable plan. A track represents a high-level unit of work that will be broken down into phases, tasks, and sub-tasks.

## Steps

1. **Determine Track Type and Description**
   - Ask the user if this is a Feature or Bug Fix
   - Get a brief description of what needs to be built or fixed
   - If no description provided, prompt for one

2. **Generate Unique Track ID**
   - Create a track ID using format: `track-YYYYMMDD-HHMMSS` or sequential number
   - Ensure the ID is unique by checking existing tracks in `conductor/tracks.md`

3. **Create Track Directory Structure**
   - Create `conductor/tracks/<track_id>/` directory
   - Create `conductor/tracks/<track_id>/spec.md` file
   - Create `conductor/tracks/<track_id>/plan.md` file
   - Create `conductor/tracks/<track_id>/metadata.json` file

4. **Generate Specification (spec.md)**
   - Analyze the track description and project context from:
     - `conductor/product.md`
     - `conductor/product-guidelines.md`
     - `conductor/tech-stack.md`
   - Create a detailed specification including:
     - **Title**: Clear, descriptive title
     - **Type**: Feature or Bug Fix
     - **Description**: What are we building/fixing and why?
     - **Requirements**: Detailed functional and non-functional requirements
     - **Acceptance Criteria**: How do we know it's done?
     - **Dependencies**: What other work or systems does this depend on?
     - **User Impact**: Who is affected and how?

5. **Generate Plan (plan.md)**
   - Break down the specification into actionable phases
   - Each phase should contain:
     - **Phase Name**: Descriptive name
     - **Tasks**: List of tasks with checkboxes `- [ ]`
     - **Sub-tasks**: Optional sub-tasks under each task
   - Structure phases logically (e.g., Setup → Implementation → Testing → Documentation)
   - Consider the workflow preferences from `conductor/workflow.md` (e.g., TDD structure)

6. **Create Metadata (metadata.json)**
   - Generate JSON file with:
     ```json
     {
       "id": "<track_id>",
       "title": "<track_title>",
       "type": "Feature|Bug",
       "status": "Planning",
       "created": "<ISO_timestamp>",
       "updated": "<ISO_timestamp>"
     }
     ```

7. **Update Tracks Tracking File**
   - Add new entry to `conductor/tracks.md` with:
     - Track ID
     - Title
     - Type
     - Status: "Planning"
     - Created date
     - Updated date

## Checklist

- [ ] Track ID generated and verified unique
- [ ] Track directory created at `conductor/tracks/<track_id>/`
- [ ] `spec.md` created with comprehensive requirements
- [ ] `plan.md` created with phases, tasks, and sub-tasks
- [ ] `metadata.json` created with track information
- [ ] `conductor/tracks.md` updated with new track entry
- [ ] Specification aligns with product context and guidelines
- [ ] Plan follows workflow preferences
