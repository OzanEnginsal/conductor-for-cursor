# Storage Template

## Overview
This document provides the standard structure and code patterns for building storage solutions. Use this template as a starting point for all new storage components to ensure consistency and best practices.

## Directory Structure

```
<storage-name>/
  src/
    schema.ts          # Schema definitions
    migrations/        # Migration files
      0001_initial.sql
      0002_add_indexes.sql
    repositories/      # Repository implementations
      user.repository.ts
      order.repository.ts
    models.ts          # Data models/types
    errors.ts          # Custom error classes
    database.ts        # Database connection and configuration
    transactions.ts    # Transaction utilities
    queries/           # Complex query implementations
      user.queries.ts
      order.queries.ts
    utils/             # Utility functions
      validation.ts    # Data validation
      backup.ts        # Backup utilities
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
    fixtures/          # Test data fixtures
      users.json
      orders.json
  backups/            # Backup scripts and procedures
    backup.sh
    restore.sh
  README.md           # Storage documentation
  package.json        # Dependencies and scripts
  tsconfig.json       # TypeScript config (if TypeScript)
```

## Code Templates

### Schema Definition (`schema.ts`)

#### Using SQL Schema (PostgreSQL example)
```sql
-- schema.sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(255) NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_created_at ON users(created_at);

CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  total DECIMAL(10, 2) NOT NULL,
  status VARCHAR(50) NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
  
  CONSTRAINT check_total_positive CHECK (total >= 0),
  CONSTRAINT check_status_valid CHECK (status IN ('pending', 'processing', 'completed', 'cancelled'))
);

CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created_at ON orders(created_at);
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

#### Using ORM Schema (TypeScript/Prisma example)
```typescript
// schema.prisma
model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")
  
  orders    Order[]
  
  @@index([email])
  @@index([createdAt])
  @@map("users")
}

model Order {
  id        String   @id @default(uuid())
  userId    String   @map("user_id")
  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  total     Decimal  @db.Decimal(10, 2)
  status    OrderStatus
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")
  
  @@index([userId])
  @@index([status])
  @@index([createdAt])
  @@index([userId, status])
  @@map("orders")
}

enum OrderStatus {
  pending
  processing
  completed
  cancelled
}
```

#### Using Drizzle ORM (TypeScript example)
```typescript
// schema.ts
import { pgTable, uuid, varchar, timestamp, decimal, pgEnum, index } from 'drizzle-orm/pg-core';
import { relations } from 'drizzle-orm';

export const orderStatusEnum = pgEnum('order_status', ['pending', 'processing', 'completed', 'cancelled']);

export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: varchar('email', { length: 255 }).notNull().unique(),
  name: varchar('name', { length: 255 }).notNull(),
  createdAt: timestamp('created_at', { withTimezone: true }).notNull().defaultNow(),
  updatedAt: timestamp('updated_at', { withTimezone: true }).notNull().defaultNow(),
}, (table) => ({
  emailIdx: index('idx_users_email').on(table.email),
  createdAtIdx: index('idx_users_created_at').on(table.createdAt),
}));

export const orders = pgTable('orders', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').notNull().references(() => users.id, { onDelete: 'cascade' }),
  total: decimal('total', { precision: 10, scale: 2 }).notNull(),
  status: orderStatusEnum('status').notNull(),
  createdAt: timestamp('created_at', { withTimezone: true }).notNull().defaultNow(),
  updatedAt: timestamp('updated_at', { withTimezone: true }).notNull().defaultNow(),
}, (table) => ({
  userIdIdx: index('idx_orders_user_id').on(table.userId),
  statusIdx: index('idx_orders_status').on(table.status),
  createdAtIdx: index('idx_orders_created_at').on(table.createdAt),
  userStatusIdx: index('idx_orders_user_status').on(table.userId, table.status),
}));

export const usersRelations = relations(users, ({ many }) => ({
  orders: many(orders),
}));

export const ordersRelations = relations(orders, ({ one }) => ({
  user: one(users, {
    fields: [orders.userId],
    references: [users.id],
  }),
}));
```

### Migration File Structure

#### Migration Template
```sql
-- migrations/0001_initial_schema.sql
BEGIN;

-- Create users table
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(255) NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW()
);

-- Create indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_created_at ON users(created_at);

-- Track migration
INSERT INTO schema_migrations (version, applied_at)
VALUES ('0001_initial_schema', NOW())
ON CONFLICT (version) DO NOTHING;

COMMIT;

-- Rollback migration
-- BEGIN;
-- DROP INDEX IF EXISTS idx_users_created_at;
-- DROP INDEX IF EXISTS idx_users_email;
-- DROP TABLE IF EXISTS users;
-- DELETE FROM schema_migrations WHERE version = '0001_initial_schema';
-- COMMIT;
```

#### Migration with Data Transformation
```sql
-- migrations/0002_add_user_role.sql
BEGIN;

-- Add role column with default
ALTER TABLE users ADD COLUMN role VARCHAR(50) NOT NULL DEFAULT 'user';

-- Create index on role
CREATE INDEX idx_users_role ON users(role);

-- Update existing data if needed
-- UPDATE users SET role = 'admin' WHERE email IN ('admin@example.com');

-- Track migration
INSERT INTO schema_migrations (version, applied_at)
VALUES ('0002_add_user_role', NOW())
ON CONFLICT (version) DO NOTHING;

COMMIT;
```

### Repository Pattern (`repositories/user.repository.ts`)

```typescript
import { eq, and, desc } from 'drizzle-orm';
import { db } from '../database';
import { users } from '../schema';
import { User, CreateUserDto, UpdateUserDto } from '../models';

export interface UserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  findAll(options?: { limit?: number; offset?: number }): Promise<User[]>;
  create(data: CreateUserDto): Promise<User>;
  update(id: string, data: UpdateUserDto): Promise<User>;
  delete(id: string): Promise<void>;
}

export class PostgresUserRepository implements UserRepository {
  async findById(id: string): Promise<User | null> {
    const [user] = await db
      .select()
      .from(users)
      .where(eq(users.id, id))
      .limit(1);
    
    return user || null;
  }

  async findByEmail(email: string): Promise<User | null> {
    const [user] = await db
      .select()
      .from(users)
      .where(eq(users.email, email))
      .limit(1);
    
    return user || null;
  }

  async findAll(options?: { limit?: number; offset?: number }): Promise<User[]> {
    let query = db.select().from(users).orderBy(desc(users.createdAt));
    
    if (options?.limit) {
      query = query.limit(options.limit);
    }
    
    if (options?.offset) {
      query = query.offset(options.offset);
    }
    
    return await query;
  }

  async create(data: CreateUserDto): Promise<User> {
    const [user] = await db
      .insert(users)
      .values({
        email: data.email,
        name: data.name,
      })
      .returning();
    
    return user;
  }

  async update(id: string, data: UpdateUserDto): Promise<User> {
    const [user] = await db
      .update(users)
      .set({
        ...data,
        updatedAt: new Date(),
      })
      .where(eq(users.id, id))
      .returning();
    
    if (!user) {
      throw new NotFoundError(`User with id ${id} not found`);
    }
    
    return user;
  }

  async delete(id: string): Promise<void> {
    const result = await db
      .delete(users)
      .where(eq(users.id, id));
    
    if (result.rowCount === 0) {
      throw new NotFoundError(`User with id ${id} not found`);
    }
  }
}
```

### CRUD Operations with Transactions (`transactions.ts`)

```typescript
import { db } from './database';
import { users, orders } from './schema';
import { eq } from 'drizzle-orm';

export class TransactionManager {
  /**
   * Transfer funds between users (example transaction)
   */
  async transferFunds(
    fromUserId: string,
    toUserId: string,
    amount: number
  ): Promise<void> {
    return await db.transaction(async (tx) => {
      // Lock rows for update
      const [fromUser] = await tx
        .select()
        .from(users)
        .where(eq(users.id, fromUserId))
        .for('update')
        .limit(1);
      
      const [toUser] = await tx
        .select()
        .from(users)
        .where(eq(users.id, toUserId))
        .for('update')
        .limit(1);
      
      if (!fromUser || !toUser) {
        throw new Error('User not found');
      }
      
      // Perform transfer
      await tx
        .update(users)
        .set({ balance: fromUser.balance - amount })
        .where(eq(users.id, fromUserId));
      
      await tx
        .update(users)
        .set({ balance: toUser.balance + amount })
        .where(eq(users.id, toUserId));
      
      // Create transaction record
      await tx.insert(transactions).values({
        fromUserId,
        toUserId,
        amount,
        status: 'completed',
      });
    });
  }

  /**
   * Create order with inventory check
   */
  async createOrder(orderData: CreateOrderDto): Promise<Order> {
    return await db.transaction(async (tx) => {
      // Check inventory
      for (const item of orderData.items) {
        const [product] = await tx
          .select()
          .from(products)
          .where(eq(products.id, item.productId))
          .for('update')
          .limit(1);
        
        if (!product || product.quantity < item.quantity) {
          throw new InsufficientInventoryError(
            `Insufficient inventory for product ${item.productId}`
          );
        }
        
        // Update inventory
        await tx
          .update(products)
          .set({ quantity: product.quantity - item.quantity })
          .where(eq(products.id, item.productId));
      }
      
      // Create order
      const [order] = await tx
        .insert(orders)
        .values({
          userId: orderData.userId,
          total: orderData.total,
          status: 'pending',
        })
        .returning();
      
      return order;
    });
  }
}
```

### Query Optimization Examples (`queries/user.queries.ts`)

```typescript
import { db } from '../database';
import { users, orders } from '../schema';
import { eq, and, gte, desc, sql } from 'drizzle-orm';

export class UserQueries {
  /**
   * Get user with recent orders (optimized with join)
   */
  async getUserWithRecentOrders(
    userId: string,
    limit: number = 10
  ): Promise<UserWithOrders> {
    const result = await db
      .select({
        user: users,
        order: orders,
      })
      .from(users)
      .leftJoin(orders, eq(orders.userId, users.id))
      .where(eq(users.id, userId))
      .orderBy(desc(orders.createdAt))
      .limit(limit);
    
    if (result.length === 0) {
      throw new NotFoundError(`User ${userId} not found`);
    }
    
    const user = result[0].user;
    const orders = result
      .map((r) => r.order)
      .filter((o) => o !== null);
    
    return { user, orders };
  }

  /**
   * Get users with order count (optimized with aggregation)
   */
  async getUsersWithOrderCount(
    options?: { limit?: number; offset?: number }
  ): Promise<UserWithOrderCount[]> {
    const query = db
      .select({
        user: users,
        orderCount: sql<number>`COUNT(${orders.id})::int`,
      })
      .from(users)
      .leftJoin(orders, eq(orders.userId, users.id))
      .groupBy(users.id)
      .orderBy(desc(sql`COUNT(${orders.id})`));
    
    if (options?.limit) {
      query.limit(options.limit);
    }
    
    if (options?.offset) {
      query.offset(options.offset);
    }
    
    return await query;
  }

  /**
   * Get active users (users with orders in last 30 days)
   */
  async getActiveUsers(): Promise<User[]> {
    const thirtyDaysAgo = new Date();
    thirtyDaysAgo.setDate(thirtyDaysAgo.getDate() - 30);
    
    const result = await db
      .selectDistinct({ user: users })
      .from(users)
      .innerJoin(orders, eq(orders.userId, users.id))
      .where(gte(orders.createdAt, thirtyDaysAgo));
    
    return result.map((r) => r.user);
  }
}
```

### Database Connection (`database.ts`)

```typescript
import { drizzle } from 'drizzle-orm/node-postgres';
import { Pool } from 'pg';
import * as schema from './schema';

const pool = new Pool({
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT || '5432'),
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  max: 20, // Maximum connections
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
  ssl: process.env.DB_SSL === 'true' ? { rejectUnauthorized: false } : false,
});

// Handle pool errors
pool.on('error', (err) => {
  console.error('Unexpected error on idle client', err);
  process.exit(-1);
});

export const db = drizzle(pool, { schema });

// Health check
export async function checkConnection(): Promise<boolean> {
  try {
    await pool.query('SELECT 1');
    return true;
  } catch (error) {
    return false;
  }
}

// Graceful shutdown
export async function closeConnection(): Promise<void> {
  await pool.end();
}
```

### Data Validation (`utils/validation.ts`)

```typescript
import { z } from 'zod';

export const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(255),
});

export const UpdateUserSchema = z.object({
  email: z.string().email().optional(),
  name: z.string().min(1).max(255).optional(),
});

export type CreateUserDto = z.infer<typeof CreateUserSchema>;
export type UpdateUserDto = z.infer<typeof UpdateUserSchema>;

export function validateCreateUser(data: unknown): CreateUserDto {
  return CreateUserSchema.parse(data);
}

export function validateUpdateUser(data: unknown): UpdateUserDto {
  return UpdateUserSchema.parse(data);
}
```

### Backup Utilities (`utils/backup.ts`)

```typescript
import { exec } from 'child_process';
import { promisify } from 'util';
import * as fs from 'fs/promises';
import * as path from 'path';

const execAsync = promisify(exec);

export class BackupManager {
  constructor(
    private dbConfig: {
      host: string;
      port: number;
      database: string;
      user: string;
      password: string;
    },
    private backupDir: string = './backups'
  ) {}

  async createBackup(): Promise<string> {
    const timestamp = new Date().toISOString().replace(/:/g, '-');
    const backupFile = path.join(
      this.backupDir,
      `backup-${timestamp}.sql`
    );
    
    // Ensure backup directory exists
    await fs.mkdir(this.backupDir, { recursive: true });
    
    // Create backup using pg_dump
    const pgDumpCmd = `PGPASSWORD=${this.dbConfig.password} pg_dump \
      -h ${this.dbConfig.host} \
      -p ${this.dbConfig.port} \
      -U ${this.dbConfig.user} \
      -d ${this.dbConfig.database} \
      -F c \
      -f ${backupFile}`;
    
    await execAsync(pgDumpCmd);
    
    // Verify backup file exists
    const stats = await fs.stat(backupFile);
    if (stats.size === 0) {
      throw new Error('Backup file is empty');
    }
    
    return backupFile;
  }

  async restoreBackup(backupFile: string): Promise<void> {
    if (!await this.fileExists(backupFile)) {
      throw new Error(`Backup file not found: ${backupFile}`);
    }
    
    // Restore backup using pg_restore
    const pgRestoreCmd = `PGPASSWORD=${this.dbConfig.password} pg_restore \
      -h ${this.dbConfig.host} \
      -p ${this.dbConfig.port} \
      -U ${this.dbConfig.user} \
      -d ${this.dbConfig.database} \
      -c \
      ${backupFile}`;
    
    await execAsync(pgRestoreCmd);
  }

  private async fileExists(filePath: string): Promise<boolean> {
    try {
      await fs.access(filePath);
      return true;
    } catch {
      return false;
    }
  }
}
```

### Custom Error Classes (`errors.ts`)

```typescript
export class StorageError extends Error {
  constructor(
    message: string,
    public code: string,
    public details?: any
  ) {
    super(message);
    this.name = this.constructor.name;
    Error.captureStackTrace(this, this.constructor);
  }
}

export class NotFoundError extends StorageError {
  constructor(resource: string, id?: string) {
    const message = id
      ? `${resource} with id ${id} not found`
      : `${resource} not found`;
    super(message, 'NOT_FOUND');
  }
}

export class ValidationError extends StorageError {
  constructor(message: string, details?: any) {
    super(message, 'VALIDATION_ERROR', details);
  }
}

export class DataIntegrityError extends StorageError {
  constructor(message: string, details?: any) {
    super(message, 'DATA_INTEGRITY_ERROR', details);
  }
}

export class InsufficientInventoryError extends StorageError {
  constructor(message: string, details?: any) {
    super(message, 'INSUFFICIENT_INVENTORY', details);
  }
}
```

### Test Examples (`tests/unit/repositories/user.repository.test.ts`)

```typescript
import { describe, it, expect, beforeEach, afterEach } from 'vitest';
import { PostgresUserRepository } from '../../../src/repositories/user.repository';
import { db } from '../../../src/database';
import { users } from '../../../src/schema';

describe('PostgresUserRepository', () => {
  let repository: PostgresUserRepository;

  beforeEach(async () => {
    repository = new PostgresUserRepository();
    // Clean up test data
    await db.delete(users);
  });

  afterEach(async () => {
    // Clean up test data
    await db.delete(users);
  });

  describe('create', () => {
    it('should create a new user', async () => {
      const userData = {
        email: 'test@example.com',
        name: 'Test User',
      };

      const user = await repository.create(userData);

      expect(user).toHaveProperty('id');
      expect(user.email).toBe(userData.email);
      expect(user.name).toBe(userData.name);
    });

    it('should throw error if email already exists', async () => {
      const userData = {
        email: 'test@example.com',
        name: 'Test User',
      };

      await repository.create(userData);

      await expect(repository.create(userData)).rejects.toThrow();
    });
  });

  describe('findById', () => {
    it('should find user by id', async () => {
      const user = await repository.create({
        email: 'test@example.com',
        name: 'Test User',
      });

      const found = await repository.findById(user.id);

      expect(found).not.toBeNull();
      expect(found?.id).toBe(user.id);
    });

    it('should return null if user not found', async () => {
      const found = await repository.findById('non-existent-id');

      expect(found).toBeNull();
    });
  });
});
```

## README Template

```markdown
# [Storage Component Name]

## Overview
Brief description of the storage component and what it stores.

## Storage Type
- **Type**: [Database, Object Storage, Cache, File System]
- **Backend**: [PostgreSQL, MongoDB, S3, Redis, etc.]

## Schema

### Entities
- **Users**: User accounts
- **Orders**: Customer orders

### Relationships
- Users have many Orders

## Installation

\`\`\`bash
npm install
\`\`\`

## Configuration

Set the following environment variables:

\`\`\`
DB_HOST=localhost
DB_PORT=5432
DB_NAME=myapp
DB_USER=postgres
DB_PASSWORD=password
\`\`\`

## Usage

\`\`\`typescript
import { PostgresUserRepository } from './repositories/user.repository';

const userRepo = new PostgresUserRepository();

// Create user
const user = await userRepo.create({
  email: 'user@example.com',
  name: 'John Doe',
});

// Find user
const found = await userRepo.findById(user.id);
\`\`\`

## Migrations

Run migrations:

\`\`\`bash
npm run migrate
\`\`\`

## Backup and Recovery

### Create Backup
\`\`\`bash
npm run backup
\`\`\`

### Restore Backup
\`\`\`bash
npm run restore <backup-file>
\`\`\`

## Testing

Run tests:

\`\`\`bash
npm test
\`\`\`

## Contributing
[Contributing guidelines]
```

## Notes

- Adapt templates to your chosen database and ORM
- Follow storage standards from `storage-standards.md`
- Customize for storage-specific requirements
- Add storage-specific patterns as needed
- Keep code DRY and maintainable
- Focus on data integrity and performance

