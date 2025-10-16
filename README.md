# Gogs CLI

A comprehensive command-line interface for interacting with Gogs servers via their [REST API v1](https://github.com/gogs/docs-api).

## Quick Start

```bash
# Show all available actions
./gogs

# Show help for a specific action
./gogs <action> --help

# Run an action
./gogs <action> <arguments...>
```

## Features

### 🔐 Authentication & API Tokens
- **create-api-token** - Create new API tokens for users
- **list-api-tokens** - List existing API tokens
- **get-api-token-user** - Get current authenticated user info

### 👥 User Management
- **create-user** *(admin)* - Create new users
- **get-user** - Get user information
- **delete-user** *(admin)* - Delete users

### 🏢 Organization Management
- **create-own-org** - Create your own organization
- **create-org** *(admin)* - Create organization for any user
- **get-org** - Get organization information

### 📁 Repository Management
- **create-own-repo** - Create repository under your account
- **create-org-repo** - Create repository under organization
- **create-repo** *(admin)* - Create repository for any user/org
- **create-mirror-repo** - Create mirrored repository from external source
- **list-own-repos** - List your repositories
- **list-org-repos** - List organization repositories
- **list-user-repos** - List user repositories
- **get-repo** - Get repository information
- **delete-repo** - Delete repository

### 🤝 Repository Collaboration
- **add-collaborator** - Add user as repository collaborator
- **list-collaborators** - List repository collaborators
- **get-collaborators** - Get detailed collaborator information
- **remove-collaborator** - Remove repository collaborator

### 👥 Team Management
- **create-team** *(admin)* - Create organization team
- **list-teams** - List organization teams
- **get-teams** - Get detailed team information
- **get-team** - Get specific team information

### 👤 Team Member Management
- **add-team-member** *(admin)* - Add user to team
- **list-team-members** *(admin)* - List team members
- **get-team-members** *(admin)* - Get detailed team member information
- **remove-team-member** *(admin)* - Remove user from team

### 📚 Team Repository Management
- **add-team-repo** *(admin)* - Add repository to team
- **remove-team-repo** *(admin)* - Remove repository from team

### 📄 Content & Issues
- **get-commit** - Get commit information
- **get-raw** - Get raw file content
- **enable-issues** - Enable/disable repository issues

### 🐛 Issue Management
- **create-issue** - Create new issues
- **list-issues** - List repository issues
- **get-issue** - Get issue details
- **update-issue** - Update issue title, state, or body
- **add-issue-comment** - Add comments to issues
- **list-issue-comments** - List issue comments

### 🔀 Pull Request Management
- **create-pull-request** - Create new pull requests
- **list-pull-requests** - List repository pull requests
- **get-pull-request** - Get pull request details
- **update-pull-request** - Update PR title, state, or body
- **add-pull-request-comment** - Add comments to pull requests
- **list-pull-request-comments** - List PR comments
- **merge-pull-request** - Merge pull requests

## Configuration

### Server URL
Set your Gogs server URL using one of these methods:

**Environment Variable:**
```bash
export GOGS_SERVER_URL="https://try.gogs.io"
```

**Configuration File:**
```bash
echo "https://try.gogs.io" > gogs-server-url.txt
```

### API Token
Create an API token at `https://YOUR.GOGS.SERVER/user/settings/applications` and set it using:

**Environment Variable:**
```bash
export GOGS_API_TOKEN="your-api-token-here"
```

**Configuration File:**
```bash
echo "your-api-token-here" > gogs-api-token.txt
```

**Encrypted Configuration File:**
```bash
# make sure gpg can talk to your terminal
export GPG_TTY=$(tty)

# let gpg read the input file directly so it can pop the passphrase prompt
gpg -c -o gogs-api-token.txt.gpg gogs-api-token.txt && \
rm -i gogs-api-token.txt
```

## Command Line Options

- `-h, --help` - Show help for the action
- `-d, --dry-run` - Preview commands without executing them
- `-D, --debug` - Enable debug output with detailed API calls
- `-n, --no-checks` - Disable sanity checks (use with caution)

## Examples

```bash
# Create a new repository
./gogs create-own-repo "my-project" "A sample project" true

# List all your repositories
./gogs list-own-repos

# Add a collaborator to a repository
./gogs add-collaborator "username" "my-project" "collaborator" "write"

# Create an organization team
./gogs create-team "my-org" "developers" "Development team" "write"

# Get raw file content
./gogs get-raw "username" "my-project" "main" "README.md"

# Create an issue
./gogs create-issue "username" "my-project" "Bug Report" "Description of the bug"

# List open issues
./gogs list-issues "username" "my-project"

# Add comment to issue
./gogs add-issue-comment "username" "my-project" "1" "This is a comment"

# Create a pull request
./gogs create-pull-request "username" "my-project" "Feature Request" "feature-branch" "main" "Description of changes"

# List open pull requests
./gogs list-pull-requests "username" "my-project"

# Merge a pull request
./gogs merge-pull-request "username" "my-project" "1" "Merge feature branch"
```

## Requirements

- Bash shell
- `curl` command-line tool
- `gpg` (optional, for encrypted token storage)

## Limitations

Some Gogs REST API actions are not yet available (like organization team deletion). This CLI covers the most commonly used operations. Pull requests are welcome to add more functionality!

## License

See [LICENSE](LICENSE) file for details.
