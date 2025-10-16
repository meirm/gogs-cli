# Security Guide for Gogs CLI

This document explains how to securely manage API tokens and other sensitive configuration data when using the Gogs CLI.

## Table of Contents

1. [Why Encrypt API Tokens?](#why-encrypt-api-tokens)
2. [Setting Up Encrypted API Tokens](#setting-up-encrypted-api-tokens)
3. [Using Encrypted Tokens in Scripts](#using-encrypted-tokens-in-scripts)
4. [Security Best Practices](#security-best-practices)
5. [Troubleshooting](#troubleshooting)
6. [Advanced Security Features](#advanced-security-features)

## Why Encrypt API Tokens?

API tokens are sensitive credentials that provide access to your Gogs server. Encrypting them protects against:

- **Accidental exposure**: Prevents tokens from being visible in plain text files
- **Version control leaks**: Protects against accidentally committing sensitive data
- **File system access**: Secures tokens even if someone gains file system access
- **Compliance requirements**: Meets security standards for credential storage
- **Audit trails**: Provides better security logging and monitoring

## Setting Up Encrypted API Tokens

### Prerequisites

Ensure you have GPG installed and configured:

```bash
# Check if GPG is installed
gpg --version

# If not installed, install it:
# On macOS: brew install gnupg
# On Ubuntu/Debian: sudo apt-get install gnupg
# On CentOS/RHEL: sudo yum install gnupg
```

### Method 1: Direct Encryption (Recommended)

This is the most secure method as it avoids creating temporary plain text files:

```bash
# Create the encrypted token file directly
echo "your-api-token-here" | gpg -c -o gogs-api-token.txt.gpg
```

### Method 2: File-Based Encryption (Enhanced Security)

This method provides better control over the encryption process:

```bash
# Step 1: Create a temporary plain text file
echo "your-api-token-here" > gogs-api-token.txt

# Step 2: Make sure GPG can communicate with your terminal
export GPG_TTY=$(tty)

# Step 3: Encrypt the file and remove the plain text version
gpg -c -o gogs-api-token.txt.gpg gogs-api-token.txt && \
rm -i gogs-api-token.txt
```

**Why this method is better:**
- `export GPG_TTY=$(tty)` ensures GPG can display the passphrase prompt properly
- `gpg -c` reads the input file directly, allowing for proper passphrase prompting
- `rm -i` safely removes the plain text file with confirmation
- No temporary files are left in memory or on disk

### Method 3: Batch Setup Script

Create a setup script for multiple environments:

```bash
#!/bin/bash
# setup-encrypted-token.sh

set -e  # Exit on any error

echo "Setting up encrypted Gogs API token..."

# Check if GPG is available
if ! command -v gpg &> /dev/null; then
    echo "Error: GPG is not installed. Please install it first."
    exit 1
fi

# Set up GPG terminal communication
export GPG_TTY=$(tty)

# Get the API token
read -sp "Enter your Gogs API token: " api_token
echo

if [ -z "$api_token" ]; then
    echo "Error: No API token provided."
    exit 1
fi

# Create temporary file
echo "$api_token" > gogs-api-token.txt

# Encrypt the token
echo "Encrypting token..."
if gpg -c -o gogs-api-token.txt.gpg gogs-api-token.txt; then
    echo "✓ Token encrypted successfully"
    
    # Remove plain text file
    rm gogs-api-token.txt
    echo "✓ Plain text file removed"
    
    # Set proper permissions
    chmod 600 gogs-api-token.txt.gpg
    echo "✓ File permissions set to 600"
    
    echo "Setup complete! Your encrypted token is stored in gogs-api-token.txt.gpg"
else
    echo "Error: Failed to encrypt token"
    rm -f gogs-api-token.txt
    exit 1
fi
```

## Using Encrypted Tokens in Scripts

### Basic Usage

The Gogs CLI automatically handles encrypted tokens:

```bash
# The CLI will automatically decrypt the token when needed
./gogs list-own-repos
```

### Script Integration

#### Method 1: Environment Variable (Recommended for Scripts)

```bash
#!/bin/bash
# my-gogs-script.sh

set -e

# Ensure GPG can communicate with terminal
export GPG_TTY=$(tty)

# Decrypt token and set environment variable
echo "Decrypting API token..."
export GOGS_API_TOKEN=$(gpg -q -d gogs-api-token.txt.gpg)
export GOGS_SERVER_URL="https://your-gogs-server.com"

# Verify token is set
if [ -z "$GOGS_API_TOKEN" ]; then
    echo "Error: Failed to decrypt API token"
    exit 1
fi

# Use the CLI
echo "Listing repositories..."
./gogs list-own-repos

# Create a new repository
./gogs create-own-repo "my-project" "Project description" true

# Clean up sensitive data
unset GOGS_API_TOKEN
echo "Script completed successfully"
```

#### Method 2: Function-Based Approach

```bash
#!/bin/bash
# gogs-helper.sh

# Function to securely get the API token
get_gogs_token() {
    local token_file="gogs-api-token.txt.gpg"
    
    if [ ! -f "$token_file" ]; then
        echo "Error: Encrypted token file not found: $token_file" >&2
        return 1
    fi
    
    # Ensure GPG can communicate with terminal
    export GPG_TTY=$(tty)
    
    # Decrypt and return token
    gpg -q -d "$token_file" 2>/dev/null
}

# Function to run Gogs commands with automatic token handling
gogs_cmd() {
    local token
    token=$(get_gogs_token)
    
    if [ -z "$token" ]; then
        echo "Error: Failed to decrypt API token" >&2
        return 1
    fi
    
    # Set environment variables
    export GOGS_API_TOKEN="$token"
    export GOGS_SERVER_URL="https://your-gogs-server.com"
    
    # Run the command
    ./gogs "$@"
    
    # Clean up
    unset GOGS_API_TOKEN
}

# Usage examples:
# gogs_cmd list-own-repos
# gogs_cmd create-own-repo "project" "description" true
```

#### Method 3: Batch Operations

```bash
#!/bin/bash
# batch-repo-creation.sh

set -e

# Setup
export GPG_TTY=$(tty)
export GOGS_API_TOKEN=$(gpg -q -d gogs-api-token.txt.gpg)
export GOGS_SERVER_URL="https://your-gogs-server.com"

# Verify setup
if [ -z "$GOGS_API_TOKEN" ]; then
    echo "Error: Failed to decrypt API token"
    exit 1
fi

# Define repositories to create
repos=(
    "project1:Description for project 1:true"
    "project2:Description for project 2:false"
    "project3:Description for project 3:true"
)

# Create repositories
for repo_info in "${repos[@]}"; do
    IFS=':' read -r name description private <<< "$repo_info"
    
    echo "Creating repository: $name"
    if ./gogs create-own-repo "$name" "$description" "$private"; then
        echo "✓ Successfully created $name"
    else
        echo "✗ Failed to create $name"
    fi
    echo
done

# Clean up
unset GOGS_API_TOKEN
echo "Batch operation completed"
```

## Security Best Practices

### 1. File Permissions

Always set restrictive permissions on encrypted files:

```bash
# Set permissions to owner-read-only
chmod 600 gogs-api-token.txt.gpg

# Verify permissions
ls -la gogs-api-token.txt.gpg
# Should show: -rw------- 1 user user
```

### 2. GPG Configuration

Configure GPG for better security:

```bash
# Create or edit ~/.gnupg/gpg.conf
cat >> ~/.gnupg/gpg.conf << EOF
# Use stronger cipher
cipher-algo AES256

# Use stronger digest
digest-algo SHA512

# Use stronger compression
compress-algo 1

# Disable weak algorithms
disable-cipher-algo 3DES
disable-cipher-algo BLOWFISH
disable-cipher-algo CAST5
disable-cipher-algo IDEA
EOF
```

### 3. Passphrase Security

- Use a strong, unique passphrase
- Consider using a password manager
- Never share your passphrase
- Change passphrases periodically

### 4. Environment Variable Management

```bash
# Always unset sensitive environment variables
unset GOGS_API_TOKEN

# Use 'env' to check for sensitive variables
env | grep -i gogs
```

### 5. Script Security

```bash
#!/bin/bash
# secure-gogs-script.sh

# Exit on any error
set -e

# Disable command history for this session
set +H

# Clear sensitive data on exit
cleanup() {
    unset GOGS_API_TOKEN
    echo "Cleaned up sensitive data"
}
trap cleanup EXIT

# Your script logic here...
```

### 6. Audit and Monitoring

```bash
# Check for plain text token files
find . -name "*.txt" -exec grep -l "gogs.*token" {} \; 2>/dev/null

# Verify encrypted files exist
ls -la gogs-api-token.txt.gpg

# Check file permissions
stat -c "%a %n" gogs-api-token.txt.gpg
```

## Troubleshooting

### Common Issues

#### 1. "gpg: can't connect to the agent"

**Solution:**
```bash
# Start GPG agent
gpg-agent --daemon

# Or restart it
pkill gpg-agent
gpg-agent --daemon
```

#### 2. "gpg: no such file or directory"

**Solution:**
```bash
# Check if GPG is installed
which gpg

# Install GPG if missing
# macOS: brew install gnupg
# Ubuntu: sudo apt-get install gnupg
```

#### 3. Passphrase prompt not appearing

**Solution:**
```bash
# Set GPG_TTY environment variable
export GPG_TTY=$(tty)

# Verify terminal connection
echo $GPG_TTY
```

#### 4. "gpg: decryption failed: No secret key"

**Solution:**
```bash
# Check if you have the right key
gpg --list-secret-keys

# Re-encrypt with the correct key
gpg -c -o gogs-api-token.txt.gpg gogs-api-token.txt
```

#### 5. Permission denied errors

**Solution:**
```bash
# Fix file permissions
chmod 600 gogs-api-token.txt.gpg

# Check ownership
ls -la gogs-api-token.txt.gpg
```

### Debug Mode

Use debug mode to troubleshoot issues:

```bash
# Enable debug output
./gogs -D list-own-repos

# Check GPG operations
gpg -v -d gogs-api-token.txt.gpg
```

## Advanced Security Features

### 1. Using GPG Key Pairs

For enhanced security, use asymmetric encryption:

```bash
# Generate a GPG key pair (if you don't have one)
gpg --gen-key

# List your keys
gpg --list-keys

# Encrypt with your public key
gpg -e -r your-email@example.com -o gogs-api-token.txt.gpg gogs-api-token.txt

# Decrypt with your private key
gpg -d gogs-api-token.txt.gpg
```

### 2. Multiple Environment Support

```bash
#!/bin/bash
# multi-env-setup.sh

ENVIRONMENTS=("dev" "staging" "prod")

for env in "${ENVIRONMENTS[@]}"; do
    echo "Setting up token for $env environment..."
    
    read -sp "Enter API token for $env: " token
    echo
    
    if [ -n "$token" ]; then
        echo "$token" | gpg -c -o "gogs-api-token-$env.txt.gpg"
        chmod 600 "gogs-api-token-$env.txt.gpg"
        echo "✓ Token encrypted for $env"
    fi
done
```

### 3. Automated Token Rotation

```bash
#!/bin/bash
# rotate-token.sh

# This script would integrate with your token management system
# to automatically rotate encrypted tokens

OLD_TOKEN_FILE="gogs-api-token.txt.gpg"
NEW_TOKEN_FILE="gogs-api-token-new.txt.gpg"

# Get new token from your token management system
NEW_TOKEN=$(get_new_token_from_vault)

# Encrypt new token
echo "$NEW_TOKEN" | gpg -c -o "$NEW_TOKEN_FILE"

# Test new token
export GOGS_API_TOKEN=$(gpg -q -d "$NEW_TOKEN_FILE")
if ./gogs get-api-token-user > /dev/null 2>&1; then
    # Replace old token
    mv "$NEW_TOKEN_FILE" "$OLD_TOKEN_FILE"
    echo "✓ Token rotated successfully"
else
    rm "$NEW_TOKEN_FILE"
    echo "✗ New token failed validation"
    exit 1
fi
```

### 4. Integration with Secret Management Systems

```bash
#!/bin/bash
# vault-integration.sh

# Example integration with HashiCorp Vault
VAULT_ADDR="https://vault.example.com"
VAULT_TOKEN="your-vault-token"

# Get token from Vault
API_TOKEN=$(vault kv get -field=token secret/gogs/api-token)

# Encrypt and store locally
echo "$API_TOKEN" | gpg -c -o gogs-api-token.txt.gpg
chmod 600 gogs-api-token.txt.gpg

echo "✓ Token retrieved from Vault and encrypted locally"
```

## Summary

The encrypted API token feature in Gogs CLI provides enterprise-grade security for managing sensitive credentials. By following the practices outlined in this guide, you can:

- Protect your API tokens from unauthorized access
- Integrate securely with automated scripts and CI/CD pipelines
- Meet compliance requirements for credential storage
- Maintain security while preserving usability

Remember to always use strong passphrases, set proper file permissions, and follow the principle of least privilege when managing your encrypted tokens.
