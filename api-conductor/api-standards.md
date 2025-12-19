# API Standards

## Overview
This document defines standards and patterns for building platform API connectors. These standards ensure consistency, reliability, and maintainability across all connectors. Every connector must follow these standards.

## Authentication Patterns

### OAuth 2.0 Authorization Code Flow
- **Use Case**: User-facing applications that need user authorization
- **Flow**:
  1. Generate authorization URL with client_id, redirect_uri, scope, state
  2. User authorizes and redirects back with authorization code
  3. Exchange authorization code for access token and refresh token
  4. Store tokens securely (never in code or version control)
  5. Use access token for API requests
  6. Refresh access token using refresh token before expiration

- **Implementation Requirements**:
  - Generate secure state parameter (CSRF protection)
  - Handle authorization errors gracefully
  - Store tokens securely (environment variables, secure vault)
  - Automatically refresh tokens before expiration
  - Handle token refresh failures

- **Code Pattern**:
  ```typescript
  class OAuth2Auth {
    async getAuthorizationUrl(): Promise<string> {
      // Generate authorization URL
    }
    
    async exchangeCode(code: string): Promise<Tokens> {
      // Exchange code for tokens
    }
    
    async refreshToken(refreshToken: string): Promise<Tokens> {
      // Refresh access token
    }
    
    async getAccessToken(): Promise<string> {
      // Get valid access token (refresh if needed)
    }
  }
  ```

### OAuth 2.0 Client Credentials Flow
- **Use Case**: Server-to-server applications, machine-to-machine
- **Flow**:
  1. Exchange client_id and client_secret for access token
  2. Use access token for API requests
  3. Refresh token before expiration (if refresh token provided)

- **Implementation Requirements**:
  - Store client credentials securely
  - Cache access tokens (they're reusable until expiration)
  - Refresh tokens automatically before expiration
  - Handle authentication errors

### API Key Authentication
- **Use Case**: Simple API key-based authentication
- **Implementation Requirements**:
  - Pass API key in header (preferred) or query parameter
  - Use header name: `X-API-Key` or `Authorization: Bearer <key>`
  - Store API key securely (environment variables)
  - Never expose API key in URLs or logs

### Token Storage
- **Never**: Commit tokens to version control
- **Always**: Use environment variables or secure vaults
- **Cache**: Cache tokens in memory (not disk) during runtime
- **Refresh**: Implement automatic token refresh before expiration

## Rate Limiting Strategies

### Rate Limit Detection
- **Header Parsing**: Parse rate limit headers from API responses
  - Common headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`
  - Parse reset time and calculate wait duration
  
- **429 Status Code**: Detect rate limit exceeded (HTTP 429)
- **Response Body**: Some APIs return rate limit info in response body

### Exponential Backoff
- **Strategy**: Retry with exponentially increasing delays
- **Initial Delay**: Start with 1 second
- **Max Delay**: Cap at 60 seconds (or API-specific limit)
- **Max Retries**: 3-5 retries before failing
- **Formula**: `delay = min(initialDelay * (2 ^ attempt), maxDelay)`

- **Implementation**:
  ```typescript
  async function retryWithBackoff<T>(
    fn: () => Promise<T>,
    maxRetries = 3
  ): Promise<T> {
    for (let attempt = 0; attempt < maxRetries; attempt++) {
      try {
        return await fn();
      } catch (error) {
        if (error instanceof RateLimitError && attempt < maxRetries - 1) {
          const delay = Math.min(1000 * Math.pow(2, attempt), 60000);
          await sleep(delay);
          continue;
        }
        throw error;
      }
    }
  }
  ```

### Rate Limit Header Parsing
- **Parse Headers**: Extract rate limit information from response headers
- **Track Usage**: Track remaining requests and reset time
- **Respect Limits**: Wait until reset time before making new requests
- **Queue Requests**: Queue requests when rate limit is reached

### Request Queuing
- **Use Case**: When rate limit is reached, queue requests instead of failing
- **Implementation**:
  - Maintain request queue
  - Process queue when rate limit resets
  - Limit queue size to prevent memory issues
  - Provide timeout for queued requests

### Configurable Strategies
- **Allow Configuration**: Make rate limiting strategies configurable
- **Per-Endpoint Limits**: Some APIs have different limits per endpoint
- **Tier-Based Limits**: Handle different API tiers (free, paid, enterprise)

## Error Handling Standards

### Custom Error Classes
- **Hierarchy**: Create custom error class hierarchy
- **Base Error**: Base API error class
- **Specific Errors**: RateLimitError, AuthenticationError, NotFoundError, etc.

- **Error Structure**:
  ```typescript
  class APIError extends Error {
    constructor(
      message: string,
      public statusCode: number,
      public code: string,
      public details?: any
    ) {
      super(message);
      this.name = this.constructor.name;
    }
  }
  
  class RateLimitError extends APIError {
    constructor(
      message: string,
      public resetAt?: Date,
      public retryAfter?: number
    ) {
      super(message, 429, 'RATE_LIMIT_EXCEEDED');
    }
  }
  
  class AuthenticationError extends APIError {
    constructor(message: string) {
      super(message, 401, 'AUTHENTICATION_FAILED');
    }
  }
  ```

### Error Code Mapping
- **Map API Errors**: Map API-specific error codes to standard error types
- **User-Friendly Messages**: Provide user-friendly error messages
- **Preserve Details**: Include original API error details for debugging

### Retry Logic for Transient Errors
- **Transient Errors**: 
  - Network timeouts
  - 5xx server errors
  - Rate limit errors (429)
  - Connection errors
  
- **Non-Retryable Errors**:
  - 4xx client errors (except 429)
  - Authentication errors (401)
  - Not found errors (404)
  - Validation errors (400)

- **Retry Strategy**:
  - Use exponential backoff for transient errors
  - Don't retry non-retryable errors
  - Log retry attempts
  - Provide retry configuration

### Error Logging
- **Log Errors**: Log all errors with context
- **Include**: Request URL, method, status code, error message
- **Exclude**: Sensitive data (tokens, credentials)
- **Structured Logging**: Use structured logging format

## Data Model Conventions

### Type Definitions
- **Strong Typing**: Use strong typing (TypeScript, type hints, etc.)
- **Interfaces/Types**: Define interfaces for all API request/response types
- **Naming**: Use descriptive names matching API documentation

### Schema Validation
- **Validate Inputs**: Validate all inputs before sending to API
- **Validate Outputs**: Validate API responses match expected schema
- **Use Libraries**: Use Zod, Pydantic, JSON Schema, or similar
- **Fail Fast**: Fail early with clear validation errors

### Serialization/Deserialization
- **Consistent**: Use consistent serialization/deserialization
- **Handle Nulls**: Handle null/undefined values appropriately
- **Date Handling**: Parse dates consistently (ISO 8601)
- **Nested Objects**: Handle nested objects and arrays correctly

### Example:
```typescript
import { z } from 'zod';

const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email(),
  createdAt: z.string().datetime(),
});

type User = z.infer<typeof UserSchema>;

function parseUser(data: unknown): User {
  return UserSchema.parse(data);
}
```

## API Versioning Approach

### Version Detection
- **URL Versioning**: Extract version from URL (e.g., `/v2/users`)
- **Header Versioning**: Check version headers (e.g., `API-Version: 2`)
- **Default Version**: Use latest version if not specified

### Version Support
- **Current Version**: Support current API version
- **Deprecation Warnings**: Log warnings for deprecated endpoints
- **Migration Path**: Plan for API version migrations

### Version Configuration
- **Configurable**: Allow version to be configured
- **Per-Connector**: Each connector can specify API version
- **Default**: Use sensible default version

## Logging and Monitoring Requirements

### Logging Levels
- **DEBUG**: Detailed debugging information
- **INFO**: General information (requests, responses)
- **WARN**: Warnings (rate limits, retries)
- **ERROR**: Errors (failed requests, authentication failures)

### What to Log
- **Requests**: Log request method, URL, headers (sanitized)
- **Responses**: Log response status, timing
- **Errors**: Log error details, stack traces
- **Rate Limits**: Log rate limit hits and waits

### What NOT to Log
- **Credentials**: Never log tokens, API keys, passwords
- **Sensitive Data**: Don't log PII or sensitive user data
- **Full Responses**: Don't log full response bodies (may contain sensitive data)

### Monitoring Metrics
- **Request Rate**: Track requests per second/minute
- **Error Rate**: Track error percentage
- **Latency**: Track request/response times
- **Rate Limit Hits**: Track rate limit occurrences
- **Token Refresh**: Track token refresh frequency

## Security Best Practices

### Credential Storage
- **Never Commit**: Never commit credentials to version control
- **Environment Variables**: Use environment variables for credentials
- **Secure Vaults**: Use secure vaults (AWS Secrets Manager, HashiCorp Vault) for production
- **Rotation**: Support credential rotation

### HTTPS Only
- **Enforce HTTPS**: Always use HTTPS for API calls
- **Validate Certificates**: Validate SSL certificates
- **No HTTP**: Never allow HTTP connections

### Input Validation
- **Validate All Inputs**: Validate all inputs before sending to API
- **Sanitize**: Sanitize user inputs
- **Type Check**: Ensure types match expected format

### Token Security
- **Secure Storage**: Store tokens securely
- **Expiration**: Respect token expiration
- **Refresh**: Refresh tokens before expiration
- **Revocation**: Handle token revocation

### Error Messages
- **Don't Expose**: Don't expose sensitive information in error messages
- **Generic Messages**: Use generic messages for authentication failures
- **Detailed Logs**: Include details in logs (not user-facing messages)

## Code Patterns

### Request Wrapper
- **Consistent Interface**: Use consistent interface for all API requests
- **Error Handling**: Wrap requests with error handling
- **Rate Limiting**: Integrate rate limiting into request wrapper
- **Retry Logic**: Include retry logic in request wrapper

### Response Parsing
- **Consistent Parsing**: Parse responses consistently
- **Error Detection**: Detect errors in responses
- **Validation**: Validate response structure
- **Type Safety**: Return typed responses

### Configuration
- **Environment-Based**: Use environment-based configuration
- **Defaults**: Provide sensible defaults
- **Validation**: Validate configuration on startup
- **Documentation**: Document all configuration options

## Testing Standards

### Mock API Usage
- **Development**: Always use mock APIs for development
- **Unit Tests**: Use mock APIs for all unit tests
- **Fixtures**: Create reusable test fixtures

### Integration Tests
- **Sandbox**: Use sandbox/test environments for integration tests
- **Real Scenarios**: Test real API scenarios
- **Cleanup**: Clean up test data after tests

### Error Testing
- **Test Errors**: Test all error scenarios
- **Rate Limits**: Test rate limit handling
- **Network Failures**: Test network failure scenarios
- **Authentication Failures**: Test authentication error handling

## Notes

- These standards should be applied consistently across all connectors
- When in doubt, prioritize security and reliability
- Document any deviations from these standards
- Review and update standards as patterns evolve
- Consider platform-specific requirements while following these standards
