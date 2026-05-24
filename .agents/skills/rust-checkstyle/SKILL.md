# Rust Checkstyle Skill

This skill enforces Rust code style and best practices for the OpenMetadata project.

## Overview

Applies Rust-specific linting, formatting checks, and idiomatic code patterns using `clippy` and `rustfmt` conventions.

## Rules

### Formatting
- All code must be formatted with `rustfmt` using default settings
- Maximum line length: 100 characters
- Use 4 spaces for indentation (no tabs)
- Trailing commas required in multi-line expressions

### Naming Conventions
- `snake_case` for variables, functions, modules, and crates
- `PascalCase` for types, traits, enums, and structs
- `SCREAMING_SNAKE_CASE` for constants and static variables
- Prefix unused variables with `_`

### Code Quality
- No `unwrap()` calls in production code — use `?` operator or explicit error handling
- No `expect()` without a descriptive message
- Prefer `if let` or `match` over `.unwrap()` for `Option` and `Result`
- Avoid `clone()` when borrowing is sufficient
- Use `&str` instead of `&String` for function parameters where possible
- Prefer iterators over manual index-based loops

### Error Handling
- Define custom error types using `thiserror` crate
- Propagate errors with `?` operator
- Avoid `panic!` in library code
- Use `anyhow` for application-level error handling

### Documentation
- All public functions, structs, enums, and traits must have doc comments (`///`)
- Module-level documentation required (`//!`)
- Include examples in doc comments for complex public APIs
- Use `# Errors` section in doc comments for fallible functions
- Use `# Panics` section if function can panic

### Imports
- Group imports: standard library, external crates, internal modules
- Separate groups with blank lines
- Use `use` statements at the top of the file
- Avoid wildcard imports (`use module::*`) except in test modules

### Safety
- Minimize use of `unsafe` blocks
- Every `unsafe` block must have a `// SAFETY:` comment explaining invariants
- Prefer safe abstractions over raw pointer manipulation

### Testing
- Unit tests in the same file using `#[cfg(test)]` module
- Integration tests in `tests/` directory
- Use `#[test]` attribute for all test functions
- Test function names should describe what is being tested: `test_<function>_<scenario>`
- Use `assert_eq!`, `assert_ne!`, `assert!` macros appropriately

### Performance
- Avoid unnecessary heap allocations in hot paths
- Use `Cow<str>` when a function may or may not need to own data
- Prefer stack allocation for small, fixed-size data

### Clippy
The following clippy lints are enforced:
```
#![deny(clippy::all)]
#![warn(clippy::pedantic)]
#![warn(clippy::nursery)]
```

Common clippy fixes required:
- `clippy::needless_pass_by_value` — pass by reference when ownership not needed
- `clippy::must_use_candidate` — annotate functions whose return value should be used
- `clippy::missing_errors_doc` — document error conditions
- `clippy::redundant_closure_for_method_calls` — use method reference instead of closure

## Functions

### `fetch_metadata`

Fetches entity metadata from the OpenMetadata API.

```rust
/// Fetches metadata for a given entity from the OpenMetadata API.
///
/// # Arguments
/// * `client` - The HTTP client to use for requests
/// * `entity_type` - The type of entity to fetch (e.g., "table", "pipeline")
/// * `fqn` - The fully qualified name of the entity
///
/// # Returns
/// Returns `Ok(EntityMetadata)` on success.
///
/// # Errors
/// Returns `Err(MetadataError)` if the request fails or the entity is not found.
pub async fn fetch_metadata(
    client: &Client,
    entity_type: &str,
    fqn: &str,
) -> Result<EntityMetadata, MetadataError> {
    // implementation
}
```

## Enforcement

This skill is enforced via CI pipeline using:
- `cargo fmt --check`
- `cargo clippy -- -D warnings`
- `cargo test`

All checks must pass before a pull request can be merged.
