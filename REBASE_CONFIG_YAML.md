# Rebase Configuration Format (YAML)

This document describes the YAML-based configuration format for the automated rebase workflow. This format replaces the previous custom text format to provide better parsing reliability and safety.

## File Location

Create the configuration file at: `.github/rebase-config.yml`

## Format Structure

```yaml
# Base commit - the commit before the ones you want to modify
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"

# List of operations to perform
operations:
  - action: "pick"
    commit: "b2c3d4e5f6789012345678901234567890abcde"
    
  - action: "squash" 
    commit: "c3d4e5f6789012345678901234567890abcdef"
    message: |
      Fix authentication and add comprehensive tests
      
      - Add unit tests for login flow
      - Fix password validation bug
      - Add integration tests
      
      Co-authored-by: Jane Smith <jane@example.com>
      
  - action: "squash"
    commit: "d4e5f6789012345678901234567890abcdef01"
    message: "Refactor user service"
```

## Field Descriptions

### `base_commit` (required)
- **Type**: String
- **Description**: The SHA hash of the commit before the ones you want to modify
- **Format**: Full 40-character SHA hash (recommended) or shorter hash
- **Example**: `"a1b2c3d4e5f6789012345678901234567890abcd"`

### `operations` (required)
- **Type**: Array of operation objects
- **Description**: List of operations to perform on commits in chronological order

### Operation Object Fields

#### `action` (required)
- **Type**: String
- **Values**: `"pick"` or `"squash"`
- **Description**: 
  - `"pick"`: Keep the commit as-is
  - `"squash"`: Combine with the previous commit

#### `commit` (required)
- **Type**: String
- **Description**: The SHA hash of the commit to operate on
- **Format**: Full 40-character SHA hash (recommended) or shorter hash
- **Example**: `"b2c3d4e5f6789012345678901234567890abcde"`

#### `message` (optional)
- **Type**: String (supports multi-line)
- **Description**: New commit message for squashed commits
- **Features**:
  - Supports multi-line messages
  - Preserves Co-authored-by lines
  - Maintains original formatting
- **Note**: If not provided for squash operations, uses the original commit message

## Multi-line Message Support

The YAML format natively supports multi-line strings using the `|` (literal) or `>` (folded) operators:

```yaml
# Literal style - preserves line breaks
message: |
  Fix authentication system
  
  - Add OAuth2 support
  - Fix session handling
  - Update documentation
  
  Co-authored-by: John Doe <john@example.com>

# Folded style - folds line breaks into spaces
message: >
  This is a long commit message that spans multiple lines
  in the YAML file but will be folded into a single
  paragraph in the actual commit message.
```

## Examples

### Example 1: Simple Squash
```yaml
base_commit: "abc123def456789012345678901234567890abcd"
operations:
  - action: "pick"
    commit: "def456789012345678901234567890abcdef12"
  - action: "squash"
    commit: "789012345678901234567890abcdef123456"
    message: "Implement user authentication feature"
```

### Example 2: Complex Multi-line with Co-authors
```yaml
base_commit: "abc123def456789012345678901234567890abcd"
operations:
  - action: "pick"
    commit: "def456789012345678901234567890abcdef12"
  - action: "squash"
    commit: "789012345678901234567890abcdef123456"
    message: |
      Implement comprehensive user authentication
      
      This commit combines the initial authentication work with bug fixes
      and comprehensive testing. Key changes include:
      
      - OAuth2 integration with Google and GitHub
      - Session management with Redis backend  
      - Password strength validation
      - Multi-factor authentication support
      - Comprehensive unit and integration tests
      
      Fixes #123, #124, #125
      
      Co-authored-by: Alice Developer <alice@company.com>
      Co-authored-by: Bob Tester <bob@company.com>
  - action: "squash"
    commit: "012345678901234567890abcdef12345678"
```

### Example 3: Multiple Squash Groups
```yaml
base_commit: "abc123def456789012345678901234567890abcd"
operations:
  - action: "pick"
    commit: "def456789012345678901234567890abcdef12"
  - action: "squash"
    commit: "789012345678901234567890abcdef123456"
    message: "Implement authentication system"
  - action: "pick"
    commit: "012345678901234567890abcdef12345678"
  - action: "squash"
    commit: "345678901234567890abcdef1234567890"
    message: "Add user profile management"
```

## Migration from Text Format

If you have existing `.github/rebase-todo` files in the old text format:

**Old format:**
```
a1b2c3d4e5f6789012345678901234567890abcd
pick b2c3d4e5f6789012345678901234567890abcde
squash c3d4e5f6789012345678901234567890abcdef Fix auth and add tests
```

**New YAML format:**
```yaml
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"
operations:
  - action: "pick"
    commit: "b2c3d4e5f6789012345678901234567890abcde"
  - action: "squash"
    commit: "c3d4e5f6789012345678901234567890abcdef"
    message: "Fix auth and add tests"
```

## Validation

The workflow validates:
- ✅ YAML syntax correctness
- ✅ Required fields presence
- ✅ Commit hash existence in repository
- ✅ Base commit validity
- ✅ Operation action values

## Benefits of YAML Format

1. **Parser Reliability**: Uses well-tested YAML parsers instead of custom parsing
2. **Multi-line Support**: Native support for complex, multi-line commit messages
3. **Injection Safety**: YAML parsing prevents script injection attacks
4. **Validation**: Built-in structure validation
5. **Readability**: Clear, structured format that's easy to read and write
6. **Tool Support**: IDE syntax highlighting and validation support