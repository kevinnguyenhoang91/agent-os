# Shell Script Style Guide

Based on Google Shell Style Guide with project-specific adaptations.

## General Guidelines

### When to Use Shell Scripts
- Use shell scripts for simple utilities and automation tasks
- Consider other languages (Python, Node.js) for complex logic
- Limit shell scripts to < 100 lines when possible
- Use shell for build scripts, deployment automation, and environment setup

### Shell Selection
- Use `#!/bin/bash` for all shell scripts
- Avoid `sh` unless strict POSIX compliance is required
- Use `#!/usr/bin/env bash` for portability when needed

```bash
#!/bin/bash
# Good - explicit bash requirement

# Description of what this script does
set -euo pipefail
```

## Script Structure

### Shebang and Headers
- Always start with proper shebang
- Include script description and usage
- Set error handling options early

```bash
#!/bin/bash
#
# Build and deploy Backstage application
#
# Usage: ./deploy.sh [environment] [--dry-run]
#
# Arguments:
#   environment  Target environment (dev, staging, prod)
#   --dry-run    Show what would be done without executing
#
# Example:
#   ./deploy.sh staging --dry-run

set -euo pipefail
```

### Error Handling
- Always use `set -euo pipefail` for robust error handling
- Handle errors explicitly for critical operations
- Provide meaningful error messages

```bash
#!/bin/bash
set -euo pipefail

# Good - explicit error handling
deploy_application() {
  local environment="$1"
  
  if ! validate_environment "$environment"; then
    echo "ERROR: Invalid environment: $environment" >&2
    echo "Valid environments: dev, staging, prod" >&2
    return 1
  fi
  
  if ! build_application; then
    echo "ERROR: Application build failed" >&2
    return 1
  fi
  
  echo "Deploying to $environment..."
}

# Good - trap for cleanup
cleanup() {
  echo "Cleaning up temporary files..."
  rm -f /tmp/deploy-$$.*
}
trap cleanup EXIT
```

## Variables and Naming

### Variable Naming
- Use lowercase with underscores for local variables
- Use UPPERCASE for environment variables and constants
- Use descriptive names, avoid abbreviations

```bash
# Good - descriptive variable names
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly CONFIG_FILE="${SCRIPT_DIR}/config.yaml"
readonly MAX_RETRY_COUNT=3

user_name="john_doe"
database_url="postgresql://localhost:5432/backstage"
build_timestamp="$(date +%Y%m%d_%H%M%S)"

# Bad - unclear abbreviations
cfg_file="/path/to/config"
usr="john"
db_url="postgres://..."
```

### Variable Declaration
- Use `local` for function variables
- Use `readonly` for constants
- Quote all variable expansions
- Use `declare` for arrays and associative arrays

```bash
# Good - proper variable declarations
readonly BACKUP_DIR="/var/backups"
readonly -a ENVIRONMENTS=("dev" "staging" "prod")
declare -A service_ports=(
  ["frontend"]="3000"
  ["backend"]="7007"
  ["postgres"]="5432"
)

process_user() {
  local user_id="$1"
  local user_name="$2"
  local readonly timestamp="$(date -Iseconds)"
  
  echo "Processing user: ${user_name} (ID: ${user_id}) at ${timestamp}"
}
```

### Quoting
- Quote all variable expansions unless you specifically need word splitting
- Use double quotes for variables, single quotes for literal strings
- Use `$()` for command substitution instead of backticks

```bash
# Good - proper quoting
readonly current_user="$(whoami)"
readonly script_name="$(basename "$0")"
readonly config_path="${HOME}/.backstage/config.yaml"

# Check if file exists
if [[ -f "$config_path" ]]; then
  echo "Loading config from: $config_path"
fi

# Good - array handling
readonly files=("file1.txt" "file with spaces.txt" "file3.txt")
for file in "${files[@]}"; do
  if [[ -f "$file" ]]; then
    echo "Processing: $file"
  fi
done

# Bad - unquoted variables
if [[ -f $config_path ]]; then  # Fails with spaces
  echo Loading config from: $config_path
fi
```

## Functions

### Function Definitions
- Use descriptive function names with underscores
- Document function purpose and parameters
- Use `local` for all function variables
- Return meaningful exit codes

```bash
# Good - well-documented function
#######################################
# Validates that the specified environment is supported
# Arguments:
#   $1 - Environment name to validate
# Returns:
#   0 if environment is valid, 1 otherwise
#######################################
validate_environment() {
  local environment="$1"
  local -a valid_environments=("dev" "staging" "prod")
  
  for valid_env in "${valid_environments[@]}"; do
    if [[ "$environment" == "$valid_env" ]]; then
      return 0
    fi
  done
  
  return 1
}

#######################################
# Builds the Backstage application
# Arguments:
#   None
# Returns:
#   0 on success, 1 on failure
# Outputs:
#   Build progress to stdout, errors to stderr
#######################################
build_application() {
  local readonly build_dir="${SCRIPT_DIR}/dist"
  
  echo "Building application..."
  
  if ! yarn install --frozen-lockfile; then
    echo "ERROR: Failed to install dependencies" >&2
    return 1
  fi
  
  if ! yarn build:all; then
    echo "ERROR: Build failed" >&2
    return 1
  fi
  
  echo "Build completed successfully"
  return 0
}
```

### Function Usage
- Check function arguments and provide usage info
- Use meaningful variable names for parameters
- Validate inputs before processing

```bash
# Good - parameter validation
deploy_service() {
  if [[ $# -lt 2 ]]; then
    echo "Usage: deploy_service <service_name> <environment>" >&2
    echo "Example: deploy_service backend prod" >&2
    return 1
  fi
  
  local readonly service_name="$1"
  local readonly environment="$2"
  local readonly image_tag="${3:-latest}"
  
  # Validate service name
  if [[ ! "$service_name" =~ ^[a-z][a-z0-9-]*$ ]]; then
    echo "ERROR: Invalid service name: $service_name" >&2
    echo "Service names must start with a letter and contain only lowercase letters, numbers, and hyphens" >&2
    return 1
  fi
  
  echo "Deploying $service_name:$image_tag to $environment"
}
```

## Control Structures

### Conditionals
- Use `[[ ]]` for tests instead of `[ ]`
- Put `then` and `do` on the same line as `if` and `while`
- Use explicit comparisons for clarity

```bash
# Good - modern bash conditionals
if [[ -f "$config_file" && -r "$config_file" ]]; then
  echo "Config file found and readable"
elif [[ -f "$config_file" ]]; then
  echo "ERROR: Config file exists but is not readable" >&2
  exit 1
else
  echo "Creating default config file..."
  create_default_config "$config_file"
fi

# Good - string comparisons
if [[ "$environment" == "prod" ]]; then
  echo "Production deployment requires confirmation"
  read -p "Continue? (y/N): " -r confirmation
  if [[ ! "$confirmation" =~ ^[Yy]$ ]]; then
    echo "Deployment cancelled"
    exit 0
  fi
fi

# Good - pattern matching
case "$command" in
  start)
    start_services
    ;;
  stop)
    stop_services
    ;;
  restart)
    stop_services
    start_services
    ;;
  status)
    show_service_status
    ;;
  *)
    echo "ERROR: Unknown command: $command" >&2
    echo "Usage: $0 {start|stop|restart|status}" >&2
    exit 1
    ;;
esac
```

### Loops
- Use appropriate loop constructs for the task
- Quote variables properly in loops
- Consider using arrays for lists of items

```bash
# Good - iterating over arrays
readonly services=("frontend" "backend" "postgres")
for service in "${services[@]}"; do
  if systemctl is-active --quiet "$service"; then
    echo "$service is running"
  else
    echo "Starting $service..."
    systemctl start "$service"
  fi
done

# Good - reading files line by line
while IFS= read -r line; do
  if [[ "$line" =~ ^[[:space:]]*# ]]; then
    continue  # Skip comments
  fi
  process_config_line "$line"
done < "$config_file"

# Good - C-style loop for numeric iteration
for ((i = 1; i <= MAX_RETRY_COUNT; i++)); do
  echo "Attempt $i of $MAX_RETRY_COUNT"
  if perform_operation; then
    echo "Operation succeeded"
    break
  elif [[ $i -eq $MAX_RETRY_COUNT ]]; then
    echo "ERROR: Operation failed after $MAX_RETRY_COUNT attempts" >&2
    exit 1
  else
    echo "Retrying in 5 seconds..."
    sleep 5
  fi
done
```

## Command Execution

### Command Substitution
- Use `$()` instead of backticks
- Quote command substitutions appropriately
- Handle command failures explicitly

```bash
# Good - modern command substitution
readonly current_branch="$(git rev-parse --abbrev-ref HEAD)"
readonly commit_hash="$(git rev-parse --short HEAD)"
readonly build_timestamp="$(date -Iseconds)"

# Good - handling command failures
if ! git_status="$(git status --porcelain 2>/dev/null)"; then
  echo "ERROR: Not in a git repository" >&2
  exit 1
fi

if [[ -n "$git_status" ]]; then
  echo "WARNING: Working directory has uncommitted changes" >&2
  echo "$git_status"
fi
```

### Pipeline and Command Chaining
- Use proper error handling with pipelines
- Break long pipelines into multiple lines
- Use meaningful intermediate variables

```bash
# Good - pipeline with error handling
set -o pipefail

# Get list of running containers
readonly running_containers="$(
  docker ps --format "table {{.Names}}\t{{.Status}}" \
    | grep -v NAMES \
    | sort
)"

# Good - breaking down complex operations
get_service_dependencies() {
  local readonly service_name="$1"
  local dependencies
  
  # Extract dependencies from docker-compose.yml
  dependencies="$(
    yq eval ".services.${service_name}.depends_on[]" docker-compose.yml 2>/dev/null \
      | grep -v "null" \
      | sort \
      | uniq
  )" || {
    echo "No dependencies found for $service_name"
    return 0
  }
  
  echo "$dependencies"
}
```

## Input/Output

### User Input
- Validate all user input
- Provide clear prompts and examples
- Handle edge cases (empty input, invalid values)

```bash
# Good - robust input handling
get_user_confirmation() {
  local readonly prompt="${1:-Continue?}"
  local readonly default="${2:-N}"
  local response
  
  while true; do
    read -p "$prompt (y/N): " -r response
    
    # Use default if empty
    if [[ -z "$response" ]]; then
      response="$default"
    fi
    
    case "$response" in
      [Yy]|[Yy][Ee][Ss])
        return 0
        ;;
      [Nn]|[Nn][Oo])
        return 1
        ;;
      *)
        echo "Please answer yes or no."
        ;;
    esac
  done
}

# Good - getting required input
get_environment_input() {
  local environment
  
  while [[ -z "$environment" ]]; do
    echo "Available environments: dev, staging, prod"
    read -p "Select environment: " -r environment
    
    if ! validate_environment "$environment"; then
      echo "ERROR: Invalid environment: $environment" >&2
      environment=""
    fi
  done
  
  echo "$environment"
}
```

### Output Formatting
- Use consistent formatting for different message types
- Write errors to stderr
- Provide progress indicators for long operations

```bash
# Good - consistent output formatting
readonly SCRIPT_NAME="$(basename "$0")"

log_info() {
  echo "[INFO] $*"
}

log_warn() {
  echo "[WARN] $*" >&2
}

log_error() {
  echo "[ERROR] $*" >&2
}

log_debug() {
  if [[ "${DEBUG:-false}" == "true" ]]; then
    echo "[DEBUG] $*" >&2
  fi
}

# Good - progress indicators
show_progress() {
  local current="$1"
  local total="$2"
  local operation="$3"
  
  printf "\r[%d/%d] %s..." "$current" "$total" "$operation"
  
  if [[ $current -eq $total ]]; then
    echo " Done!"
  fi
}

# Usage example
readonly files=("file1" "file2" "file3")
for i in "${!files[@]}"; do
  show_progress $((i + 1)) ${#files[@]} "Processing ${files[i]}"
  process_file "${files[i]}"
done
```

## File Operations

### File Handling
- Always check if files exist before operations
- Use proper permissions and ownership
- Clean up temporary files

```bash
# Good - safe file operations
create_backup() {
  local readonly source_file="$1"
  local readonly backup_dir="${2:-/var/backups}"
  local readonly timestamp="$(date +%Y%m%d_%H%M%S)"
  local readonly backup_file="${backup_dir}/$(basename "$source_file").${timestamp}"
  
  # Validate input
  if [[ ! -f "$source_file" ]]; then
    log_error "Source file does not exist: $source_file"
    return 1
  fi
  
  # Create backup directory if needed
  if [[ ! -d "$backup_dir" ]]; then
    if ! mkdir -p "$backup_dir"; then
      log_error "Failed to create backup directory: $backup_dir"
      return 1
    fi
  fi
  
  # Create backup
  if cp "$source_file" "$backup_file"; then
    log_info "Backup created: $backup_file"
    echo "$backup_file"
  else
    log_error "Failed to create backup"
    return 1
  fi
}

# Good - temporary file handling
readonly temp_dir="$(mktemp -d)"
readonly temp_file="$(mktemp)"

cleanup_temp_files() {
  log_debug "Cleaning up temporary files"
  rm -rf "$temp_dir"
  rm -f "$temp_file"
}
trap cleanup_temp_files EXIT
```

### Path Handling
- Use absolute paths when possible
- Handle paths with spaces correctly
- Validate path security

```bash
# Good - robust path handling
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly PROJECT_ROOT="$(cd "$SCRIPT_DIR/.." && pwd)"
readonly CONFIG_DIR="${PROJECT_ROOT}/config"

resolve_config_path() {
  local config_name="$1"
  local config_path
  
  # Try different locations
  for dir in "$CONFIG_DIR" "$HOME/.backstage" "/etc/backstage"; do
    config_path="${dir}/${config_name}"
    if [[ -f "$config_path" ]]; then
      # Return absolute path
      realpath "$config_path"
      return 0
    fi
  done
  
  log_error "Config file not found: $config_name"
  return 1
}

# Good - path validation
validate_directory() {
  local directory="$1"
  
  # Check if it's a valid directory
  if [[ ! -d "$directory" ]]; then
    log_error "Directory does not exist: $directory"
    return 1
  fi
  
  # Check if it's readable
  if [[ ! -r "$directory" ]]; then
    log_error "Directory is not readable: $directory"
    return 1
  fi
  
  # Security check - ensure it's not a symlink to somewhere dangerous
  local resolved_path
  if ! resolved_path="$(realpath "$directory")"; then
    log_error "Failed to resolve directory path: $directory"
    return 1
  fi
  
  # Check if resolved path is under project root (security measure)
  if [[ ! "$resolved_path" == "$PROJECT_ROOT"* ]]; then
    log_error "Directory is outside project root: $directory"
    return 1
  fi
  
  return 0
}
```

## Project-Specific Guidelines

### Backstage Scripts
- Follow project directory structure
- Use consistent logging and error handling
- Integrate with existing build and deployment processes

```bash
#!/bin/bash
#
# Backstage environment setup script
#
set -euo pipefail

readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly PROJECT_ROOT="$(cd "$SCRIPT_DIR/.." && pwd)"
readonly LOG_FILE="${PROJECT_ROOT}/logs/setup.log"

# Ensure log directory exists
mkdir -p "$(dirname "$LOG_FILE")"

# Setup logging
exec 1> >(tee -a "$LOG_FILE")
exec 2> >(tee -a "$LOG_FILE" >&2)

log_info "Starting Backstage environment setup"
log_info "Project root: $PROJECT_ROOT"
log_info "Log file: $LOG_FILE"

# Good - environment validation
validate_backstage_environment() {
  local errors=0
  
  # Check required tools
  local -a required_tools=("node" "yarn" "docker" "docker-compose")
  for tool in "${required_tools[@]}"; do
    if ! command -v "$tool" >/dev/null 2>&1; then
      log_error "Required tool not found: $tool"
      ((errors++))
    fi
  done
  
  # Check Node.js version
  local node_version
  node_version="$(node --version | sed 's/v//')"
  local required_node_version="18.0.0"
  
  if ! version_compare "$node_version" ">=" "$required_node_version"; then
    log_error "Node.js version $node_version is too old. Required: $required_node_version+"
    ((errors++))
  fi
  
  return $errors
}

# Good - version comparison function
version_compare() {
  local version1="$1"
  local operator="$2"
  local version2="$3"
  
  # Use sort -V for version comparison
  case "$operator" in
    ">=")
      [[ "$(printf '%s\n' "$version1" "$version2" | sort -V | head -n1)" == "$version2" ]]
      ;;
    ">")
      [[ "$version1" != "$version2" ]] && version_compare "$version1" ">=" "$version2"
      ;;
    "<=")
      [[ "$(printf '%s\n' "$version1" "$version2" | sort -V | tail -n1)" == "$version2" ]]
      ;;
    "<")
      [[ "$version1" != "$version2" ]] && version_compare "$version1" "<=" "$version2"
      ;;
    "==")
      [[ "$version1" == "$version2" ]]
      ;;
    *)
      log_error "Invalid operator: $operator"
      return 1
      ;;
  esac
}
```

### Integration with Build System
- Use consistent exit codes
- Provide machine-readable output when needed
- Support CI/CD pipeline requirements

```bash
# Good - CI/CD friendly script structure
main() {
  local environment="dev"
  local dry_run=false
  local verbose=false
  
  # Parse command line arguments
  while [[ $# -gt 0 ]]; do
    case $1 in
      --env|--environment)
        environment="$2"
        shift 2
        ;;
      --dry-run)
        dry_run=true
        shift
        ;;
      --verbose)
        verbose=true
        shift
        ;;
      --help)
        show_usage
        exit 0
        ;;
      *)
        log_error "Unknown option: $1"
        show_usage >&2
        exit 1
        ;;
    esac
  done
  
  # Set debug mode
  if [[ "$verbose" == "true" ]]; then
    export DEBUG=true
    set -x
  fi
  
  # Validate environment
  if ! validate_environment "$environment"; then
    exit 1
  fi
  
  # Execute main operation
  if [[ "$dry_run" == "true" ]]; then
    log_info "DRY RUN: Would deploy to $environment"
    show_deployment_plan "$environment"
  else
    deploy_to_environment "$environment"
  fi
}

# Only run main if script is executed directly
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
  main "$@"
fi
```

This shell script style guide provides comprehensive guidelines following Google's Shell Style Guide while being specifically adapted for the Backstage project's build, deployment, and automation needs.