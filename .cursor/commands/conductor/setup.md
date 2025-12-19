# Setup

## Overview
Initialize the Conductor environment and set up project context. This command scaffolds the project structure and guides you through defining core components: product context, product guidelines, tech stack, and workflow preferences. Run this once per project to establish the foundation for Context-Driven Development.

## Steps

1. **Create Conductor Directory Structure**
   - Create `conductor/` directory at project root if it doesn't exist
   - Create `conductor/code_styleguides/` directory for style guide files
   - Create `conductor/tracks/` directory for track artifacts

2. **Define Product Context**
   - Guide the user through defining:
     - Target users and personas
     - Product goals and vision
     - High-level features and capabilities
     - Key user journeys
   - Generate `conductor/product.md` with the provided information

3. **Define Product Guidelines**
   - Guide the user through defining:
     - Prose style and tone
     - Brand messaging guidelines
     - Visual identity standards
     - Content standards
   - Generate `conductor/product-guidelines.md` with the provided information

4. **Define Tech Stack**
   - Guide the user through defining:
     - Programming languages and versions
     - Frameworks and libraries
     - Database and data storage
     - Development tools and dependencies
     - Architecture patterns
     - Deployment and infrastructure preferences
   - Generate `conductor/tech-stack.md` with the provided information

5. **Define Workflow Preferences**
   - Guide the user through defining:
     - Testing approach (TDD, BDD, etc.)
     - Commit strategy and conventions
     - Code review process
     - Branching strategy
     - Documentation standards
   - Generate `conductor/workflow.md` with the provided information

6. **Initialize Tracking File**
   - Create `conductor/tracks.md` with header and structure for tracking all tracks
   - Include columns for: Track ID, Title, Type (Feature/Bug), Status, Created Date, Updated Date

## Checklist

- [ ] `conductor/` directory structure created
- [ ] `conductor/product.md` generated with product context
- [ ] `conductor/product-guidelines.md` generated with guidelines
- [ ] `conductor/tech-stack.md` generated with technical preferences
- [ ] `conductor/workflow.md` generated with workflow preferences
- [ ] `conductor/tracks.md` initialized with tracking structure
- [ ] All context files are properly formatted and complete
