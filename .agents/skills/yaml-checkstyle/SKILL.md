# YAML Checkstyle Skill

This skill enforces YAML formatting and style conventions for the OpenMetadata project.

## Rules

### Indentation
- Use 2 spaces for indentation (no tabs)
- Nested keys must be consistently indented
- List items (`-`) must align with their parent key's indentation level + 2 spaces

### Spacing
- Add a single space after colons in key-value pairs: `key: value`
- No trailing whitespace on any line
- Files must end with a single newline character
- No multiple consecutive blank lines

### Quoting
- Use double quotes for strings containing special characters (`:`, `{`, `}`, `[`, `]`, `,`, `#`, `&`, `*`, `?`, `|`, `-`, `<`, `>`, `=`, `!`, `%`, `@`, `` ` ``)
- Avoid unnecessary quoting of simple scalar values
- Boolean values should be unquoted: `true`, `false`
- Numeric values should be unquoted unless string context is required

### Structure
- Each YAML file should start with `---` document separator when used in multi-document contexts
- Avoid deep nesting beyond 5 levels where possible
- Group related keys together logically
- Use comments (`#`) to explain non-obvious configuration blocks

### Naming Conventions
- Use `camelCase` for keys in OpenMetadata service configuration files
- Use `kebab-case` for Kubernetes manifest keys (following K8s conventions)
- Use `SCREAMING_SNAKE_CASE` for environment variable references
- Use `snake_case` for Airflow DAG configuration keys

### Lists and Mappings
- Prefer block style over flow style for readability:
  ```yaml
  # Preferred
  items:
    - foo
    - bar

  # Avoid
  items: [foo, bar]
  ```
- Flow style is acceptable for short, simple inline lists with fewer than 4 items

### Anchors and Aliases
- Use YAML anchors (`&`) and aliases (`*`) to reduce duplication in complex configs
- Name anchors descriptively: `&defaultDatabaseConfig`
- Place anchor definitions at the top of the relevant block

### Comments
- Comments must start with `# ` (hash followed by a single space)
- Inline comments must be separated from the value by two spaces: `value  # comment`
- Use comments to document required vs optional fields

## Examples

### Valid
```yaml
# Database connection configuration
databaseConfig:
  host: localhost
  port: 5432
  dbScheme: postgresql+psycopg2
  username: openmetadata_user
  password: "${AIRFLOW_DB_PASSWORD}"
  driverConfig:  # Optional: override default driver settings
    connectTimeout: 30
    readTimeout: 60

services:
  - name: my-service
    type: Mysql
    enabled: true
```

### Invalid
```yaml
databaseConfig:
    host:localhost          # Missing space after colon
    port:  5432             # Extra space after colon
    dbScheme : postgresql   # Space before colon
    username: 'admin'       # Single quotes instead of double
    password: secret        # Should be quoted (special context)

services: [{name: svc1, type: Mysql}, {name: svc2}]  # Flow style too complex
```

## Automated Checks

Run the following to validate YAML files:

```bash
# Install yamllint
pip install yamllint

# Run with OpenMetadata config
yamllint -c .yamllint.yml .

# Check a specific file
yamllint -c .yamllint.yml openmetadata.yaml
```

### `.yamllint.yml` Configuration
```yaml
---
extends: default
rules:
  indentation:
    spaces: 2
    indent-sequences: true
    check-multi-line-strings: false
  line-length:
    max: 120
    level: warning
  comments:
    min-spaces-from-content: 2
  truthy:
    allowed-values: ['true', 'false']
    check-keys: true
```

## Integration

This skill is applied during PR review for any `.yaml` or `.yml` files changed in:
- `conf/`
- `docker/`
- `charts/`
- `ingestion/`
- `.github/workflows/`
