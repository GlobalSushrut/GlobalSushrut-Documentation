# bidding-app - documentation

*This documentation is from the private repository bidding-app.*

---

# Bidding Application Documentation

## Table of Contents

1. [System Architecture](#system-architecture)
2. [YAML Configuration System](#yaml-configuration-system)
3. [Authentication & Authorization Framework](#authentication--authorization-framework)
4. [API Integration & Middleware](#api-integration--middleware)
5. [Data Flow & Caching Strategy](#data-flow--caching-strategy)
6. [User Experience (UX) Guidelines](#user-experience-ux-guidelines)
7. [Implementation Plan](#implementation-plan)
8. [Security Considerations](#security-considerations)

## System Architecture

### Overview

The Bidding Application is built on a modern, decoupled architecture that separates configuration from implementation through a YAML-driven infrastructure. This approach allows for rapid adjustments to application behavior without changing code, while maintaining a secure and performant user experience.

### Core Components

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│                 │     │                  │     │                 │
│  React Client   │◄───►│  Express Server  │◄───►│  Data Storage   │
│                 │     │                  │     │                 │
└─────────────────┘     └──────────────────┘     └─────────────────┘
        │                       │                        │
        │                       │                        │
        ▼                       ▼                        ▼
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│                 │     │                  │     │                 │
│  YAML-Driven    │     │  YAML-Driven     │     │  YAML-Driven    │
│  UI Components  │     │  API Routing     │     │  Data Access    │
│                 │     │                  │     │                 │
└─────────────────┘     └──────────────────┘     └─────────────────┘
        │                       │                        │
        │                       │                        │
        └───────────────────────┼────────────────────────┘
                                │
                                ▼
                      ┌──────────────────┐
                      │                  │
                      │  Integration     │
                      │  Configuration   │
                      │                  │
                      └──────────────────┘
```

### Key Technical Features

1. **YAML-Driven Configuration**: Application behavior defined through YAML files
2. **Component-Based Frontend**: React components with clear separation of concerns
3. **RESTful API Architecture**: Consistent API patterns with proper status codes and responses
4. **Real-Time Notifications**: Socket.io integration for bidding updates and notifications
5. **Security-First Approach**: Comprehensive authentication and verification system
6. **Caching Strategy**: Memory and Redis-based caching for improved performance
7. **Data Flow Management**: Clear pipelines for data transformation and validation

## YAML Configuration System

### Configuration Files Structure

The application utilizes five primary YAML configuration files, each handling specific aspects of the system:

| Configuration File | Purpose | Handler |
|-------------------|---------|---------|
| `api-config.yaml` | Defines API endpoints, methods, and routes | `api-yaml-handler.js` |
| `auth-config.yaml` | Controls authentication, roles, and permissions | `auth-yaml-handler.js` |
| `cache-config.yaml` | Manages caching and real-time socket events | `yaml-cache-handler.js` |
| `db-routing-config.yaml` | Handles database connections and query routing | `yaml-db-router.js` |
| `integration-config.yaml` | Connects pages, components, and data flows | `yaml-integration-handler.js` |

### How YAML Configurations Work

1. **Loading**: Each handler loads and parses its corresponding YAML file
2. **Initialization**: Configurations are used to set up middleware, routes, and services
3. **Runtime Usage**: Application behavior is determined by the loaded configurations
4. **Hot Reloading**: Some aspects support configuration updates without restart

### Example: API Configuration

```yaml
# api-config.yaml (excerpt)
endpoints:
  - path: "/api/listings"
    method: "GET"
    controller: "ListingController"
    function: "getAllListings"
    auth_required: false
    cache: true
    rate_limit:
      max_requests: 100
      window: 60
```

The corresponding handler (`api-yaml-handler.js`) reads this configuration and creates the appropriate Express route with all necessary middleware.

## Authentication & Authorization Framework

### Authentication Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│             │     │             │     │             │     │             │
│  Login or   │────►│  Validate   │────►│ Generate    │────►│  Return     │
│  Register   │     │  Credentials│     │ JWT Token   │     │  User+Token │
│             │     │             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
                           │
                           │ If failure
                           ▼
                    ┌─────────────┐
                    │             │
                    │  Handle     │
                    │  Auth Error │
                    │             │
                    └─────────────┘
```

### Authorization System

The authorization system is role-based with granular permissions, defined in `auth-config.yaml`:

1. **Roles**: Pre-defined user types (guest, user, seller, admin, moderator)
2. **Permissions**: Specific actions a role can perform (e.g., `listings:create:own`)
3. **Resource Access**: Resources that require specific permissions
4. **Owner Checks**: Verifying a user owns a resource they're modifying

### Verification System

The application implements a multi-stage verification system for adult content and sensitive operations:

1. **Age Verification**: Confirms users are 18+ before accessing adult content
2. **Identity Verification**: Document-based identity confirmation
3. **Face Recognition**: Matches user's face with identity documents
4. **Agreement Acceptance**: Requires users to accept specific agreements

## API Integration & Middleware

### Middleware Chain

API requests flow through a series of middleware components:

```
Request → Authentication → Authorization → Validation → Handler → Response
```

Each middleware is conditionally applied based on the YAML configuration:

```javascript
// Example middleware chain from yaml-integration-handler.js
router[method](path, 
  endpoint.auth_required ? authMiddleware : null,
  endpoint.requires_agreement ? requireAgreement(endpoint.requires_agreement) : null,
  endpoint.requires_verification ? [requireAgeVerification(), requireIdentityVerification()] : null,
  endpoint.owner_check ? requireOwnership(/* owner check function */) : null,
  endpoint.rate_limit ? rateLimit(endpoint.rate_limit.max_requests, endpoint.rate_limit.window) : null,
  endpoint.cache_key && method === 'get' ? createCacheMiddleware(apiGroup.cache_region, /* cache key function */) : null,
  routeHandler
);
```

### API Connector Integration

The client-side `api-connector.js` uses the same YAML configuration to connect to the API:

1. Loads the API configuration
2. Creates matching client-side methods
3. Handles authentication tokens and headers
4. Provides consistent error handling
5. Supports mock data for development

## Data Flow & Caching Strategy

### Data Flow Pipeline

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│             │     │             │     │             │     │             │
│  Component  │────►│  API Call   │────►│ Cache Check │────►│  Database   │
│  Request    │     │  (Connector)│     │ (If GET)    │     │  Query      │
│             │     │             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
       ▲                                        │                  │
       │                                        │                  │
       │                                        │                  │
┌─────────────┐     ┌─────────────┐            │                  │
│             │     │             │            │                  │
│  UI Update  │◄────│  Component  │◄───────────┴──────────────────┘
│             │     │  Response   │
│             │     │  Handler    │
└─────────────┘     └─────────────┘
```

### Caching Strategy

The application uses a multi-layer caching approach:

1. **Memory Cache**: In-memory cache for frequently accessed data
2. **Redis Cache**: Optional distributed cache for scaling
3. **Region-Based Caching**: Different TTLs for different data types
4. **Event-Based Invalidation**: Cache invalidated by relevant events

### Real-Time Updates

Socket.io integration enables real-time updates:

1. **Namespaces**: Separate channels for different features (listings, bids, etc.)
2. **Rooms**: Users join rooms for specific items they're interested in
3. **Event Broadcasting**: Updates automatically pushed to relevant users
4. **Cache Integration**: Cache invalidation triggers socket events

## User Experience (UX) Guidelines

### User Flow Design

The user journey through the application follows these core principles:

1. **Intuitive Navigation**: Clear paths between related pages
2. **Progressive Disclosure**: Show only what's needed at each step
3. **Minimal Friction**: Reduce steps required to complete actions
4. **Consistent Feedback**: Clear indicators of system status
5. **Error Prevention**: Guide users away from mistakes

### Critical User Flows

#### Bidding Flow

```
Browse Listings → View Item → Place Bid → Receive Updates → Win/Lose Notification
```

**UX Improvements Needed:**
- Add bid suggestions based on current price
- Provide clearer countdown timers for auctions
- Implement immediate feedback after placing a bid
- Add notification preferences for bid updates

#### Listing Creation Flow

```
Create Listing → Add Details → Upload Photos → Set Price/Auction → Publish
```

**UX Improvements Needed:**
- Implement drag-and-drop image uploading
- Add image editing/cropping tools
- Provide pricing suggestions based on similar items
- Implement a clearer review step before publishing

#### Verification Flow

```
Start Verification → Age Verification → Identity Verification → Face Recognition → Completion
```

**UX Improvements Needed:**
- Break process into clearly defined steps with progress indicators
- Provide better guidance for document photography
- Add clear privacy explanations at each step
- Implement a verification status dashboard

### Mobile Responsiveness

The application must follow these mobile-specific guidelines:

1. **Touch-Friendly Targets**: All interactive elements at least 44×44px
2. **Simplified Navigation**: Collapsible menus for smaller screens
3. **Reduced Input**: Minimize typing required on mobile
4. **Optimized Images**: Responsive images that load appropriately for device
5. **Orientation Support**: Proper function in both portrait and landscape

## Implementation Plan

### Integration Phase 1: Core Infrastructure

1. **Initialize YAML Handlers**:
   - Ensure all YAML configuration files are properly loaded
   - Connect handlers to the Express application
   - Verify configuration validation

2. **Authentication Integration**:
   - Update login/register components to use auth-connector.js
   - Implement token management and renewal
   - Set up verification flow components

3. **API Connection**:
   - Connect all components to API through api-connector.js
   - Implement proper error handling
   - Set up loading states for all API calls

### Integration Phase 2: Data Flow & UI Components

1. **Component Integration**:
   - Update all components to use the YAML-based data flow
   - Implement consistent state management
   - Connect components to real-time events

2. **Navigation System**:
   - Implement navigation structure from YAML
   - Set up proper route guards based on authentication and verification
   - Add breadcrumb navigation for deep pages

3. **Form Implementations**:
   - Standardize form validation across the application
   - Connect forms to API endpoints with proper error handling
   - Implement agreement acceptance UI

### Integration Phase 3: Optimization & Polishing

1. **Caching Implementation**:
   - Enable region-based caching for API responses
   - Implement local storage caching for appropriate data
   - Set up proper cache invalidation

2. **Real-Time Features**:
   - Connect all components to socket events
   - Implement notification system
   - Add real-time bid updates and counters

3. **Performance Optimization**:
   - Implement lazy loading for images and components
   - Add proper loading states and skeletons
   - Optimize bundle size through code splitting

## Security Considerations

### Authentication Security

1. **Token Management**:
   - Use secure HTTP-only cookies for token storage
   - Implement proper token expiration and refresh
   - Apply CSRF protection for all sensitive operations

2. **Password Security**:
   - Enforce strong password requirements
   - Implement account lockout after failed attempts
   - Provide secure password reset flow

### Content Security

1. **User-Generated Content**:
   - Sanitize all user inputs to prevent XSS
   - Implement content moderation for listings and messages
   - Apply proper file type validation for uploads

2. **Adult Content Protection**:
   - Ensure robust age verification
   - Implement proper content warnings
   - Provide clear reporting mechanisms for inappropriate content

### Data Protection

1. **Personal Information**:
   - Minimize collection of personal data
   - Implement proper data encryption
   - Provide clear data deletion mechanisms

2. **Payment Security**:
   - Use trusted third-party payment processors
   - Implement proper order verification
   - Never store sensitive payment details
