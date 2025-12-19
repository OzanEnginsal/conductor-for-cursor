# API Conductor for Cursor

**Build reliable platform API connectors with confidence.**

API Conductor is a specialized Context-Driven Development system for building platform API connectors (like LinkedIn, Twitter, GitHub, Stripe, etc.). It extends the Conductor philosophy with API-specific patterns, standards, and workflows to ensure every connector is reliable, well-tested, and maintainable.

## Philosophy

API Conductor brings structured planning and implementation workflows specifically tailored for API connector development. By treating API connector patterns as managed artifacts alongside your code, you ensure consistency, reliability, and best practices across all connectors.

## Features

* **API-Focused Planning**: Create specs and plans optimized for API connector development
* **Authentication Patterns**: Built-in support for OAuth 2.0, API keys, and other auth methods
* **Rate Limiting Standards**: Standardized rate limiting and retry logic patterns
* **Error Handling**: Consistent error handling across all connectors
* **Mock API Testing**: Infrastructure for testing with mock APIs
* **API Standards**: Enforced standards for authentication, rate limiting, error handling, and security
* **Connector Templates**: Code templates and structure patterns for quick connector development

## Installation

1. Copy the `.cursor/commands/api-conductor/` directory to your project root
2. Copy the `api-conductor/` directory to your project root (or let the `/api-conductor-setup` command create it)

That's it! The commands will be available in Cursor when you type `/` in the chat.

## Usage

API Conductor manages the entire lifecycle of your API connector development.

### 1. Set Up the Project (Run Once)

When you run `/api-conductor-setup`, API Conductor helps you define the core components for API connector development:

* **Product**: Define API connector product context (target users, goals, features)
* **Tech Stack**: Configure API-specific technologies (HTTP clients, OAuth libraries, mock API tools)
* **Workflow**: Set API testing workflows (mock APIs, integration tests, contract testing)
* **API Standards**: Define authentication, rate limiting, error handling, and security standards
* **Connector Template**: Set up standard connector structure and code patterns

**Generated Artifacts:**

* `api-conductor/product.md`
* `api-conductor/tech-stack.md`
* `api-conductor/workflow.md`
* `api-conductor/api-standards.md`
* `api-conductor/connector-template.md`
* `api-conductor/tracks.md`

```
/api-conductor-setup
```

### 2. Start a New Connector

When you're ready to build a new API connector, run `/api-conductor-new-connector`. This initializes a **connector** — a high-level unit of work for a specific platform API. API Conductor helps you generate:

* **Specs**: Detailed requirements including platform details, API type, authentication method, endpoints, rate limiting, and error handling
* **Plan**: An actionable plan with API-specific phases:
  - Authentication setup
  - Core API client implementation
  - Rate limiting & retry logic
  - Error handling
  - Data models
  - Testing (unit, integration, mock API)
  - Documentation

**Generated Artifacts:**

* `api-conductor/connectors/<connector_id>/spec.md`
* `api-conductor/connectors/<connector_id>/plan.md`
* `api-conductor/connectors/<connector_id>/metadata.json`

```
/api-conductor-new-connector
# OR with a description
/api-conductor-new-connector "Build LinkedIn API connector with OAuth 2.0"
```

### 3. Implement the Connector

Once you approve the plan, run `/api-conductor-implement`. Your coding agent then works through the `plan.md` file, following API standards and checking off tasks as it completes them.

**Updated Artifacts:**

* `api-conductor/tracks.md` (Status updates)
* `api-conductor/connectors/<connector_id>/plan.md` (Status updates)

```
/api-conductor-implement
```

API Conductor will:

1. Select the next pending task
2. Follow the defined workflow (e.g., TDD: Write Test → Fail → Implement → Pass)
3. Apply API standards (authentication, rate limiting, error handling)
4. Set up mock API servers for testing
5. Update the status in the plan as it progresses
6. **Verify Progress**: Guide you through manual verification at the end of each phase

During implementation, you can also:

* **Check status**: Get a high-level overview of your connectors' progress
```
/api-conductor-status
```
* **Revert work**: Undo a connector or specific task if needed
```
/api-conductor-revert
```

## Commands Reference

| Command | Description | Artifacts |
|---------|-------------|-----------|
| `/api-conductor-setup` | Scaffolds the project and sets up the API Conductor environment. Run this once per project. | `api-conductor/product.md`<br>`api-conductor/tech-stack.md`<br>`api-conductor/workflow.md`<br>`api-conductor/api-standards.md`<br>`api-conductor/connector-template.md`<br>`api-conductor/tracks.md` |
| `/api-conductor-new-connector` | Starts a new API connector track. Generates spec.md and plan.md with API-specific details. | `api-conductor/connectors/<id>/spec.md`<br>`api-conductor/connectors/<id>/plan.md`<br>`api-conductor/tracks.md` |
| `/api-conductor-implement` | Executes the tasks defined in the current connector's plan, following API standards. | `api-conductor/tracks.md`<br>`api-conductor/connectors/<id>/plan.md` |
| `/api-conductor-status` | Displays the current progress of all connectors. | Reads `api-conductor/tracks.md` |
| `/api-conductor-revert` | Reverts a connector, phase, or task by analyzing git history. | Reverts git history |

## Directory Structure

```
.cursor/
  commands/
    api-conductor/
      setup.md
      new-connector.md
      implement.md
      status.md
      revert.md
api-conductor/
  product.md
  tech-stack.md
  workflow.md
  api-standards.md
  connector-template.md
  tracks.md
  code_styleguides/
    (directory for style guide files)
  connectors/
    <connector_id>/
      spec.md
      plan.md
      metadata.json
```

## Key Differences from Base Conductor

### Naming
- Uses `api-conductor` instead of `conductor`
- Uses `connectors` instead of `tracks`
- Commands prefixed with `/api-conductor-`

### Specialization
- **API-Focused**: All workflows and templates optimized for API connector development
- **Authentication**: Built-in patterns for OAuth 2.0, API keys, and other auth methods
- **Rate Limiting**: Standardized rate limiting and retry logic
- **Error Handling**: Consistent error handling patterns
- **Mock APIs**: Infrastructure for mock API testing

### Standards
- **API Standards File**: Dedicated `api-standards.md` with connector development standards
- **Connector Template**: `connector-template.md` with code patterns and structure
- **API-Specific Phases**: Plan phases tailored for API development (auth, rate limiting, etc.)

### Testing
- **Mock API Emphasis**: Strong focus on mock API servers for development
- **Integration Testing**: Workflows for testing with sandbox/test environments
- **Contract Testing**: Support for API contract verification

## API Standards

All connectors must follow the standards defined in `api-standards.md`:

- **Authentication**: OAuth 2.0 flows, token refresh, secure storage
- **Rate Limiting**: Exponential backoff, header parsing, request queuing
- **Error Handling**: Custom error classes, retry logic, error code mapping
- **Data Models**: Schema validation, type safety, serialization
- **Security**: Credential storage, HTTPS only, input validation
- **Logging**: Structured logging, sensitive data exclusion

## Connector Template

Use `connector-template.md` as a starting point for all new connectors. It includes:

- Standard directory structure
- Code templates for authentication (OAuth 2.0, API keys)
- Rate limiting implementation patterns
- Error handling patterns
- Data model examples
- Mock API setup
- Test examples

## Example: Building a LinkedIn Connector

1. **Setup** (once):
   ```
   /api-conductor-setup
   ```

2. **Create Connector**:
   ```
   /api-conductor-new-connector "LinkedIn API connector with OAuth 2.0"
   ```
   - Platform: LinkedIn
   - API Type: REST
   - Auth Method: OAuth 2.0 Authorization Code Flow

3. **Review Plan**: Review the generated spec and plan

4. **Implement**:
   ```
   /api-conductor-implement
   ```
   - Implements authentication
   - Implements API client
   - Adds rate limiting
   - Implements error handling
   - Creates data models
   - Sets up tests with mock API
   - Documents connector

5. **Verify**: Test with mock API and LinkedIn sandbox

## Best Practices

1. **Always Use Mock APIs**: Develop and test with mock APIs, use real APIs only for integration tests
2. **Follow API Standards**: Every connector must follow `api-standards.md`
3. **Test Thoroughly**: Aim for 90%+ test coverage, especially for auth and rate limiting
4. **Document Everything**: Complete README and API docs for every connector
5. **Security First**: Never commit credentials, always use secure storage
6. **Handle Errors Gracefully**: Implement proper retry logic and error handling

## Contributing

Contributions welcome! Please ensure all connectors follow the API standards and include comprehensive tests.

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](../LICENSE) for details.

## Acknowledgments

* Inspired by [Conductor](https://github.com/gemini-cli-extensions/conductor) by gemini-cli-extensions
* Built for [Cursor IDE](https://cursor.sh/)
* Specialized for platform API connector development
