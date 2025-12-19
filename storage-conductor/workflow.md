# Workflow Preferences

## Overview
This document defines team workflow preferences for building storage components including testing approach, migration strategy, commit strategy, code review process, branching strategy, and documentation standards. These preferences guide how storage development work is executed and ensure consistency across all storage components.

## Testing Approach

### Testing Strategy
- **Primary Approach**: Schema-first development with comprehensive testing
- **Description**: Design schema first, then implement data access layer. Write tests for repositories, queries, and migrations. Use test databases for integration testing.

### Test Types
- **Unit Tests**: 
  - Test repository methods in isolation
  - Use in-memory database or mocks
  - Test validation logic, error handling
  - Test transaction handling
  - Mock database operations when appropriate
  
- **Integration Tests**: 
  - Test against real test database
  - Test migrations end-to-end
  - Test complex queries
  - Test transaction behavior
  - Test data integrity constraints
  - Clean up test data after tests
  
- **Migration Tests**: 
  - Test forward migrations
  - Test backward migrations (rollbacks)
  - Test migration idempotency
  - Test migration on sample data
  - Verify schema after migration

### Test Structure
- **Naming Convention**: 
  - `describe` blocks: Group by feature/repository
  - Test names: `should [expected behavior] when [condition]`
  - Example: `should create user when valid data provided`
  
- **Organization**: 
  ```
  tests/
    unit/
      repositories/
        user.repository.test.ts
        order.repository.test.ts
      queries/
        user.queries.test.ts
    integration/
      schema.test.ts
      migrations.test.ts
      transactions.test.ts
    fixtures/
      users.json
      orders.json
  ```

### Test Coverage
- **Target Coverage**: 90%+
- **Critical Areas**: 
  - Repository methods: 90%+
  - Data validation: 100%
  - Transaction handling: 100%
  - Migration scripts: 100%
  - Data integrity constraints: 100%

## Migration Strategy

### Migration Approach
- **Primary Approach**: Versioned migrations (schema-first)
- **Description**: Create explicit migration files for each schema change. Migrations are versioned and tracked.

### Migration Workflow
1. **Design Schema**: Design schema changes
2. **Create Migration**: Generate migration file
3. **Test Migration**: Test migration on test database
4. **Review**: Code review migration file
5. **Apply to Staging**: Apply migration to staging environment
6. **Verify**: Verify schema and data integrity
7. **Apply to Production**: Apply migration to production with backup

### Migration Best Practices
- **Idempotent Migrations**: Migrations should be safe to run multiple times
- **Atomic Migrations**: Each migration should be a single transaction
- **Backward Compatibility**: Consider backward compatibility when possible
- **Data Migrations**: Separate schema migrations from data migrations
- **Test Migrations**: Always test migrations on test data first

### Migration Naming
- **Format**: `YYYYMMDDHHMMSS_description.sql` or `version_description`
- **Examples**:
  - `20240101120000_add_user_table.sql`
  - `20240101130000_add_user_indexes.sql`
  - `20240101140000_add_user_role_column.sql`

## Commit Strategy

### Commit Message Format
- **Format**: Conventional Commits
- **Example**: 
  ```
  feat(storage): add user repository with CRUD operations
  
  Implements UserRepository with findById, findByEmail, create, update, delete methods.
  Includes data validation and transaction support.
  
  Closes #123
  ```

### Commit Types
- `feat`: New storage component or feature
- `fix`: Bug fix in storage component
- `refactor`: Code refactoring
- `migration`: Schema migration
- `perf`: Performance optimization
- `test`: Test additions/changes
- `docs`: Documentation changes
- `chore`: Build process or dependency updates

### Commit Guidelines
- **Atomic Commits**: One logical change per commit (e.g., one migration, one repository method)
- **Commit Frequency**: Commit after each task completion
- **Commit Size**: Keep commits focused and reviewable
- **Include Tests**: Always include tests with implementation commits
- **Migration Commits**: Separate migration commits from code commits

## Code Review Process

### Review Requirements
- **Required For**: All PRs to main branch
- **Reviewers**: At least one team member familiar with storage patterns

### Review Checklist
- [ ] Code follows storage standards (`storage-standards.md`)
- [ ] Tests are included and passing
- [ ] Migrations are tested and documented
- [ ] Schema design follows best practices
- [ ] Indexes are appropriate for query patterns
- [ ] Data validation is implemented
- [ ] Transaction handling is correct
- [ ] Backup procedures are documented
- [ ] No hardcoded credentials or secrets
- [ ] Code follows style guidelines

### Review Guidelines
- **Tone**: Professional, constructive
- **Focus Areas**: 
  - Schema design and normalization
  - Migration safety and reversibility
  - Query performance and indexing
  - Data integrity and validation
  - Transaction handling correctness
  - Backup and recovery procedures
- **Response Time**: Within 24 hours

## Branching Strategy

### Branch Naming
- **Format**: `storage/<component>/<feature>` or `storage/<component>/migration/<description>`
- **Examples**:
  - `storage/users/add-repository`
  - `storage/orders/add-indexes`
  - `storage/users/migration/add-user-role`

### Branch Workflow
- **Main Branch**: `main` - Production-ready storage components
- **Development Branch**: `develop` - Integration branch for storage components (if using)
- **Feature Branches**: Created from `main` or `develop` for new storage components or features
- **Migration Branches**: Separate branches for migrations requiring special attention

### Merge Strategy
- **Method**: Squash and merge (preferred) or merge commits
- **When to Use**: 
  - Squash: For feature branches with multiple commits
  - Merge: For branches that should preserve commit history

## Documentation Standards

### Code Documentation
- **Comments**: 
  - Explain "why" not "what"
  - Document complex queries and optimization decisions
  - Document schema design decisions
  - Document migration rationale
  
- **Function Documentation**: 
  ```typescript
  /**
   * Finds a user by email address
   * 
   * Uses index on email column for optimal performance.
   * Returns null if user not found.
   * 
   * @param email - User email address
   * @returns User entity or null if not found
   * @throws {DatabaseError} When database query fails
   */
  async findByEmail(email: string): Promise<User | null> {
    // Implementation
  }
  ```

### README Files
- **Required Sections**: 
  - Overview and purpose
  - Storage type and backend
  - Schema documentation
  - Installation and setup
  - Usage examples
  - Migration guide
  - Backup and recovery procedures
  - Performance characteristics
  - Contributing
  
- **Update Frequency**: Update with every schema change or significant feature

### Schema Documentation
- **Format**: Markdown or ER diagrams
- **Content**: 
  - Entity descriptions
  - Relationship diagrams
  - Index documentation
  - Constraint documentation
  - Query patterns

## Development Workflow

### Daily Workflow
1. **Check Status**: Run `/storage-conductor-status` to see active storage components
2. **Select Component**: Choose storage component to work on
3. **Review Plan**: Review current phase and tasks
4. **Design Schema**: Design schema changes (if needed)
5. **Create Migration**: Create migration file (if schema change)
6. **Implement**: Implement repository methods or queries
7. **Test**: Write and run tests
8. **Commit**: Commit changes with descriptive message
9. **Verify**: Verify migrations and data integrity

### Storage Component Development Process
1. **Setup**: Run `/storage-conductor-setup` (once per project)
2. **Plan**: Run `/storage-conductor-new-storage` to create spec and plan
3. **Review**: Review spec and plan, adjust if needed
4. **Schema Design**: Design schema and create initial migration
5. **Implement**: Run `/storage-conductor-implement` to build storage component
6. **Test**: Verify with test database and integration tests
7. **Document**: Complete README and schema documentation
8. **Review**: Create PR and get code review
9. **Deploy**: Merge and apply migrations to staging, then production

### Migration Process
1. **Design**: Design schema changes
2. **Create**: Create migration file following naming convention
3. **Test**: Test migration on test database with sample data
4. **Review**: Code review migration file
5. **Staging**: Apply migration to staging environment
6. **Verify**: Verify schema and data integrity in staging
7. **Backup**: Create backup of production database
8. **Production**: Apply migration to production
9. **Verify**: Verify schema and data integrity in production

### Bug Fix Process
1. **Identify**: Identify issue in storage component
2. **Reproduce**: Reproduce with tests
3. **Fix**: Implement fix (may require migration)
4. **Test**: Verify fix with tests
5. **Document**: Update documentation if needed
6. **Review**: Create PR and get review
7. **Deploy**: Merge fix and apply migrations if needed

## Code Style

### Style Guide
- **Guide**: Follow language-specific style guide
- **Location**: `storage-conductor/code_styleguides/`

### Formatting
- **Tool**: [e.g., Prettier, Black, gofmt]
- **Configuration**: [Reference to config]
- **Auto-format**: Format on save

### Linting
- **Tool**: [e.g., ESLint, Pylint, golangci-lint]
- **Configuration**: [Reference to config]
- **Rules**: Enforce storage standards, SQL best practices

## Communication

### Team Communication
- **Channels**: [Communication channels used]
- **Standups**: [Standup format and frequency]
- **Updates**: Share storage component progress, migration plans, performance issues

## Notes

- Always test migrations on test data before production
- Backup database before applying migrations to production
- Document schema design decisions - they're important for future maintenance
- Monitor query performance proactively - add indexes before they're needed
- Keep migrations small and focused - easier to review and rollback
- Test backup and recovery procedures regularly

