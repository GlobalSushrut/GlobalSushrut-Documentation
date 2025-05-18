# backend-integrator - infra

*This documentation is from the private repository backend-integrator.*

---

# Backend Integrator: Runtime YAML Infrastructure

This document explains how to use the Backend Integrator infrastructure and what it can do for your projects.

## What Is It?

Backend Integrator is a modern infrastructure automation framework that uses **runtime YAML interpretation** instead of traditional code generation. This means:

1. You define your application structure in YAML configuration files
2. The infrastructure interprets these files at runtime
3. No repetitive code is generated
4. Changes to YAML files are immediately reflected without regeneration
5. You can switch technology stacks without changing configurations

## Core Components

The infrastructure consists of three main engines:

### 1. Counter Engine (`counter.yaml`)

Defines the structure of your application:
- Pages and their routes
- UI components and their properties
- Data pipelines and transformations
- API endpoints and their behavior

```yaml
# Example counter.yaml
name: E-Commerce Application
description: A demo e-commerce application
pages:
  - name: Dashboard
    path: /dashboard
    components:
      - SalesChart
      - RecentOrders
    pipeline:
      data:
        - sales
        - orders
```

### 2. Roller Engine (`roller.yaml`)

Handles authentication and role-based access control:
- User roles and permissions
- Authentication methods
- Protected routes
- Role-based component visibility

```yaml
# Example roller.yaml
name: Authentication System
description: Role-based access control
roles:
  - name: admin
    permissions: ['read', 'write', 'delete']
  - name: manager
    permissions: ['read', 'write']
  - name: user
    permissions: ['read']
```

### 3. Cloud Injector (`cloud.yaml`)

Manages deployment and infrastructure:
- Environment configurations
- SSL settings
- Container definitions
- Scaling parameters
- Monitoring setup

```yaml
# Example cloud.yaml
name: Infrastructure Configuration
description: Deployment and monitoring
environments:
  - name: production
    domain: example.com
    ssl: true
  - name: staging
    domain: staging.example.com
    ssl: true
```

## Multi-Language Support

One of the most powerful features is the ability to switch seamlessly between different technology stacks:

### Frontend Frameworks
- React
- Vue
- Angular
- Svelte

### Backend Frameworks
- Express (Node.js)
- FastAPI (Python)
- Go
- Rust
- Ruby

The same YAML configuration files work across all these technology combinations, allowing you to switch frameworks without rewriting your application logic.

## How to Use

### 1. Install the Framework

```bash
# Clone the repository
git clone https://github.com/yourusername/backend-integrator.git
cd backend-integrator

# Install dependencies
pip install -r requirements.txt

# Or install as a package
pip install -e .
```

### 2. Create a New Project

```bash
# Using the connector tunnel CLI
python -m infra_builder.connector_tunnel \
  --config-dir ./templates/config \
  --frontend react \
  --backend express \
  --scaffold ./my-project
```

This creates a new project structure with:
- Template YAML configuration files
- Starter frontend and backend code
- Project structure based on the selected technology stack

### 3. Edit YAML Configuration Files

Edit the YAML files in `./my-project/config/`:
- `counter.yaml`: Define your pages, components, and API endpoints
- `roller.yaml`: Set up authentication and role-based access control
- `cloud.yaml`: Configure deployment settings

The infrastructure will interpret these files at runtime to create your application.

### 4. Switch Technology Stack

You can switch between different frontend and backend technologies at any time:

```bash
python -m infra_builder.connector_tunnel \
  --config-dir ./my-project/config \
  --frontend vue \
  --backend fastapi \
  --connect
```

This will reconfigure your application to use Vue.js for the frontend and FastAPI for the backend, all without changing your YAML configurations.

### 5. Deploy Your Application

When you're ready to deploy:

```bash
python -m infra_builder.connector_tunnel \
  --config-dir ./my-project/config \
  --deploy ./deployment
```

This generates all necessary deployment files (Docker configurations, nginx configs, deployment scripts) based on your cloud.yaml configuration.

## Practical Examples

### Create a Dashboard Page

In `counter.yaml`:

```yaml
pages:
  - name: Dashboard
    path: /dashboard
    components:
      - name: SalesChart
        type: chart
        dataSource: sales
      - name: RecentOrders
        type: table
        dataSource: orders
    api:
      - path: /api/dashboard/sales
        method: GET
      - path: /api/dashboard/orders
        method: GET
```

No need to write repetitive code for components or API endpoints - the infrastructure creates them at runtime.

### Add Role-Based Access Control

In `roller.yaml`:

```yaml
roles:
  - name: admin
    permissions: ['dashboard:read', 'dashboard:write', 'orders:read', 'orders:write']
  - name: user
    permissions: ['dashboard:read', 'orders:read']

protected_routes:
  - path: /dashboard
    requiredPermissions: ['dashboard:read']
  - path: /api/orders
    requiredPermissions: ['orders:read']
```

This automatically restricts access based on user roles.

## Advanced Usage

### Custom Component Templates

Create custom component templates that work across all frontend frameworks:

```yaml
# In counter.yaml
components:
  - name: ProductCard
    template: templates/ProductCard
    props:
      - name: product
        type: object
      - name: onAddToCart
        type: function
```

### Data Pipelines

Define complex data transformations without writing code:

```yaml
# In counter.yaml
pipelines:
  - name: salesData
    source: /api/sales
    transformations:
      - type: filter
        condition: "value > 100"
      - type: map
        expression: "{ amount: value, date: key }"
      - type: sort
        key: "amount"
        order: "desc"
```

### Dynamic API Routes

Create API endpoints with parameters:

```yaml
# In counter.yaml
api:
  - path: /api/products/:id
    method: GET
    parameters:
      - name: id
        type: string
        required: true
    responses:
      - status: 200
        schema: Product
      - status: 404
        message: "Product not found"
```

## Benefits Over Code Generation

1. **Single Source of Truth**: All configuration in YAML files
2. **No Repetitive Code**: No need to write the same code patterns repeatedly
3. **Technology Agnostic**: Switch frameworks without rewriting code
4. **Immediate Updates**: Changes to YAML are reflected instantly
5. **Consistent Patterns**: Enforces best practices across the application
6. **Reduced Maintenance**: Less code to maintain and debug

## Real-World Success Stories

Our infrastructure has been successfully tested and validated across multiple technology combinations:

1. **E-commerce Platform**: Built with React + Express, then later switched to Vue + FastAPI without changing business logic
2. **Admin Dashboard**: Created with Angular + Go, using the same YAML configuration files
3. **Multi-tenant SaaS Application**: Built with Svelte + Ruby, leveraging role-based access control from roller.yaml

## Extending the Framework

The infrastructure is designed to be extensible:

1. **Custom Engines**: Create your own YAML interpreters for specialized functionality
2. **New Technology Support**: Add support for additional frontend or backend frameworks
3. **Custom Templates**: Define your own component and page templates
4. **Integration with External Systems**: Add connectors for databases, messaging systems, etc.

## Troubleshooting

### Common Issues

1. **YAML Syntax Errors**: Make sure your YAML files follow the correct format
2. **Missing Dependencies**: Ensure all required packages are installed
3. **Port Conflicts**: Check for running servers on the same ports
4. **Path Issues**: Use absolute paths or relative paths correctly

### Debugging

Set the log level to debug for more detailed information:

```bash
export LOG_LEVEL=DEBUG
python -m infra_builder.connector_tunnel ...
```

## Conclusion

The Backend Integrator infrastructure provides a powerful, flexible way to build applications using runtime YAML interpretation instead of code generation. It allows you to:

1. Define your application structure in simple YAML files
2. Switch between different technology stacks seamlessly
3. Deploy to various environments with minimal configuration
4. Follow consistent patterns and best practices
5. Reduce code duplication and maintenance overhead

By separating the configuration from the implementation, you can focus on your business logic while the infrastructure handles the technical details.
