# Python Checkstyle Skill

This skill enforces Python code style and quality standards for the OpenMetadata project.

## Overview

Use this skill when reviewing or writing Python code to ensure consistency with the project's coding standards, PEP 8 guidelines, and OpenMetadata-specific conventions.

## Tools

The following tools are available for Python style checking:

- **flake8**: PEP 8 style guide enforcement
- **black**: Opinionated code formatter
- **isort**: Import sorting
- **mypy**: Static type checking
- **pylint**: Comprehensive linting

## Style Rules

### Formatting

- Maximum line length: **120 characters**
- Use **4 spaces** for indentation (no tabs)
- Use **double quotes** for strings (black default)
- Always include a **trailing newline** at end of file
- Remove **trailing whitespace** on all lines

### Imports

- Sort imports using `isort` with the `black` profile
- Group imports in order:
  1. Standard library imports
  2. Third-party imports
  3. Local/project imports
- Separate each group with a blank line
- Avoid wildcard imports (`from module import *`)

```python
# Correct
import os
import sys
from typing import Dict, List, Optional

import requests
from pydantic import BaseModel

from openmetadata.utils import helper
```

### Type Hints

- **Always** add type hints to function signatures
- Use `Optional[T]` for nullable values
- Use `Union[T1, T2]` sparingly; prefer `Optional` where applicable
- Use built-in generics (`list[str]`, `dict[str, int]`) for Python 3.9+

```python
# Correct
def get_entity(entity_id: str, include_deleted: bool = False) -> Optional[Dict[str, Any]]:
    ...

# Incorrect
def get_entity(entity_id, include_deleted=False):
    ...
```

### Docstrings

- Use **Google-style** docstrings
- All public functions, classes, and modules must have docstrings
- Include `Args`, `Returns`, and `Raises` sections where applicable

```python
def fetch_metadata(source: str, timeout: int = 30) -> List[Dict]:
    """Fetch metadata from the specified source.

    Args:
        source: The URL or identifier of the metadata source.
        timeout: Request timeout in seconds. Defaults to 30.

    Returns:
        A list of metadata dictionaries.

    Raises:
        ConnectionError: If the source is unreachable.
        ValueError: If the source identifier is invalid.
    """
    ...
```

### Naming Conventions

- **Classes**: `PascalCase` (e.g., `MetadataService`)
- **Functions/Methods**: `snake_case` (e.g., `get_table_schema`)
- **Variables**: `snake_case` (e.g., `table_name`)
- **Constants**: `UPPER_SNAKE_CASE` (e.g., `MAX_RETRY_COUNT`)
- **Private members**: prefix with single underscore (e.g., `_internal_method`)
- **Dunder methods**: double underscore prefix/suffix (e.g., `__init__`)

### Error Handling

- Always catch specific exceptions, not bare `except:`
- Log exceptions with context before re-raising or handling
- Use custom exception classes defined in `openmetadata.exceptions`

```python
# Correct
try:
    result = api_client.get(endpoint)
except requests.Timeout as err:
    logger.error("Request timed out for endpoint %s: %s", endpoint, err)
    raise ConnectionError(f"Timeout connecting to {endpoint}") from err

# Incorrect
try:
    result = api_client.get(endpoint)
except:
    pass
```

### Logging

- Use the standard `logging` module
- Obtain loggers via `logging.getLogger(__name__)`
- Use `%s` style formatting in log messages (not f-strings)
- Log at appropriate levels: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`

## Common Violations to Avoid

| Violation | Bad | Good |
|-----------|-----|------|
| Mutable default args | `def f(x=[])` | `def f(x=None)` |
| Bare except | `except:` | `except Exception as e:` |
| Missing type hints | `def f(x):` | `def f(x: str) -> None:` |
| F-string in logging | `logger.info(f"val={v}")` | `logger.info("val=%s", v)` |
| Wildcard import | `from mod import *` | `from mod import Foo, Bar` |
