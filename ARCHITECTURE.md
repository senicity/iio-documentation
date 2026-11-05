# InstanceIO Architecture

## Overview

InstanceIO follows a sophisticated agent-based architecture where different "agents" handle specific responsibilities within the application lifecycle. This architecture enables clean separation of concerns, high reusability, and excellent performance.

## Core Architecture Principles

### 1. Component-First Design
- **Reusable UI Components**: Building blocks encapsulating HTML, CSS, and PHP logic
- **Separation of Concerns**: Each component has dedicated files for structure, style, and logic
- **Composition Over Inheritance**: Components can be nested and composed to build complex interfaces

### 2. Dual-Mode Operation
- **Dynamic Mode**: Full PHP runtime with server-side rendering
- **Static Mode**: Pre-compiled HTML export for serverless deployment
- **HTMx Integration**: Partial page updates without JavaScript frameworks

### 3. Configuration-Driven Architecture
- **Single Configuration File**: `config.json` controls all aspects of the application
- **Environment Adaptation**: Same codebase adapts to development/production/static modes
- **Extensible Structure**: Directory paths and behaviors configurable via JSON

## System Architecture

### 1. Application Flow
```
HTTP Request → Kernel → Route System → Controller → Repository → View System → Response
```

### 2. Component Rendering Pipeline
```
Component Tag → Component Class → Template Parsing → Variable Injection → HTML Output
```

## Core Components

### 1. Kernel System (`app/Kernel.php`)
**Purpose**: Application bootstrap and orchestration

**Responsibilities:**
- Loads and parses configuration
- Initializes route systems (Web and API)
- Sets up view rendering system
- Coordinates application execution

### 2. Route System (`routes/`)
**Web Routes (`web.php`)**:
- Full-page rendering with layouts
- Traditional server-side rendering
- Complete HTML document output

**API Routes (`api.php`)**:
- Headless/partial rendering
- HTMx/AJAX endpoint support
- Component-only HTML responses

### 3. Controller Layer (`app/Http/Controllers/`)
**Purpose**: Request handling and response coordination

**Pattern:**
```php
class ControllerName extends Controller {
    public function action() {
        return $this->defaultResponse(
            $this->repository->method()
        );
    }
}
```

### 4. Repository Layer (`app/Repositories/`)
**Purpose**: Data access and view management abstraction

**Example:** `ViewsRepository`
- Manages template retrieval
- Integrates with external APIs via Services
- Handles data injection into views
- Supports local and remote template sources

### 5. Component System (`resources/components/`)
**Component Structure:**
```
ComponentName/
├── ComponentName.php      # PHP class with logic
├── ComponentName.html     # HTML template
└── ComponentName.css      # Component styles
```

**Key Features:**
- **Attribute System**: Pass data via HTML attributes
- **Default Values**: Fallback values for missing attributes
- **Method Handlers**: Dynamic content generation
- **Child Composition**: Nest components within components
- **JSON Processing**: Handle structured data in attributes

### 6. View System (`app/Http/Views/`)
**Purpose**: Template compilation and rendering

**Responsibilities:**
- Load `.iio` template files
- Inject global variables (navigation, footers)
- Handle 404 responses
- Manage template caching
- Assemble final HTML output

### 7. Service Layer (`app/Services/`)
**Purpose**: External service integration abstraction

**Example Services:**
- `HttpService`: HTTP client for API communication
- Extensible for database, caching, authentication, etc.

### 8. Console System (`console/`)
**Purpose**: CLI tooling for development and deployment

**Command Categories:**
- **Component Management**: Create/update components
- **Route Management**: Add/remove routes
- **Build System**: Static HTML export
- **Docker Management**: Development environment
- **Project Scaffolding**: Code generation

## Agent Architecture

InstanceIO employs an agent-based architecture where different "agents" handle specific responsibilities:

### 1. Kernel Agent
- Application bootstrap and orchestration
- Configuration management
- System coordination

### 2. Route Agents
- HTTP request routing
- Endpoint management
- Headless mode detection

### 3. Controller Agents
- Request handling
- Business logic execution
- Response preparation

### 4. Component Agents
- UI rendering
- Template processing
- Data binding

### 5. Service Agents
- External service integration
- API communication
- Data transformation

## Data Flow Architecture

### 1. HTTP Request Processing
```
1. Request → Kernel
2. Kernel → Route Matching
3. Route → Controller Method
4. Controller → Repository Call
5. Repository → View/Data Retrieval
6. View → Component Rendering
7. Response → Client
```

### 2. Component Data Flow
```
1. Component Tag with Attributes
2. Component Class Instantiation
3. Default Values Merged with Attributes
4. Template Variable Replacement
5. Method Handler Execution (if specified)
6. HTML Assembly
7. Final Render Output
```

### 3. Static Export Flow
```
1. Read sitemap.json for URLs
2. For each URL: Simulate HTTP Request
3. Process through Normal Pipeline
4. Capture HTML Output
5. Write to dist/ Directory
6. Generate Static Assets
```

## Template System Architecture

### 1. `.iio` Template Files
**Location:** `resources/views/local/`
**Syntax:** HTML with component tags and template variables

**Features:**
- Component tag syntax: `<component_name attribute="value" />`
- Template variables: `{%variable_name%}`
- Conditional rendering: `{ condition : html }`
- Method handlers: `attribute="methodName"`

### 2. Template Processing Pipeline
```
1. Load .iio File
2. Parse Component Tags
3. Instantiate Component Classes
4. Process Attributes and Defaults
5. Execute Method Handlers
6. Replace Template Variables
7. Inject Global Variables
8. Output Final HTML
```

## Performance Architecture

### 1. Caching Strategies
- Template compilation caching
- Component output caching
- API response caching
- Asset file caching

### 2. Optimization Features
- CSS minification in production
- JavaScript bundling
- Image optimization
- Lazy loading support

### 3. Scalability Considerations
- Stateless component design
- Cache-friendly architecture
- CDN integration support
- Horizontal scaling capability

## Security Architecture

### 1. Input Validation
- Component attribute sanitization
- Template variable escaping
- JSON data validation
- API request filtering

### 2. Output Encoding
- Automatic HTML entity encoding
- JavaScript context escaping
- URL parameter encoding
- CSS property validation

### 3. Access Control
- Route-level protection
- Component visibility controls
- API endpoint authentication
- Configuration file security

## Deployment Architecture

### 1. Deployment Modes
**Dynamic PHP Application:**
- Traditional web server (Apache/Nginx)
- PHP runtime required
- Real-time template compilation

**Static HTML Export:**
- Serverless deployment (S3, Netlify, Vercel)
- No PHP runtime required
- Pre-compiled HTML files

**Docker Container:**
- Isolated environment
- Consistent runtime
- Easy scaling

### 2. Asset Management
**Development:**
- Local asset serving
- Live reload
- Source maps

**Production:**
- CDN integration
- Versioned assets
- Minified bundles

## Extensibility Architecture

### 1. Plugin System
- Component library extensions
- Service provider interfaces
- Command line tool plugins
- Template engine extensions

### 2. Customization Points
- Component base class extension
- Route system modification
- View rendering customization
- Configuration schema extension

### 3. Integration Patterns
**External APIs:**
- Service layer abstraction
- HTTP client integration
- Webhook support
- Real-time updates

**Database Integration:**
- Repository pattern
- ORM/Query builder
- Migration system
- Seeding support

## Framework vs SDK Architecture

### Core Framework (`iio/`)
- Base classes and interfaces
- Component rendering engine
- Command system infrastructure
- Template parsing system
- Configuration management

### SDK (`iio-sdk/`)
- Application implementation
- Custom components and controllers
- Project-specific configuration
- Deployment configurations
- Custom commands and utilities

## Future Architecture Directions

### 1. Planned Features
- **Authentication System**: User management and sessions
- **Database ORM**: Object-relational mapping
- **Real-time Updates**: WebSocket integration
- **Internationalization**: Multi-language support
- **Progressive Web App**: PWA capabilities

### 2. Performance Enhancements
- **Edge Computing**: Cloudflare Workers integration
- **Image Optimization**: Automatic image processing
- **Code Splitting**: Dynamic component loading
- **Predictive Prefetching**: Intelligent resource loading

### 3. Developer Experience
- **Visual Component Editor**: GUI for component design
- **API Explorer**: Interactive API documentation
- **Performance Profiler**: Application performance analysis
- **Debugging Tools**: Enhanced development tools

---

*This architecture provides a solid foundation for building modern web applications with excellent performance, security, and developer experience.*

## Current Static and Template Architecture

The current build pipeline validates and renders every route in `sitemap.json`. API responses that an HTMx static demo needs can be included as build routes, producing files such as `dist/api/example/index.html`. This preserves the original request path while replacing runtime PHP execution with a build-time HTML response.

The IPFS pipeline then adds the contents of `dist/` as the CID root, verifies the root index, and optionally submits the CID to a remote pinning service. Local daemon availability and remote provider availability are separate concerns.

The planned Templates Hub extends the component architecture with an open library of base templates and reusable components. Hub entries can expose source structure, component metadata, usage guidance, and AI knowledge-base context without hiding the underlying PHP, HTML, CSS, route, or configuration files.
