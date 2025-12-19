# Storage Standards

## Overview
This document defines standards and patterns for building storage solutions. These standards ensure consistency, reliability, data integrity, and maintainability across all storage components. Every storage component must follow these standards.

## Data Consistency Patterns

### ACID Transactions
- **Use Case**: When data integrity is critical and operations must be atomic
- **Implementation Requirements**:
  - Use database transactions for multi-step operations
  - Ensure atomicity: all steps succeed or all fail
  - Maintain isolation levels appropriate for the use case
  - Ensure consistency: data remains valid after transaction
  - Ensure durability: committed changes persist

- **Code Pattern**:
  ```typescript
  async function transferFunds(fromId: string, toId: string, amount: number) {
    await db.transaction(async (tx) => {
      await tx.update(fromAccount).set({ balance: sql`balance - ${amount}` });
      await tx.update(toAccount).set({ balance: sql`balance + ${amount}` });
      await tx.insert(transactions).values({ fromId, toId, amount });
    });
  }
  ```

### Eventual Consistency
- **Use Case**: When high availability and partition tolerance are prioritized over immediate consistency
- **Implementation Requirements**:
  - Design schemas to tolerate eventual consistency
  - Implement conflict resolution strategies
  - Use version vectors or timestamps for conflict detection
  - Handle stale data gracefully
  - Document consistency guarantees

### Consistency Levels
- **Strong Consistency**: All reads see latest write (ACID)
- **Eventual Consistency**: System will become consistent over time
- **Read Your Writes**: User sees their own writes immediately
- **Monotonic Reads**: User never sees older data after seeing newer data

## Backup and Recovery Strategies

### Automated Backups
- **Frequency**: Based on data criticality and change rate
  - Critical data: Hourly or continuous backups
  - Important data: Daily backups
  - Standard data: Weekly backups
- **Retention Policy**: 
  - Keep daily backups for 30 days
  - Keep weekly backups for 3 months
  - Keep monthly backups for 1 year

### Backup Types
- **Full Backup**: Complete database/storage backup
- **Incremental Backup**: Only changed data since last backup
- **Differential Backup**: All changes since last full backup
- **Point-in-Time Recovery**: Continuous transaction log backups

### Backup Implementation
```typescript
class BackupManager {
  async createBackup(storageId: string): Promise<BackupResult> {
    // Create snapshot or export
    const timestamp = new Date().toISOString();
    const backupPath = `backups/${storageId}/${timestamp}`;
    
    await db.backup({
      destination: backupPath,
      type: 'full',
      compression: true,
    });
    
    // Verify backup integrity
    await this.verifyBackup(backupPath);
    
    return { path: backupPath, timestamp };
  }
  
  async restoreBackup(backupPath: string): Promise<void> {
    // Verify backup exists and is valid
    await this.verifyBackup(backupPath);
    
    // Restore from backup
    await db.restore({ source: backupPath });
    
    // Verify data integrity after restore
    await this.verifyDataIntegrity();
  }
}
```

### Recovery Procedures
- **Document Recovery Steps**: Clear steps for manual recovery
- **Test Recovery**: Regularly test backup restoration
- **Recovery Time Objective (RTO)**: Target time to restore service
- **Recovery Point Objective (RPO)**: Maximum acceptable data loss

## Indexing and Query Optimization

### Index Design Principles
- **Index on Foreign Keys**: Always index foreign key columns
- **Index on Query Filters**: Index columns used in WHERE clauses
- **Index on Sort Columns**: Index columns used in ORDER BY
- **Composite Indexes**: Create for multi-column queries
- **Avoid Over-Indexing**: Too many indexes slow down writes

### Index Types
- **B-Tree Index**: Default for most queries, supports range queries
- **Hash Index**: Fast equality lookups, no range support
- **GIN Index**: For array and full-text search (PostgreSQL)
- **Bitmap Index**: For low-cardinality columns

### Query Optimization Guidelines
- **Use EXPLAIN**: Analyze query plans before optimization
- **Avoid N+1 Queries**: Use joins or batch loading
- **Limit Result Sets**: Use LIMIT and pagination
- **Select Only Needed Columns**: Avoid SELECT *
- **Use Prepared Statements**: For repeated queries
- **Monitor Slow Queries**: Set up query performance monitoring

### Example:
```sql
-- Good: Indexed query with limit
CREATE INDEX idx_user_email ON users(email);
SELECT * FROM users WHERE email = 'user@example.com' LIMIT 1;

-- Good: Composite index for multi-column query
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);
SELECT * FROM orders WHERE user_id = 123 AND created_at > '2024-01-01';

-- Bad: Full table scan
SELECT * FROM users WHERE UPPER(email) = 'USER@EXAMPLE.COM';
```

## Migration Management Practices

### Migration File Structure
- **Naming Convention**: `YYYYMMDDHHMMSS_description.sql` or `version_description`
- **Forward Migration**: Schema changes to apply
- **Backward Migration**: Rollback changes (if supported)
- **Version Tracking**: Track applied migrations in database

### Migration Best Practices
- **Idempotent Migrations**: Migrations should be safe to run multiple times
- **Atomic Migrations**: Each migration should be a single transaction
- **Backward Compatibility**: Consider backward compatibility when possible
- **Data Migrations**: Separate schema migrations from data migrations
- **Test Migrations**: Test migrations on staging before production

### Migration Pattern:
```sql
-- Migration: 20240101120000_add_user_indexes.sql
BEGIN;

-- Forward migration
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_users_email ON users(email);
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_users_created_at ON users(created_at);

-- Track migration
INSERT INTO schema_migrations (version, applied_at)
VALUES ('20240101120000', NOW())
ON CONFLICT (version) DO NOTHING;

COMMIT;

-- Rollback migration (if needed)
-- BEGIN;
-- DROP INDEX IF EXISTS idx_users_email;
-- DROP INDEX IF EXISTS idx_users_created_at;
-- DELETE FROM schema_migrations WHERE version = '20240101120000';
-- COMMIT;
```

### Migration Tools
- **Alembic**: Python/SQLAlchemy migrations
- **Flyway**: Java/Database migrations
- **Liquibase**: Database migrations
- **Prisma Migrate**: TypeScript/Prisma migrations
- **ActiveRecord Migrations**: Ruby on Rails

## Data Validation and Integrity Constraints

### Database-Level Constraints
- **Primary Keys**: Ensure uniqueness and non-null
- **Foreign Keys**: Maintain referential integrity
- **Unique Constraints**: Prevent duplicate values
- **Check Constraints**: Validate data ranges and formats
- **NOT NULL Constraints**: Ensure required fields

### Application-Level Validation
- **Schema Validation**: Use Zod, Pydantic, or similar
- **Type Validation**: Validate types before storage
- **Business Logic Validation**: Validate business rules
- **Input Sanitization**: Sanitize user inputs

### Validation Pattern:
```typescript
import { z } from 'zod';

const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  age: z.number().int().min(0).max(150),
  createdAt: z.date(),
});

// Validate before database operation
async function createUser(data: unknown): Promise<User> {
  const validated = UserSchema.parse(data);
  
  // Additional database constraints will catch any remaining issues
  return await db.insert(users).values(validated).returning();
}
```

### Data Integrity Checks
- **Referential Integrity**: Foreign keys must reference valid records
- **Data Consistency**: Related data must be consistent
- **Constraint Validation**: All constraints must be satisfied
- **Custom Integrity Checks**: Periodic validation of business rules

## Transaction Handling Patterns

### Transaction Isolation Levels
- **Read Uncommitted**: Lowest isolation, may see uncommitted changes
- **Read Committed**: Default in most databases, see only committed changes
- **Repeatable Read**: Consistent reads within transaction
- **Serializable**: Highest isolation, prevent all anomalies

### Transaction Best Practices
- **Keep Transactions Short**: Minimize lock time
- **Avoid Long-Running Transactions**: Can cause deadlocks
- **Handle Deadlocks**: Implement retry logic with exponential backoff
- **Use Appropriate Isolation Level**: Balance consistency vs performance
- **Explicit Transactions**: Use explicit transactions for multi-step operations

### Transaction Pattern:
```typescript
async function processOrder(orderData: OrderData): Promise<Order> {
  return await db.transaction(async (tx) => {
    // Create order
    const [order] = await tx.insert(orders)
      .values(orderData)
      .returning();
    
    // Update inventory
    for (const item of orderData.items) {
      await tx.update(inventory)
        .set({ quantity: sql`quantity - ${item.quantity}` })
        .where(eq(inventory.productId, item.productId));
    }
    
    // Create payment record
    await tx.insert(payments).values({
      orderId: order.id,
      amount: orderData.total,
    });
    
    return order;
  });
}
```

### Deadlock Handling
- **Retry Logic**: Retry failed transactions with exponential backoff
- **Timeout**: Set transaction timeouts
- **Lock Ordering**: Acquire locks in consistent order
- **Deadlock Detection**: Monitor and log deadlocks

## Performance Optimization Standards

### Query Optimization
- **Use Indexes**: Ensure queries use appropriate indexes
- **Avoid Full Table Scans**: Use WHERE clauses with indexed columns
- **Limit Result Sets**: Use pagination for large result sets
- **Use Connection Pooling**: Reuse database connections
- **Monitor Query Performance**: Track slow queries

### Connection Management
- **Connection Pooling**: Use connection pools for efficiency
- **Connection Limits**: Set appropriate connection limits
- **Connection Timeout**: Configure connection timeouts
- **Connection Health Checks**: Monitor connection health

### Caching Strategies
- **Query Result Caching**: Cache frequently accessed query results
- **Cache Invalidation**: Implement proper cache invalidation
- **Cache Layers**: Use multiple cache layers (L1, L2)
- **Cache Warming**: Pre-populate cache for critical data

### Example Connection Pool:
```typescript
import { Pool } from 'pg';

const pool = new Pool({
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT || '5432'),
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  max: 20, // Maximum connections
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});

async function query(sql: string, params: any[]) {
  const client = await pool.connect();
  try {
    return await client.query(sql, params);
  } finally {
    client.release();
  }
}
```

## Schema Versioning Approach

### Version Management
- **Schema Version Table**: Track schema version in database
- **Version Numbering**: Use semantic versioning or timestamp-based
- **Migration Tracking**: Track which migrations have been applied
- **Rollback Support**: Maintain ability to rollback to previous versions

### Version Detection
- **On Startup**: Check schema version on application startup
- **Migration Check**: Verify migrations are up to date
- **Version Mismatch**: Handle version mismatches gracefully

## Security Best Practices

### Credential Storage
- **Never Commit**: Never commit database credentials to version control
- **Environment Variables**: Use environment variables for credentials
- **Secure Vaults**: Use secure vaults (AWS Secrets Manager, HashiCorp Vault) for production
- **Credential Rotation**: Support credential rotation

### Access Control
- **Principle of Least Privilege**: Grant minimum necessary permissions
- **Separate Users**: Use different database users for different purposes
- **Role-Based Access**: Implement role-based access control
- **Audit Logging**: Log all data access and modifications

### Data Encryption
- **Encryption at Rest**: Encrypt sensitive data at rest
- **Encryption in Transit**: Use TLS/SSL for database connections
- **Field-Level Encryption**: Encrypt sensitive fields (PII, passwords)
- **Key Management**: Secure key storage and rotation

### SQL Injection Prevention
- **Parameterized Queries**: Always use parameterized queries
- **Prepared Statements**: Use prepared statements
- **Input Validation**: Validate and sanitize all inputs
- **ORM Usage**: Use ORMs that handle SQL injection prevention

### Example:
```typescript
// Good: Parameterized query
await db.query('SELECT * FROM users WHERE email = $1', [email]);

// Good: Using ORM
await db.select().from(users).where(eq(users.email, email));

// Bad: String concatenation (SQL injection risk)
await db.query(`SELECT * FROM users WHERE email = '${email}'`);
```

## Logging and Monitoring Requirements

### Logging Levels
- **DEBUG**: Detailed debugging information (queries, transactions)
- **INFO**: General information (connections, migrations)
- **WARN**: Warnings (slow queries, connection issues)
- **ERROR**: Errors (failed queries, data integrity violations)

### What to Log
- **Query Performance**: Log slow queries (>100ms or configurable threshold)
- **Transaction Failures**: Log all transaction failures
- **Migration Events**: Log migration starts and completions
- **Connection Issues**: Log connection pool exhaustion, timeouts
- **Data Integrity Violations**: Log constraint violations

### What NOT to Log
- **Sensitive Data**: Never log passwords, tokens, PII
- **Full Query Results**: Don't log full result sets (may contain sensitive data)
- **Connection Strings**: Don't log full connection strings with credentials

### Monitoring Metrics
- **Query Performance**: Track query execution times
- **Connection Pool**: Track connection pool usage
- **Transaction Rate**: Track transactions per second
- **Error Rate**: Track database error rate
- **Backup Status**: Monitor backup success/failure
- **Storage Usage**: Track database size and growth

## Code Patterns

### Repository Pattern
- **Consistent Interface**: Use repository pattern for data access
- **Abstraction**: Abstract database implementation details
- **Testability**: Easy to mock for testing

### Example:
```typescript
interface UserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  create(user: CreateUserDto): Promise<User>;
  update(id: string, data: UpdateUserDto): Promise<User>;
  delete(id: string): Promise<void>;
}

class PostgresUserRepository implements UserRepository {
  constructor(private db: Database) {}
  
  async findById(id: string): Promise<User | null> {
    const [user] = await this.db.select()
      .from(users)
      .where(eq(users.id, id))
      .limit(1);
    return user || null;
  }
  
  // ... other methods
}
```

### Data Access Layer
- **Separation of Concerns**: Separate data access from business logic
- **Type Safety**: Use type-safe query builders or ORMs
- **Error Handling**: Consistent error handling across data access

## Testing Standards

### Unit Testing
- **Mock Database**: Use in-memory database or mocks for unit tests
- **Test Data Access Logic**: Test repository methods
- **Test Validation**: Test data validation logic
- **Test Transactions**: Test transaction handling

### Integration Testing
- **Test Database**: Use test database for integration tests
- **Test Migrations**: Test migrations on test database
- **Test Queries**: Test actual database queries
- **Cleanup**: Clean up test data after tests

### Data Integrity Testing
- **Constraint Testing**: Test database constraints
- **Transaction Testing**: Test transaction behavior
- **Concurrency Testing**: Test concurrent access scenarios
- **Backup/Restore Testing**: Test backup and restore procedures

## Notes

- These standards should be applied consistently across all storage components
- When in doubt, prioritize data integrity and reliability
- Document any deviations from these standards
- Review and update standards as patterns evolve
- Consider storage-specific requirements (database vs object storage vs cache)

