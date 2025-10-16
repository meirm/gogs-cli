# Gogs CLI - Complete How-To Guide

This comprehensive guide will walk you through setting up and using the Gogs CLI tool effectively.

## Table of Contents

1. [Installation & Setup](#installation--setup)
2. [Configuration](#configuration)
3. [Authentication](#authentication)
4. [Basic Usage](#basic-usage)
5. [User Management](#user-management)
6. [Organization Management](#organization-management)
7. [Repository Management](#repository-management)
8. [Collaboration Management](#collaboration-management)
9. [Team Management](#team-management)
10. [Advanced Features](#advanced-features)
11. [Troubleshooting](#troubleshooting)
12. [Best Practices](#best-practices)

## Installation & Setup

### Prerequisites

- Bash shell (version 4.0 or higher)
- `curl` command-line tool
- `gpg` (optional, for encrypted token storage)
- Access to a Gogs server

### Download and Setup

1. **Clone or download the repository:**
   ```bash
   git clone <repository-url>
   cd gogs-cli
   ```

2. **Make the script executable:**
   ```bash
   chmod +x gogs
   ```

3. **Test the installation:**
   ```bash
   ./gogs
   ```

## Configuration

### Server URL Configuration

The CLI needs to know which Gogs server to connect to. You can configure this in three ways:

#### Method 1: Environment Variable (Recommended)
```bash
export GOGS_SERVER_URL="https://try.gogs.io"
```

#### Method 2: Configuration File
```bash
echo "https://try.gogs.io" > gogs-server-url.txt
```

#### Method 3: Interactive Setup
If no configuration is found, the CLI will prompt you:
```bash
./gogs
# No Gogs server URL (e.g. https://try.gogs.io) configured.
# URL: https://try.gogs.io
```

### API Token Configuration

You'll need an API token to authenticate with the Gogs server. Configure it using one of these methods:

#### Method 1: Environment Variable (Recommended)
```bash
export GOGS_API_TOKEN="your-api-token-here"
```

#### Method 2: Plain Text File
```bash
echo "your-api-token-here" > gogs-api-token.txt
```

#### Method 3: Encrypted File (Most Secure)
```bash
echo "your-api-token-here" | gpg -c -o gogs-api-token.txt.gpg
```

#### Method 4: Interactive Setup
The CLI will prompt for the token if not configured:
```bash
./gogs
# No Gogs API token configured.
# Token: [hidden input]
```

## Authentication

### Creating API Tokens

#### Via Web Interface
1. Go to `https://YOUR.GOGS.SERVER/user/settings/applications`
2. Click "Generate New Token"
3. Enter a token name (e.g., "CLI Access")
4. Select appropriate scopes
5. Copy the generated token

#### Via CLI (if you have user credentials)
```bash
./gogs create-api-token "username" "CLI Token" "password"
```

### Verifying Authentication
```bash
./gogs get-api-token-user
```

This will show your current user information if authentication is working.

## Basic Usage

### Command Structure
```bash
./gogs [OPTIONS] ACTION [ARGUMENTS...]
```

### Available Options
- `-h, --help` - Show help for the action
- `-d, --dry-run` - Preview commands without executing them
- `-D, --debug` - Enable debug output with detailed API calls
- `-n, --no-checks` - Disable sanity checks (use with caution)

### Getting Help
```bash
# Show all available actions
./gogs

# Show help for a specific action
./gogs create-own-repo --help
```

### Debug Mode
Use debug mode to see exactly what API calls are being made:
```bash
./gogs -D create-own-repo "test-repo" "Test repository" true
```

### Dry Run Mode
Test commands without actually executing them:
```bash
./gogs -d create-own-repo "test-repo" "Test repository" true
```

## User Management

### Creating Users (Admin Only)
```bash
./gogs create-user "newuser" "user@example.com" "securepassword"
```

### Getting User Information
```bash
# Get all user information
./gogs get-user "username"

# Get specific property
./gogs get-user "username" "email"
./gogs get-user "username" "full_name"
```

### Deleting Users (Admin Only)
```bash
./gogs delete-user "username"
```

## Organization Management

### Creating Your Own Organization
```bash
./gogs create-own-org "my-organization"
```

### Creating Organization for Another User (Admin Only)
```bash
./gogs create-org "new-org" "owner-username"
```

### Getting Organization Information
```bash
# Get all organization information
./gogs get-org "organization-name"

# Get specific property
./gogs get-org "organization-name" "description"
```

## Repository Management

### Creating Repositories

#### Create Repository Under Your Account
```bash
./gogs create-own-repo "repo-name" "Repository description" true
```
- `true` = private repository
- `false` = public repository

#### Create Repository Under Organization
```bash
./gogs create-org-repo "org-name" "repo-name" "Description" false
```

#### Create Repository for Any User/Org (Admin Only)
```bash
./gogs create-repo "username" "repo-name" "Description" true
```

#### Create Mirrored Repository
```bash
./gogs create-mirror-repo "https://github.com/user/repo.git" "local-repo-name" "username" true true "Mirrored repository"
```

### Listing Repositories

#### List Your Own Repositories
```bash
./gogs list-own-repos
```

#### List Organization Repositories
```bash
./gogs list-org-repos "organization-name"
```

#### List User Repositories
```bash
./gogs list-user-repos "username"
```

### Getting Repository Information
```bash
# Get all repository information
./gogs get-repo "username" "repo-name"

# Get specific property
./gogs get-repo "username" "repo-name" "description"
./gogs get-repo "username" "repo-name" "private"
```

### Deleting Repositories
```bash
./gogs delete-repo "username" "repo-name"
```

## Collaboration Management

### Adding Collaborators
```bash
./gogs add-collaborator "username" "repo-name" "collaborator-username" "write"
```
Permissions: `read`, `write`, or `admin`

### Listing Collaborators
```bash
# List collaborator usernames
./gogs list-collaborators "username" "repo-name"

# Get detailed collaborator information
./gogs get-collaborators "username" "repo-name"
./gogs get-collaborators "username" "repo-name" "permission"
```

### Removing Collaborators
```bash
./gogs remove-collaborator "username" "repo-name" "collaborator-username"
```

## Team Management

### Creating Teams (Admin Only)
```bash
./gogs create-team "organization-name" "team-name" "Team description" "write"
```
Permissions: `read`, `write`, or `admin`

### Listing Teams
```bash
# List team names
./gogs list-teams "organization-name"

# Get detailed team information
./gogs get-teams "organization-name"
./gogs get-teams "organization-name" "permission"
```

### Getting Specific Team Information
```bash
./gogs get-team "organization-name" "team-name"
./gogs get-team "organization-name" "team-name" "id"
```

### Team Member Management (Admin Only)

#### Adding Team Members
```bash
# Using team name
./gogs add-team-member "organization-name" "team-name" "username"

# Using team ID
./gogs add-team-member "123" "username"
```

#### Listing Team Members
```bash
# List member usernames
./gogs list-team-members "organization-name" "team-name"

# Get detailed member information
./gogs get-team-members "organization-name" "team-name"
```

#### Removing Team Members
```bash
./gogs remove-team-member "organization-name" "team-name" "username"
```

### Team Repository Management (Admin Only)

#### Adding Repositories to Teams
```bash
./gogs add-team-repo "organization-name" "team-name" "repo-name"
```

#### Removing Repositories from Teams
```bash
./gogs remove-team-repo "organization-name" "team-name" "repo-name"
```

## Advanced Features

### Working with Commits
```bash
# Get commit information
./gogs get-commit "username" "repo-name" "main"
./gogs get-commit "username" "repo-name" "abc123def"
./gogs get-commit "username" "repo-name" "v1.0.0"
```

### Getting Raw File Content
```bash
./gogs get-raw "username" "repo-name" "main" "README.md"
./gogs get-raw "username" "repo-name" "abc123def" "src/main.py"
```

### Managing Issues

#### Enable/Disable Issues
```bash
# Enable issues
./gogs enable-issues "username" "repo-name" true

# Disable issues
./gogs enable-issues "username" "repo-name" false
```

#### Creating Issues
```bash
# Create a simple issue with just a title
./gogs create-issue "username" "repo-name" "Bug Report"

# Create an issue with title and description
./gogs create-issue "username" "repo-name" "Feature Request" "Please add support for dark mode"
```

#### Listing Issues
```bash
# List open issues (default)
./gogs list-issues "username" "repo-name"

# List closed issues
./gogs list-issues "username" "repo-name" "closed"

# List all issues (open and closed)
./gogs list-issues "username" "repo-name" "all"
```

#### Getting Issue Details
```bash
# Get all issue information
./gogs get-issue "username" "repo-name" "1"

# Get specific issue properties
./gogs get-issue "username" "repo-name" "1" "title"
./gogs get-issue "username" "repo-name" "1" "body"
./gogs get-issue "username" "repo-name" "1" "state"
```

#### Updating Issues
```bash
# Update issue title
./gogs update-issue "username" "repo-name" "1" "title" "Updated Bug Report"

# Update issue state (open/closed)
./gogs update-issue "username" "repo-name" "1" "state" "closed"

# Update issue description
./gogs update-issue "username" "repo-name" "1" "body" "Updated description with more details"
```

#### Issue Comments
```bash
# Add a comment to an issue
./gogs add-issue-comment "username" "repo-name" "1" "This is a helpful comment"

# List all comments on an issue
./gogs list-issue-comments "username" "repo-name" "1"
```

### Managing Pull Requests

#### Creating Pull Requests
```bash
# Create a pull request
./gogs create-pull-request "username" "repo-name" "Feature Branch" "feature-branch" "main"

# Create a pull request with description
./gogs create-pull-request "username" "repo-name" "Bug Fix" "bugfix-branch" "main" "Fixes the critical bug in authentication"
```

#### Listing Pull Requests
```bash
# List open pull requests (default)
./gogs list-pull-requests "username" "repo-name"

# List closed pull requests
./gogs list-pull-requests "username" "repo-name" "closed"

# List all pull requests
./gogs list-pull-requests "username" "repo-name" "all"
```

#### Getting Pull Request Details
```bash
# Get all pull request information
./gogs get-pull-request "username" "repo-name" "1"

# Get specific pull request properties
./gogs get-pull-request "username" "repo-name" "1" "title"
./gogs get-pull-request "username" "repo-name" "1" "body"
./gogs get-pull-request "username" "repo-name" "1" "state"
./gogs get-pull-request "username" "repo-name" "1" "head"
./gogs get-pull-request "username" "repo-name" "1" "base"
```

#### Updating Pull Requests
```bash
# Update pull request title
./gogs update-pull-request "username" "repo-name" "1" "title" "Updated Feature Request"

# Update pull request state (open/closed)
./gogs update-pull-request "username" "repo-name" "1" "state" "closed"

# Update pull request description
./gogs update-pull-request "username" "repo-name" "1" "body" "Updated description with implementation details"
```

#### Pull Request Comments
```bash
# Add a comment to a pull request
./gogs add-pull-request-comment "username" "repo-name" "1" "Great work! Just a small suggestion..."

# List all comments on a pull request
./gogs list-pull-request-comments "username" "repo-name" "1"
```

#### Merging Pull Requests
```bash
# Merge a pull request with default message
./gogs merge-pull-request "username" "repo-name" "1"

# Merge a pull request with custom message
./gogs merge-pull-request "username" "repo-name" "1" "Merge feature branch with bug fixes"
```

### API Token Management
```bash
# List all API tokens for a user
./gogs list-api-tokens "username" "password"

# Create new API token
./gogs create-api-token "username" "Token Name" "password"
```

## Troubleshooting

### Common Issues

#### "No Gogs server URL configured"
**Solution:** Set the server URL using one of the configuration methods above.

#### "No Gogs API token configured"
**Solution:** Create and configure an API token using one of the methods above.

#### "User does NOT exist" or "Repository does NOT exist"
**Solution:** Verify the user/repository names are correct. Use debug mode to see the exact API calls:
```bash
./gogs -D get-user "username"
```

#### "Token could NOT be created. Wrong password?"
**Solution:** Verify the username and password are correct for the create-api-token command.

#### Permission Denied Errors
**Solution:** Ensure your API token has the necessary permissions for the operation you're trying to perform.

### Debug Mode
Always use debug mode when troubleshooting:
```bash
./gogs -D <action> <arguments>
```

This will show:
- Exact API endpoints being called
- Request data being sent
- Response from the server
- Any error messages

### Checking Configuration
```bash
./gogs
```
This will show your current configuration and authentication status.

## Best Practices

### Security
1. **Use environment variables** for API tokens in production
2. **Use encrypted files** for sensitive configurations
3. **Never commit API tokens** to version control
4. **Use minimal required permissions** for API tokens

### Configuration Management
```bash
# Create a configuration script
cat > setup-gogs.sh << 'EOF'
#!/bin/bash
export GOGS_SERVER_URL="https://your-gogs-server.com"
export GOGS_API_TOKEN="your-token-here"
EOF

# Source it when needed
source setup-gogs.sh
```

### Error Handling
1. **Always check return codes** in scripts
2. **Use dry-run mode** before executing destructive operations
3. **Enable debug mode** when troubleshooting
4. **Test with non-critical data** first

### Scripting Examples

#### Batch Repository Creation
```bash
#!/bin/bash
repos=("project1" "project2" "project3")

for repo in "${repos[@]}"; do
    echo "Creating repository: $repo"
    ./gogs create-own-repo "$repo" "Description for $repo" true
    if [ $? -eq 0 ]; then
        echo "✓ Successfully created $repo"
    else
        echo "✗ Failed to create $repo"
    fi
done
```

#### Backup Repository List
```bash
#!/bin/bash
echo "Backing up repository list..."
./gogs list-own-repos > my-repos-backup.txt
echo "Repository list saved to my-repos-backup.txt"
```

#### Team Setup Automation
```bash
#!/bin/bash
ORG="my-organization"
TEAM="developers"
MEMBERS=("user1" "user2" "user3")

# Create team
./gogs create-team "$ORG" "$TEAM" "Development team" "write"

# Add members
for member in "${MEMBERS[@]}"; do
    ./gogs add-team-member "$ORG" "$TEAM" "$member"
done

# Add repositories to team
./gogs add-team-repo "$ORG" "$TEAM" "project1"
./gogs add-team-repo "$ORG" "$TEAM" "project2"
```

#### Issue Management Automation
```bash
#!/bin/bash
REPO_OWNER="username"
REPO_NAME="my-project"

# Create multiple issues from a list
issues=(
    "Bug: Login fails with special characters"
    "Feature: Add dark mode support"
    "Enhancement: Improve error messages"
)

for issue in "${issues[@]}"; do
    echo "Creating issue: $issue"
    ./gogs create-issue "$REPO_OWNER" "$REPO_NAME" "$issue"
    sleep 1  # Rate limiting
done

# Close old issues
echo "Closing issues older than 30 days..."
./gogs list-issues "$REPO_OWNER" "$REPO_NAME" "open" | while read -r line; do
    issue_num=$(echo "$line" | cut -d' ' -f1)
    if [ -n "$issue_num" ]; then
        echo "Closing issue #$issue_num"
        ./gogs update-issue "$REPO_OWNER" "$REPO_NAME" "$issue_num" "state" "closed"
    fi
done
```

#### Pull Request Workflow Automation
```bash
#!/bin/bash
REPO_OWNER="username"
REPO_NAME="my-project"
FEATURE_BRANCH="feature/new-feature"
BASE_BRANCH="main"

# Create a pull request
echo "Creating pull request for $FEATURE_BRANCH..."
PR_NUMBER=$(./gogs create-pull-request "$REPO_OWNER" "$REPO_NAME" "New Feature Implementation" "$FEATURE_BRANCH" "$BASE_BRANCH" "This PR implements the new feature as requested in issue #123")

# Add a comment to the PR
./gogs add-pull-request-comment "$REPO_OWNER" "$REPO_NAME" "$PR_NUMBER" "This PR is ready for review. Please test the new functionality."

# Auto-merge if all checks pass (example)
echo "Waiting for CI checks to complete..."
sleep 30

# Check if PR is mergeable and merge
if ./gogs get-pull-request "$REPO_OWNER" "$REPO_NAME" "$PR_NUMBER" "mergeable" | grep -q "true"; then
    echo "Merging pull request #$PR_NUMBER..."
    ./gogs merge-pull-request "$REPO_OWNER" "$REPO_NAME" "$PR_NUMBER" "Auto-merge: Feature implementation complete"
else
    echo "Pull request #$PR_NUMBER is not mergeable"
fi
```

#### Issue and PR Reporting
```bash
#!/bin/bash
REPO_OWNER="username"
REPO_NAME="my-project"

echo "=== Repository Status Report ==="
echo "Repository: $REPO_OWNER/$REPO_NAME"
echo

# Issue statistics
echo "=== Issue Statistics ==="
open_issues=$(./gogs list-issues "$REPO_OWNER" "$REPO_NAME" "open" | wc -l)
closed_issues=$(./gogs list-issues "$REPO_OWNER" "$REPO_NAME" "closed" | wc -l)
echo "Open issues: $open_issues"
echo "Closed issues: $closed_issues"
echo

# Pull request statistics
echo "=== Pull Request Statistics ==="
open_prs=$(./gogs list-pull-requests "$REPO_OWNER" "$REPO_NAME" "open" | wc -l)
closed_prs=$(./gogs list-pull-requests "$REPO_OWNER" "$REPO_NAME" "closed" | wc -l)
echo "Open pull requests: $open_prs"
echo "Closed pull requests: $closed_prs"
echo

# Recent activity
echo "=== Recent Open Issues ==="
./gogs list-issues "$REPO_OWNER" "$REPO_NAME" "open" | head -5

echo
echo "=== Recent Open Pull Requests ==="
./gogs list-pull-requests "$REPO_OWNER" "$REPO_NAME" "open" | head -5
```

### Integration with CI/CD
```bash
# In your CI/CD pipeline
export GOGS_SERVER_URL="$GOGS_SERVER"
export GOGS_API_TOKEN="$GOGS_TOKEN"

# Create release repository
./gogs create-org-repo "$ORG" "$REPO_NAME" "Release repository" true

# Add team access
./gogs add-team-repo "$ORG" "release-team" "$REPO_NAME"
```

This comprehensive guide should help you get started with the Gogs CLI and use it effectively for managing your Gogs server. For additional help, use the built-in help system with `./gogs <action> --help`.
