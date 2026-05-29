# Component System

## Overview

Components are the building blocks of InstanceIO applications. Each component is a self-contained unit that includes PHP logic, HTML structure, and CSS styling. Components can be reused across your application and composed to build complex interfaces.

## Component Structure

Each component consists of three files in its own directory:

```
ComponentName/
├── ComponentName.php      # PHP class with logic and defaults
├── ComponentName.html     # HTML template with template variables
└── ComponentName.css      # Component-specific styles
```

## Creating Components

### Using the CLI
```bash
php console/cmd.php Make component ComponentName
```

This creates:
- `resources/components/ComponentName/ComponentName.php`
- `resources/components/ComponentName/ComponentName.html`
- `resources/components/ComponentName/ComponentName.css`

### Manual Creation
1. Create directory: `resources/components/YourComponent/`
2. Create the three component files
3. Follow the naming convention: `YourComponent.php`, `YourComponent.html`, `YourComponent.css`

## Component PHP Class

### Basic Structure
```php
class ComponentName extends Component {
    
    // Default values for attributes
    public array $defaults = [
        'title' => 'Default Title',
        'content' => 'Default content goes here'
    ];
    
    // Constructor with parameters
    public function __construct(
        public array $params,                    // Attributes passed from template
        public string $component = __DIR__ . '/ComponentName'  // Component directory
    ) {
        parent::__construct($this->params, $this->component);
    }
    
    // Custom method handlers
    public function handleData() {
        // Process data and return HTML
        return '<div>Processed content</div>';
    }
}
```

### Default Values
Default values provide fallback content when attributes are not provided:

```php
public array $defaults = [
    'title' => 'Welcome',
    'subtitle' => 'Learn more about our product',
    'buttonText' => 'Get Started'
];
```

## HTML Template

### Basic Template
```html
<div class="component-name">
    <h1>{%title%}</h1>
    <p>{%content%}</p>
    <div class="dynamic-content">
        {%handler-output%}
    </div>
</div>
```

### Template Variables
- Use `{%variable_name%}` to insert PHP variable values
- Variables come from component attributes or defaults
- All variables are automatically escaped for security

### Conditional Rendering
```html
<div class="component">
    { hasImage : <img src="{%image%}" alt="{%alt%}" /> }
    { hasDescription : <p class="description">{%description%}</p> }
</div>
```

Conditional blocks only render if the attribute has a non-empty value.

## Component Usage

### In .iio Templates
```html
<component_name 
    title="Hello World"
    content="This is my first component"
    dynamic-content="handleData"
    data='{"items": ["one", "two", "three"]}'
/>
```

### Attributes
- **Simple attributes**: `title="Hello"`
- **Method handlers**: `content="handleData"` (calls `handleData()` method)
- **JSON data**: `data='{"key": "value"}'` (accessible via `$this->params['data']`)

### Method Handlers
Method handlers allow dynamic content generation:

```php
public function handleData() {
    // Access component parameters
    $data = $this->json($this->params['data'], 'decode');
    
    $output = '';
    foreach ($data['items'] as $item) {
        $output .= "<li>$item</li>";
    }
    
    return "<ul>$output</ul>";
}
```

## Advanced Component Features

### Child Components
Components can include other components:

```php
public function renderChildComponents() {
    $childData = $this->json($this->params['child-data'], 'decode');
    $output = '';
    
    foreach ($childData as $data) {
        $output .= (new ChildComponent($data))->return();
    }
    
    return $output;
}
```

### JSON Processing
```php
// Decode JSON from attribute
$data = $this->json($this->params['json-data'], 'decode');

// Encode data to JSON
$json = $this->json($data, 'encode');

// With base64 encoding
$encoded = $this->json($data, 'encode', true);
```

### Data Injection
Load data from external JSON files:

```php
public function __construct(public array $params) {
    parent::__construct($this->params, __DIR__ . '/ComponentName');
    $this->inject('data.json');  // Loads from resources/data/data.json
}

public function getData() {
    $data = $this->json($this->jsonInjection, 'decode');
    // Process injected data
}
```

## Component CSS

### Scoped Styling
```css
.component-name {
    /* Component container styles */
}

.component-name__header {
    /* Element styles */
}

.component-name--highlighted {
    /* Modifier styles */
}
```

### Best Practices
- Use component-specific class names
- Follow BEM-like naming conventions
- Keep styles scoped to the component
- Use CSS variables for theming
- Consider mobile-first responsive design

## Component Examples

### Simple Card Component
**`Card.php`:**
```php
class Card extends Component {
    public array $defaults = [
        'title' => 'Card Title',
        'content' => 'Card content goes here',
        'link' => '#',
        'linkText' => 'Learn More'
    ];
    
    public function __construct(public array $params) {
        parent::__construct($this->params, __DIR__ . '/Card');
    }
}
```

**`Card.html`:**
```html
<div class="card">
    <div class="card-header">
        <h3>{%title%}</h3>
    </div>
    <div class="card-body">
        <p>{%content%}</p>
    </div>
    { link : 
    <div class="card-footer">
        <a href="{%link%}" class="btn">{%linkText%}</a>
    </div>
    }
</div>
```

**`Card.css`:**
```css
.card {
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    padding: 1.5rem;
    background: white;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.card-header h3 {
    margin: 0 0 1rem 0;
    color: #333;
}

.card-body p {
    color: #666;
    line-height: 1.6;
}

.card-footer {
    margin-top: 1rem;
    padding-top: 1rem;
    border-top: 1px solid #e0e0e0;
}

.btn {
    display: inline-block;
    padding: 0.5rem 1rem;
    background: #007bff;
    color: white;
    text-decoration: none;
    border-radius: 4px;
}
```

### Dynamic List Component
**`DynamicList.php`:**
```php
class DynamicList extends Component {
    public array $defaults = [];
    
    public function __construct(public array $params) {
        parent::__construct($this->params, __DIR__ . '/DynamicList');
    }
    
    public function renderItems() {
        $items = $this->json($this->params['items'], 'decode');
        $output = '';
        
        foreach ($items as $item) {
            $output .= "<li>{$item['name']}: {$item['value']}</li>";
        }
        
        return $output;
    }
}
```

**`DynamicList.html`:**
```html
<div class="dynamic-list">
    <h2>{%title%}</h2>
    <ul class="list-items">
        {%items%}
    </ul>
</div>
```

**Usage:**
```html
<dynamic_list 
    title="Product Features"
    items="renderItems"
    items-data='[
        {"name": "Fast", "value": "Lightning quick performance"},
        {"name": "Secure", "value": "Enterprise-grade security"},
        {"name": "Scalable", "value": "Grows with your needs"}
    ]'
/>
```

## Best Practices

### 1. Single Responsibility
- Each component should do one thing well
- Keep components focused and reusable
- Avoid business logic in components

### 2. Default Values
- Always provide sensible defaults
- Make components usable without attributes
- Document default values

### 3. Performance
- Cache expensive operations
- Minimize database/API calls in components
- Use lazy loading for heavy components

### 4. Security
- Always escape output in templates
- Validate and sanitize JSON data
- Use prepared statements for database queries

### 5. Testing
- Test components in isolation
- Verify template variable replacement
- Test with different attribute combinations
- Check responsive behavior

### 6. Documentation
- Document component attributes and defaults
- Provide usage examples
- Include CSS customization options
- Document method handler requirements

## Component Lifecycle

1. **Instantiation**: Component class created with parameters
2. **Template Loading**: HTML template loaded from file
3. **Default Merging**: Parameters merged with defaults
4. **Method Execution**: Method handlers called if specified
5. **Variable Binding**: Template variables replaced with values
6. **Conditional Processing**: Conditional blocks evaluated
7. **HTML Assembly**: Final HTML assembled
8. **Output**: Rendered HTML returned

## Common Patterns

### 1. Container Components
Components that orchestrate other components:

```php
public function renderDashboard() {
    return 
        (new Header(['title' => 'Dashboard']))->return() .
        (new Stats(['metrics' => $this->getMetrics()]))->return() .
        (new Chart(['data' => $this->getChartData()]))->return() .
        (new Footer())->return();
}
```

### 2. Data-Driven Components
Components that render based on data:

```php
public function renderFromApi() {
    $data = $this->service->http()->get('/api/data');
    return $this->renderTemplate($data);
}
```

### 3. Layout Components
Components that provide structure:

```html
<layout_container>
    <layout_header />
    <layout_main>
        {%content%}
    </layout_main>
    <layout_footer />
</layout_container>
```

## Troubleshooting

### Common Issues

**1. Component Not Found**
- Check component directory exists
- Verify file naming matches class name
- Ensure component is in `resources/components/`

**2. Variables Not Rendering**
- Check variable syntax: `{%variable%}`
- Verify attribute is passed from template
- Check for typos in variable names

**3. Method Handlers Not Working**
- Ensure method exists in component class
- Check method is public
- Verify attribute value matches method name

**4. JSON Data Issues**
- Validate JSON syntax
- Check for proper escaping in templates
- Use `$this->json()` for decoding

**5. CSS Not Applying**
- Verify CSS file exists
- Check class names in HTML
- Ensure CSS is loaded in template

## Next Steps

- Learn about [Template System](TEMPLATES.md)
- Explore [Advanced Component Patterns](ADVANCED_COMPONENTS.md)
- Study [Component Performance](PERFORMANCE.md#components)
- Check [Component Examples](EXAMPLES.md)

---

*Components are the heart of InstanceIO applications. Master them to build powerful, reusable, and maintainable web interfaces.*