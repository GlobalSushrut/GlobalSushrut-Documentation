# hospital-management-system-blockchain - DEVELOPER_MODE

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# Hospital Management System - Developer Mode

This guide explains how to use the Developer Mode feature in the Hospital Management System. Developer Mode allows you to test all project functionalities without authentication, making development and testing easier.

## What is Developer Mode?

Developer Mode is a special configuration option that bypasses all authentication and authorization checks in the Hospital Management System. When enabled:

- No login is required to access protected routes
- All API endpoints are accessible without authentication tokens
- Role-based access control is disabled (all users have admin privileges)
- The system logs warnings to indicate that Developer Mode is active

## ⚠️ Warning

**NEVER USE DEVELOPER MODE IN PRODUCTION ENVIRONMENTS**

Developer Mode completely bypasses all security measures and would expose sensitive patient data if used in production. It is intended for local development and testing only.

## How to Enable/Disable Developer Mode

### Method 1: Using the utility script

We've provided a convenient utility script that can toggle Developer Mode across all necessary configuration files:

```bash
# Check current status
./dev-mode.py status

# Enable Developer Mode
./dev-mode.py on

# Disable Developer Mode
./dev-mode.py off
```

After changing the mode, you'll need to restart the application for changes to take effect.

### Method 2: Manual configuration

You can manually edit the `.env` files in your project and set:

```
DEVELOPER_MODE=true
```

This should be added to:
- `/flask-backend/.env` (for backend services)
- `/.env` (for root configuration)
- `/integration-tests/.env` (for testing services)

## Using Developer Mode for Testing

When Developer Mode is enabled:

1. Start your backend server normally
2. Start your frontend application
3. You'll be automatically authenticated as an admin user
4. All API endpoints will be accessible without a token
5. The system will display warnings in logs to remind you that Developer Mode is active

## Verifying Developer Mode Status

You can check if Developer Mode is currently active in several ways:

1. Look for warning messages in the console output when starting the application
2. Use the utility script: `./dev-mode.py status`
3. Check for a banner in the backend logs indicating "DEVELOPER MODE ACTIVE"

## Integration with System Validation

The system validation script will detect when Developer Mode is active and will indicate this in its output. This is useful for ensuring all components are functioning correctly without needing to set up authentication.

## Security Considerations

- Developer Mode configuration files should never be committed to version control
- Always check that Developer Mode is disabled before deploying to staging or production
- The system will display prominent warnings when Developer Mode is enabled to prevent accidental use in production

## Troubleshooting

If you encounter issues with Developer Mode:

1. Verify that the `DEVELOPER_MODE` environment variable is set correctly
2. Restart all services (frontend, backend, blockchain) after toggling the mode
3. Check application logs for any specific errors
4. Ensure you've edited all the necessary `.env` files
