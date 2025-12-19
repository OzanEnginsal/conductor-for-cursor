# Setup

## Overview
Initialize the API Conductor environment and set up project context for building platform API connectors. This command scaffolds the project structure and guides you through defining core components: product context, tech stack, workflow preferences, API standards, and connector templates. Run this once per project to establish the foundation for API connector development.

## Steps

1. **Create API Conductor Directory Structure**
   - Create `api-conductor/` directory at project root if it doesn't exist
   - Create `api-conductor/code_styleguides/` directory for style guide files
   - Create `api-conductor/connectors/` directory for connector artifacts

2. **Define Product Context**
   - Guide the user through defining:
     - Target users (developers building integrations)
     - Product goals (reliable, well-tested API connectors)
     - High-level features (authentication, rate limiting, error handling, data models)
     - Key user journeys (building connectors, testing integrations)
   - Generate `api-conductor/product.md` with the provided information

3. **Define Tech Stack**
   - Guide the user through defining:
     - Programming languages and versions (preferred for API development)
     - HTTP clients (axios, fetch, httpx, etc.)
     - OAuth libraries and authentication tools
     - Rate limiting libraries
     - Mock API tools (MSW, WireMock, responses, etc.)
     - API testing frameworks
     - Schema validation tools (JSON Schema, Zod, Pydantic, etc.)
     - Development tools and dependencies
     - Architecture patterns for API clients
     - Deployment and infrastructure preferences
   - Generate `api-conductor/tech-stack.md` with the provided information

4. **Define Workflow Preferences**
   - Guide the user through defining:
     - Testing approach (TDD, BDD, etc.) with emphasis on API testing
     - Mock API server setup and usage
     - Integration testing with sandbox/test environments
     - Contract testing approach
     - Commit strategy and conventions
     - Code review process
     - Branching strategy
     - API documentation standards (OpenAPI, README structure)
     - Version management for API clients
   - Generate `api-conductor/workflow.md` with the provided information

5. **Define API Standards**
   - Guide the user through defining:
     - Authentication patterns (OAuth 2.0 flows, token refresh mechanisms)
     - Rate limiting strategies (exponential backoff, rate limit headers)
     - Error handling standards (retry logic, error types, error code mapping)
     - Data model conventions
     - API versioning approach
     - Logging and monitoring requirements
     - Security best practices
   - Generate `api-conductor/api-standards.md` with the provided information

6. **Create Connector Template**
   - Generate `api-conductor/connector-template.md` with:
     - Standard connector directory structure
     - Code templates for common patterns:
       - OAuth 2.0 authorization code flow
       - OAuth 2.0 client credentials flow
       - API key authentication
       - Token refresh mechanisms
       - Rate limiting implementation
       - Error handling patterns
       - Data model definitions
       - Mock API server setup
       - Test fixtures and helpers

7. **Initialize Tracking File**
   - Create `api-conductor/tracks.md` with header and structure for tracking all connectors
   - Include columns for: Connector ID, Title, Platform, Status, Created Date, Updated Date

## Checklist

- [ ] `api-conductor/` directory structure created
- [ ] `api-conductor/product.md` generated with API connector product context
- [ ] `api-conductor/tech-stack.md` generated with API-specific technical preferences
- [ ] `api-conductor/workflow.md` generated with API testing workflow preferences
- [ ] `api-conductor/api-standards.md` generated with connector standards
- [ ] `api-conductor/connector-template.md` generated with connector structure templates
- [ ] `api-conductor/tracks.md` initialized with tracking structure
- [ ] All context files are properly formatted and complete
