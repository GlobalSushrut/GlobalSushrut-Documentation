# bidding-app - YAML-API-GUIDE

*This documentation is from the private repository bidding-app.*

---

# YAML-Based API Integration Guide

This guide explains how to use the YAML-based API configuration system to connect your frontend static pages with the server backend APIs.

## Overview

The YAML-based API approach provides several benefits:

1. **Single Source of Truth**: All API endpoints are defined in one place (`api-config.yaml`)
2. **Automatic Documentation**: The YAML file serves as living documentation
3. **Consistent Implementation**: Both frontend and backend use the same configuration
4. **Easy Maintenance**: Update endpoints in one place instead of throughout the codebase
5. **Reduced Bug Fixing**: Standardized approach minimizes point-to-point bugs

## Project Structure

```
/bidding app
├── api-config.yaml            # Main API configuration file
├── yaml-server-integration.js # Example server integration
├── client/
│   └── src/
│       └── utils/
│           └── api-connector.js # Frontend API connector
└── server/
    ├── middleware/
    │   └── yaml-api-middleware.js # Server-side middleware
    └── utils/
        └── api-yaml-handler.js # Server-side YAML handler
```

## How It Works

1. The `api-config.yaml` file defines all API endpoints, their methods, authorization requirements, and associated frontend components
2. The server reads this configuration and automatically sets up routes with proper middleware
3. The frontend uses the same configuration to connect to these endpoints

## Using the API in Frontend Components

### Basic Usage

Import the API connector in your components:

```jsx
import api from '../utils/api-connector';

function ListingsPage() {
  const [listings, setListings] = useState([]);
  
  useEffect(() => {
    // Use the predefined method from the API connector
    api.getListings()
      .then(data => {
        setListings(data.listings);
      })
      .catch(error => {
        console.error('Failed to fetch listings:', error);
      });
  }, []);
  
  return (
    <div>
      <h1>Listings</h1>
      {listings.map(listing => (
        <div key={listing.id}>
          <h2>{listing.title}</h2>
          <p>{listing.description}</p>
          <span>${listing.price}</span>
        </div>
      ))}
    </div>
  );
}
```

### Using with Parameters

For endpoints that require parameters:

```jsx
import { useParams } from 'react-router-dom';
import api from '../utils/api-connector';

function ListingDetail() {
  const { id } = useParams();
  const [listing, setListing] = useState(null);
  
  useEffect(() => {
    // Use the predefined method with ID parameter
    api.getListing(id)
      .then(data => {
        setListing(data);
      })
      .catch(error => {
        console.error(`Failed to fetch listing ${id}:`, error);
      });
  }, [id]);
  
  if (!listing) return <div>Loading...</div>;
  
  return (
    <div>
      <h1>{listing.title}</h1>
      <p>{listing.description}</p>
      <span>${listing.price}</span>
    </div>
  );
}
```

### Submitting Data

For POST/PUT endpoints:

```jsx
import { useState } from 'react';
import api from '../utils/api-connector';

function CreateListing() {
  const [formData, setFormData] = useState({
    title: '',
    description: '',
    price: 0
  });
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    try {
      // Use the predefined method to create a listing
      const result = await api.createListing(formData);
      alert('Listing created successfully!');
      // Redirect or update UI as needed
    } catch (error) {
      console.error('Failed to create listing:', error);
      alert('Failed to create listing. Please try again.');
    }
  };
  
  // Form handling omitted for brevity
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields here */}
      <button type="submit">Create Listing</button>
    </form>
  );
}
```

## Server-Side Integration

The server integration is handled automatically through the `yaml-server-integration.js` file, which:

1. Reads the YAML configuration
2. Maps endpoints to controller functions
3. Applies appropriate middleware (auth, admin-only, etc.)
4. Creates stub implementations for any endpoints not yet implemented

To use this in your production server:

1. Update your controller structure to match the YAML file categories
2. Use the `createApiRouter` function to generate routes
3. Mount the router at your API base path

## Updating the API

When you need to add or modify an endpoint:

1. Update the `api-config.yaml` file with the new endpoint details
2. Add the corresponding controller function on the server side
3. Use the API connector method in your frontend component

The system will automatically handle the connections between the two.

## Best Practices

1. Always update the YAML file first when making API changes
2. Keep endpoint descriptions clear and updated
3. List all frontend components that use each endpoint
4. Use the predefined API connector methods instead of direct Axios calls
5. Test new endpoints with the provided stubs before implementation

## Troubleshooting

If you encounter issues:

1. Check that your YAML syntax is valid
2. Verify that controller functions match the expected names
3. Ensure middleware requirements (auth, admin) are being met
4. Look for any missing parameters in API calls
5. Check that the API base URL is correctly configured

By following this YAML-based approach, you'll have a more maintainable and consistent API integration between your frontend and backend, significantly reducing the need for individual bug fixes and making the codebase easier to understand and maintain.
