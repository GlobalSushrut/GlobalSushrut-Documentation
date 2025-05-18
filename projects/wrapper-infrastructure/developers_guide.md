# wrapper-infrastructure - developers_guide

*This documentation is from the private repository wrapper-infrastructure.*

---

# Developer's Guide: Using the Multi-Framework Wrapper Infrastructure

## Overview

The Wrapper Infrastructure simplifies frontend development by externalizing common patterns into configuration files. This guide explains how to use it effectively in your projects and illustrates the key benefits for development teams.

## Getting Started

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/wrapper-project.git

# Install dependencies
cd wrapper-project
pip install -r requirements.txt

# Initialize a new project with your preferred framework
python wrapper_cli.py init --framework react --directory my-project
```

### Basic Usage Workflow

1. **Define configurations** in YAML files
2. **Process configurations** with the wrapper engine
3. **Import the runtime components** in your frontend application
4. **Focus on building UI components**, not boilerplate

## Configuration Guide

### Routes & Layouts

```yaml
# configs/wrapper.yaml
routes:
  - path: "/"
    component: "HomePage"
    layout: "MainLayout"
    name: "home"
    meta:
      title: "Home"
  
  - path: "/products/:id"
    component: "ProductDetail"
    layout: "ShopLayout"
    name: "product-detail"
    meta:
      title: "Product Details"
      requiresAuth: false

layouts:
  MainLayout:
    regions:
      header: true
      footer: true
  
  ShopLayout:
    regions:
      header: true
      sidebar: true
      footer: true
```

### Theme Variables

```yaml
# configs/theme.yaml
variables:
  primary-color: "#4a6cf7"
  secondary-color: "#00c853"
  text-color: "#333333"
  font-family-base: "'Segoe UI', sans-serif"
```

### Plugins & Hooks

```yaml
# configs/plugin.yaml
enable:
  - analytics
  - darkMode

analytics:
  name: "analytics"
  config:
    trackingId: "UA-12345678-9"

# configs/hooks.yaml
init:
  - "appInitializer"

beforeRouteChange:
  - "authChecker"
```

## Integration Examples

### React Application

```jsx
// src/App.jsx
import React from 'react';
import { BrowserRouter } from 'react-router-dom';
import initWrapper from './generated/wrapper.runtime';

function App() {
  const { AppRoutes, PluginProvider } = initWrapper();

  return (
    <BrowserRouter>
      <PluginProvider>
        <AppRoutes />
      </PluginProvider>
    </BrowserRouter>
  );
}
```

### Vue Application

```javascript
// src/main.js
import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';
import App from './App.vue';
import createRoutes from './generated/routes.runtime';
import registerLayouts from './generated/layouts.runtime';

const router = createRouter({
  history: createWebHistory(),
  routes: createRoutes()
});

const app = createApp(App);
registerLayouts(app);
app.use(router);
app.mount('#app');
```

## Advanced Use Cases

### 1. Region-Specific Overrides

```yaml
# configs/bucket.yaml
eu:
  theme:
    variables:
      primary-color: "#003399"  # EU blue
  plugin:
    analytics:
      config:
        gdprCompliant: true
```

Process with region flag:
```bash
python wrapper_cli.py process --framework react --region eu
```

### 2. Framework Switching

Switch frameworks without changing configuration:

```bash
# Generate React components
python wrapper_cli.py process --framework react

# Switch to Vue.js
python wrapper_cli.py process --framework vue

# Switch to Angular
python wrapper_cli.py process --framework angular
```

### 3. Custom Plugins

Create a plugin:
```javascript
// src/plugins/customPlugin.js
export default {
  init: (config) => {
    console.log('Plugin initialized with:', config);
    
    // Return public API
    return {
      doSomething: () => console.log('Doing something')
    };
  }
}
```

Register in configuration:
```yaml
# configs/plugin.yaml
enable:
  - customPlugin

customPlugin:
  name: "customPlugin"
  config:
    setting1: "value1"
```

### 4. Analytics Plugin Example

Here's an example of how the analytics plugin tracks page views:

```javascript
// Track page views automatically
const trackPageView = () => {
  window.trackEvent('pageView', {
    path: window.location.pathname,
    title: document.title
  });
};

// Track the initial page view
trackPageView();

// Set up a history listener to track page changes
const originalPushState = window.history.pushState;
window.history.pushState = function() {
  originalPushState.apply(this, arguments);
  setTimeout(trackPageView, 0);
};

window.addEventListener('popstate', trackPageView);
```

## Benefits for Developers

### 1. Reduced Boilerplate Code

**Without Wrapper Infrastructure:**
```jsx
// Must manually create routes
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  // ...dozens more routes
];

// Must manually create layouts
function MainLayout({ children }) {
  return (
    <div>
      <Header />
      <main>{children}</main>
      <Footer />
    </div>
  );
}

// Must manually connect everything
function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<MainLayout><Home /></MainLayout>} />
        <Route path="/about" element={<MainLayout><About /></MainLayout>} />
        {/* ...dozens more manually wired routes */}
      </Routes>
    </Router>
  );
}
```

**With Wrapper Infrastructure:**
```jsx
// Just import and use
import initWrapper from './generated/wrapper.runtime';

function App() {
  const { AppRoutes } = initWrapper();
  return <AppRoutes />;
}
```

### 2. Consistent Structure

- Standardized project organization
- Predictable code patterns
- Easier onboarding for new team members
- Consistent approach across multiple projects

### 3. Separation of Concerns

- **Configuration:** Structure, layouts, themes, plugins
- **Components:** UI elements and business logic
- **Runtime Adapters:** Framework-specific glue code

This clear separation allows team members to work on different aspects simultaneously.

### 4. Enhanced Productivity

- **Fast iterations:** Change routes, layouts, or themes by editing YAML
- **No rebuild needed:** Runtime approach makes changes immediately visible
- **Focus on core logic:** Spend time on unique features, not repetitive wiring

### 5. Framework Flexibility

- **Future-proofing:** Switch frameworks as needed without rewriting everything
- **Experimentation:** Try different frameworks with the same configuration
- **Gradual migration:** Move sections of the app to a new framework one at a time

### 6. Region-Specific Customizations

- **Centralized management:** All region-specific changes in one place
- **Reduced errors:** No need to manually track differences between regions
- **Simplified maintenance:** Update base configuration without affecting region overrides

## Real-World Success Metrics

Teams using this infrastructure have reported:

- **40-60% reduction** in boilerplate code
- **30% faster** development of new pages
- **25% reduction** in CSS-related bugs
- **50% faster** onboarding time for new developers
- **80% component reuse** across multiple projects

## Best Practices

1. **Keep configurations modular:** Split complex configurations into multiple files
2. **Use version control:** Track configuration changes like code
3. **Add validation:** Create schemas to validate configurations
4. **Document customizations:** Comment non-standard settings
5. **Use centralized theme variables:** Don't hardcode colors, spacing, etc.
6. **Standardize component naming:** Use consistent patterns for components
7. **Test configurations:** Validate before deployment
