# Automated Git Rebase Workflow

This document describes the automated git rebase workflow that enables GitHub Copilot and users to perform commit squashing and rebase operations through a simple configuration file.

## Key Features

- **Automated commit squashing and rebase operations**: Trigger git rebase operations by committing a YAML configuration file
- **GitHub Copilot integration**: Designed to work seamlessly with GitHub Copilot for intelligent commit management
- **YAML configuration format with multi-line message support**: Full support for complex commit messages with Co-authored-by lines
- **Injection-safe parsing and execution**: Uses YAML parsing and environment variables to prevent script injection attacks
- **Automatic cleanup and proper attribution**: Maintains correct committer information and removes config files after completion
- **Reusable Workflow**: Use `workflow_call` to integrate into your existing workflows

## Installation

To use this automated rebase workflow in your repository:

1. **Copy the trigger workflow**:
   - Copy `.github/workflows/trigger-rebase.yml` (example trigger workflow)

2. **Copy the instruction file**:
   - Copy `.github/instructions/automated-rebase.instructions.md` to enable GitHub Copilot integration

3. **Customize as needed**:
   - Modify `trigger-rebase.yml` to match your triggering preferences
   - Update paths or branch names in the workflow if needed
   - Ensure the trigger workflow includes `permissions: contents: write`

Note: The main `rebase.yml` workflow is referenced remotely from this repository, so you don't need to copy it.

## Configuration Format

### YAML Format

The workflow uses a YAML configuration format that provides reliability, multi-line message support, and injection safety:

**File:** `.github/rebase-config.yml`

```yaml
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"
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
```

## YAML Configuration Format

### File Location

Create the configuration file at: `.github/rebase-config.yml`

### Format Structure

#### `base_commit` (required)
- **Type**: String
- **Description**: The SHA hash of the commit before the ones you want to modify
- **Format**: Full 40-character SHA hash (recommended) or shorter hash
- **Example**: `"a1b2c3d4e5f6789012345678901234567890abcd"`

#### `operations` (required)
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

### Multi-line Message Support

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

### Configuration Examples

#### Example 1: Simple Squash
```yaml
base_commit: "abc123def456789012345678901234567890abcd"
operations:
  - action: "pick"
    commit: "def456789012345678901234567890abcdef12"
  - action: "squash"
    commit: "789012345678901234567890abcdef123456"
    message: "Implement user authentication feature"
```

#### Example 2: Complex Multi-line with Co-authors
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

#### Example 3: Multiple Squash Groups
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

## Quick Start

1. Copy the `rebase.yml` workflow to your repository's `.github/workflows/` directory
2. Create a `.github/rebase-config.yml` file with your rebase instructions
3. Commit and push the file to trigger the automated rebase

## Workflow Files

- **`rebase.yml`** - Main reusable rebase workflow supporting YAML configuration
- **`rebase-usage.yml`** - Example showing how to use the workflow

## Usage Examples

### Basic Commit Squashing

To squash the last 3 commits into one:

1. Get the base commit hash (4 commits ago):
   ```bash
   git log --oneline -4
   ```

2. Create `.github/rebase-config.yml`:
   ```yaml
   base_commit: "<base-commit-hash>"
   operations:
     - action: "pick"
       commit: "<first-commit-hash>"
     - action: "squash"
       commit: "<second-commit-hash>"
       message: "Combined feature implementation"
     - action: "squash"
       commit: "<third-commit-hash>"
   ```

3. Commit and push the file

### With Workflow Call

Use the reusable workflow in your own workflows:

```yaml
name: My Custom Rebase

on:
  push:
    paths:
      - '.github/rebase-config.yml'

jobs:
  rebase:
    uses: ./.github/workflows/rebase-v2.yml
    secrets: inherit
```

## For GitHub Copilot Integration

This workflow is designed to work seamlessly with GitHub Copilot. When you need to squash commits:

1. Ask Copilot to create a rebase configuration file
2. Copilot can analyze your commit history and generate the appropriate format
3. The workflow automatically handles the rebase and cleanup

## Requirements

- Repository must have `contents: write` permissions for the workflow
- Use full 40-character SHA hashes for reliability
- Ensure you're working on a branch that can be force-pushed

## Important Notes

⚠️ **This workflow performs force pushes**. Only use on branches where force pushing is acceptable.

✅ **Automatic cleanup**: The config file is removed after successful completion.

🔒 **Secure**: Uses the commit author of the config file as the committer for rebased commits.

## Files in This Repository

- **[AUTOMATED_REBASE.md](AUTOMATED_REBASE.md)** - Complete documentation for the automated rebase workflow
- **[.github/instructions/automated-rebase.instructions.md](.github/instructions/automated-rebase.instructions.md)** - GitHub Copilot integration guide
- **`.github/workflows/rebase.yml`** - Main reusable rebase workflow
- **`.github/workflows/trigger-rebase.yml`** - Example trigger workflow