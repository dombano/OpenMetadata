# Go Checkstyle Skill

This skill enforces Go code style and quality standards for the OpenMetadata project.

## Overview

Applies Go-specific linting, formatting, and static analysis checks to ensure consistent code quality across all Go source files.

## Tools Used

- `gofmt` — Standard Go code formatter
- `golint` — Go source code linter
- `go vet` — Reports suspicious constructs
- `staticcheck` — Advanced static analysis
- `errcheck` — Checks for unchecked errors
- `gosec` — Security-focused static analysis

## Rules

### Formatting
- All Go files MUST be formatted with `gofmt`
- Use tabs for indentation (Go standard)
- Maximum line length: 120 characters
- No trailing whitespace

### Naming Conventions
- Exported identifiers: `PascalCase`
- Unexported identifiers: `camelCase`
- Constants: `PascalCase` or `SCREAMING_SNAKE_CASE` for package-level constants
- Interfaces: Should end with `-er` suffix when describing behavior (e.g., `Reader`, `Writer`)
- Acronyms: Keep consistent casing (e.g., `HTTPServer`, `userID`)

### Package Structure
- One package per directory
- Package names: lowercase, single word, no underscores
- Avoid `util`, `common`, `misc` as package names — be specific
- Internal packages should use `internal/` directory

### Error Handling
- Always handle errors explicitly — do not use `_` to discard errors unless intentional
- Wrap errors with context using `fmt.Errorf("context: %w", err)`
- Define sentinel errors as package-level variables: `var ErrNotFound = errors.New("not found")`
- Avoid `panic` in library code; reserve for truly unrecoverable states

### Comments and Documentation
- All exported functions, types, and variables MUST have doc comments
- Doc comments start with the name of the thing being described
- Use complete sentences ending with a period
- Example:
  ```go
  // FetchMetadata retrieves metadata for the given entity from OpenMetadata.
  func FetchMetadata(entityID string) (*Metadata, error) {
  ```

### Imports
- Group imports in the following order, separated by blank lines:
  1. Standard library
  2. Third-party packages
  3. Internal/local packages
- Use `goimports` to manage import groups automatically
- Avoid dot imports (`. "package"`) except in tests
- Avoid blank imports (`_ "package"`) unless required for side effects

### Concurrency
- Always document goroutine ownership and lifetime
- Use `context.Context` as the first parameter for functions that may block
- Prefer `sync.Mutex` over channels for simple state protection
- Avoid `init()` functions for complex initialization

### Testing
- Test files: `*_test.go`
- Test functions: `TestFunctionName(t *testing.T)`
- Benchmark functions: `BenchmarkFunctionName(b *testing.B)`
- Use table-driven tests where applicable
- Minimum test coverage: 80% for new packages
- Use `testify/assert` for assertions

### Security
- No hardcoded credentials or secrets
- Validate all external inputs
- Use `crypto/rand` for random number generation, not `math/rand`
- Avoid `unsafe` package unless absolutely necessary with explicit justification

## Configuration

```yaml
# .golangci.yml
linters:
  enable:
    - gofmt
    - golint
    - govet
    - errcheck
    - staticcheck
    - gosec
    - misspell
    - ineffassign
    - unused

linters-settings:
  govet:
    check-shadowing: true
  golint:
    min-confidence: 0.8

run:
  timeout: 5m
  skip-dirs:
    - vendor
    - generated
```

## Usage

```bash
# Run all checks
golangci-lint run ./...

# Format code
gofmt -w .

# Run go vet
go vet ./...

# Run tests with coverage
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out
```

## CI Integration

This skill is automatically applied in the CI pipeline via `.github/workflows/go-checkstyle.yml` on all pull requests modifying `*.go` files.
