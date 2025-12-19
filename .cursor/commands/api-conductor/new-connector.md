# New Connector

## Overview
Start a new API connector track. This command generates a unique connector ID, creates the connector directory structure, and helps you create detailed specifications and an actionable plan for building a platform API connector (e.g., LinkedIn, Twitter, GitHub). A connector represents a high-level unit of work that will be broken down into phases, tasks, and sub-tasks.

## Steps

1. **Determine Connector Details**
   - Ask the user for:
     - Platform name (e.g., LinkedIn, Twitter, GitHub, Stripe)
     - API type (REST, GraphQL, WebSocket, gRPC)
     - Authentication method (OAuth 2.0, API Key, Basic Auth, etc.)
     - Primary use cases (what endpoints/features are needed)
   - Get a brief description of what needs to be built
   - If no description provided, prompt for one

2. **Generate Unique Connector ID**
   - Create a connector ID using format: `connector-YYYYMMDD-HHMMSS` or sequential number
   - Ensure the ID is unique by checking existing connectors in `api-conductor/tracks.md`
   - Alternative format: `connector-<platform>-<sequential>` (e.g., `connector-linkedin-001`)

3. **Create Connector Directory Structure**
   - Create `api-conductor/connectors/<connector_id>/` directory
   - Create `api-conductor/connectors/<connector_id>/spec.md` file
   - Create `api-conductor/connectors/<connector_id>/plan.md` file
   - Create `api-conductor/connectors/<connector_id>/metadata.json` file

4. **Generate Specification (spec.md)**
   - Analyze the connector details and project context from:
     - `api-conductor/product.md`
     - `api-conductor/tech-stack.md`
     - `api-conductor/api-standards.md`
     - `api-conductor/connector-template.md`
   - Create a detailed specification including:
     - **Title**: Clear, descriptive title (e.g., "LinkedIn API Connector")
     - **Type**: Connector
     - **Platform**: Platform name and API documentation URL
     - **Description**: What connector are we building and why?
     - **API Type**: REST, GraphQL, WebSocket, etc.
     - **Authentication**: Authentication method and flow details
     - **Endpoints**: List of API endpoints to implement
     - **Data Models**: Required data models/schemas
     - **Rate Limiting**: Rate limiting requirements and strategies
     - **Error Handling**: Error handling requirements
     - **Requirements**: Detailed functional and non-functional requirements
     - **Acceptance Criteria**: How do we know it's done?
     - **Dependencies**: What other work or systems does this depend on?
     - **User Impact**: Who is affected and how?

5. **Generate Plan (plan.md)**
   - Break down the specification into actionable phases
   - Each phase should contain:
     - **Phase Name**: Descriptive name
     - **Tasks**: List of tasks with checkboxes `- [ ]`
     - **Sub-tasks**: Optional sub-tasks under each task
   - Standard phases for API connectors:
     - **Phase 1: Authentication Setup**
       - Implement authentication flow
       - Token management and refresh
       - Credential storage and security
     - **Phase 2: Core API Client Implementation**
       - HTTP client setup
       - Base request/response handling
       - Endpoint implementations
     - **Phase 3: Rate Limiting & Retry Logic**
       - Rate limit detection and handling
       - Exponential backoff implementation
       - Request queuing (if needed)
     - **Phase 4: Error Handling**
       - Custom error classes
       - Error code mapping
       - Retry logic for transient errors
     - **Phase 5: Data Models**
       - Type definitions/schemas
       - Data validation
       - Serialization/deserialization
     - **Phase 6: Testing**
       - Unit tests
       - Integration tests
       - Mock API server setup
       - Test fixtures
     - **Phase 7: Documentation**
       - README with usage examples
       - API endpoint documentation
       - Authentication guide
   - Consider the workflow preferences from `api-conductor/workflow.md` (e.g., TDD structure)
   - Reference `api-conductor/connector-template.md` for code patterns

6. **Create Metadata (metadata.json)**
   - Generate JSON file with:
     ```json
     {
       "id": "<connector_id>",
       "title": "<connector_title>",
       "type": "Connector",
       "platform": "<platform_name>",
       "api_type": "<REST|GraphQL|WebSocket|gRPC>",
       "auth_method": "<OAuth2|APIKey|Basic|etc>",
       "status": "Planning",
       "created": "<ISO_timestamp>",
       "updated": "<ISO_timestamp>"
     }
     ```

7. **Update Connectors Tracking File**
   - Add new entry to `api-conductor/tracks.md` with:
     - Connector ID
     - Title
     - Platform
     - Type: "Connector"
     - Status: "Planning"
     - Created date
     - Updated date

## Checklist

- [ ] Connector ID generated and verified unique
- [ ] Connector directory created at `api-conductor/connectors/<connector_id>/`
- [ ] `spec.md` created with comprehensive requirements including API details
- [ ] `plan.md` created with phases, tasks, and sub-tasks following connector template
- [ ] `metadata.json` created with connector information
- [ ] `api-conductor/tracks.md` updated with new connector entry
- [ ] Specification aligns with API standards and connector template
- [ ] Plan follows workflow preferences and includes API-specific phases
