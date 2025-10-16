# Getting Started with Gogs CLI

Welcome to the Gogs CLI! This guide will help you get up and running quickly with the command-line interface for managing your Gogs server.

## Table of Contents

1. [Quick Start](#quick-start)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [First Steps](#first-steps)
5. [Common Tasks](#common-tasks)
6. [Troubleshooting](#troubleshooting)
7. [Next Steps](#next-steps)

## Quick Start

If you're in a hurry, here's the fastest way to get started:

```bash
# 1. Make the script executable
chmod +x gogs

# 2. Set your server URL
export GOGS_SERVER_URL="https://your-gogs-server.com"

# 3. Set your API token
export GOGS_API_TOKEN="your-api-token-here"

# 4. Test it works
./gogs get-api-token-user
```

That's it! You're ready to use the Gogs CLI.

## Installation

### Prerequisites

Before you begin, ensure you have:

- **Bash shell** (version 4.0 or higher)
- **curl** command-line tool
- **gpg** (optional, for encrypted token storage)
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

   You should see the help output with all available actions.

### System Compatibility

The Gogs CLI works on:
- **Linux** (Ubuntu, CentOS, RHEL, etc.)
- **macOS** (Darwin)
- **Windows** (with WSL or Git Bash)

**Note for macOS users:** The script has been tested and fixed for BSD sed compatibility.

## Configuration

### Step 1: Set Your Gogs Server URL

Choose one of these methods:

#### Method 1: Environment Variable (Recommended)
```bash
export GOGS_SERVER_URL="https://try.gogs.io"
```

#### Method 2: Configuration File
```bash
echo "https://try.gogs.io" > gogs-server-url.txt
```

#### Method 3: Interactive Setup
If you don't configure anything, the CLI will ask you:
```bash
./gogs
# No Gogs server URL (e.g. https://try.gogs.io) configured.
# URL: https://try.gogs.io
```

### Step 2: Get an API Token

You need an API token to authenticate with your Gogs server.

#### Option A: Create via Web Interface
1. Go to `https://YOUR.GOGS.SERVER/user/settings/applications`
2. Click "Generate New Token"
3. Enter a token name (e.g., "CLI Access")
4. Select appropriate scopes
5. Copy the generated token

#### Option B: Create via CLI (if you have credentials)
```bash
./gogs create-api-token "your-username" "CLI Token" "your-password"
```

### Step 3: Configure Your API Token

Choose one of these methods:

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
# Create encrypted token file
echo "your-api-token-here" | gpg -c -o gogs-api-token.txt.gpg

# Or use the enhanced method for better terminal interaction
echo "your-api-token-here" > gogs-api-token.txt
export GPG_TTY=$(tty)
gpg -c -o gogs-api-token.txt.gpg gogs-api-token.txt && \
rm -i gogs-api-token.txt
```

#### Method 4: Interactive Setup
The CLI will prompt for the token if not configured:
```bash
./gogs
# No Gogs API token configured.
# Token: [hidden input]
```

## First Steps

### Verify Your Setup

1. **Check your configuration:**
   ```bash
   ./gogs
   ```
   This shows your current configuration and authentication status.

2. **Test authentication:**
   ```bash
   ./gogs get-api-token-user
   ```
   This should return your user information if everything is working.

3. **List your repositories:**
   ```bash
   ./gogs list-own-repos
   ```

### Understanding the Output

The CLI provides different output modes:

- **Normal mode**: Clean, formatted output
- **Debug mode** (`-D`): Shows detailed API calls and responses
- **Dry-run mode** (`-d`): Shows what would be done without executing

Try debug mode to see what's happening:
```bash
./gogs -D list-own-repos
```

## Common Tasks

### Repository Management

#### List Your Repositories
```bash
./gogs list-own-repos
```

#### Create a New Repository
```bash
./gogs create-own-repo "my-project" "A sample project" true
```
- `true` = private repository
- `false` = public repository

#### Get Repository Information
```bash
./gogs get-repo "your-username" "my-project"
```

#### Delete a Repository
```bash
./gogs delete-repo "your-username" "my-project"
```

### User Management

#### Get User Information
```bash
# Get your own information
./gogs get-api-token-user

# Get another user's information
./gogs get-user "username"
```

#### Create a User (Admin Only)
```bash
./gogs create-user "newuser" "user@example.com" "password"
```

### Organization Management

#### Create an Organization
```bash
./gogs create-own-org "my-organization"
```

#### List Organization Repositories
```bash
./gogs list-org-repos "my-organization"
```

### Issue Management

#### Create an Issue
```bash
./gogs create-issue "username" "repo-name" "Bug Report" "Description of the bug"
```

#### List Issues
```bash
./gogs list-issues "username" "repo-name"
```

#### Add a Comment to an Issue
```bash
./gogs add-issue-comment "username" "repo-name" "1" "This is a helpful comment"
```

### Pull Request Management

#### Create a Pull Request
```bash
./gogs create-pull-request "username" "repo-name" "Feature Request" "feature-branch" "main" "Description of changes"
```

#### List Pull Requests
```bash
./gogs list-pull-requests "username" "repo-name"
```

#### Merge a Pull Request
```bash
./gogs merge-pull-request "username" "repo-name" "1" "Merge feature branch"
```

## Troubleshooting

### Common Issues and Solutions

#### 1. "No Gogs server URL configured"
**Problem:** The CLI doesn't know which server to connect to.

**Solution:**
```bash
export GOGS_SERVER_URL="https://your-gogs-server.com"
```

#### 2. "No Gogs API token configured"
**Problem:** The CLI doesn't have authentication credentials.

**Solution:**
```bash
export GOGS_API_TOKEN="your-api-token-here"
```

#### 3. "User does NOT exist" or "Repository does NOT exist"
**Problem:** The specified user or repository doesn't exist.

**Solution:**
- Verify the names are correct
- Use debug mode to see the exact API calls:
  ```bash
  ./gogs -D get-user "username"
  ```

#### 4. "Token could NOT be created. Wrong password?"
**Problem:** Invalid credentials when creating API tokens.

**Solution:**
- Verify username and password are correct
- Check if the user has permission to create tokens

#### 5. Permission Denied Errors
**Problem:** Your API token doesn't have the necessary permissions.

**Solution:**
- Check your token's scopes in the web interface
- Create a new token with appropriate permissions

#### 6. sed: bad flag in substitute command (macOS)
**Problem:** This was a compatibility issue with BSD sed on macOS.

**Solution:** This has been fixed in the latest version. If you still see this error, make sure you have the latest version of the script.

### Debug Mode

Always use debug mode when troubleshooting:

```bash
./gogs -D <action> <arguments>
```

Debug mode shows:
- Exact API endpoints being called
- Request data being sent
- Response from the server
- Any error messages

### Checking Your Configuration

```bash
./gogs
```

This command shows:
- Your current server URL
- Your API token status (masked)
- Your authentication status
- Available actions

### Testing Connectivity

```bash
# Test basic connectivity
curl -I https://your-gogs-server.com

# Test API access
curl -H "Authorization: token your-api-token" https://your-gogs-server.com/api/v1/user
```

### Common Error Messages

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "No Gogs server URL configured" | Missing server configuration | Set `GOGS_SERVER_URL` environment variable |
| "No Gogs API token configured" | Missing authentication | Set `GOGS_API_TOKEN` environment variable |
| "User does NOT exist" | Invalid username | Verify the username is correct |
| "Repository does NOT exist" | Invalid repository name | Check the repository name and owner |
| "Token could NOT be created" | Invalid credentials | Verify username and password |
| "Permission denied" | Insufficient permissions | Check token scopes or user permissions |

## Next Steps

Now that you have the basics working, here are some next steps:

### 1. Explore Advanced Features

- **Team Management**: Create and manage organization teams
- **Collaboration**: Add collaborators to repositories
- **Content Management**: Get raw files, commit information
- **Automation**: Create scripts for common tasks

### 2. Read Additional Documentation

- **[Security.md](Security.md)**: Learn about secure token management
- **[HOWTO.md](HOWTO.md)**: Comprehensive guide with examples
- **[README.md](README.md)**: Complete feature reference

### 3. Create Useful Scripts

Start with simple automation:

```bash
#!/bin/bash
# backup-repos.sh

echo "Backing up repository list..."
./gogs list-own-repos > my-repos-backup.txt
echo "Repository list saved to my-repos-backup.txt"
```

### 4. Set Up Your Development Workflow

```bash
#!/bin/bash
# create-dev-repo.sh

REPO_NAME="$1"
if [ -z "$REPO_NAME" ]; then
    echo "Usage: $0 <repository-name>"
    exit 1
fi

echo "Creating development repository: $REPO_NAME"
./gogs create-own-repo "$REPO_NAME" "Development repository for $REPO_NAME" true

echo "Repository created successfully!"
echo "Clone URL: https://your-gogs-server.com/your-username/$REPO_NAME.git"
```

### 5. Integration with CI/CD

Set up environment variables in your CI/CD system:

```bash
# In your CI/CD pipeline
export GOGS_SERVER_URL="$GOGS_SERVER"
export GOGS_API_TOKEN="$GOGS_TOKEN"

# Use the CLI in your pipeline
./gogs create-org-repo "$ORG" "$REPO_NAME" "CI/CD managed repository" true
```

### 6. Join the Community

- Report issues and feature requests
- Contribute improvements
- Share your use cases and scripts

## Getting Help

If you need help:

1. **Check the documentation**: Start with this guide, then check the other documentation files
2. **Use debug mode**: Always use `-D` flag when troubleshooting
3. **Check your configuration**: Run `./gogs` to see your current setup
4. **Test connectivity**: Verify your server URL and API token work
5. **Search for similar issues**: Check if others have encountered the same problem

## Quick Reference

### Essential Commands
```bash
# Check configuration
./gogs

# Test authentication
./gogs get-api-token-user

# List repositories
./gogs list-own-repos

# Create repository
./gogs create-own-repo "name" "description" true

# Get help for specific action
./gogs create-own-repo --help
```

### Environment Variables
```bash
export GOGS_SERVER_URL="https://your-gogs-server.com"
export GOGS_API_TOKEN="your-api-token-here"
```

### Useful Options
- `-h, --help`: Show help
- `-d, --dry-run`: Preview without executing
- `-D, --debug`: Show detailed output
- `-n, --no-checks`: Disable sanity checks

---

**Congratulations!** You're now ready to use the Gogs CLI effectively. Start with simple commands and gradually explore more advanced features as you become comfortable with the tool.
