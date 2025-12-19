# New Storage

## Overview
Start a new storage component track. This command generates a unique storage component ID, creates the storage component directory structure, and helps you create detailed specifications and an actionable plan for building a storage solution (database schema, object storage, cache system, etc.). A storage component represents a high-level unit of work that will be broken down into phases, tasks, and sub-tasks.

## Steps

1. **Determine Storage Component Details**
   - Ask the user for:
     - Storage type (database, object storage, cache, file system, hybrid)
     - Storage backend (PostgreSQL, MySQL, MongoDB, Redis, S3, GCS, Azure Blob, etc.)
     - Primary use cases (what data needs to be stored and retrieved)
     - Performance requirements (throughput, latency, scalability)
   - Get a brief description of what needs to be built
   - If no description provided, prompt for one

2. **Generate Unique Storage Component ID**
   - Create a storage component ID using format: `storage-YYYYMMDD-HHMMSS` or sequential number
   - Ensure the ID is unique by checking existing storage components in `storage-conductor/tracks.md`
   - Alternative format: `storage-<type>-<sequential>` (e.g., `storage-database-001`)

3. **Create Storage Component Directory Structure**
   - Create `storage-conductor/storages/<storage_id>/` directory
   - Create `storage-conductor/storages/<storage_id>/spec.md` file
   - Create `storage-conductor/storages/<storage_id>/plan.md` file
   - Create `storage-conductor/storages/<storage_id>/metadata.json` file

4. **Generate Specification (spec.md)**
   - Analyze the storage component details and project context from:
     - `storage-conductor/product.md`
     - `storage-conductor/tech-stack.md`
     - `storage-conductor/storage-standards.md`
     - `storage-conductor/storage-template.md`
   - Create a detailed specification including:
     - **Title**: Clear, descriptive title (e.g., "User Database Schema")
     - **Type**: Storage Component
     - **Storage Type**: Database, object storage, cache, file system, etc.
     - **Storage Backend**: PostgreSQL, MongoDB, S3, Redis, etc.
     - **Description**: What storage component are we building and why?
     - **Data Model Requirements**: Entities, relationships, data structures
     - **Schema Design**: Schema requirements, constraints, indexes
     - **Migration Requirements**: Initial schema, migration strategy
     - **Backup and Recovery**: Backup strategies, recovery procedures
     - **Performance Requirements**: Throughput, latency, scalability needs
     - **Query Patterns**: Common query patterns and access patterns
     - **Data Integrity**: Constraints, validation requirements
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
   - Standard phases for storage components:
     - **Phase 1: Schema Design**
       - Define data models and entities
       - Design relationships and constraints
       - Plan indexes and query optimization
       - Document schema design decisions
     - **Phase 2: Migration Setup**
       - Set up migration tooling
       - Create initial migration structure
       - Define migration workflow
       - Set up development and test environments
     - **Phase 3: Core Storage Implementation**
       - Implement schema/migrations
       - Create CRUD operations
       - Implement core queries
       - Set up connection handling
     - **Phase 4: Indexing & Optimization**
       - Create indexes based on query patterns
       - Optimize slow queries
       - Implement query caching if applicable
       - Performance testing and tuning
     - **Phase 5: Data Integrity**
       - Implement constraints and validations
       - Add transaction handling
       - Implement data validation logic
       - Test data integrity scenarios
     - **Phase 6: Backup & Recovery**
       - Implement backup strategies
       - Create recovery procedures
       - Test backup and restore processes
       - Document backup/recovery procedures
     - **Phase 7: Testing**
       - Unit tests for storage operations
       - Integration tests with test database
       - Data integrity tests
       - Performance tests
       - Backup/recovery tests
     - **Phase 8: Documentation**
       - Schema documentation
       - Migration guides
       - Usage examples and patterns
       - Performance characteristics
       - Backup and recovery documentation
   - Consider the workflow preferences from `storage-conductor/workflow.md` (e.g., schema-first, TDD structure)
   - Reference `storage-conductor/storage-template.md` for code patterns

6. **Create Metadata (metadata.json)**
   - Generate JSON file with:
     ```json
     {
       "id": "<storage_id>",
       "title": "<storage_title>",
       "type": "Storage Component",
       "storage_type": "<database|object_storage|cache|file_system|hybrid>",
       "backend": "<PostgreSQL|MySQL|MongoDB|Redis|S3|GCS|etc>",
       "status": "Planning",
       "created": "<ISO_timestamp>",
       "updated": "<ISO_timestamp>"
     }
     ```

7. **Update Storage Components Tracking File**
   - Add new entry to `storage-conductor/tracks.md` with:
     - Storage ID
     - Title
     - Storage Type
     - Backend
     - Type: "Storage Component"
     - Status: "Planning"
     - Created date
     - Updated date

## Checklist

- [ ] Storage component ID generated and verified unique
- [ ] Storage component directory created at `storage-conductor/storages/<storage_id>/`
- [ ] `spec.md` created with comprehensive requirements including storage details
- [ ] `plan.md` created with phases, tasks, and sub-tasks following storage template
- [ ] `metadata.json` created with storage component information
- [ ] `storage-conductor/tracks.md` updated with new storage component entry
- [ ] Specification aligns with storage standards and storage template
- [ ] Plan follows workflow preferences and includes storage-specific phases

