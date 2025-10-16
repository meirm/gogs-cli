# Gogs CLI - Release Notes

## Version 0.1.0 - Initial Stable Release

**Release Date:** October 16, 2025

### 🎉 First Stable Release

This is the first stable release of the Gogs CLI tool, providing a comprehensive command-line interface for interacting with Gogs servers via their REST API v1.

### ✨ Features

#### 🔐 Authentication & API Tokens
- **create-api-token** - Create new API tokens for users
- **list-api-tokens** - List existing API tokens  
- **get-api-token-user** - Get current authenticated user info

#### 👥 User Management
- **create-user** *(admin)* - Create new users
- **get-user** - Get user information
- **delete-user** *(admin)* - Delete users

#### 🏢 Organization Management
- **create-own-org** - Create your own organization
- **create-org** *(admin)* - Create organization for any user
- **get-org** - Get organization information

#### 📁 Repository Management
- **create-own-repo** - Create repository under your account
- **create-org-repo** - Create repository under organization
- **create-repo** *(admin)* - Create repository for any user/org
- **create-mirror-repo** - Create mirrored repository from external source
- **list-own-repos** - List your repositories
- **list-org-repos** - List organization repositories
- **list-user-repos** - List user repositories
- **get-repo** - Get repository information
- **delete-repo** - Delete repository

#### 🤝 Repository Collaboration
- **add-collaborator** - Add user as repository collaborator
- **list-collaborators** - List repository collaborators
- **get-collaborators** - Get detailed collaborator information
- **remove-collaborator** - Remove repository collaborator

#### 👥 Team Management
- **create-team** *(admin)* - Create organization team
- **list-teams** - List organization teams
- **get-teams** - Get detailed team information
- **get-team** - Get specific team information

#### 👤 Team Member Management
- **add-team-member** *(admin)* - Add user to team
- **list-team-members** *(admin)* - List team members
- **get-team-members** *(admin)* - Get detailed team member information
- **remove-team-member** *(admin)* - Remove user from team

#### 📚 Team Repository Management
- **add-team-repo** *(admin)* - Add repository to team
- **remove-team-repo** *(admin)* - Remove repository from team

#### 📄 Content & Issues
- **get-commit** - Get commit information
- **get-raw** - Get raw file content
- **enable-issues** - Enable/disable repository issues

#### 🐛 Issue Management
- **create-issue** - Create new issues
- **list-issues** - List repository issues
- **get-issue** - Get issue details
- **update-issue** - Update issue title, state, or body
- **add-issue-comment** - Add comments to issues
- **list-issue-comments** - List issue comments

#### 🔀 Pull Request Management
- **create-pull-request** - Create new pull requests
- **list-pull-requests** - List repository pull requests
- **get-pull-request** - Get pull request details
- **update-pull-request** - Update PR title, state, or body
- **add-pull-request-comment** - Add comments to pull requests
- **list-pull-request-comments** - List PR comments
- **merge-pull-request** - Merge pull requests

### 🛠️ Configuration Options

#### Server URL Configuration
- Environment variable: `GOGS_SERVER_URL`
- Configuration file: `gogs-server-url.txt`

#### API Token Configuration
- Environment variable: `GOGS_API_TOKEN`
- Plain text file: `gogs-api-token.txt`
- Encrypted file: `gogs-api-token.txt.gpg` (using GPG)

### 🎛️ Command Line Options

- `-h, --help` - Show help for the action
- `-d, --dry-run` - Preview commands without executing them
- `-D, --debug` - Enable debug output with detailed API calls
- `-n, --no-checks` - Disable sanity checks (use with caution)

### 🐛 Bug Fixes in This Release

#### Critical Fixes
- **Fixed URL Construction**: Resolved double-slash issue in API URLs that was preventing all API calls from working
- **Fixed Response Capture**: Fixed issue where curl responses weren't being captured properly in non-debug mode
- **Fixed JSON Processing**: Replaced broken `json-property` function with reliable `sed`-based parsing
- **Fixed Authentication Display**: Resolved issue where user authentication info wasn't displaying in help output

#### API Call Improvements
- **Proper Error Handling**: All API calls now properly capture and return responses
- **Debug Mode Separation**: Debug output now goes to stderr, preventing interference with actual data
- **Response Processing**: Fixed repository listing and user information extraction

### 📋 Requirements

- Bash shell (version 4.0 or higher)
- `curl` command-line tool
- `gpg` (optional, for encrypted token storage)

### 🚀 Getting Started

1. **Download and Setup**:
   ```bash
   git clone <repository-url>
   cd gogs-cli
   chmod +x gogs
   ```

2. **Configure Server URL**:
   ```bash
   export GOGS_SERVER_URL="https://your-gogs-server.com"
   # OR
   echo "https://your-gogs-server.com" > gogs-server-url.txt
   ```

3. **Configure API Token**:
   ```bash
   export GOGS_API_TOKEN="your-api-token-here"
   # OR
   echo "your-api-token-here" > gogs-api-token.txt
   ```

4. **Test the Installation**:
   ```bash
   ./gogs list-own-repos
   ```

### 📖 Documentation

- **README.md** - Complete usage guide and examples
- **HOWTO.md** - Comprehensive how-to guide with best practices
- **Security.md** - Security considerations and recommendations

### 🔧 Examples

```bash
# List your repositories
./gogs list-own-repos

# Create a new repository
./gogs create-own-repo "my-project" "A sample project" true

# Add a collaborator
./gogs add-collaborator "username" "my-project" "collaborator" "write"

# Create an issue
./gogs create-issue "username" "my-project" "Bug Report" "Description of the bug"

# Create a pull request
./gogs create-pull-request "username" "my-project" "Feature Request" "feature-branch" "main" "Description of changes"
```

### 🏷️ Version Information

- **Version**: 0.1.0
- **API Compatibility**: Gogs REST API v1
- **License**: See LICENSE file for details

### 🤝 Contributing

This is the initial stable release. Future versions will include additional features and improvements. Pull requests are welcome!

### 📝 Notes

- This release has been tested with Gogs servers and is ready for production use
- All major functionality has been implemented and tested
- The CLI provides comprehensive coverage of the most commonly used Gogs API operations
- Some advanced features may require admin privileges on the Gogs server

---

**Full Changelog**: This is the initial release - all features are new in this version.
