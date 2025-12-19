# Product Context

## Overview
This document defines the core product context for building storage solutions. This context guides all storage development decisions and ensures alignment with the goal of creating reliable, performant, scalable, and maintainable storage components.

## Target Users

### Primary Users
- **Storage Engineers**: Developers building data persistence layers
  - Demographics: Backend engineers, database engineers, data engineers
  - Goals: Build reliable storage solutions, design efficient schemas, manage migrations safely, ensure data integrity
  - Pain Points: Complex migration management, data integrity issues, poor query performance, backup/recovery complexity
  - Tech Proficiency: High - experienced with databases, ORMs, schema design

- **Full-Stack Developers**: Developers building applications that need storage
  - Demographics: Software engineers building web applications
  - Goals: Implement storage quickly, follow best practices, maintain data consistency
  - Pain Points: Schema design decisions, migration complexity, query optimization, transaction handling
  - Tech Proficiency: Medium-High - experienced with application development

### Secondary Users
- **DevOps Engineers**: Engineers managing storage infrastructure
  - Demographics: Infrastructure engineers, platform engineers
  - Goals: Ensure storage reliability, manage backups, monitor performance
  - Pain Points: Backup failures, performance issues, migration deployment
  - Tech Proficiency: High - experienced with infrastructure and databases

- **End Users of Applications**: End users who benefit from reliable data storage
  - Demographics: General application users
  - Goals: Fast, reliable access to their data
  - Pain Points: Data loss, slow queries, data inconsistencies

## Product Goals

### Vision Statement
Create a systematic approach to building storage solutions that ensures data integrity, reliability, performance, and maintainability. Every storage component should follow proven patterns for schema design, migration management, data validation, and backup strategies.

### Core Objectives
1. **Data Integrity**: Ensure data consistency and correctness through constraints, validations, and transactions
2. **Reliability**: Build storage solutions that handle failures gracefully and recover automatically
3. **Performance**: Design schemas and queries for optimal performance with proper indexing
4. **Maintainability**: Make storage components easy to understand, update, and evolve
5. **Developer Experience**: Streamline the process of building new storage components

### Success Metrics
- **Data Integrity**: 99.99% data consistency (no corruption or constraint violations)
- **Query Performance**: 95% of queries execute in <100ms
- **Migration Success**: 100% successful migrations, zero downtime migrations
- **Backup Reliability**: 100% successful backups, tested recovery procedures
- **Developer Speed**: New storage components can be built in <1 week

## High-Level Features

### Core Features
1. **Schema Design & Management**
   - Purpose: Design efficient, normalized schemas with proper relationships and constraints
   - User Value: Developers have clear patterns for schema design, reduced schema evolution issues

2. **Migration Management**
   - Purpose: Version-controlled, reversible database migrations
   - User Value: Safe schema evolution, ability to rollback changes

3. **Data Integrity & Validation**
   - Purpose: Enforce data consistency through constraints and validation
   - User Value: Prevents bad data, ensures application correctness

4. **Query Optimization**
   - Purpose: Efficient queries with proper indexing and query patterns
   - User Value: Fast application performance, scalable data access

5. **Transaction Management**
   - Purpose: Reliable multi-step operations with ACID guarantees
   - User Value: Data consistency during complex operations

6. **Backup & Recovery**
   - Purpose: Automated backups and tested recovery procedures
   - User Value: Data protection, quick recovery from failures

7. **Performance Monitoring**
   - Purpose: Monitor query performance, identify bottlenecks
   - User Value: Proactive performance optimization, issue detection

### Future Features
- Automated schema optimization recommendations
- Visual schema diagram generation
- Migration impact analysis tools
- Automated backup testing and validation
- Performance regression detection

## Key User Journeys

### Journey 1: Building a New Storage Component
1. **Setup**: Developer runs `/storage-conductor-setup` to initialize storage conductor
2. **Planning**: Developer runs `/storage-conductor-new-storage` and provides storage requirements
3. **Schema Design**: Developer reviews generated schema design and plan
4. **Implementation**: Developer runs `/storage-conductor-implement` to build the storage component
5. **Testing**: Developer verifies storage works with test data and validates migrations
6. **Completion**: Storage component is documented and ready for use

### Journey 2: Evolving an Existing Storage Component
1. **Status Check**: Developer runs `/storage-conductor-status` to see storage component state
2. **Change Identification**: Developer identifies needed schema changes or optimizations
3. **Migration Planning**: Developer creates migration plan following standards
4. **Implementation**: Developer implements migration and tests on staging
5. **Deployment**: Developer applies migration to production following deployment procedure
6. **Verification**: Developer verifies data integrity and performance after migration

### Journey 3: Recovering from Data Issues
1. **Issue Detection**: Developer or monitoring detects data integrity issue
2. **Backup Selection**: Developer identifies appropriate backup point
3. **Recovery**: Developer restores from backup following recovery procedures
4. **Validation**: Developer verifies data integrity after recovery
5. **Root Cause**: Developer investigates and fixes root cause
6. **Prevention**: Developer implements preventive measures

### Journey 4: Optimizing Query Performance
1. **Performance Issue**: Developer identifies slow query
2. **Analysis**: Developer analyzes query execution plan
3. **Optimization**: Developer adds indexes or refactors query
4. **Testing**: Developer tests optimized query performance
5. **Deployment**: Developer deploys optimization with migration
6. **Monitoring**: Developer monitors performance improvement

## Product Principles

1. **Data Integrity First**: Data integrity is non-negotiable - always prioritize correctness
2. **Schema-First Development**: Design schema before implementation when possible
3. **Migration Safety**: All migrations must be tested, reversible, and safe
4. **Performance by Design**: Design for performance from the start, optimize proactively
5. **Document Everything**: Every schema, migration, and query decision must be documented
6. **Backup & Recovery**: Automated backups are mandatory, recovery must be tested
7. **Consistency**: Use the same patterns across all storage components

## Notes

- Storage components should support multiple database backends when possible
- Focus on common storage patterns first (relational databases, object storage, caches)
- Consider both schema-first and code-first approaches
- Support both SQL and NoSQL storage solutions
- Backup and recovery procedures are critical for data protection

