# SQL Style Guide

Based on Google's SQL Style Guide with PostgreSQL and Backstage-specific adaptations.

## General Principles

### Consistency
- Use consistent naming conventions throughout the database
- Apply formatting rules uniformly across all SQL files
- Maintain consistent indentation and spacing
- Use standard SQL keywords and functions when possible

### Readability
- Write self-documenting SQL with clear structure
- Use meaningful table and column names
- Format complex queries for easy comprehension
- Add comments for business logic and complex operations

### Performance
- Write efficient queries that scale well
- Use appropriate indexes and constraints
- Avoid unnecessary subqueries and joins
- Consider query execution plans

## Naming Conventions

### Table Names
- Use singular nouns (user, component, deployment)
- Use snake_case for multi-word names
- Use descriptive, business-meaningful names
- Avoid abbreviations unless widely understood

```sql
-- Good
CREATE TABLE user_profile (
    id UUID PRIMARY KEY,
    user_id UUID NOT NULL,
    display_name VARCHAR(255) NOT NULL
);

CREATE TABLE component_dependency (
    source_component_id UUID NOT NULL,
    target_component_id UUID NOT NULL
);

-- Bad
CREATE TABLE users (id UUID); -- plural
CREATE TABLE usrProf (id UUID); -- abbreviation + camelCase
CREATE TABLE t1 (id UUID); -- non-descriptive
```

### Column Names
- Use snake_case consistently
- Use descriptive names that explain the data
- Include units in names when applicable
- Use standard suffixes for common patterns

```sql
-- Good column names
user_id          -- Foreign key reference
created_at       -- Timestamp
updated_at       -- Timestamp
is_active        -- Boolean flag
email_address    -- Descriptive
retry_count      -- With unit implied
timeout_seconds  -- With explicit unit

-- Standard patterns
id               -- Primary key
external_id      -- External system reference
deleted_at       -- Soft delete timestamp
version          -- Optimistic locking
```

### Constraints and Indexes
- Use descriptive names that indicate purpose
- Include table name in constraint names
- Use standard prefixes for constraint types

```sql
-- Primary keys
CONSTRAINT pk_user_profile PRIMARY KEY (id)

-- Foreign keys
CONSTRAINT fk_user_profile_user_id 
    FOREIGN KEY (user_id) REFERENCES users(id)

-- Unique constraints
CONSTRAINT uk_user_profile_user_id 
    UNIQUE (user_id)

-- Check constraints
CONSTRAINT chk_user_profile_email_format 
    CHECK (email_address ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$')

-- Indexes
CREATE INDEX idx_user_profile_created_at ON user_profile(created_at);
CREATE INDEX idx_component_owner_id ON component(owner_id);
```

## Formatting Rules

### Indentation and Spacing
- Use 2 spaces for indentation
- Align related elements vertically
- Use blank lines to separate logical sections
- Place commas at the end of lines

```sql
-- Good formatting
SELECT
  u.id,
  u.email_address,
  up.display_name,
  up.created_at
FROM users u
INNER JOIN user_profile up ON u.id = up.user_id
WHERE u.is_active = true
  AND up.created_at >= '2024-01-01'
ORDER BY up.created_at DESC
LIMIT 100;
```

### SQL Keywords
- Use UPPERCASE for SQL keywords and functions
- Use lowercase for user-defined names
- Be consistent throughout the project

```sql
-- Good - consistent keyword casing
SELECT
  component_id,
  component_name,
  COUNT(*) AS deployment_count
FROM deployments
WHERE deployment_status = 'success'
GROUP BY component_id, component_name
HAVING COUNT(*) > 5
ORDER BY deployment_count DESC;

-- Bad - inconsistent casing
select component_id, Count(*) as deployment_count
from deployments
WHERE deployment_status = 'success'
group by component_id;
```

### Line Breaks and Alignment
- Put each SELECT column on its own line
- Align JOIN conditions with the JOIN keyword
- Break long WHERE clauses into multiple lines
- Align conditions vertically

```sql
-- Complex query formatting
SELECT
  c.id AS component_id,
  c.name AS component_name,
  c.type AS component_type,
  s.name AS system_name,
  COUNT(d.id) AS deployment_count,
  MAX(d.created_at) AS last_deployment
FROM components c
INNER JOIN systems s 
  ON c.system_id = s.id
LEFT JOIN deployments d 
  ON c.id = d.component_id
  AND d.deployment_status = 'success'
WHERE c.lifecycle IN ('production', 'experimental')
  AND c.created_at >= CURRENT_DATE - INTERVAL '30 days'
  AND (
    c.owner_id = $1 
    OR c.system_id IN (
      SELECT system_id 
      FROM team_systems 
      WHERE team_id = $2
    )
  )
GROUP BY
  c.id,
  c.name,
  c.type,
  s.name
HAVING COUNT(d.id) >= 1
ORDER BY
  deployment_count DESC,
  last_deployment DESC
LIMIT 50;
```

## Data Types and Constraints

### PostgreSQL-Specific Types
- Use appropriate PostgreSQL data types
- Leverage JSONB for flexible document storage
- Use UUID for primary keys and references
- Use proper timestamp types with timezone awareness

```sql
-- Good data type usage
CREATE TABLE component (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  description TEXT,
  metadata JSONB DEFAULT '{}',
  tags TEXT[] DEFAULT '{}',
  created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  version INTEGER NOT NULL DEFAULT 1
);

-- Appropriate constraints
ALTER TABLE component
  ADD CONSTRAINT chk_component_name_length 
    CHECK (LENGTH(name) >= 1),
  ADD CONSTRAINT chk_component_version_positive 
    CHECK (version > 0);

-- JSON validation
ALTER TABLE component
  ADD CONSTRAINT chk_component_metadata_structure
    CHECK (
      metadata ? 'schema_version' 
      AND (metadata->>'schema_version')::INTEGER >= 1
    );
```

### Indexes for Performance
- Create indexes on frequently queried columns
- Use composite indexes for multi-column queries
- Consider partial indexes for filtered queries
- Use GIN indexes for JSONB and array columns

```sql
-- Single column indexes
CREATE INDEX idx_component_name ON component(name);
CREATE INDEX idx_component_created_at ON component(created_at);

-- Composite indexes
CREATE INDEX idx_component_type_lifecycle 
  ON component(type, lifecycle);

-- Partial indexes
CREATE INDEX idx_component_active_created_at 
  ON component(created_at) 
  WHERE lifecycle = 'production';

-- JSONB indexes
CREATE INDEX idx_component_metadata_gin 
  ON component USING GIN (metadata);

-- Array indexes
CREATE INDEX idx_component_tags_gin 
  ON component USING GIN (tags);
```

## Query Patterns

### SELECT Statements
- Always specify column names explicitly
- Use table aliases for clarity in joins
- Order columns logically (keys first, then data)
- Use LIMIT for potentially large result sets

```sql
-- Good SELECT pattern
SELECT
  c.id,
  c.name,
  c.type,
  c.lifecycle,
  c.description,
  c.created_at,
  c.updated_at
FROM components c
WHERE c.lifecycle = 'production'
ORDER BY c.name
LIMIT 100;

-- With joins and aggregation
SELECT
  s.name AS system_name,
  COUNT(c.id) AS component_count,
  COUNT(CASE WHEN c.lifecycle = 'production' THEN 1 END) AS production_count,
  MAX(c.created_at) AS newest_component
FROM systems s
LEFT JOIN components c ON s.id = c.system_id
GROUP BY s.id, s.name
HAVING COUNT(c.id) > 0
ORDER BY component_count DESC;
```

### INSERT Statements
- Always specify column names
- Use appropriate data types and constraints
- Handle conflicts gracefully with ON CONFLICT
- Use RETURNING for inserted data

```sql
-- Simple insert with explicit columns
INSERT INTO component (
  id,
  name,
  type,
  lifecycle,
  system_id,
  owner_id
) VALUES (
  gen_random_uuid(),
  'user-service',
  'service',
  'experimental',
  $1,
  $2
) RETURNING id, created_at;

-- Bulk insert with conflict handling
INSERT INTO component_tag (component_id, tag_name)
SELECT 
  $1 AS component_id,
  unnest($2::TEXT[]) AS tag_name
ON CONFLICT (component_id, tag_name) DO NOTHING;

-- Insert from select
INSERT INTO component_audit (
  component_id,
  change_type,
  changed_by,
  changed_at,
  old_values,
  new_values
)
SELECT
  c.id,
  'lifecycle_change',
  $1,
  CURRENT_TIMESTAMP,
  jsonb_build_object('lifecycle', c.lifecycle),
  jsonb_build_object('lifecycle', $2)
FROM components c
WHERE c.id = $3;
```

### UPDATE Statements
- Always include WHERE clauses
- Update only necessary columns
- Use RETURNING to verify changes
- Include version checks for optimistic locking

```sql
-- Simple update with version check
UPDATE component
SET
  name = $2,
  description = $3,
  updated_at = CURRENT_TIMESTAMP,
  version = version + 1
WHERE id = $1
  AND version = $4
RETURNING id, version, updated_at;

-- Conditional update with CASE
UPDATE component
SET
  lifecycle = CASE
    WHEN deployment_count >= 5 THEN 'production'
    WHEN deployment_count >= 1 THEN 'experimental'
    ELSE 'development'
  END,
  updated_at = CURRENT_TIMESTAMP
FROM (
  SELECT
    component_id,
    COUNT(*) AS deployment_count
  FROM deployments
  WHERE deployment_status = 'success'
  GROUP BY component_id
) deploy_stats
WHERE component.id = deploy_stats.component_id;

-- JSONB update
UPDATE component
SET
  metadata = metadata || $2::JSONB,
  updated_at = CURRENT_TIMESTAMP
WHERE id = $1;
```

### DELETE Statements
- Prefer soft deletes for audit trails
- Always include WHERE clauses
- Use CASCADE carefully
- Consider archiving before deletion

```sql
-- Soft delete pattern
UPDATE component
SET
  deleted_at = CURRENT_TIMESTAMP,
  updated_at = CURRENT_TIMESTAMP
WHERE id = $1
  AND deleted_at IS NULL
RETURNING id, deleted_at;

-- Hard delete with conditions
DELETE FROM component_tag
WHERE component_id = $1
  AND tag_name = ANY($2::TEXT[]);

-- Cleanup old records
DELETE FROM audit_log
WHERE created_at < CURRENT_DATE - INTERVAL '1 year'
  AND retention_required = false;
```

## Database Schema Patterns

### Backstage Entity Model
- Follow Backstage's entity model conventions
- Use consistent metadata patterns
- Implement proper relationships between entities

```sql
-- Core entity table
CREATE TABLE entity (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  api_version VARCHAR(50) NOT NULL,
  kind VARCHAR(50) NOT NULL,
  namespace VARCHAR(255) NOT NULL DEFAULT 'default',
  name VARCHAR(255) NOT NULL,
  uid VARCHAR(255) UNIQUE,
  metadata JSONB NOT NULL DEFAULT '{}',
  spec JSONB NOT NULL DEFAULT '{}',
  status JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT uk_entity_compound UNIQUE (api_version, kind, namespace, name)
);

-- Entity relationships
CREATE TABLE entity_relation (
  source_entity_id UUID NOT NULL,
  target_entity_id UUID NOT NULL,
  relation_type VARCHAR(50) NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  PRIMARY KEY (source_entity_id, target_entity_id, relation_type),
  
  CONSTRAINT fk_entity_relation_source
    FOREIGN KEY (source_entity_id) REFERENCES entity(id) ON DELETE CASCADE,
  CONSTRAINT fk_entity_relation_target
    FOREIGN KEY (target_entity_id) REFERENCES entity(id) ON DELETE CASCADE
);
```

### Audit and History Patterns
- Implement comprehensive audit trails
- Use triggers for automatic timestamping
- Store change history for critical entities

```sql
-- Audit log table
CREATE TABLE audit_log (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  table_name VARCHAR(255) NOT NULL,
  record_id UUID NOT NULL,
  operation VARCHAR(10) NOT NULL CHECK (operation IN ('INSERT', 'UPDATE', 'DELETE')),
  old_values JSONB,
  new_values JSONB,
  changed_by UUID,
  changed_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT chk_audit_log_values
    CHECK (
      (operation = 'INSERT' AND old_values IS NULL AND new_values IS NOT NULL) OR
      (operation = 'UPDATE' AND old_values IS NOT NULL AND new_values IS NOT NULL) OR
      (operation = 'DELETE' AND old_values IS NOT NULL AND new_values IS NULL)
    )
);

-- Automatic timestamp trigger
CREATE OR REPLACE FUNCTION update_timestamp()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = CURRENT_TIMESTAMP;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER tr_component_update_timestamp
  BEFORE UPDATE ON component
  FOR EACH ROW
  EXECUTE FUNCTION update_timestamp();
```

## Performance Optimization

### Query Optimization
- Use EXPLAIN ANALYZE to understand query plans
- Avoid N+1 query problems
- Use appropriate JOIN types
- Consider materialized views for complex aggregations

```sql
-- Efficient batch loading
SELECT
  c.id,
  c.name,
  COALESCE(
    json_agg(
      json_build_object(
        'type', r.relation_type,
        'target_name', target.name,
        'target_kind', target.kind
      )
    ) FILTER (WHERE r.relation_type IS NOT NULL),
    '[]'
  ) AS relations
FROM component c
LEFT JOIN entity_relation r ON c.id = r.source_entity_id
LEFT JOIN entity target ON r.target_entity_id = target.id
WHERE c.id = ANY($1::UUID[])
GROUP BY c.id, c.name;

-- Materialized view for expensive aggregations
CREATE MATERIALIZED VIEW component_stats AS
SELECT
  c.id AS component_id,
  c.name,
  c.type,
  COUNT(d.id) AS deployment_count,
  MAX(d.created_at) AS last_deployment,
  COUNT(CASE WHEN d.deployment_status = 'success' THEN 1 END) AS successful_deployments,
  COUNT(CASE WHEN d.deployment_status = 'failed' THEN 1 END) AS failed_deployments
FROM component c
LEFT JOIN deployment d ON c.id = d.component_id
WHERE c.deleted_at IS NULL
GROUP BY c.id, c.name, c.type;

-- Refresh strategy
CREATE UNIQUE INDEX uk_component_stats_component_id 
  ON component_stats(component_id);

-- Refresh materialized view
REFRESH MATERIALIZED VIEW CONCURRENTLY component_stats;
```

### Index Strategy
- Monitor index usage with pg_stat_user_indexes
- Drop unused indexes
- Use covering indexes when beneficial

```sql
-- Monitoring index usage
SELECT
  schemaname,
  tablename,
  indexname,
  idx_tup_read,
  idx_tup_fetch,
  idx_scan
FROM pg_stat_user_indexes
WHERE schemaname = 'public'
ORDER BY idx_scan DESC;

-- Covering index for common query pattern
CREATE INDEX idx_component_covering_list
  ON component(type, lifecycle)
  INCLUDE (id, name, created_at)
  WHERE deleted_at IS NULL;
```

## Migration Scripts

### Schema Migrations
- Use reversible migrations when possible
- Test migrations on production-like data
- Include rollback procedures
- Document breaking changes

```sql
-- Migration: Add component tags support
-- Version: 20240315_001
-- Description: Add tags array column to component table

BEGIN;

-- Add column with default
ALTER TABLE component 
  ADD COLUMN tags TEXT[] DEFAULT '{}';

-- Create index for tag searches
CREATE INDEX idx_component_tags_gin 
  ON component USING GIN (tags);

-- Migrate existing metadata tags
UPDATE component
SET tags = COALESCE(
  (
    SELECT array_agg(value)
    FROM jsonb_array_elements_text(metadata->'tags')
    WHERE jsonb_typeof(metadata->'tags') = 'array'
  ),
  '{}'
)
WHERE metadata ? 'tags';

-- Remove tags from metadata
UPDATE component
SET metadata = metadata - 'tags'
WHERE metadata ? 'tags';

-- Add constraint
ALTER TABLE component
  ADD CONSTRAINT chk_component_tags_valid
    CHECK (array_length(tags, 1) <= 20);

COMMIT;

-- Rollback script
-- BEGIN;
-- ALTER TABLE component DROP COLUMN tags;
-- DROP INDEX IF EXISTS idx_component_tags_gin;
-- COMMIT;
```

## Comments and Documentation

### SQL Comments
- Document complex business logic
- Explain performance considerations
- Document data relationships
- Include examples for complex queries

```sql
-- Component discovery query for Backstage catalog
-- This query fetches components with their relationships and metadata
-- optimized for the catalog list view with pagination
--
-- Performance notes:
-- - Uses covering index on (type, lifecycle) for WHERE clause
-- - LEFT JOINs are optimized with proper indexes
-- - LIMIT prevents unbounded result sets
--
-- @param $1 component_type - Filter by component type (optional)
-- @param $2 lifecycle - Filter by lifecycle state (optional)
-- @param $3 offset - Pagination offset
-- @param $4 limit - Maximum results (max 100)
SELECT
  -- Component basic information
  c.id,
  c.name,
  c.type,
  c.lifecycle,
  c.description,
  
  -- Owner information (resolved from metadata)
  COALESCE(
    c.metadata->>'owner',
    'unknown'
  ) AS owner,
  
  -- System relationship (many-to-one)
  s.name AS system_name,
  
  -- Deployment statistics (calculated)
  COALESCE(ds.deployment_count, 0) AS deployment_count,
  ds.last_deployment,
  
  -- Metadata for client-side processing
  c.metadata,
  c.created_at,
  c.updated_at
FROM component c
  
  -- System relationship (optional)
  LEFT JOIN system s ON c.system_id = s.id
  
  -- Pre-calculated deployment stats (materialized view)
  LEFT JOIN component_deployment_stats ds ON c.id = ds.component_id

WHERE 
  -- Exclude soft-deleted components
  c.deleted_at IS NULL
  
  -- Filter by type if provided
  AND ($1::TEXT IS NULL OR c.type = $1)
  
  -- Filter by lifecycle if provided
  AND ($2::TEXT IS NULL OR c.lifecycle = $2)

ORDER BY
  -- Most recently updated first, then by name for stability
  c.updated_at DESC,
  c.name ASC

-- Pagination with safety limit
OFFSET COALESCE($3, 0)
LIMIT LEAST(COALESCE($4, 50), 100);
```

This comprehensive SQL style guide provides standards for database development in the Backstage environment, ensuring consistency, performance, and maintainability across all database operations and schema definitions.
