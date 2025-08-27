---
applyTo: '**/*'
---

# GitHub Copilot Instructions for Automated Rebase

This repository supports automated git rebase operations through GitHub Actions. You can help users create and manage rebase operations by generating the appropriate `.github/rebase-config.yml` files using the YAML format.

## When to Use Automated Rebase

Users might want to use automated rebase when they need to:
- Squash multiple commits into one with a cleaner commit message
- Combine feature commits before merging to main branch
- Clean up commit history by combining related changes
- Prepare commits for a pull request review
- Drop initial plan commits or work-in-progress commits that are no longer needed

**Important Notes:**
- The `rebase-config.yml` commit should be the last commit without any other change since any commits after it will be removed during the rebase process
- **GitHub Copilot Limitation**: When GitHub Copilot creates rebase configurations, the automated workflow will require manual approval before execution. This is because GitHub Copilot operates as a GitHub App, and GitHub's security model requires approval for workflows with write permissions triggered by GitHub Apps. Repository owners will need to manually approve the workflow run in the Actions tab.

## Rebase Configuration File Format

The `.github/rebase-config.yml` file uses YAML format for better reliability and multi-line message support:

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
      Implement user authentication feature
      
      - Add OAuth2 support
      - Fix session handling
      - Add comprehensive tests
      
      Co-authored-by: Jane Smith <jane@example.com>
```

### Structure Details

1. **`base_commit`**: The SHA hash of the commit before the ones to modify
2. **`operations`**: Array of operations to perform on specific commits

### Supported Operations

- **`pick`**: Keep the commit as-is
  ```yaml
  - action: "pick"
    commit: "abc1234567890abcdef1234567890abcdef12"
  ```

- **`squash`**: Combine with the previous commit, with optional new message
  ```yaml
  - action: "squash"
    commit: "abc1234567890abcdef1234567890abcdef12"
    message: "New combined commit message"
  ```

### Multi-line Message Support

The YAML format supports multi-line commit messages using the `|` operator:

```yaml
message: |
  Implement comprehensive authentication system
  
  This feature includes:
  - OAuth2 integration with multiple providers
  - Session management with Redis
  - Password strength validation
  - Two-factor authentication support
  
  Fixes #123, #124
  
  Co-authored-by: Alice Developer <alice@company.com>
  Co-authored-by: Bob Tester <bob@company.com>
```

### Example Scenarios

#### Scenario 1: Squash Last 3 Commits
When a user has made 3 related commits and wants to combine them:

```yaml
# Base commit from 4 commits ago
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"

operations:
  # Keep the first commit
  - action: "pick"
    commit: "b2c3d4e5f6789012345678901234567890abcde"
  
  # Squash the next two commits with a new message
  - action: "squash"
    commit: "c3d4e5f6789012345678901234567890abcdef"
    message: |
      Implement user authentication with tests and documentation
      
      Combined changes:
      - Initial authentication implementation
      - Bug fixes and improvements  
      - Comprehensive test suite
      - API documentation updates
      
      Co-authored-by: John Doe <john@example.com>
  
  - action: "squash"
    commit: "d4e5f6789012345678901234567890abcdef01"
```

#### Scenario 2: Multiple Squash Groups
When user wants to create separate logical commits:

```yaml
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"

operations:
  - action: "pick"
    commit: "b2c3d4e5f6789012345678901234567890abcde"
  - action: "squash"
    commit: "c3d4e5f6789012345678901234567890abcdef"
    message: "Add authentication system"
  
  - action: "pick"
    commit: "d4e5f6789012345678901234567890abcdef01"
  - action: "squash"
    commit: "e5f6789012345678901234567890abcdef012"
    message: "Update documentation and tests"
```

#### Scenario 3: Dropping Initial Plan Commit
When user wants to remove an initial planning commit:

```yaml
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"

operations:
  # Skip the initial plan commit entirely by not including it
  - action: "pick"
    commit: "c3d4e5f6789012345678901234567890abcdef"  # Skip commit b2c3... (initial plan)
  - action: "squash"
    commit: "d4e5f6789012345678901234567890abcdef01"
    message: "Implement feature with complete implementation"
```

## How to Help Users

When a user asks for help with commit squashing or rebase:

1. **Analyze their git history**: Help them understand which commits they want to modify
2. **Identify the base commit**: Find the commit hash before their changes
3. **Create the YAML config file**: Generate the appropriate format based on their needs
4. **Explain the process**: Let them know the workflow will run automatically after committing the file

## Common Git Commands for Analysis

Help users find the information they need:

```bash
# View recent commits with hashes
git log --oneline -10

# View detailed commit information
git log --pretty=format:"%h %an %ad %s" --date=short -10

# Find a specific commit by message
git log --grep="some message" --oneline

# View commits in a date range
git log --since="2023-01-01" --until="2023-12-31" --oneline
```

## Safety Notes

Always remind users that:
- This workflow performs force pushes
- Only use on branches where force pushing is safe
- The rebase config file is automatically removed after successful completion
- They should backup their branch before performing complex rebases
- The YAML format prevents injection attacks and parsing errors

## Integration Example

When helping a user create a rebase config file:

```markdown
I'll help you create a rebase configuration file to squash your last 3 commits. Based on your git log, here's what I recommend:

Create `.github/rebase-config.yml` with:

```yaml
# Base commit (before your feature work)
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"

operations:
  # Keep your first commit  
  - action: "pick"
    commit: "b2c3d4e5f6789012345678901234567890abcde"
  
  # Squash the remaining commits with a clean message
  - action: "squash"
    commit: "c3d4e5f6789012345678901234567890abcdef"
    message: |
      Implement user authentication feature with tests
      
      This commit combines:
      - Initial authentication implementation
      - Bug fixes and security improvements
      - Comprehensive unit and integration tests
      - Documentation updates
      
      Co-authored-by: Jane Smith <jane@example.com>
  
  - action: "squash"
    commit: "d4e5f6789012345678901234567890abcdef01"
```

After you commit and push this file, the GitHub Actions workflow will automatically:
1. Parse the YAML configuration safely
2. Perform the rebase operation using reset + cherry-pick
3. Preserve multi-line commit messages and Co-authored-by lines
4. Force push the squashed commits
5. Remove the rebase config file

Make sure you're on a branch where force pushing is acceptable!
```