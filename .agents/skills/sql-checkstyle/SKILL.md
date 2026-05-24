# SQL Checkstyle Skill

This skill reviews SQL migration files and queries in the OpenMetadata project for style, correctness, and best practices.

## Trigger

Use this skill when:
- Reviewing `.sql` files in `bootstrap/sql/migrations/`
- Reviewing stored procedures or views
- Checking SQL queries embedded in Java or Python code
- Validating Flyway or Liquibase migration scripts

## Checklist

### Naming Conventions
- [ ] Table names use `snake_case` and are plural (e.g., `entity_relationships`)
- [ ] Column names use `snake_case`
- [ ] Index names follow pattern: `idx_<table>_<column(s)>` (e.g., `idx_entity_relationships_from_id`)
- [ ] Foreign key names follow pattern: `fk_<table>_<referenced_table>`
- [ ] Primary key names follow pattern: `pk_<table>`
- [ ] Migration files follow naming: `v<version>__<description>.sql`

### DDL Standards
- [ ] All tables have a `PRIMARY KEY` defined
- [ ] `NOT NULL` constraints are explicit where applicable
- [ ] `DEFAULT` values are provided for nullable boolean and timestamp columns
- [ ] `TIMESTAMP` columns use `DATETIME(6)` for MySQL or `TIMESTAMP` for Postgres with timezone awareness
- [ ] `VARCHAR` lengths are appropriate and consistent with existing schema
- [ ] `JSON` columns are used only where truly schemaless data is stored
- [ ] `IF NOT EXISTS` / `IF EXISTS` guards are used in migration DDL

### Migration Safety
- [ ] Migrations are backward-compatible (no breaking column drops without a prior deprecation migration)
- [ ] Large table alterations include a comment explaining performance impact
- [ ] Migrations do not assume specific data values unless seeding reference data
- [ ] Rollback considerations are documented in a comment block at the top of the file
- [ ] Migrations are idempotent where possible

### Query Style
- [ ] Keywords are UPPERCASE (`SELECT`, `FROM`, `WHERE`, `JOIN`, etc.)
- [ ] Each clause starts on a new line for multi-line queries
- [ ] Aliases are meaningful and not single letters (except in trivial cases)
- [ ] `SELECT *` is avoided in production queries; columns are explicitly listed
- [ ] `JOIN` type is always explicit (`INNER JOIN`, `LEFT JOIN`, never bare `JOIN` for clarity)
- [ ] Subqueries are avoided in favor of CTEs (`WITH` clauses) for readability
- [ ] `LIMIT` is applied to queries that could return unbounded results

### Indexing
- [ ] Columns used in `WHERE`, `JOIN ON`, and `ORDER BY` clauses have appropriate indexes
- [ ] Composite indexes are ordered by selectivity (most selective column first)
- [ ] Redundant indexes are not added (e.g., index on `(a, b)` makes index on `(a)` redundant)
- [ ] Full-text indexes are used for free-text search columns rather than `LIKE '%...'`

### Security
- [ ] No hardcoded credentials, secrets, or environment-specific values
- [ ] User input is never interpolated directly into SQL (parameterized queries enforced at app layer)
- [ ] Sensitive columns (e.g., `password_hash`, `token`) are noted with a comment

### Documentation
- [ ] Complex migrations include a header comment block with:
  - Author / ticket reference
  - Purpose of the migration
  - Any manual steps required before/after running
- [ ] Non-obvious column names have inline comments
- [ ] Deprecated columns are marked with `-- DEPRECATED: <reason> <date>`

## Common Patterns in OpenMetadata

```sql
-- Standard entity table pattern
CREATE TABLE IF NOT EXISTS entity_name (
    id            VARCHAR(36)     NOT NULL COLLATE ascii_bin,
    name          VARCHAR(256)    NOT NULL,
    displayName   VARCHAR(256),
    description   TEXT,
    json          JSON            NOT NULL,
    updatedAt     BIGINT UNSIGNED NOT NULL,
    updatedBy     VARCHAR(256)    NOT NULL,
    deleted       BOOLEAN         NOT NULL DEFAULT FALSE,
    PRIMARY KEY (id),
    UNIQUE KEY uq_entity_name_name (name)
);
```

## References
- [OpenMetadata Database Schema](https://github.com/open-metadata/OpenMetadata/tree/main/bootstrap/sql)
- [Flyway Naming Conventions](https://flywaydb.org/documentation/concepts/migrations#naming)
- [MySQL 8.0 Reference](https://dev.mysql.com/doc/refman/8.0/en/)
- [PostgreSQL 14 Reference](https://www.postgresql.org/docs/14/index.html)
