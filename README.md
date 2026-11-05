# InstanceIO Framework Documentation

```
╔══════════════════════════════════════╗
║                                      ║
║   ██╗██╗ ██████╗                     ║
║   ██║██║██╔═══██╗                    ║
║   ██║██║██║   ██║                    ║
║   ██║██║██║   ██║                    ║
║   ██║██║╚██████╔╝                    ║
║   ╚═╝╚═╝ ╚═════╝                     ║
║                                      ║
║   InstanceIO - Component Framework   ║
║                                      ║
╚══════════════════════════════════════╝
```

## Overview

InstanceIO (Iio) is a revolutionary component-based PHP framework designed for rapid development of both static and dynamic websites. Built with modern web development principles, it combines the power of traditional PHP with cutting-edge component architecture, HTMx integration, and Docker-ready deployment.

### Key Features
- **Component-First Architecture**: Build reusable UI components with clean separation of logic, structure, and style
- **Dual-Mode Operation**: Develop once, deploy as dynamic PHP application or static HTML export
- **HTMx Integration**: Create interactive applications without heavy JavaScript frameworks
- **Docker-Ready**: Full development and production container support out of the box
- **Configuration-Driven**: Single `config.json` file controls all aspects of your application
- **Command Line Interface**: Powerful CLI tools for development, building, and deployment
- **IPFS Deployment**: Publish validated static builds with Kubo, verify local CIDs, and use remote pinning providers
- **Template Hub Direction**: Open base templates, reusable components, and AI-ready knowledge context

## Documentation Structure

### Getting Started
- [Quick Start Guide](GETTING_STARTED.md) - Get up and running in minutes
- [Installation Guide](INSTALLATION.md) - Complete installation instructions
- [System Requirements](REQUIREMENTS.md) - Hardware and software requirements

### Core Concepts
- [Architecture Overview](ARCHITECTURE.md) - Framework architecture and design principles
- [Component System](COMPONENTS.md) - Building reusable UI components
- [Template System](TEMPLATES.md) - .iio template syntax and usage
- [Routing System](ROUTING.md) - Web and API route management
- [Configuration Guide](CONFIGURATION.md) - config.json reference and examples

### Development Guides
- [Component Development](DEVELOPMENT_COMPONENTS.md) - Creating and using components
- [Controller Development](DEVELOPMENT_CONTROLLERS.md) - Building controllers and business logic
- [Route Development](DEVELOPMENT_ROUTES.md) - Creating web and API routes
- [View Development](DEVELOPMENT_VIEWS.md) - Designing .iio templates and views
- [Service Development](DEVELOPMENT_SERVICES.md) - Integrating external services

### Command Line Interface
- [CLI Reference](CLI_REFERENCE.md) - Complete command reference
- [Build System](BUILD_SYSTEM.md) - Static HTML export and deployment
- [Docker Management](DOCKER_GUIDE.md) - Development and production containers
- [Code Generation](CODE_GENERATION.md) - Make commands and scaffolding

### Deployment & Operations
- [Static Deployment](DEPLOYMENT_STATIC.md) - Deploying to S3, Netlify, Vercel, etc.
- [IPFS Deployment](IPFS.md) - Kubo, immutable CIDs, local serving, and remote pinning
- [Templates and Hub](TEMPLATES_HUB.md) - Base templates, open components, and AI-ready knowledge context
- [Dynamic Deployment](DEPLOYMENT_DYNAMIC.md) - Traditional PHP server deployment
- [Docker Deployment](DEPLOYMENT_DOCKER.md) - Container-based deployment
- [Performance Optimization](PERFORMANCE.md) - Caching and optimization strategies
- [Security Guide](SECURITY.md) - Security best practices and hardening

### Advanced Topics
- [Custom Commands](ADVANCED_COMMANDS.md) - Creating custom CLI commands
- [Template Extensions](ADVANCED_TEMPLATES.md) - Custom template engines
- [Service Integration](ADVANCED_SERVICES.md) - External API and service integration
- [Performance Tuning](ADVANCED_PERFORMANCE.md) - Advanced optimization techniques
- [Migration Guide](MIGRATION.md) - Migrating from other frameworks

### Reference
- [API Reference](API_REFERENCE.md) - Framework API documentation
- [Configuration Reference](CONFIG_REFERENCE.md) - Complete config.json reference
- [Component Reference](COMPONENT_REFERENCE.md) - Component class reference
- [Template Reference](TEMPLATE_REFERENCE.md) - .iio template syntax reference
- [CLI Command Reference](CLI_COMMAND_REFERENCE.md) - Detailed command documentation

## Quick Example

### Component Definition
```php
// resources/components/HelloWorld/HelloWorld.php
class HelloWorld extends Component {
    public array $defaults = [
        'message' => 'Hello World!'
    ];
    
    public function __construct(public array $params) {
        parent::__construct($this->params, __DIR__ . '/HelloWorld');
    }
}
```

```html
<!-- resources/components/HelloWorld/HelloWorld.html -->
<div class="hello-world">
    <h1>{%message%}</h1>
</div>
```

### Template Usage
```html
<!-- resources/views/local/home.iio -->
<hello_world message="Welcome to InstanceIO!" />
```

### Command Line Usage
```bash
# Create a new component
php console/cmd.php Make component HelloWorld

# Build static HTML
php console/cmd.php Build

# Start Docker development environment
php console/cmd.php Docker build
```

## Getting Your Copy

InstanceIO is a closed-source framework available under proprietary license. To get your own copy of the framework for development:

**Contact us at: info@senicity.com**

Include in your request:
- Your name and organization
- Intended use case
- Development team size
- Preferred deployment environment

Our team will provide you with:
- Complete framework package
- License agreement
- Installation support
- Documentation access
- Optional training and consulting

## Community & Support

- **Documentation**: This documentation repository
- **Examples**: Reference implementations and sample projects
- **Updates**: Framework updates and security patches
- **Support**: Technical support for licensed users

## License

InstanceIO Framework - Proprietary License
© 2024 Senicity. All rights reserved.

Unauthorized copying, distribution, or use of this framework is strictly prohibited.

---

*InstanceIO - Build faster, deploy smarter, scale effortlessly.*