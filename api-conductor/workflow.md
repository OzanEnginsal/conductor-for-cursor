# Workflow Preferences

## Overview
This document defines team workflow preferences for building API connectors including testing approach, commit strategy, code review process, branching strategy, and documentation standards. These preferences guide how connector development work is executed and ensure consistency across all connectors.

## Testing Approach

### Testing Strategy
- **Primary Approach**: TDD (Test-Driven Development) with emphasis on API testing
- **Description**: Write tests first using mock APIs, then implement code to make tests pass. Use integration tests with sandbox/test environments for final verification.

### Test Types
- **Unit Tests**: 
  - Test individual functions and classes
  - Use mock API responses (MSW, WireMock, etc.)
  - Test authentication logic, rate limiting, error handling in isolation
  - Mock HTTP client responses
  
- **Integration Tests**: 
  - Test against sandbox/test API environments when available
  - Test full authentication flows
  - Test rate limiting behavior
  - Test error scenarios
  - Use real API responses (but with test credentials)
  
- **Contract Tests**: 
  - Verify API contracts match expectations
  - Test request/response schemas
  - Ensure backward compatibility

### Test Structure
- **Naming Convention**: 
  - `describe` blocks: Group by feature/endpoint
  - Test names: `should [expected behavior] when [condition]`
  - Example: `should retry request when rate limit exceeded`
  
- **Organization**: 
  ```
  tests/
    unit/
      auth.test.ts
      rate-limiter.test.ts
      client.test.ts
    integration/
      auth-flow.test.ts
      endpoints.test.ts
    mocks/
      responses/
        users.json
        posts.json
      handlers.ts
  ```
  
- **Example**:
  ```typescript
  describe('LinkedIn API Client', () => {
    describe('Authentication', () => {
      it('should refresh token when expired', async () => {
        // Test implementation
      });
    });
    
    describe('Rate Limiting', () => {
      it('should retry with exponential backoff', async () => {
        // Test implementation
      });
    });
  });
  ```

### Test Coverage
- **Target Coverage**: 90%+
- **Critical Areas**: 
  - Authentication flows: 100%
  - Rate limiting logic: 100%
  - Error handling: 100%
  - Core API client methods: 90%+

### Mock API Usage
- **Development**: Use mock APIs for all development and unit testing
- **Mock Server**: Set up mock API server using MSW, WireMock, or similar
- **Fixtures**: Create reusable test fixtures for common API responses
- **Edge Cases**: Mock error responses, rate limit responses, network failures

## Commit Strategy

### Commit Message Format
- **Format**: Conventional Commits
- **Example**: 
  ```
  feat(linkedin): add user profile endpoint
  
  Implements GET /v2/userProfile endpoint with proper error handling
  and rate limiting.
  
  Closes #123
  ```

### Commit Types
- `feat`: New endpoint or feature
- `fix`: Bug fix in connector
- `docs`: Documentation changes
- `test`: Test additions/changes
- `refactor`: Code refactoring
- `chore`: Build process or dependency updates
- `auth`: Authentication-related changes
- `rate-limit`: Rate limiting changes
- `error`: Error handling changes

### Commit Guidelines
- **Atomic Commits**: One logical change per commit (e.g., one endpoint)
- **Commit Frequency**: Commit after each task completion
- **Commit Size**: Keep commits focused and reviewable
- **Include Tests**: Always include tests with implementation commits

## Code Review Process

### Review Requirements
- **Required For**: All PRs to main branch
- **Reviewers**: At least one team member familiar with API connector patterns

### Review Checklist
- [ ] Code follows API standards (`api-standards.md`)
- [ ] Tests are included and passing
- [ ] Mock API tests are included
- [ ] Integration tests pass (if sandbox available)
- [ ] Documentation is updated (README, inline docs)
- [ ] Error handling follows standards
- [ ] Rate limiting is implemented correctly
- [ ] Authentication is secure
- [ ] No hardcoded credentials or secrets
- [ ] Code follows style guidelines

### Review Guidelines
- **Tone**: Professional, constructive
- **Focus Areas**: 
  - API standards compliance
  - Test coverage and quality
  - Error handling robustness
  - Security (auth, credentials)
  - Performance (rate limiting, retries)
- **Response Time**: Within 24 hours

## Branching Strategy

### Branch Naming
- **Format**: `connector/<platform>/<feature>` or `connector/<platform>/fix/<issue>`
- **Examples**:
  - `connector/linkedin/add-posts-endpoint`
  - `connector/twitter/fix/rate-limit-handling`
  - `connector/github/add-webhook-support`

### Branch Workflow
- **Main Branch**: `main` - Production-ready connectors
- **Development Branch**: `develop` - Integration branch for connectors (if using)
- **Feature Branches**: Created from `main` or `develop` for new connectors or features
- **Release Branches**: Not typically used for connectors (published as packages)

### Merge Strategy
- **Method**: Squash and merge (preferred) or merge commits
- **When to Use**: 
  - Squash: For feature branches with multiple commits
  - Merge: For branches that should preserve commit history

## Documentation Standards

### Code Documentation
- **Comments**: 
  - Explain "why" not "what"
  - Document complex logic (rate limiting, retry strategies)
  - Document API-specific quirks or limitations
  
- **Function Documentation**: 
  ```typescript
  /**
   * Fetches user profile from LinkedIn API
   * 
   * @param userId - LinkedIn user ID or "me" for authenticated user
   * @param fields - Optional fields to include in response
   * @returns User profile data
   * @throws {RateLimitError} When rate limit is exceeded
   * @throws {AuthenticationError} When authentication fails
   */
  async getUserProfile(userId: string, fields?: string[]): Promise<UserProfile> {
    // Implementation
  }
  ```

### README Files
- **Required Sections**: 
  - Overview and purpose
  - Installation
  - Authentication setup
  - Usage examples
  - API endpoints documentation
  - Error handling
  - Rate limiting information
  - Testing
  - Contributing
  
- **Update Frequency**: Update with every new endpoint or significant change

### API Documentation
- **Format**: OpenAPI/Swagger (if applicable) or Markdown
- **Location**: In connector README or separate `API.md` file
- **Content**: 
  - Endpoint descriptions
  - Request/response examples
  - Error codes and meanings
  - Rate limits
  - Authentication requirements

## Development Workflow

### Daily Workflow
1. **Check Status**: Run `/api-conductor-status` to see active connectors
2. **Select Connector**: Choose connector to work on
3. **Review Plan**: Review current phase and tasks
4. **Write Tests**: Write tests first (TDD)
5. **Implement**: Implement code to pass tests
6. **Test**: Run unit tests with mock API
7. **Commit**: Commit changes with descriptive message
8. **Verify**: Run integration tests if sandbox available

### Connector Development Process
1. **Setup**: Run `/api-conductor-setup` (once per project)
2. **Plan**: Run `/api-conductor-new-connector` to create spec and plan
3. **Review**: Review spec and plan, adjust if needed
4. **Implement**: Run `/api-conductor-implement` to build connector
5. **Test**: Verify with mock API and sandbox API
6. **Document**: Complete README and API docs
7. **Review**: Create PR and get code review
8. **Deploy**: Merge and publish connector

### Bug Fix Process
1. **Identify**: Identify issue in connector
2. **Reproduce**: Reproduce with tests
3. **Fix**: Implement fix following TDD
4. **Test**: Verify fix with tests
5. **Document**: Update documentation if needed
6. **Review**: Create PR and get review
7. **Deploy**: Merge fix

## Code Style

### Style Guide
- **Guide**: Follow language-specific style guide
- **Location**: `api-conductor/code_styleguides/`

### Formatting
- **Tool**: [e.g., Prettier, Black, gofmt]
- **Configuration**: [Reference to config]
- **Auto-format**: Format on save

### Linting
- **Tool**: [e.g., ESLint, Pylint, golangci-lint]
- **Configuration**: [Reference to config]
- **Rules**: Enforce API standards, security best practices

## Communication

### Team Communication
- **Channels**: [Communication channels used]
- **Standups**: [Standup format and frequency]
- **Updates**: Share connector progress, blockers, API changes

## Notes

- Always use mock APIs for development - never hit production APIs during development
- Test rate limiting logic thoroughly - it's critical for reliability
- Document API quirks and limitations - they're often platform-specific
- Keep connectors focused - one connector per platform
- Consider API versioning from the start
