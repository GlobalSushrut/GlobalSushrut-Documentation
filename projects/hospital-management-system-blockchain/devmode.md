# hospital-management-system-blockchain - devmode

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# Developer Mode

A special testing mode that bypasses all authentication requirements in the Hospital Management System.

## Overview

Developer Mode allows developers and testers to access all system functionality without authentication, making the development process faster and easier.

## Features

- **No Authentication Required**: Access all API endpoints without login
- **Full Admin Privileges**: All requests are treated as coming from an admin user
- **KYC Verification Bypass**: All users are considered verified
- **Simplified Testing**: Eliminates the need for token management in tests

## Usage Instructions

### Enabling Developer Mode

**Method 1**: Using the utility script
```bash
# Enable developer mode
./dev-mode.py on
```

**Method 2**: Manual configuration
```bash
# Add to your .env files
DEVELOPER_MODE=true
```

### Checking Current Status

```bash
# Check if developer mode is enabled
./dev-mode.py status
```

### Disabling Developer Mode

```bash
# Disable developer mode
./dev-mode.py off
```

## Important Notes

- **⚠️ NEVER use Developer Mode in production environments**
- Always restart the application after changing the mode
- Look for warning messages in logs to confirm when developer mode is active
- Developer mode bypasses ALL security controls in the system

## Implementation Details

The developer mode works by modifying the authentication decorators:

1. The `token_required` decorator automatically supplies admin credentials
2. The `role_required` decorator allows access to any role-protected route
3. Warning messages are logged to alert developers that the mode is active

## Example Development Workflow

1. Enable developer mode: `./dev-mode.py on`
2. Start your backend server: `cd flask-backend && flask run`
3. Start your frontend: `cd hospital-management-system-master && npm start`
4. Access any functionality without authentication
5. When finished, disable developer mode: `./dev-mode.py off`

## Compatibility with System Validation

The system validation script is aware of developer mode and will indicate when it's active. This ensures that validation tests work correctly regardless of authentication status.

## Files Affected by Developer Mode

- `/flask-backend/app/auth/__init__.py`: Core authentication bypass logic
- Environment files (.env) in various project directories
- System validation components

## Troubleshooting

If developer mode isn't working:
- Verify all .env files have been updated
- Restart all services completely
- Check application logs for warnings or errors
- Ensure you have the latest version of the codebase
