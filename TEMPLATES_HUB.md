# Templates and the Hub

InstanceIO is building an open library of reusable components and base templates for new IIO projects.

## Template library

Templates provide starting structures for:

- Server-rendered websites.
- Static HTML exports.
- HTMx interfaces.
- Documentation sites.
- Application shells.
- Common landing-page and content workflows.

A template is a starting point, not a locked product. Bring it into a project and change the PHP class, HTML template, CSS, routes, configuration, data, and component composition.

## Fully customizable components

InstanceIO components are organized as a PHP class, an HTML template, and CSS:

```text
resources/components/ComponentName/
├── ComponentName.php
├── ComponentName.html
└── ComponentName.css
```

Create a component with:

```bash
php console/cmd.php Make component ComponentName
```

Use it in an `.iio` view:

```html
<hero
    title="Your product"
    description="Your message"
    primary-cta="Get started"
    primary-cta-link="/docs"
/>
```

The component can be replaced, extended, composed, or restyled without giving up the framework conventions.

## The Hub

The Templates Hub is planned as an open browser for:

- Base templates that can seed a new project.
- Reusable components that can be copied into an application.
- Source previews and implementation details.
- Usage notes, configuration guidance, and project structure.
- AI-ready metadata and knowledge-base context.

The Hub preview at the website’s `/hub` route shows the intended direction. The public catalog is not live yet.

## AI-ready templates

IIO templates use structured source instead of opaque page exports. PHP classes, HTML templates, CSS, routes, and configuration remain inspectable and can be indexed, transformed, and adapted by AI tools.

This structure gives an AI system clear boundaries for making changes:

- Component behavior belongs in the PHP class.
- Markup belongs in the HTML template.
- Presentation belongs in CSS.
- Page composition belongs in the `.iio` view.
- URL and deployment behavior belongs in routes and build configuration.

## AI knowledge bases

The planned template ecosystem will pair source templates with knowledge bases containing:

- Component descriptions.
- Attribute and usage patterns.
- Configuration guidance.
- Design and composition conventions.
- Examples of safe customization.

Knowledge bases should help AI tools select a useful starting point, explain the generated structure, and preserve project conventions. They complement source and documentation; they do not replace developer review or control of the code.

## Current status

The Hub and its AI knowledge-base integrations are planned. Until the catalog is available, use the framework documentation, component source, and existing website examples as the current reference.
