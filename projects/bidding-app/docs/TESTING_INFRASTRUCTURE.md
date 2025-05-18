# bidding-app - TESTING_INFRASTRUCTURE

*This documentation is from the private repository bidding-app.*

---

# Bidding Application Testing Infrastructure

This document provides a comprehensive guide to the testing infrastructure implemented in the Bidding Application. The infrastructure supports various types of tests, including API, integration, UI, performance, and security tests, with a focus on maintainability, reliability, and comprehensive coverage.

## Table of Contents

1. [Overview](#overview)
2. [Directory Structure](#directory-structure)
3. [Configuration](#configuration)
4. [Test Types](#test-types)
   - [API Tests](#api-tests)
   - [Integration Tests](#integration-tests)
   - [UI Tests](#ui-tests)
   - [Security Tests](#security-tests)
   - [Performance Tests](#performance-tests)
5. [Utilities](#utilities)
   - [Logger](#logger)
   - [Variable Resolver](#variable-resolver)
   - [Test Data Manager](#test-data-manager)
   - [Authentication Manager](#authentication-manager)
   - [Browser Console Capture](#browser-console-capture)
6. [Logging Infrastructure](#logging-infrastructure)
   - [Server Logs](#server-logs)
   - [Browser Console Logs](#browser-console-logs)
   - [Log Viewer](#log-viewer)
7. [Running Tests](#running-tests)
8. [Test Report Generation](#test-report-generation)
9. [Credentials Management](#credentials-management)
10. [Docker Setup for Testing](#docker-setup-for-testing)
11. [Continuous Integration](#continuous-integration)
12. [Best Practices](#best-practices)

## Overview

The testing infrastructure for the Bidding Application is designed to provide comprehensive test coverage across all layers of the application, from individual API endpoints to complete user workflows. Key features include:

- **Configuration-Driven Testing**: Tests are defined in YAML configuration files, making them easy to update and maintain
- **Multiple Test Types**: Support for API, integration, UI, security, and performance tests
- **Comprehensive Logging**: Capture and visualization of logs from all parts of the application
- **Secure Credential Management**: Secure handling of test credentials and sensitive data
- **Containerized Testing**: Docker setup for consistent test environments
- **YAML-Based Configuration**: Centralized configuration for tests, minimizing code duplication

## Directory Structure

```
bidding app/
├── client/                     # Frontend application
│   └── src/
│       └── components/
│           └── admin/
│               └── LogViewer.jsx  # UI component for viewing logs
├── server/
│   └── middleware/
│       └── server-log-middleware.js  # Server log capture middleware
├── test/
│   ├── fixtures/               # Test fixtures and sample data
│   ├── runners/                # Test runners for each test type
│   │   ├── api-test-runner.js     # API test runner
│   │   ├── integration-test-runner.js  # Integration test runner
│   │   ├── ui-test-runner.js      # UI test runner
│   │   ├── security-test-runner.js  # Security test runner
│   │   └── performance-test-runner.js  # Performance test runner
│   ├── utils/                  # Test utilities
│   │   ├── logger.js              # Logging utility
│   │   ├── variable-resolver.js   # Variable resolution for test configs
│   │   ├── test-data-manager.js   # Test data management
│   │   ├── auth-manager.js        # Authentication helper
│   │   └── browser-console-capture.js  # Browser console log capture
│   ├── reports/                # Test reports output directory
│   ├── test-runner.js          # Main test runner
│   └── schemas/                # JSON schemas for API validation
├── logs/                      # Log output directory
│   ├── server-combined.log     # Combined server logs
│   ├── server-error.log        # Server error logs
│   ├── test-combined.log       # Combined test logs
│   ├── test-error.log          # Test error logs
│   └── browser-console/        # Browser console logs
├── test-config.yaml           # Test configuration
├── ui-config.yaml             # UI configuration
└── docs/
    └── TESTING_INFRASTRUCTURE.md  # This documentation
```

## Configuration

The testing infrastructure uses YAML configuration files to define tests. The main configuration file is `test-config.yaml`, which defines test environments, test accounts, and test cases for various test types.

### Test Configuration

The `test-config.yaml` file contains:

- **Environment Configurations**: Settings for different environments (development, staging, production)
- **Test Accounts**: Definitions of test user accounts for different roles
- **API Tests**: Definitions of API endpoint tests
- **Integration Tests**: Definitions of integration test workflows
- **UI Tests**: Definitions of UI test flows
- **Security Tests**: Security test configurations
- **Performance Tests**: Performance test configurations

Example:

```yaml
# Test environments
environments:
  development:
    baseUrl: "http://localhost:3000"
    apiUrl: "http://localhost:3000/api"
    wsUrl: "ws://localhost:3000"
    db:
      host: "localhost"
      port: 5432
      name: "bidding_dev"
      username: "${ENV:TEST_DB_USER}"
      password: "${ENV:TEST_DB_PASSWORD}"
  
  staging:
    baseUrl: "https://staging.bidding-app.com"
    apiUrl: "https://staging.bidding-app.com/api"
    wsUrl: "wss://staging.bidding-app.com"

# Test accounts - encrypted and managed by credentials-manager
testAccounts:
  admin:
    email: "${CREDENTIAL:test.accounts.admin.email}"
    password: "${CREDENTIAL:test.accounts.admin.password}"
  seller:
    email: "${CREDENTIAL:test.accounts.seller.email}"
    password: "${CREDENTIAL:test.accounts.seller.password}"

# API Endpoint Tests
apiTests:
  auth:
    login:
      endpoint: "/auth/login"
      method: "POST"
      contentType: "application/json"
      requestBody:
        valid:
          email: "${TEST_ACCOUNT:buyer.email}"
          password: "${TEST_ACCOUNT:buyer.password}"
```

## Test Types

### API Tests

API tests verify that individual API endpoints function correctly. The API test runner (`api-test-runner.js`) makes HTTP requests to API endpoints and validates the responses against expected outcomes. Features include:

- **Request/Response Validation**: Validate request parameters and response formats
- **Schema Validation**: Verify response data against JSON schemas
- **Authentication Testing**: Test authenticated and unauthenticated requests
- **Error Handling**: Test error responses and edge cases

API tests are defined in the `apiTests` section of the `test-config.yaml` file.

### Integration Tests

Integration tests verify that multiple components work together correctly. The integration test runner (`integration-test-runner.js`) executes multi-step workflows that involve multiple API calls and potentially database operations. Features include:

- **Workflow Testing**: Test complete business workflows
- **Sequential and Parallel Steps**: Support for both sequential and parallel test steps
- **Context Sharing**: Share data between steps in a workflow
- **Database Operations**: Direct database interactions for setup and verification

Integration tests are defined in the `integrationTests` section of the `test-config.yaml` file.

### UI Tests

UI tests verify the frontend application's behavior from a user perspective. The UI test runner (`ui-test-runner.js`) uses Puppeteer to automate browser interactions. Features include:

- **Browser Automation**: Automated browser interactions like clicking, typing, and navigation
- **Screenshot Capture**: Visual verification with screenshots
- **Console Log Capture**: Capture browser console logs
- **Element Verification**: Verify UI elements and their properties

UI tests are defined in the `uiTests` section of the `test-config.yaml` file.

### Security Tests

Security tests verify that the application is secure against common vulnerabilities. The security test runner (`security-test-runner.js`) tests for vulnerabilities like CSRF, XSS, SQL injection, and more. Features include:

- **Authentication Security**: Test JWT security, password security, etc.
- **Injection Testing**: Test for SQL injection and XSS vulnerabilities
- **CSRF Testing**: Test for CSRF vulnerabilities
- **Sensitive Data Exposure**: Test for exposure of sensitive data

Security tests are defined in the `securityTests` section of the `test-config.yaml` file.

### Performance Tests

Performance tests verify that the application performs well under load. The performance test runner (not shown in the code) would test response times, throughput, and resource usage. Features include:

- **Load Testing**: Test application performance under load
- **Stress Testing**: Test application behavior at or beyond capacity
- **Endurance Testing**: Test application behavior over extended periods
- **Baseline Comparison**: Compare performance against baseline metrics

Performance tests would be defined in the `performanceTests` section of the `test-config.yaml` file.

## Utilities

### Logger

The logger utility (`logger.js`) provides consistent logging across all test components. Features include:

- **Multiple Log Levels**: Support for error, warn, info, debug log levels
- **File and Console Logging**: Log to both files and console
- **Formatting**: Consistent log formatting with timestamps and colors
- **Test-Specific Logging**: Methods for test-specific logging (test, pass, fail)

Example usage:

```javascript
const logger = require('./utils/logger');

logger.info('This is an informational message');
logger.error('This is an error message');
logger.test('testName', 'This is a test message');
logger.pass('testName');
logger.fail('testName', 'This test failed because...');
```

### Variable Resolver

The variable resolver utility (`variable-resolver.js`) resolves variables in test configurations with dynamic values. Features include:

- **Environment Variables**: Resolve `${ENV:VAR_NAME}` to environment variables
- **Random Values**: Resolve `${RANDOM:type}` to random values (email, name, etc.)
- **Test Fixtures**: Resolve `${FIXTURE:id}` to test fixtures
- **Test Accounts**: Resolve `${TEST_ACCOUNT:role.property}` to test account data
- **Response Data**: Resolve `${RESPONSE:path.to.property}` to previous API response data
- **Calculated Values**: Resolve `${CALC:expression}` to calculated values
- **Credentials**: Resolve `${CREDENTIAL:path}` to credentials

Example usage:

```javascript
const variableResolver = require('./utils/variable-resolver');

// Resolve a string with variables
const resolved = variableResolver.resolveVariable('Hello, ${RANDOM:name}!');
```

### Test Data Manager

The test data manager utility (`test-data-manager.js`) manages test data, fixtures, and response data for tests. Features include:

- **Fixture Management**: Load and access test fixtures
- **Response Data Management**: Store and access API response data
- **Test Account Management**: Manage test account credentials
- **Cleanup**: Clean up test data after tests

Example usage:

```javascript
const testDataManager = require('./utils/test-data-manager');

// Get a fixture
const fixture = testDataManager.getFixture('listing-1');

// Save response data
testDataManager.saveResponseData('auth.login', { token: '...' });

// Get response data
const token = testDataManager.getResponseData('auth.login.token');

// Get test accounts
const accounts = testDataManager.getTestAccounts();
```

### Authentication Manager

The authentication manager utility (`auth-manager.js`) handles authentication for tests. Features include:

- **Token Management**: Get and manage authentication tokens
- **Caching**: Cache tokens to avoid unnecessary login requests
- **JWT Decoding**: Decode JWT tokens to access payload data

Example usage:

```javascript
const authManager = require('./utils/auth-manager');

// Get an authentication token for a role
const token = await authManager.getAuthToken('buyer');

// Decode a token
const decodedToken = authManager.decodeToken(token);
```

### Browser Console Capture

The browser console capture utility (`browser-console-capture.js`) captures browser console logs for analysis. Features include:

- **Console Log Capture**: Capture browser console logs
- **Log Filtering**: Filter logs by source and level
- **Log Export**: Export logs to file
- **Integration with Puppeteer**: Easy integration with Puppeteer for UI tests

Example usage:

```javascript
const browserConsoleCapture = require('./utils/browser-console-capture');

// Initialize console capture
browserConsoleCapture.initialize();

// Set up capture for a Puppeteer page
const cleanup = browserConsoleCapture.setupPuppeteerCapture(page);

// Get captured logs
const logs = browserConsoleCapture.getLogs();

// Clean up
cleanup();
```

## Logging Infrastructure

The logging infrastructure captures logs from all parts of the application, making them available for analysis and debugging.

### Server Logs

Server logs are captured by the server log middleware (`server-log-middleware.js`). Features include:

- **Request/Response Logging**: Log HTTP requests and responses
- **Console Log Capture**: Capture console logs from the server
- **Log Level Filtering**: Filter logs by level
- **File Logging**: Log to files for persistent storage
- **API Access**: API endpoints for accessing logs

### Browser Console Logs

Browser console logs are captured by the browser console capture utility (`browser-console-capture.js`). Features include:

- **Console Log Capture**: Capture browser console logs
- **Console Error Capture**: Capture browser console errors
- **Page Error Capture**: Capture page errors
- **File Logging**: Log to files for persistent storage

### Log Viewer

The log viewer component (`LogViewer.jsx`) provides a UI for viewing and analyzing logs. Features include:

- **Log Filtering**: Filter logs by source, level, and search term
- **Log Export**: Export logs to file
- **Log Visualization**: Visualize log data in a tabular format
- **Real-Time Updates**: Automatically refresh logs
- **Tab Organization**: Organize logs by type (recent, API requests, browser console, log files)

## Running Tests

Tests can be run using the main test runner script (`test-runner.js`). The test runner loads the test configuration, sets up the test environment, and executes the requested tests.

### Command Line Interface

Tests can be run from the command line using:

```bash
node test/test-runner.js --env development --type api
```

Command line options include:

- `--env`: The test environment to use (development, staging, production)
- `--type`: The type of tests to run (api, integration, ui, security, performance, all)
- `--group`: Optional group of tests to run
- `--test`: Optional specific test to run
- `--report`: Generate a test report
- `--verbose`: Enable verbose logging

### Programmatic Interface

Tests can also be run programmatically:

```javascript
const testRunner = require('./test/test-runner');

testRunner.run({
  env: 'development',
  type: 'api',
  group: 'auth',
  test: 'login',
  report: true,
  verbose: true
}).then(results => {
  console.log('Test results:', results);
}).catch(error => {
  console.error('Test error:', error);
});
```

## Test Report Generation

The test infrastructure can generate test reports in various formats (HTML, JSON, XML). Reports include:

- **Test Results**: Pass/fail status for each test
- **Test Duration**: Duration of each test and the total test run
- **Error Details**: Details of any errors that occurred
- **Screenshots**: Screenshots for UI tests
- **Logs**: Relevant logs for failed tests

Reports are generated in the `test/reports` directory.

## Credentials Management

The credentials manager (`credentials-manager.js`) securely manages sensitive credentials. Features include:

- **Encrypted Storage**: Store credentials in an encrypted format
- **Access Control**: Control access to credentials based on roles
- **Environment Variables**: Support for environment variable-based credentials
- **Cross-Environment Support**: Manage credentials across different environments

## Docker Setup for Testing

The Docker setup for testing ensures a consistent test environment. Features include:

- **Containerized Testing**: Run tests in a containerized environment
- **Isolated Environment**: Isolate tests from the host environment
- **Reproducible Results**: Ensure test results are reproducible
- **CI/CD Integration**: Easy integration with CI/CD pipelines

## Continuous Integration

The testing infrastructure is designed to work with continuous integration systems. Features include:

- **CI-Friendly**: Command line interface for easy CI integration
- **Report Generation**: Generate reports that can be consumed by CI systems
- **Environment Variables**: Support for environment variables for CI configuration
- **Exit Codes**: Appropriate exit codes for CI pipelines

## Best Practices

Guidelines for writing effective tests:

1. **Keep Tests Independent**: Each test should be independent of other tests
2. **Use Configuration for Test Data**: Define test data in configuration files rather than hardcoding
3. **Test Positive and Negative Cases**: Test both success and failure scenarios
4. **Use Appropriate Assertions**: Use specific assertions that clearly indicate what's being tested
5. **Clean Up After Tests**: Clean up any data created during tests
6. **Use Meaningful Test Names**: Use descriptive names for tests
7. **Isolate Test Environments**: Use isolated environments for testing
8. **Monitor Test Performance**: Watch for slow tests and optimize them
9. **Review Test Coverage**: Regularly review test coverage and add tests for uncovered code
10. **Keep Tests Maintainable**: Refactor tests when necessary to keep them maintainable
