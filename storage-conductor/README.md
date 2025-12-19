# Storage Conductor for Cursor

**Build reliable storage solutions with confidence.**

Storage Conductor is a specialized Context-Driven Development system for building storage solutions (databases, object storage, cache systems, data persistence layers). It extends the Conductor philosophy with storage-specific patterns, standards, and workflows to ensure every storage component is reliable, performant, and maintainable.

## Philosophy

Storage Conductor brings structured planning and implementation workflows specifically tailored for storage development. By treating storage patterns as managed artifacts alongside your code, you ensure data integrity, performance, and best practices across all storage components.

## Features

* **Storage-Focused Planning**: Create specs and plans optimized for storage component development
* **Schema Design Patterns**: Built-in patterns for schema design, relationships, and constraints
* **Migration Management**: Standardized migration workflow with forward and backward support
* **Data Integrity**: Consistent patterns for data validation, constraints, and transactions
* **Backup & Recovery**: Infrastructure and procedures for backups and recovery
* **Storage Standards**: Enforced standards for data consistency, indexing, query optimization, and security
* **Storage Templates**: Code templates and structure patterns for quick storage component development

## Installation

1. Copy the `.cursor/commands/storage-conductor/` directory to your project root
2. Copy the `storage-conductor/` directory to your project root (or let the `/storage-conductor-setup` command create it)

That's it! The commands will be available in Cursor when you type `/` in the chat.

## Usage

Storage Conductor manages the entire lifecycle of your storage component development.

### 1. Set Up the Project (Run Once)

When you run `/storage-conductor-setup`, Storage Conductor helps you define the core components for storage development:

* **Product**: Define storage product context (target users, goals, features)
* **Tech Stack**: Configure storage technologies (databases, ORMs, migration tools)
* **Workflow**: Set storage development workflows (schema-first, migration strategy, testing)
* **Storage Standards**: Define data consistency, backup, indexing, migration, and security standards
* **Storage Template**: Set up standard storage component structure and code patterns

**Generated Artifacts:**

* `storage-conductor/product.md`
* `storage-conductor/tech-stack.md`
* `storage-conductor/workflow.md`
* `storage-conductor/storage-standards.md`
* `storage-conductor/storage-template.md`
* `storage-conductor/tracks.md`

```
/storage-conductor-setup
```

### 2. Start a New Storage Component

When you're ready to build a new storage solution, run `/storage-conductor-new-storage`. This initializes a **storage component** — a high-level unit of work for a specific storage solution. Storage Conductor helps you generate:

* **Specs**: Detailed requirements including storage type, backend, data models, schema design, migration needs, backup requirements, and performance requirements
* **Plan**: An actionable plan with storage-specific phases:
  - Schema design
  - Migration setup
  - Core storage implementation
  - Indexing & optimization
  - Data integrity
  - Backup & recovery
  - Testing (unit, integration, data integrity)
  - Documentation

**Generated Artifacts:**

* `storage-conductor/storages/<storage_id>/spec.md`
* `storage-conductor/storages/<storage_id>/plan.md`
* `storage-conductor/storages/<storage_id>/metadata.json`

```
/storage-conductor-new-storage
# OR with a description
/storage-conductor-new-storage "Build user database schema with PostgreSQL"
```

### 3. Implement the Storage Component

Once you approve the plan, run `/storage-conductor-implement`. Your coding agent then works through the `plan.md` file, following storage standards and checking off tasks as it completes them.

**Updated Artifacts:**

* `storage-conductor/tracks.md` (Status updates)
* `storage-conductor/storages/<storage_id>/plan.md` (Status updates)

```
/storage-conductor-implement
```

Storage Conductor will:

1. Select the next pending task
2. Follow the defined workflow (e.g., schema-first: Design Schema → Create Migration → Implement)
3. Apply storage standards (data consistency, backups, indexing, migrations)
4. Implement migrations and test them
5. Update the status in the plan as it progresses
6. **Verify Progress**: Guide you through manual verification at the end of each phase

During implementation, you can also:

* **Check status**: Get a high-level overview of your storage components' progress
```
/storage-conductor-status
```
* **Revert work**: Undo a storage component or specific task if needed
```
/storage-conductor-revert
```

## Commands Reference

| Command | Description | Artifacts |
|---------|-------------|-----------|
| `/storage-conductor-setup` | Scaffolds the project and sets up the Storage Conductor environment. Run this once per project. | `storage-conductor/product.md`<br>`storage-conductor/tech-stack.md`<br>`storage-conductor/workflow.md`<br>`storage-conductor/storage-standards.md`<br>`storage-conductor/storage-template.md`<br>`storage-conductor/tracks.md` |
| `/storage-conductor-new-storage` | Starts a new storage component track. Generates spec.md and plan.md with storage-specific details. | `storage-conductor/storages/<id>/spec.md`<br>`storage-conductor/storages/<id>/plan.md`<br>`storage-conductor/tracks.md` |
| `/storage-conductor-implement` | Executes the tasks defined in the current storage component's plan, following storage standards. | `storage-conductor/tracks.md`<br>`storage-conductor/storages/<id>/plan.md` |
| `/storage-conductor-status` | Displays the current progress of all storage components. | Reads `storage-conductor/tracks.md` |
| `/storage-conductor-revert` | Reverts a storage component, phase, or task by analyzing git history. | Reverts git history |

## Directory Structure

```
.cursor/
  commands/
    storage-conductor/
      setup.md
      new-storage.md
      implement.md
      status.md
      revert.md
storage-conductor/
  product.md
  tech-stack.md
  workflow.md
  storage-standards.md
  storage-template.md
  tracks.md
  code_styleguides/
    (directory for style guide files)
  storages/
    <storage_id>/
      spec.md
      plan.md
      metadata.json
```

## Key Differences from Base Conductor

### Naming
- Uses `storage-conductor` instead of `conductor`
- Uses `storages` instead of `tracks`
- Commands prefixed with `/storage-conductor-`

### Specialization
- **Storage-Focused**: All workflows and templates optimized for storage development
- **Schema Design**: Built-in patterns for schema design and relationships
- **Migration Management**: Standardized migration workflow with tracking
- **Data Integrity**: Consistent patterns for constraints, validation, and transactions
- **Backup & Recovery**: Infrastructure for backup and recovery procedures

### Standards
- **Storage Standards File**: Dedicated `storage-standards.md` with storage development standards
- **Storage Template**: `storage-template.md` with code patterns and structure
- **Storage-Specific Phases**: Plan phases tailored for storage development (schema, migrations, indexing, etc.)

### Testing
- **Data Integrity Testing**: Strong focus on data integrity and constraint testing
- **Migration Testing**: Workflows for testing migrations forward and backward
- **Performance Testing**: Support for query performance verification

## Storage Standards

All storage components must follow the standards defined in `storage-standards.md`:

- **Data Consistency**: ACID transactions, eventual consistency patterns
- **Backup & Recovery**: Automated backups, recovery procedures
- **Indexing**: Index design principles, query optimization
- **Migration Management**: Forward and backward migrations, migration tracking
- **Data Validation**: Database and application-level validation
- **Transaction Handling**: Transaction patterns, isolation levels
- **Performance**: Query optimization, connection pooling
- **Security**: Credential storage, access control, data encryption

## Storage Template

Use `storage-template.md` as a starting point for all new storage components. It includes:

- Standard directory structure
- Code templates for common patterns:
  - Schema definitions (SQL, Prisma, Drizzle)
  - Migration file structure
  - Repository pattern implementations
  - CRUD operation templates
  - Transaction handling examples
  - Query optimization patterns
  - Backup utilities
  - Data validation patterns
  - Test templates

## Storage Types Supported

Storage Conductor supports various storage types:

- **Relational Databases**: PostgreSQL, MySQL, SQLite, etc.
- **NoSQL Databases**: MongoDB, DynamoDB, etc.
- **Object Storage**: S3, GCS, Azure Blob Storage, etc.
- **Cache Systems**: Redis, Memcached, etc.
- **File Systems**: Local file storage, network file systems
- **Hybrid**: Combinations of the above

## Best Practices

1. **Schema-First Development**: Design schema before implementation when possible
2. **Migration Safety**: Always test migrations on test data before production
3. **Data Integrity**: Prioritize data integrity over convenience
4. **Performance by Design**: Design for performance from the start
5. **Backup Regularly**: Automated backups are mandatory
6. **Document Everything**: Document schema decisions, migrations, and optimizations
7. **Test Thoroughly**: Test migrations, data integrity, and recovery procedures

## Examples

### Example: User Database Schema

Create a PostgreSQL schema for user management:

```
/storage-conductor-new-storage "User database schema with authentication support"
```

Storage Conductor will generate a plan including:
- User table schema with email, password hash, created_at
- Indexes on email and created_at
- Migration files
- Repository implementation
- Validation and constraints
- Backup procedures

### Example: Object Storage for Files

Create an S3-based object storage component:

```
/storage-conductor-new-storage "File storage using S3"
```

Storage Conductor will generate a plan including:
- S3 client setup
- File upload/download operations
- Versioning and lifecycle policies
- Backup strategies
- Access control

## Differences from API Conductor

While both follow similar patterns, Storage Conductor focuses on:

- **Data Persistence**: Instead of API calls
- **Schema Evolution**: Instead of API versioning
- **Query Optimization**: Instead of rate limiting
- **Backup & Recovery**: Instead of error retry logic
- **Data Integrity**: Instead of request/response validation

## Contributing

When contributing to storage components:

1. Follow storage standards from `storage-standards.md`
2. Use templates from `storage-template.md`
3. Test migrations thoroughly
4. Document schema decisions
5. Include backup/recovery procedures
6. Optimize queries and indexes

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](../LICENSE) for details.

## Acknowledgments

* Inspired by [Conductor](https://github.com/gemini-cli-extensions/conductor) by gemini-cli-extensions
* Built for [Cursor IDE](https://cursor.sh/)

