# wrapper-infrastructure - example_usage

*This documentation is from the private repository wrapper-infrastructure.*

---

# Using the Multi-Framework Wrapper Infrastructure

This document demonstrates how to integrate the Wrapper Infrastructure into your frontend application with various frameworks.

## 1. Initialize Your Frontend Project

First, make sure you have a React, Vue, or Angular project set up.

## 2. Process Configuration

Run the wrapper engine to process your configuration files:

```bash
python wrapper_engine.py --framework react
```

For region-specific configurations:

```bash
python wrapper_engine.py --framework react --region eu
```

## 3. Integration with Different Frameworks

### React Integration

Import and use the runtime components in your React application:

```jsx
// src/App.jsx
import React from 'react';
import { BrowserRouter } from 'react-router-dom';
import initWrapper from './generated/wrapper.runtime';

// Initialize the wrapper
const { AppRoutes, PluginProvider, loadPageCSS, hooks } = initWrapper();

// Use hooks to run initialization
React.useEffect(() => {
  // Run the onMount hook
  hooks.runHook('onMount');
  
  // Load page-specific CSS (when navigating)
  loadPageCSS('HomePage');
  
  return () => {
    // Cleanup
    hooks.runHook('onUnmount');
  };
}, []);

function App() {
  return (
    <BrowserRouter>
      <PluginProvider>
        <AppRoutes />
      </PluginProvider>
    </BrowserRouter>
  );
}

export default App;
```

### Vue.js Integration

Import and use the runtime components in your Vue application:

```javascript
// src/main.js
import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';
import App from './App.vue';
import createRoutes from './generated/routes.runtime';
import registerLayouts from './generated/layouts.runtime';

// Create the router instance
const router = createRouter({
  history: createWebHistory(),
  routes: createRoutes()
});

// Create and mount the app
const app = createApp(App);

// Register layouts
registerLayouts(app);

// Use the router
app.use(router);

// Mount the app
app.mount('#app');
```

### Angular Integration

Import and use the runtime components in your Angular application:

```typescript
// src/app/app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
import { AppComponent } from './app.component';
import { WrapperModule, ROUTES } from '../generated/wrapper.runtime';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    RouterModule.forRoot(ROUTES),
    WrapperModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### Svelte Integration

Import and use the runtime components in your Svelte application:

```javascript
// src/main.js
import App from './App.svelte';
import routes from './generated/routes.runtime';
import { Router } from 'svelte-routing';

const app = new App({
  target: document.body,
  props: {
    routes
  }
});

export default app;
```

### Next.js Integration

Create a file for page layout handling in your Next.js application:

```javascript
// src/pages/_app.js
import { useEffect } from 'react';
import { getPageConfig } from '../generated/pages.runtime';
import { withLayout } from '../generated/layouts.runtime';

function MyApp({ Component, pageProps, router }) {
  useEffect(() => {
    // Load any global runtime configurations
    import('../generated/wrapper.runtime.json').then(config => {
      // Apply theme
      const themeVars = config.theme;
      Object.entries(themeVars).forEach(([key, value]) => {
        document.documentElement.style.setProperty(`--${key}`, value);
      });
    });
  }, []);
  
  // Get the page configuration based on the current path
  const pageConfig = getPageConfig(router.pathname);
  
  // Apply the layout if specified in the configuration
  if (pageConfig && pageConfig.layout) {
    return withLayout(Component, pageConfig.layout)(pageProps);
  }
  
  // Default rendering without layout
  return <Component {...pageProps} />;
}

export default MyApp;
```

### Nuxt.js Integration

Customize the router and layouts in your Nuxt.js application:

```javascript
// nuxt.config.js
import getRoutes from './generated/routes.runtime';

export default {
  // ... other Nuxt config
  router: {
    extendRoutes(routes, resolve) {
      // Clear existing routes
      routes.splice(0, routes.length);
      
      // Add routes from runtime configuration
      const configRoutes = getRoutes();
      routes.push(...configRoutes);
    }
  },
  hooks: {
    'modules:before': () => {
      // Load any initialization code
      require('./generated/wrapper.runtime');
    }
  }
};
```

## 4. Creating Components and Layouts

Create your components and layouts according to the configuration:

### Example Layout Component

```jsx
// src/layouts/MainLayout.jsx
import React from 'react';

const MainLayout = ({ children }) => {
  return (
    <div className="main-layout">
      <header className="header">Header Content</header>
      <main className="content">
        {children}
      </main>
      <footer className="footer">Footer Content</footer>
    </div>
  );
};

export default MainLayout;
```

### Example Page Component

```jsx
// src/pages/HomePage.jsx
import React from 'react';
import hooks from '../generated/hooks.runtime';

const HomePage = () => {
  // Use hooks from the runtime system
  hooks.useHook('pageView', ['HomePage']);
  
  return (
    <div className="home-page">
      <h1>Welcome to Homepage</h1>
      <p>This is the main content of the home page.</p>
    </div>
  );
};

export default HomePage;
```

## 5. Creating Plugins

Example plugin implementation:

```jsx
// src/plugins/analytics.js
export default {
  init: (config) => {
    console.log('Analytics plugin initialized with config:', config);
    
    // Set up analytics tracking
    window.trackEvent = (eventName, eventData) => {
      if (config.disableInDevelopment && process.env.NODE_ENV === 'development') {
        console.log('Event tracked (development):', eventName, eventData);
        return;
      }
      
      // In a real implementation, this would send data to an analytics service
      console.log('Event tracked:', eventName, eventData);
    };
    
    return {
      trackPageView: (pageName) => {
        window.trackEvent('pageView', { page: pageName });
      },
      trackUserAction: (action, data) => {
        window.trackEvent('userAction', { action, ...data });
      }
    };
  }
};
```

## 6. Creating Hooks

Example hook implementation:

```jsx
// src/hooks/analyticsTracker.js
export default async (route) => {
  // Track page view when route changes
  if (window.trackEvent) {
    window.trackEvent('pageView', {
      path: route.path,
      name: route.name,
      title: route.meta?.title
    });
  }
  
  return true; // Return value can be used by other hooks
};
```

## 7. Benefits of the Multi-Framework Approach

1. **Framework Flexibility**: Switch between frontend frameworks without changing configuration files
2. **Unified Configuration**: One configuration system powers all your frontend applications
3. **Dynamic Runtime**: Changes to configuration take effect immediately without code regeneration
4. **Reduced Learning Curve**: Developers familiar with one framework can easily work with others
5. **Future-Proof**: As new frameworks emerge, they can be added as adapters without changing configs
6. **Consistent UX**: Ensure consistent user experience across different frontend implementations
7. **Testing Efficiency**: Test your business logic once, deploy to multiple frontend frameworks
8. **Easier Migration**: Gradually migrate from one framework to another without a complete rewrite
9. **Shared Ecosystem**: Plugins and hooks work across all supported frameworks
10. **Edge-Ready**: Apply region-specific overrides consistently across all frontend frameworks
