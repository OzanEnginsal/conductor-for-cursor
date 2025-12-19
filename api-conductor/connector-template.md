# Connector Template

## Overview
This document provides the standard structure and code patterns for building platform API connectors. Use this template as a starting point for all new connectors to ensure consistency and best practices.

## Directory Structure

```
<connector-name>/
  src/
    client.ts          # Main API client class
    auth.ts            # Authentication logic
    models.ts          # Data models/types/schemas
    errors.ts          # Custom error classes
    rate-limiter.ts    # Rate limiting logic
    endpoints/         # Endpoint implementations
      users.ts
      posts.ts
      # ... other endpoints
    utils/             # Utility functions
      request.ts       # HTTP request wrapper
      response.ts      # Response parsing
  tests/
    unit/
      auth.test.ts
      rate-limiter.test.ts
      client.test.ts
      endpoints/
        users.test.ts
    integration/
      auth-flow.test.ts
      endpoints.test.ts
    mocks/            # Mock API responses
      handlers.ts     # Mock request handlers
      responses/     # Mock response fixtures
        users.json
        posts.json
  README.md          # Connector documentation
  package.json       # Dependencies and scripts
  tsconfig.json      # TypeScript config (if TypeScript)
```

## Code Templates

### Main API Client (`client.ts`)

```typescript
import { Auth } from './auth';
import { RateLimiter } from './rate-limiter';
import { APIError } from './errors';

export interface ClientConfig {
  baseURL: string;
  apiKey?: string;
  clientId?: string;
  clientSecret?: string;
  redirectUri?: string;
  timeout?: number;
}

export class APIClient {
  private auth: Auth;
  private rateLimiter: RateLimiter;
  private config: ClientConfig;

  constructor(config: ClientConfig) {
    this.config = config;
    this.auth = new Auth(config);
    this.rateLimiter = new RateLimiter();
  }

  /**
   * Make authenticated API request
   */
  async request<T>(
    method: string,
    path: string,
    options?: RequestOptions
  ): Promise<T> {
    // Get access token
    const token = await this.auth.getAccessToken();
    
    // Check rate limits
    await this.rateLimiter.waitIfNeeded();
    
    // Make request
    const response = await this.makeRequest(method, path, {
      ...options,
      headers: {
        ...options?.headers,
        Authorization: `Bearer ${token}`,
      },
    });
    
    // Update rate limiter with response headers
    this.rateLimiter.updateFromResponse(response);
    
    return response.data;
  }

  private async makeRequest(
    method: string,
    path: string,
    options?: RequestOptions
  ): Promise<Response> {
    // Implementation
  }
}
```

### Authentication (`auth.ts`)

#### OAuth 2.0 Authorization Code Flow

```typescript
export interface Tokens {
  accessToken: string;
  refreshToken?: string;
  expiresAt?: Date;
}

export class OAuth2Auth {
  private config: OAuth2Config;
  private tokens?: Tokens;

  constructor(config: OAuth2Config) {
    this.config = config;
  }

  /**
   * Generate authorization URL
   */
  getAuthorizationUrl(state?: string): string {
    const params = new URLSearchParams({
      client_id: this.config.clientId,
      redirect_uri: this.config.redirectUri,
      response_type: 'code',
      scope: this.config.scope.join(' '),
      state: state || this.generateState(),
    });
    
    return `${this.config.authURL}?${params.toString()}`;
  }

  /**
   * Exchange authorization code for tokens
   */
  async exchangeCode(code: string): Promise<Tokens> {
    const response = await fetch(this.config.tokenURL, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: new URLSearchParams({
        grant_type: 'authorization_code',
        code,
        client_id: this.config.clientId,
        client_secret: this.config.clientSecret,
        redirect_uri: this.config.redirectUri,
      }),
    });

    if (!response.ok) {
      throw new AuthenticationError('Failed to exchange code for tokens');
    }

    const data = await response.json();
    this.tokens = {
      accessToken: data.access_token,
      refreshToken: data.refresh_token,
      expiresAt: new Date(Date.now() + data.expires_in * 1000),
    };

    return this.tokens;
  }

  /**
   * Refresh access token
   */
  async refreshToken(): Promise<Tokens> {
    if (!this.tokens?.refreshToken) {
      throw new AuthenticationError('No refresh token available');
    }

    const response = await fetch(this.config.tokenURL, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: new URLSearchParams({
        grant_type: 'refresh_token',
        refresh_token: this.tokens.refreshToken,
        client_id: this.config.clientId,
        client_secret: this.config.clientSecret,
      }),
    });

    if (!response.ok) {
      throw new AuthenticationError('Failed to refresh token');
    }

    const data = await response.json();
    this.tokens = {
      accessToken: data.access_token,
      refreshToken: data.refresh_token || this.tokens.refreshToken,
      expiresAt: new Date(Date.now() + data.expires_in * 1000),
    };

    return this.tokens;
  }

  /**
   * Get valid access token (refresh if needed)
   */
  async getAccessToken(): Promise<string> {
    if (!this.tokens) {
      throw new AuthenticationError('Not authenticated');
    }

    // Refresh if expired or expiring soon (within 5 minutes)
    if (this.tokens.expiresAt && 
        this.tokens.expiresAt.getTime() - Date.now() < 5 * 60 * 1000) {
      await this.refreshToken();
    }

    return this.tokens.accessToken;
  }

  private generateState(): string {
    return crypto.randomUUID();
  }
}
```

#### OAuth 2.0 Client Credentials Flow

```typescript
export class ClientCredentialsAuth {
  private config: ClientCredentialsConfig;
  private tokens?: Tokens;

  constructor(config: ClientCredentialsConfig) {
    this.config = config;
  }

  /**
   * Get access token (fetch or use cached)
   */
  async getAccessToken(): Promise<string> {
    if (this.tokens && 
        this.tokens.expiresAt && 
        this.tokens.expiresAt.getTime() > Date.now()) {
      return this.tokens.accessToken;
    }

    const response = await fetch(this.config.tokenURL, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: new URLSearchParams({
        grant_type: 'client_credentials',
        client_id: this.config.clientId,
        client_secret: this.config.clientSecret,
        scope: this.config.scope.join(' '),
      }),
    });

    if (!response.ok) {
      throw new AuthenticationError('Failed to get access token');
    }

    const data = await response.json();
    this.tokens = {
      accessToken: data.access_token,
      expiresAt: new Date(Date.now() + data.expires_in * 1000),
    };

    return this.tokens.accessToken;
  }
}
```

#### API Key Authentication

```typescript
export class APIKeyAuth {
  private apiKey: string;

  constructor(apiKey: string) {
    this.apiKey = apiKey;
  }

  /**
   * Get API key for request headers
   */
  getAuthHeader(): Record<string, string> {
    return {
      'X-API-Key': this.apiKey,
    };
  }

  async getAccessToken(): Promise<string> {
    return this.apiKey;
  }
}
```

### Rate Limiting (`rate-limiter.ts`)

```typescript
export class RateLimiter {
  private remaining: number = Infinity;
  private resetAt?: Date;
  private queue: Array<() => void> = [];

  /**
   * Update rate limit info from response headers
   */
  updateFromResponse(response: Response): void {
    const limit = response.headers.get('X-RateLimit-Limit');
    const remaining = response.headers.get('X-RateLimit-Remaining');
    const reset = response.headers.get('X-RateLimit-Reset');

    if (limit) this.limit = parseInt(limit);
    if (remaining) this.remaining = parseInt(remaining);
    if (reset) {
      const resetTime = parseInt(reset);
      this.resetAt = new Date(resetTime * 1000);
    }
  }

  /**
   * Wait if rate limit is reached
   */
  async waitIfNeeded(): Promise<void> {
    if (this.remaining <= 0 && this.resetAt) {
      const waitTime = this.resetAt.getTime() - Date.now();
      if (waitTime > 0) {
        await this.sleep(waitTime);
      }
    }
  }

  /**
   * Handle rate limit error with exponential backoff
   */
  async handleRateLimit(attempt: number = 0, maxRetries: number = 3): Promise<void> {
    if (attempt >= maxRetries) {
      throw new RateLimitError('Rate limit exceeded after retries');
    }

    const delay = Math.min(1000 * Math.pow(2, attempt), 60000);
    await this.sleep(delay);
  }

  private sleep(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}
```

### Error Handling (`errors.ts`)

```typescript
export class APIError extends Error {
  constructor(
    message: string,
    public statusCode: number,
    public code: string,
    public details?: any
  ) {
    super(message);
    this.name = this.constructor.name;
    Error.captureStackTrace(this, this.constructor);
  }
}

export class RateLimitError extends APIError {
  constructor(
    message: string,
    public resetAt?: Date,
    public retryAfter?: number
  ) {
    super(message, 429, 'RATE_LIMIT_EXCEEDED');
  }
}

export class AuthenticationError extends APIError {
  constructor(message: string, details?: any) {
    super(message, 401, 'AUTHENTICATION_FAILED', details);
  }
}

export class NotFoundError extends APIError {
  constructor(message: string) {
    super(message, 404, 'NOT_FOUND');
  }
}

export class ValidationError extends APIError {
  constructor(message: string, details?: any) {
    super(message, 400, 'VALIDATION_ERROR', details);
  }
}
```

### Data Models (`models.ts`)

```typescript
import { z } from 'zod';

// User schema
export const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email(),
  createdAt: z.string().datetime(),
});

export type User = z.infer<typeof UserSchema>;

// Post schema
export const PostSchema = z.object({
  id: z.string(),
  title: z.string(),
  content: z.string(),
  authorId: z.string(),
  createdAt: z.string().datetime(),
});

export type Post = z.infer<typeof PostSchema>;

// Validation helpers
export function parseUser(data: unknown): User {
  return UserSchema.parse(data);
}

export function parsePost(data: unknown): Post {
  return PostSchema.parse(data);
}
```

### Endpoint Implementation (`endpoints/users.ts`)

```typescript
import { APIClient } from '../client';
import { User, parseUser } from '../models';

export class UsersEndpoint {
  constructor(private client: APIClient) {}

  /**
   * Get user by ID
   */
  async getUser(userId: string): Promise<User> {
    const data = await this.client.request<User>(
      'GET',
      `/v2/users/${userId}`
    );
    return parseUser(data);
  }

  /**
   * List users
   */
  async listUsers(options?: ListOptions): Promise<User[]> {
    const params = new URLSearchParams();
    if (options?.limit) params.set('limit', options.limit.toString());
    if (options?.offset) params.set('offset', options.offset.toString());

    const data = await this.client.request<{ users: User[] }>(
      'GET',
      `/v2/users?${params.toString()}`
    );
    
    return data.users.map(parseUser);
  }
}
```

### Mock API Setup (`tests/mocks/handlers.ts`)

```typescript
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';

export const handlers = [
  // Mock user endpoint
  http.get('https://api.example.com/v2/users/:id', ({ params }) => {
    const { id } = params;
    return HttpResponse.json({
      id,
      name: 'Test User',
      email: 'test@example.com',
      createdAt: new Date().toISOString(),
    });
  }),

  // Mock rate limit response
  http.get('https://api.example.com/v2/users', () => {
    return HttpResponse.json(
      { error: 'Rate limit exceeded' },
      { 
        status: 429,
        headers: {
          'X-RateLimit-Limit': '100',
          'X-RateLimit-Remaining': '0',
          'X-RateLimit-Reset': Math.floor(Date.now() / 1000 + 60).toString(),
        },
      }
    );
  }),
];

export const server = setupServer(...handlers);
```

### Test Example (`tests/unit/client.test.ts`)

```typescript
import { describe, it, expect, beforeEach, afterEach } from 'vitest';
import { server } from '../mocks/handlers';
import { APIClient } from '../../src/client';

beforeEach(() => {
  server.listen();
});

afterEach(() => {
  server.resetHandlers();
});

describe('APIClient', () => {
  it('should make authenticated request', async () => {
    const client = new APIClient({
      baseURL: 'https://api.example.com',
      apiKey: 'test-key',
    });

    const user = await client.request('GET', '/v2/users/123');
    expect(user).toHaveProperty('id', '123');
  });

  it('should handle rate limit errors', async () => {
    const client = new APIClient({
      baseURL: 'https://api.example.com',
      apiKey: 'test-key',
    });

    await expect(
      client.request('GET', '/v2/users')
    ).rejects.toThrow('Rate limit exceeded');
  });
});
```

## README Template

```markdown
# [Platform] API Connector

## Overview
Brief description of the connector and what it does.

## Installation
\`\`\`bash
npm install @your-org/[platform]-connector
\`\`\`

## Authentication

### OAuth 2.0
[Describe OAuth setup]

### API Key
[Describe API key setup]

## Usage

\`\`\`typescript
import { APIClient } from '@your-org/[platform]-connector';

const client = new APIClient({
  baseURL: 'https://api.platform.com',
  apiKey: process.env.API_KEY,
});

const user = await client.users.getUser('123');
\`\`\`

## API Endpoints

### Users
- `getUser(id: string): Promise<User>`
- `listUsers(options?: ListOptions): Promise<User[]>`

## Error Handling
[Describe error handling]

## Rate Limiting
[Describe rate limiting behavior]

## Testing
[Describe testing setup]

## Contributing
[Contributing guidelines]
```

## Notes

- Adapt templates to your chosen language and framework
- Follow API standards from `api-standards.md`
- Customize for platform-specific requirements
- Add platform-specific endpoints as needed
- Keep code DRY and maintainable
