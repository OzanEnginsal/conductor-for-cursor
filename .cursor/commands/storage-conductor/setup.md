# Setup

## Overview
Initialize the Storage Conductor environment and set up project context for building storage solutions. This command scaffolds the project structure and guides you through defining core components: product context, tech stack, workflow preferences, storage standards, and storage templates. Run this once per project to establish the foundation for storage development.

## Steps

1. **Create Storage Conductor Directory Structure**
   - Create `storage-conductor/` directory at project root if it doesn't exist
   - Create `storage-conductor/code_styleguides/` directory for style guide files
   - Create `storage-conductor/storages/` directory for storage component artifacts

2. **Define Product Context**
   - Guide the user through defining:
     - Target users (developers building storage solutions)
     - Product goals (reliable, performant, scalable storage components)
     - High-level features (data persistence, schema management, migrations, backups, query optimization)
     - Key user journeys (designing schemas, implementing storage, managing migrations, ensuring data integrity)
   - Generate `storage-conductor/product.md` with the provided information

3. **Define Tech Stack**
   - Guide the user through defining:
     - Programming languages and versions (preferred for storage development)
     - Database systems (PostgreSQL, MySQL, MongoDB, Redis, etc.)
     - ORMs and query builders (Prisma, SQLAlchemy, TypeORM, Sequelize, etc.)
     - Migration tools (Alembic, Flyway, Liquibase, Prisma Migrate, etc.)
     - Object storage solutions (S3, GCS, Azure Blob, etc.)
     - Cache systems (Redis, Memcached, etc.)
     - Schema validation tools (Zod, Pydantic, JSON Schema, etc.)
     - Database testing tools and fixtures
     - Development tools and dependencies
     - Architecture patterns for storage layers
     - Deployment and infrastructure preferences
   - Generate `storage-conductor/tech-stack.md` with the provided information

4. **Define Workflow Preferences**
   - Guide the user through defining:
     - Testing approach (TDD, BDD, etc.) with emphasis on data integrity testing
     - Schema-first vs code-first development approach
     - Migration strategy (versioned migrations, automatic migrations)
     - Backup and recovery procedures
     - Data validation approach
     - Commit strategy and conventions
     - Code review process
     - Branching strategy
     - Storage documentation standards (schema docs, migration guides, ER diagrams)
     - Version management for storage schemas
   - Generate `storage-conductor/workflow.md` with the provided information

5. **Define Storage Standards**
   - Guide the user through defining:
     - Data consistency patterns (ACID transactions, eventual consistency)
     - Backup and recovery strategies (automated backups, point-in-time recovery)
     - Indexing and query optimization guidelines
     - Migration management practices (forward and backward migrations)
     - Data validation and integrity constraints
     - Transaction handling patterns
     - Performance optimization standards (query optimization, connection pooling)
     - Schema versioning approach
     - Security best practices (encryption, access control, credential management)
   - Generate `storage-conductor/storage-standards.md` with the provided information

6. **Create Storage Template**
   - Generate `storage-conductor/storage-template.md` with:
     - Standard storage component directory structure
     - Code templates for common patterns:
       - Schema definition patterns
       - Migration file structure
       - CRUD operation templates
       - Query optimization examples
       - Backup and recovery code patterns
       - Data integrity test templates
       - Transaction handling examples
       - Index creation patterns
       - Data validation patterns

7. **Initialize Tracking File**
   - Create `storage-conductor/tracks.md` with header and structure for tracking all storage components
   - Include columns for: Storage ID, Title, Storage Type, Backend, Status, Created Date, Updated Date

## Checklist

- [ ] `storage-conductor/` directory structure created
- [ ] `storage-conductor/product.md` generated with storage product context
- [ ] `storage-conductor/tech-stack.md` generated with storage-specific technical preferences
- [ ] `storage-conductor/workflow.md` generated with storage development workflow preferences
- [ ] `storage-conductor/storage-standards.md` generated with storage standards
- [ ] `storage-conductor/storage-template.md` generated with storage component structure templates
- [ ] `storage-conductor/tracks.md` initialized with tracking structure
- [ ] All context files are properly formatted and complete

