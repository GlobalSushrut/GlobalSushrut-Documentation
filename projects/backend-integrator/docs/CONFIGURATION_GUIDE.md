# backend-integrator - CONFIGURATION_GUIDE

*This documentation is from the private repository backend-integrator.*

---

# Backend Integrator - Configuration Guide

This guide explains how to write configuration files for the Backend Integrator framework. The framework uses three main YAML files to generate production-ready code for multiple technology stacks.

## Table of Contents

1. [Overview](#overview)
2. [Supported Technologies](#supported-technologies)
3. [Counter Engine (counter.yaml)](#counter-engine)
4. [Roller Engine (roller.yaml)](#roller-engine)
5. [Cloud Injector (cloud.yaml)](#cloud-injector)
6. [Complete Examples](#complete-examples)
7. [CLI Usage](#cli-usage)
8. [Production Deployment](#production-deployment)
9. [Troubleshooting](#troubleshooting)

## Overview

The Backend Integrator framework uses three configuration files to generate a complete application stack:

- **counter.yaml**: Defines application structure, pages, and API endpoints
- **roller.yaml**: Configures authentication, authorization, and role-based access control
- **cloud.yaml**: Specifies deployment and infrastructure settings

## Supported Technologies

### Frontend Frameworks
- **React**: Modern component-based UI library
- **Angular**: Full-featured MVC framework
- **Vue.js**: Progressive JavaScript framework
- **Svelte**: Compile-time framework with minimal runtime

### Backend Languages/Frameworks
- **Express (Node.js)**: Lightweight web framework for Node.js
- **FastAPI (Python)**: Modern, high-performance Python web framework
- **Go (Gin)**: Lightweight HTTP web framework for Go
- **Rust (Actix)**: Safe, concurrent web framework for Rust
- **Ruby (Rails)**: Full-featured web application framework for Ruby

## Counter Engine

The `counter.yaml` file defines your application structure, pages, and API endpoints. It's responsible for generating the core application code.

### Schema

```yaml
frontend:
  framework: <frontend-framework>  # react, vue, angular, or svelte
backend:
  language: <backend-language>     # express, fastapi, go, rust, or ruby

pages:
  <PageName>:
    path: "<route-path>"
    components: [<ComponentName1>, <ComponentName2>, ...]
    api_calls:
      - name: <apiMethodName>
        endpoint: "<api-endpoint-path>"
        method: <HTTP-METHOD>      # GET, POST, PUT, DELETE
        params: <optional-boolean> # Whether this endpoint accepts parameters
        secured: <optional-boolean> # Whether this endpoint requires authentication
        roles: [<Role1>, <Role2>]   # Optional list of roles that can access this endpoint
```

### Frontend Framework-Specific Options

#### React
```yaml
frontend:
  framework: react
  options:
    routing: "react-router-v6"  # Options: react-router-v5, react-router-v6
    state: "redux"             # Options: redux, context, recoil
    styling: "styled-components" # Options: css, sass, styled-components, emotion
```

#### Angular
```yaml
frontend:
  framework: angular
  options:
    version: "15"              # Angular version
    standalone: true           # Use standalone components (Angular 14+)
    state: "ngrx"              # Options: ngrx, services, signals
```

#### Vue
```yaml
frontend:
  framework: vue
  options:
    version: "3"               # Vue version
    routing: "vue-router"      # Options: vue-router
    state: "pinia"             # Options: vuex, pinia
```

#### Svelte
```yaml
frontend:
  framework: svelte
  options:
    routing: "svelte-routing"  # Options: svelte-routing, svelte-navigator
    state: "stores"            # Options: stores, svelte-store
```

### Backend Language-Specific Options

#### Express (Node.js)
```yaml
backend:
  language: express
  options:
    database: "mongodb"        # Options: mongodb, postgresql, mysql
    orm: "mongoose"           # Options: mongoose, sequelize, prisma
```

#### FastAPI (Python)
```yaml
backend:
  language: fastapi
  options:
    database: "postgresql"     # Options: postgresql, mysql, sqlite
    orm: "sqlalchemy"         # Options: sqlalchemy, tortoise-orm
```

### Complete Example

```yaml
frontend:
  framework: react
  options:
    routing: "react-router-v6"
    state: "redux"
backend:
  language: express
  options:
    database: "mongodb"
    orm: "mongoose"

pages:
  Dashboard:
    path: "/dashboard"
    components: [UserTable, DashboardStats]
    api_calls:
      - name: getUsers
        endpoint: "/api/users"
        method: GET
        secured: true
        roles: [admin]
      - name: getStats
        endpoint: "/api/stats"
        method: GET
        secured: true

  Profile:
    path: "/profile"
    components: [ProfileDetails, UserActivity]
    api_calls:
      - name: getProfile
        endpoint: "/api/profile"
        method: GET
        secured: true
      - name: updateProfile
        endpoint: "/api/profile"
        method: PUT
        secured: true
        params: true
```

## Roller Engine

The `roller.yaml` file configures authentication, authorization, and role-based access control (RBAC) for your application.

### Schema

```yaml
auth:
  method: <auth-method>          # jwt, oauth2, firebase, etc.
  jwt_secret: "<secret-key>"      # Required for JWT authentication
  token_expiry: <expiry-seconds>  # Token expiration time in seconds

roles:
  <RoleName>:
    access_pages: [<Page1>, <Page2>, ...]
    access_apis: ["<api-path-1>", "<api-path-2>", ...]
    permissions: [<Permission1>, <Permission2>, ...]

password_policy:
  min_length: <integer>          # Minimum password length
  hash_algorithm: <algorithm>    # bcrypt, argon2, etc.
  require_special: <boolean>     # Require special characters
  require_uppercase: <boolean>   # Require uppercase letters
```

### Advanced Authentication Options

#### JWT Authentication
```yaml
auth:
  method: jwt
  jwt_secret: "${JWT_SECRET}"    # Use environment variable in production
  token_expiry: 3600             # 1 hour in seconds
  refresh_token: true            # Support refresh tokens
  refresh_expiry: 604800         # 7 days in seconds
```

#### OAuth2 Authentication
```yaml
auth:
  method: oauth2
  providers:
    - name: "google"
      client_id: "${GOOGLE_CLIENT_ID}"
      client_secret: "${GOOGLE_CLIENT_SECRET}"
      callback_url: "/auth/google/callback"
    - name: "github"
      client_id: "${GITHUB_CLIENT_ID}"
      client_secret: "${GITHUB_CLIENT_SECRET}"
      callback_url: "/auth/github/callback"
```

### Advanced RBAC Example

```yaml
roles:
  admin:
    access_pages: [Dashboard, Settings, Users, Analytics]
    access_apis: ["/api/users", "/api/stats", "/api/settings", "/api/analytics"]
    permissions: [create_user, delete_user, edit_settings]
  manager:
    access_pages: [Dashboard, Users]
    access_apis: ["/api/users", "/api/stats"]
    permissions: [create_user]
  user:
    access_pages: [Profile, Settings]
    access_apis: ["/api/profile", "/api/settings"]
    permissions: [edit_profile]
```

## Cloud Injector

The `cloud.yaml` file configures deployment and infrastructure settings for your application.

### Schema

```yaml
environment:
  name: <environment-name>        # dev, staging, production
  region: <cloud-region>          # us-east-1, eu-west-1, etc.

deployment:
  provider: <provider-name>       # aws, azure, gcp, docker
  strategy: <deployment-strategy> # blue-green, canary, rolling

infrastructure:
  database:
    type: <database-type>         # mongodb, postgresql, mysql
    version: <version-string>     # Latest version for the chosen database
  cache:
    enabled: <boolean>            # Whether to use a cache
    type: <cache-type>           # redis, memcached
  scaling:
    min_instances: <integer>      # Minimum number of instances
    max_instances: <integer>      # Maximum number of instances
    auto_scaling: <boolean>       # Whether to enable auto-scaling
  Profile:
    path: "/profile"
    components: [ProfileDetails, UserActivity]
    api_calls:
      - name: getProfile
        endpoint: "/api/profile"
        method: GET
      - name: updateProfile
        endpoint: "/api/profile"
        method: PUT
```

### Best Practices

1. **Naming Conventions**:
   - Use PascalCase for page names
   - Use camelCase for API method names
   - Use kebab-case for route paths

2. **API Endpoints**:
   - Follow RESTful API conventions
   - Group related endpoints under the same base path
   - Use plural nouns for collection endpoints (e.g., `/api/users`)

3. **Components**:
   - List all components needed for each page
   - Components will be automatically imported in the generated code

## Roller Engine

The `roller.yaml` file configures authentication, authorization, and role-based access control for your application.

### Schema

```yaml
auth:
  method: <auth-method>            # jwt, oauth, etc.
  jwt_secret: "<secret-string>"    # For JWT authentication
  token_expiry: <seconds>          # Token expiry time in seconds

roles:
  <RoleName>:
    access_pages: [<PageName1>, <PageName2>, ...]
    access_apis: ["<api-endpoint1>", "<api-endpoint2>", ...]

password_policy:
  min_length: <number>
  hash_algorithm: <algorithm>      # bcrypt, argon2, etc.
```

### Example

```yaml
auth:
  method: jwt
  jwt_secret: "your-secret-key-here"  # Use environment variables in production
  token_expiry: 3600  # 1 hour

roles:
  admin:
    access_pages: [Dashboard, Settings]
    access_apis: ["/api/users", "/api/stats", "/api/settings"]
  user:
    access_pages: [Profile, Settings]
    access_apis: ["/api/profile", "/api/settings"]

password_policy:
  min_length: 8
  hash_algorithm: bcrypt
```

### Best Practices

1. **Security**:
   - Never commit your actual JWT secret to version control
   - Use environment variables for secrets in production
   - Set reasonable token expiry times (1-24 hours typical)

2. **Role-Based Access Control**:
   - Define clear roles with specific permissions
   - Use the principle of least privilege
   - Consider using wildcards for API access (`/api/users/*`)

3. **Password Policies**:
   - Require sufficiently strong passwords (min 8 characters)
   - Use modern hashing algorithms (bcrypt, argon2)
   - Consider adding complexity requirements

## Cloud Injector

The `cloud.yaml` file specifies deployment and infrastructure settings for your application.

### Schema

```yaml
domain: "<domain-name>"
ssl_email: "<email-for-ssl>"
frontend_port: <port-number>
backend_port: <port-number>

docker:
  frontend_image: "<docker-image>"
  backend_image: "<docker-image>"
  db_image: "<docker-image>"

certbot:
  use_certbot: <boolean>
```

### Example

```yaml
domain: "myapp.com"
ssl_email: "admin@myapp.com"
frontend_port: 3000
backend_port: 8000

docker:
  frontend_image: "node:20-alpine"
  backend_image: "node:20-alpine"
  db_image: "mongo:latest"

certbot:
  use_certbot: true
```

### Best Practices

1. **Domain Configuration**:
   - Use a proper domain name for production
   - Use a valid email for SSL certificates

2. **Docker Images**:
   - Specify exact versions to ensure consistency
   - Use lightweight images when possible
   - Consider security-hardened images for production

3. **Ports**:
   - Use standard ports when possible
   - Ensure ports don't conflict with other services

## Complete Example

A complete example of all three configuration files working together:

**counter.yaml**:
```yaml
frontend:
  framework: react
backend:
  language: express

pages:
  Login:
    path: "/login"
    components: [LoginForm]
    api_calls:
      - name: login
        endpoint: "/auth/login"
        method: POST
  Dashboard:
    path: "/dashboard"
    components: [UserTable, DashboardStats]
    api_calls:
      - name: getUsers
        endpoint: "/api/users"
        method: GET
      - name: getStats
        endpoint: "/api/stats"
        method: GET
  Profile:
    path: "/profile"
    components: [ProfileDetails, UserActivity]
    api_calls:
      - name: getProfile
        endpoint: "/api/profile"
        method: GET
      - name: updateProfile
        endpoint: "/api/profile"
        method: PUT
  Settings:
    path: "/settings"
    components: [SettingsForm]
    api_calls:
      - name: getSettings
        endpoint: "/api/settings"
        method: GET
      - name: updateSettings
        endpoint: "/api/settings"
        method: PUT
```

**roller.yaml**:
```yaml
auth:
  method: jwt
  jwt_secret: "${JWT_SECRET}"  # Use environment variable
  token_expiry: 86400  # 24 hours

roles:
  admin:
    access_pages: [Dashboard, Profile, Settings]
    access_apis: ["/api/users", "/api/stats", "/api/profile", "/api/settings"]
  user:
    access_pages: [Profile, Settings]
    access_apis: ["/api/profile", "/api/settings"]

password_policy:
  min_length: 10
  hash_algorithm: bcrypt
```

**cloud.yaml**:
```yaml
domain: "myproductionapp.com"
ssl_email: "devops@myproductionapp.com"
frontend_port: 3000
backend_port: 8000

docker:
  frontend_image: "node:20-alpine"
  backend_image: "node:20-alpine"
  db_image: "mongo:5.0.6"

certbot:
  use_certbot: true
```

## CLI Usage

Once you've created your configuration files, use the CLI to generate code:

```bash
# Initialize a new project with Vue.js frontend and FastAPI backend
python cli.py init my-project --frontend vue --backend fastapi

# Generate code from existing configurations
python cli.py generate all --config-dir ./config --output-dir ./generated --frontend react --backend express

# Generate only the frontend code
python cli.py generate counter --frontend svelte

# Generate only the authentication code
python cli.py generate roller --backend rust
```

### Framework Matrix

The Backend Integrator supports the following technology combinations:

| Frontend | Backend |
|----------|---------|
| React    | Express (Node.js) |
| Vue.js   | FastAPI (Python) |
| Angular  | Go (Gin) |
| Svelte   | Rust (Actix Web) |
|          | Ruby on Rails |

Choose the combination that best suits your project requirements and team expertise.

---

By following this guide, you can quickly set up a production-ready application with proper authentication, authorization, and infrastructure. The generated code follows best practices for each framework and includes security features required for production applications.
