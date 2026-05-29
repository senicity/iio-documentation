# Getting Started with InstanceIO

## Quick Start

### 1. Prerequisites
- PHP 8.1 or higher
- Composer (for dependency management)
- Docker (for containerized development)
- Git (for version control)

### 2. Getting the Framework

InstanceIO is a closed-source framework available under proprietary license. To get your copy:

**Contact: info@senicity.com**

Include in your request:
- Your name and organization
- Intended use case
- Development team size
- Preferred deployment environment

### 3. Installation

Once you receive the framework package:

```bash
# Extract the framework
tar -xzf instanceio-framework.tar.gz
cd instanceio-sdk

# Run the installer
php install.php
```

The installer will:
- Set up the vendor directory
- Install the core framework
- Configure basic project structure
- Set up initial components

### 4. Your First Component

Create a simple welcome component:

```bash
php console/cmd.php Make component WelcomeMessage
```

Edit the component files:

**`resources/components/WelcomeMessage/WelcomeMessage.php`:**
```php
class WelcomeMessage extends Component {
    public array $defaults = [
        'greeting' => 'Hello',
        'name' => 'World'
    ];
    
    public function __construct(public array $params) {
        parent::__construct($this->params, __DIR__ . '/WelcomeMessage');
    }
}
```

**`resources/components/WelcomeMessage/WelcomeMessage.html`:**
```html
<div class="welcome-message">
    <h1>{%greeting%}, {%name%}!</h1>
    <p>Welcome to InstanceIO Framework</p>
</div>
```

**`resources/components/WelcomeMessage/WelcomeMessage.css`:**
```css
.welcome-message {
    text-align: center;
    padding: 2rem;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-radius: 10px;
}
```

### 5. Create a View

Create a simple home view:

**`resources/views/local/home.iio`:**
```html
<welcome_message 
    greeting="Welcome" 
    name="Developer" 
/>
```

### 6. Update the Route

Edit `routes/web.php` to use your new view:

```php
public function route()
{
    $this->routeBuild('/', $this->homeController->home());
    return $this->routes;
}
```

Update `app/Repositories/ViewsRepository.php`:

```php
public function home()
{
    return $this->view('home');
}
```

### 7. Start Development Server

```bash
# Start Docker development environment
php console/cmd.php Docker build

# Or run locally with PHP built-in server
php -S localhost:8000 -t public
```

Visit `http://localhost:8000` to see your component in action!

## Project Structure Overview

```
instanceio-sdk/
├── app/                    # Application core
│   ├── Http/              # HTTP layer
│   ├── Repositories/      # Data access
│   ├── Services/          # External services
│   └── Kernel.php         # Application kernel
├── resources/             # Assets and templates
│   ├── components/        # UI components
│   ├── views/            # .iio templates
│   └── assets/           # Static assets
├── routes/               # Route definitions
│   ├── web.php          # Web routes
│   └── api.php          # API routes
├── console/              # CLI commands
│   └── cmd.php          # Command runner
├── public/               # Web root
│   └── index.php        # Entry point
├── config.json          # Configuration
└── sitemap.json         # Static export URLs
```

## Configuration Basics

Edit `config.json` to customize your application:

```json
{
  "name": "My InstanceIO App",
  "template": "geneva",
  "root": {
    "dir": "/webroot",
    "app": "app",
    "resources": "resources"
  },
  "docker": {
    "port": 6767
  },
  "api": {
    "endpoint": "https://api.example.com"
  }
}
```

## Common Commands

### Development Commands
```bash
# Create components
php console/cmd.php Make component ComponentName

# Create controllers
php console/cmd.php Make controller ControllerName

# Create routes
php console/cmd.php Make route web methodName "/path" viewMethod ControllerName --make

# Start Docker environment
php console/cmd.php Docker build

# Watch for changes
php console/cmd.php Docker watch
```

### Build Commands
```bash
# Build static HTML
php console/cmd.php Build

# Build production Docker image
php console/cmd.php Docker build prod-app

# Build static HTML Docker image
php console/cmd.php Docker build prod-html
```

## Next Steps

### 1. Explore Components
- Learn about [Component System](COMPONENTS.md)
- Study [Template Syntax](TEMPLATES.md)
- Practice with [Component Examples](EXAMPLES.md)

### 2. Build Your Application
- Set up [Routing](ROUTING.md)
- Create [Controllers](DEVELOPMENT_CONTROLLERS.md)
- Integrate [External Services](DEVELOPMENT_SERVICES.md)

### 3. Deploy
- Choose [Deployment Strategy](DEPLOYMENT.md)
- Optimize [Performance](PERFORMANCE.md)
- Implement [Security](SECURITY.md)

## Troubleshooting

### Common Issues

**1. Installer Fails**
- Ensure PHP 8.1+ is installed
- Check directory permissions
- Verify framework package integrity

**2. Components Not Rendering**
- Check component file permissions
- Verify template variable syntax
- Ensure routes are properly configured

**3. Docker Issues**
- Docker daemon must be running
- Port 6767 should be available
- Check Docker installation

**4. Build Errors**
- Verify sitemap.json exists
- Check configuration values
- Ensure all dependencies are installed

## Getting Help

### Documentation
- This documentation repository
- Framework reference guides
- Example projects

### Support
For licensed users:
- Technical support via email
- Framework updates
- Security patches
- Consulting services

### Community
- Reference implementations
- Best practices guide
- Migration examples

## License Information

InstanceIO Framework is proprietary software. Your license includes:
- Framework usage rights
- Documentation access
- Technical support
- Framework updates
- Security patches

**Unauthorized copying, distribution, or use is strictly prohibited.**

---

*Start building amazing web applications with InstanceIO today!*