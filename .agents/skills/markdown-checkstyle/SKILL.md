# Markdown Checkstyle Skill

This skill reviews Markdown files for style, structure, and quality issues in the OpenMetadata project.

## Trigger

Use this skill when:
- Reviewing `.md` or `.mdx` files
- Checking documentation quality
- Validating README files, changelogs, or wiki pages
- Ensuring consistent documentation formatting across the project

## Checks to Perform

### 1. Heading Structure
- Document must have exactly one H1 (`#`) heading
- Headings must be in sequential order (no skipping levels, e.g., H1 → H3)
- Headings should not end with punctuation (except `?` for FAQ-style docs)
- Avoid duplicate heading text within the same document

### 2. Line Length
- Prose lines should not exceed **120 characters**
- Code blocks and tables are exempt from line length limits
- URLs in links are exempt from line length limits

### 3. Code Blocks
- All fenced code blocks must specify a language identifier
  ```
  // Good
  ```typescript
  const x = 1;
  ```

  // Bad
  ```
  const x = 1;
  ```
  ```
- Use backtick fences (` ``` `), not tilde fences (`~~~`)

### 4. Links
- All links must be valid (no empty `href` or `[]()` patterns)
- Prefer reference-style links for URLs used more than once
- Avoid bare URLs — wrap them in angle brackets or use link syntax
- Internal links (relative paths) should point to existing files

### 5. Lists
- Unordered lists must use consistent markers (`-` preferred over `*` or `+`)
- List items should not be blank
- Nested lists must be indented by 2 or 4 spaces consistently
- Ordered lists should start at `1.` and increment sequentially

### 6. Whitespace & Blank Lines
- No trailing whitespace on any line
- File must end with a single newline
- No more than one consecutive blank line between sections
- There must be a blank line before and after headings
- There must be a blank line before and after code blocks

### 7. Emphasis & Formatting
- Use `**bold**` for important terms, not `__bold__`
- Use `*italic*` for emphasis, not `_italic_`
- Avoid excessive emphasis (no more than 3 bold/italic spans per paragraph)
- Inline code (backticks) should be used for: file names, function names, CLI commands, variable names

### 8. Tables
- Tables must have a header row and a separator row
- All rows must have the same number of columns
- Prefer aligned table formatting for readability

### 9. Images
- All images must have non-empty alt text
- Alt text should be descriptive, not just the filename
  ```
  // Good
  ![OpenMetadata entity relationship diagram](./assets/erd.png)

  // Bad
  ![](./assets/erd.png)
  ![erd.png](./assets/erd.png)
  ```

### 10. Front Matter (MDX / Docusaurus)
- MDX files used in the docs site should include YAML front matter
- Required fields: `title`, `slug`
- Optional but recommended: `description`, `tags`
  ```yaml
  ---
  title: Getting Started
  slug: /getting-started
  description: Learn how to set up OpenMetadata in minutes.
  tags: [setup, quickstart]
  ---
  ```

## Severity Levels

| Severity | Examples |
|----------|----------|
| **Error** | Missing H1, broken internal links, code blocks without language, missing image alt text |
| **Warning** | Line too long, duplicate headings, bare URLs, inconsistent list markers |
| **Info** | Missing front matter in MDX, suggestion to use reference-style links |

## Auto-fixable Issues

The following issues can typically be auto-corrected by tools like `markdownlint --fix` or `prettier`:
- Trailing whitespace
- Missing blank lines around headings/code blocks
- Inconsistent list markers
- Missing final newline

## Recommended Tools

- [`markdownlint-cli2`](https://github.com/DavidAnson/markdownlint-cli2) — primary linter
- [`prettier`](https://prettier.io/) — formatting
- [`markdown-link-check`](https://github.com/tcort/markdown-link-check) — link validation

## Example `.markdownlint.json` Config

```json
{
  "default": true,
  "MD013": { "line_length": 120, "code_blocks": false, "tables": false },
  "MD033": false,
  "MD041": true
}
```
