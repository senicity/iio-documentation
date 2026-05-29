# Command Line Interface Reference

## Overview

InstanceIO provides a powerful command line interface for development, building, and deployment. All commands are executed through the main command runner:

```bash
php console/cmd.php <command> [options] [arguments]
```

## Command Structure

```
php console/cmd.php <command> <subcommand> [parameters] [--flags]
```

## Available Commands

### 1. Make Commands
Create new framework elements.

#### Make Component
```bash
php console/cmd.php Make component <ComponentName>
```
Creates a new component with PHP class, HTML template, and CSS file.

**Example:**
```bash
php console/cmd.php Make component Header
```
Creates: `resources/components/Header/Header.php`, `Header.html`, `Header.css`

#### Make Controller
```bash
php console/cmd.php Make controller <ControllerName> [method] [viewMethod]
```
Creates a new controller class.

**Parameters:**
- `ControllerName`: Name of the controller (e.g., `UserController`)
- `method`: Controller method name (default: `index`)
- `viewMethod`: View repository method name (default: `index`)

**Examples:**
```bash
# Basic controller
php console/cmd.php Make controller UserController

# Controller with custom methods
php console/cmd.php Make controller ProductController showProduct productView
```

#### Make Route
```bash
php console/cmd.php Make route <type> <method> <path> <viewMethod> <controller> [--make]
```
Creates a new route and optionally creates the controller.

**Parameters:**
- `type`: Route type (`web` or `api`)
- `method`: Controller method name
- `path`: URL path (e.g., `/users`)
- `viewMethod`: View repository method name
- `controller`: Controller class name
- `--make`: Create controller if it doesn't exist

**Examples:**
```bash
# Web route without creating controller
php console/cmd.php Make route web index "/about" aboutView AboutController

# API route with controller creation
php console/cmd.php Make route api getUser "/api/user" userView UserController --make
```

#### Make Command
```bash
php console/cmd.php Make command <CommandName>
```
Creates a new CLI command class.

**Example:**
```bash
php console/cmd.php Make command DatabaseBackup
```
Creates: `console/Command/DatabaseBackup.php`

### 2. Build Commands
Manage application building and deployment.

#### Build
```bash
php console/cmd.php Build
```
Creates a static HTML export of your application.

**Output:** `dist/` directory with compiled HTML files

**Process:**
1. Reads `sitemap.json` for URLs
2. Simulates requests to each endpoint
3. Captures rendered HTML
4. Writes to corresponding HTML files
5. Copies assets to output directory

**Example:**
```bash
php console/cmd.php Build
```

#### Build with Options
```bash
# Build with specific sitemap
php console/cmd.php Build --sitemap=custom-sitemap.json

# Build with clean output
php console/cmd.php Build --clean

# Build with verbose output
php console/cmd.php Build --verbose
```

### 3. Docker Commands
Manage Docker development and production environments.

#### Docker Build
```bash
php console/cmd.php Docker build [type] [--container]
```
Builds Docker images and containers.

**Types:**
- (empty): Development environment
- `prod-app`: Production PHP application
- `prod-html`: Production static HTML

**Flags:**
- `--container`: Build and run container immediately

**Examples:**
```bash
# Development environment
php console/cmd.php Docker build

# Production PHP app
php console/cmd.php Docker build prod-app

# Production static HTML with container
php console/cmd.php Docker build prod-html --container
```

#### Docker Watch
```bash
php console/cmd.php Docker watch
```
Starts a watcher container that monitors component changes and recompiles CSS.

**Features:**
- Monitors `resources/components/` directory
- Auto-compiles component CSS
- Live reload for development
- Mounts local directories

**Example:**
```bash
php console/cmd.php Docker watch
```

#### Docker Destroy
```bash
php console/cmd.php Docker destroy [type]
```
Destroys Docker containers and images.

**Types:**
- (empty): Development container only
- `prod-app`: Production PHP container and image
- `prod-html`: Production static container and image

**Examples:**
```bash
# Destroy development container
php console/cmd.php Docker destroy

# Destroy production PHP environment
php console/cmd.php Docker destroy prod-app

# Destroy production static environment
php console/cmd.php Docker destroy prod-html
```

### 4. System Commands
Framework management and utilities.

#### Test
```bash
php console/cmd.php Test [type]
```
Runs framework tests.

**Types:**
- `components`: Test component rendering
- `routes`: Test route definitions
- `controllers`: Test controller functionality
- `all`: Run all tests (default)

**Examples:**
```bash
# Run all tests
php console/cmd.php Test

# Test components only
php console/cmd.php Test components

# Test with verbose output
php console/cmd.php Test --verbose
```

#### Update
```bash
php console/cmd.php Update
```
Updates the framework to the latest version.

**Process:**
1. Checks for updates
2. Downloads new framework version
3. Updates vendor directory
4. Preserves custom configurations

**Example:**
```bash
php console/cmd.php Update
```

#### Clean
```bash
php console/cmd.php Clean [target]
```
Cleans various framework artifacts.

**Targets:**
- `cache`: Clear application cache
- `build`: Remove build artifacts
- `docker`: Clean Docker artifacts
- `all`: Clean everything (default)

**Examples:**
```bash
# Clean everything
php console/cmd.php Clean

# Clear cache only
php console/cmd.php Clean cache

# Remove build artifacts
php console/cmd.php Clean build
```

## Command Examples

### Complete Development Workflow

```bash
# 1. Create a new component
php console/cmd.php Make component ProductCard

# 2. Create a controller for products
php console/cmd.php Make controller ProductController show productView

# 3. Create a route for products
php console/cmd.php Make route web show "/products" productView ProductController --make

# 4. Start Docker development
php console/cmd.php Docker build

# 5. Watch for changes
php console/cmd.php Docker watch

# 6. Test the application
php console/cmd.php Test

# 7. Build for production
php console/cmd.php Build

# 8. Create production Docker image
php console/cmd.php Docker build prod-app --container
```

### Deployment Workflow

```bash
# 1. Build static HTML
php console/cmd.php Build

# 2. Test the build
php console/cmd.php Test

# 3. Create production Docker image
php console/cmd.php Docker build prod-html --container

# 4. Deploy to server
# (Docker image is ready for deployment)
```

## Command Output

### Success Output
```
▗▄▄▄▖▗▄▄▄▖ ▗▄▖    
  █    █  ▐▌ ▐▌   
  █    █  ▐▌ ▐▌   
▗▄█▄▖▗▄█▄▖▝▚▄▞▘   

--// InstanceIO Command Runner

Running Command: Make component Header
✅ SUCCESS
Command Successfully Executed
```

### Error Output
```
▗▄▄▄▖▗▄▄▄▖ ▗▄▖    
  █    █  ▐▌ ▐▌   
  █    █  ▐▌ ▐▌   
▗▄█▄▖▗▄█▄▖▝▚▄▞▘   

--// InstanceIO Command Runner

Running Command: Make component
❌ ERROR: Missing Command Option in Request
```

## Creating Custom Commands

### Command Structure
```php
class CustomCommand extends IioCommandHelpers {
    
    public function __construct(
        public array $params = [],
        public array $flags = []
    ) {}
    
    public function execute() {
        // Command logic here
        
        // Use helpers for output
        $this->success('Command completed successfully');
        // or
        $this->error('Something went wrong');
        
        return true; // Return true for success, false for failure
    }
}
```

### Available Helpers
```php
// Output formatting
$this->color('Text', 'color'); // Colors: red, green, blue, yellow, etc.
$this->success('Message');
$this->error('Message');
$this->warning('Message');
$this->info('Message');

// File operations
$this->createFile($path, $content);
$this->readFile($path);
$this->directoryExists($path);

// User input
$this->ask('Question?');
$this->confirm('Are you sure?');

// Process execution
$this->runCommand('command');
$this->executeInBackground('command');
```

### Example Custom Command
```php
class DatabaseBackup extends IioCommandHelpers {
    
    public function __construct(
        public array $params = [],
        public array $flags = []
    ) {}
    
    public function execute() {
        $this->info('Starting database backup...');
        
        // Get database name from params or config
        $database = $this->params[1] ?? 'default';
        
        // Create backup
        $backupFile = "backup-{$database}-" . date('Y-m-d-H-i-s') . '.sql';
        
        if ($this->runCommand("mysqldump {$database} > {$backupFile}")) {
            $this->success("Backup created: {$backupFile}");
            return true;
        } else {
            $this->error('Backup failed');
            return false;
        }
    }
}
```

## Command Flags

### Global Flags
- `--help`: Show command help
- `--verbose`: Enable verbose output
- `--quiet`: Suppress output
- `--version`: Show framework version
- `--no-color`: Disable colored output

### Command-Specific Flags
- `--make`: Create controller when making routes
- `--container`: Build and run Docker container
- `--clean`: Clean output before building
- `--force`: Force operation without confirmation

## Environment Variables

Commands can use environment variables:

```bash
# Set environment variable
export IIO_ENV=production

# Use in command
php console/cmd.php Build --env=$IIO_ENV
```

### Available Environment Variables
- `IIO_ENV`: Environment (development, production, staging)
- `IIO_DEBUG`: Debug mode (0 or 1)
- `IIO_CACHE`: Cache enabled (0 or 1)
- `IIO_DOCKER_HOST`: Docker host address

## Exit Codes

- `0`: Success
- `1`: General error
- `2`: Invalid command
- `3`: Missing parameter
- `4`: File system error
- `5`: Permission denied
- `127`: Command not found

## Troubleshooting

### Common Issues

**1. Command Not Found**
```bash
❌ ERROR: Command Does Not Exist
```
- Check command spelling
- Verify command exists in `console/Command/`
- Ensure command file has correct permissions

**2. Missing Parameters**
```bash
❌ ERROR: Missing Parameter: <parameter>
```
- Check command syntax
- Review required parameters
- Use `--help` for command usage

**3. Permission Denied**
```bash
❌ ERROR: Permission Denied
```
- Check file permissions
- Verify user has write access
- Run with appropriate privileges

**4. Docker Issues**
```bash
❌ ERROR: Docker Not Available
```
- Ensure Docker is installed and running
- Check Docker permissions
- Verify Docker daemon is accessible

### Debugging Commands

```bash
# Enable debug output
php console/cmd.php <command> --verbose

# Check command structure
php console/cmd.php <command> --help

# Test with minimal configuration
php console/cmd.php <command> --dry-run
```

## Best Practices

### 1. Command Naming
- Use descriptive names
- Follow existing naming conventions
- Use camel case for multi-word names

### 2. Error Handling
- Provide clear error messages
- Suggest solutions for common errors
- Use appropriate exit codes
- Log errors for debugging

### 3. User Experience
- Provide helpful output
- Include progress indicators
- Support both interactive and scripted use
- Document command usage

### 4. Performance
- Optimize command execution
- Minimize file system operations
- Use caching where appropriate
- Clean up temporary files

### 5. Security
- Validate all inputs
- Sanitize file paths
- Check permissions before operations
- Avoid command injection vulnerabilities

## Advanced Usage

### Command Chaining
```bash
# Chain commands with &&
php console/cmd.php Clean cache && php console/cmd.php Build

# Use in scripts
#!/bin/bash
php console/cmd.php Clean all
php console/cmd.php Test
php console/cmd.php Build
php console/cmd.php Docker build prod-html --container
```

### Scheduled Commands
Use cron or systemd timers for scheduled tasks:

```cron
# Daily backup at 2 AM
0 2 * * * cd /path/to/app && php console/cmd.php DatabaseBackup

# Hourly cache cleanup
0 * * * * cd /path/to/app && php console/cmd.php Clean cache
```

### Integration with CI/CD
```yaml
# GitHub Actions example
name: Build and Test
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install dependencies
        run: php install.php
      - name: Run tests
        run: php console/cmd.php Test
      - name: Build static site
        run: php console/cmd.php Build
      - name: Deploy
        run: |
          php console/cmd.php Docker build prod-html
          docker push myregistry/instanceio-app
```

## Next Steps

- Learn about [Custom Command Development](ADVANCED_COMMANDS.md)
- Explore [Build System](BUILD_SYSTEM.md)
- Study [Docker Integration](DOCKER_GUIDE.md)
- Check [Command Examples](EXAMPLES.md#cli)

---

*The InstanceIO CLI provides powerful tools for every stage of development, from creating components to deploying production applications.*