# Codeflow Development Guide

This guide provides comprehensive guidelines for developing, testing, and contributing to the Codeflow project.

## Build & Test Commands

### Type Checking
```bash
bun run typecheck
```
- Strict TypeScript validation without emit
- Ensures type safety across the entire codebase

### Linting
```bash
bun run lint        # Check for issues
bun run lint:fix    # Auto-fix issues
```
- ESLint with TypeScript rules
- Enforces code quality and consistency

### Formatting
```bash
bun run format
```
- Prettier formatting with consistent style
- Semi-colons: true, single quotes, trailing commas (es5), 100 char width

### Testing
```bash
bun run test           # All test suites
bun run test:unit      # Unit tests only
bun run test:integration # Integration tests
bun run test:e2e       # End-to-end tests
bun test <file>        # Single test file
bun run test:watch     # Watch mode
bun run test:coverage  # Coverage report
```

### Specialized Test Commands
```bash
bun run test:agents    # Agent-specific tests
bun run test:commands  # Command-specific tests
bun run test:cli       # CLI-specific tests
bun run test:conversion # Conversion tests
```

## Code Style Guidelines

### TypeScript Configuration
- **Strict Mode**: Enabled for maximum type safety
- **ES Modules**: `import`/`export` exclusively (no CommonJS)
- **Bun Runtime**: Optimized for Bun with `bun-types`
- **Target**: Modern JavaScript features

### Import/Export Style
```typescript
// Correct
import { readFile } from 'node:fs/promises';
import { join } from 'node:path';
import type { Agent } from './agent-registry.js';

// Incorrect
import * as fs from 'fs';
import { readFileSync } from 'fs';
```

### Naming Conventions
- **Variables/Functions**: camelCase (`processAgent`, `validateInput`)
- **Types/Interfaces**: PascalCase (`AgentRegistry`, `WorkflowContext`)
- **Files**: kebab-case (`agent-registry.ts`, `workflow-engine.ts`)
- **Constants**: UPPER_SNAKE_CASE (`MAX_RETRIES`, `DEFAULT_TIMEOUT`)

### File Structure
```
src/
├── cli/               # CLI commands and interface
├── adapters/          # Platform-specific adapters
├── catalog/           # Agent catalog system
├── sync/              # Synchronization engine
├── utils/             # Shared utilities
├── config/            # Configuration management
├── security/          # Security utilities
└── optimization/      # Performance optimizations
```

### Error Handling
```typescript
// Preferred: Specific error types
try {
  await processAgent(agent);
} catch (error) {
  if (error instanceof ValidationError) {
    console.error('Validation failed:', error.message);
  } else if (error instanceof NetworkError) {
    console.error('Network error:', error.message);
  } else {
    console.error('Unexpected error:', error);
  }
}

// Avoid: Generic catches
try {
  // code
} catch (error: any) {
  console.error(error);
}
```

### Type Safety
```typescript
// Preferred: Explicit types
interface AgentConfig {
  name: string;
  version: string;
  timeout?: number;
}

function createAgent(config: AgentConfig): Agent {
  // implementation
}

// Avoid: any types
function processData(data: any): any {
  return data;
}

// Use unknown for truly unknown types
function handleUnknown(input: unknown): void {
  if (typeof input === 'string') {
    console.log(input.toUpperCase());
  }
}
```

### Async/Await Patterns
```typescript
// Preferred: Async functions with proper error handling
async function loadAgents(): Promise<Agent[]> {
  try {
    const agents = await fetchAgents();
    return agents.map(validateAgent);
  } catch (error) {
    throw new AgentLoadError('Failed to load agents', { cause: error });
  }
}

// Avoid: Promise chains
function loadAgents(): Promise<Agent[]> {
  return fetchAgents()
    .then(agents => agents.map(validateAgent))
    .catch(error => {
      throw new Error('Failed to load agents');
    });
}
```

## Architecture Patterns

### CLI-First Design
- All functionality accessible via command line
- Rich interactive modes for complex operations
- Consistent command structure and help system

### Modular Architecture
- Clear separation of concerns
- Dependency injection for testability
- Interface-based design for extensibility

### Agent System Design
- Privacy-safe agent loading
- Dynamic agent discovery and registration
- Context-aware agent selection

### Workflow Orchestration
- Multi-phase workflow execution
- Dependency management and parallel processing
- Quality metrics and progress tracking

## Testing Strategy

### Unit Tests
- Test individual functions and classes
- Mock external dependencies
- Focus on logic and edge cases

### Integration Tests
- Test component interactions
- Use real dependencies where possible
- Validate data flow between components

### End-to-End Tests
- Test complete user workflows
- Validate CLI commands and outputs
- Ensure system integration works

### Test Structure
```typescript
describe('AgentRegistry', () => {
  let registry: AgentRegistry;

  beforeEach(() => {
    registry = new AgentRegistry();
  });

  describe('loadAgents', () => {
    it('should load built-in agents', async () => {
      const agents = await registry.loadAgents();
      expect(agents.length).toBeGreaterThan(0);
    });

    it('should handle invalid agent files gracefully', async () => {
      // Test error handling
    });
  });
});
```

## Development Workflow

### Local Development Setup
```bash
# Clone repository
git clone https://github.com/ferg-cod3s/codeflow.git
cd codeflow

# Install dependencies
bun install

# Run tests
bun run test

# Start development server
bun run server:dev
```

### Code Quality Checks
```bash
# Run all quality checks
bun run typecheck
bun run lint
bun run test

# Format code
bun run format
```

### Contributing Process
1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/new-feature`)
3. **Implement** changes with tests
4. **Run** quality checks (`bun run typecheck && bun run lint && bun run test`)
5. **Format** code (`bun run format`)
6. **Commit** changes (`bun run commit`)
7. **Create** pull request

### Commit Message Format
```bash
type(scope): description

[optional body]

[optional footer]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

### Pull Request Guidelines
- **Title**: Clear, descriptive title
- **Description**: What changes, why, and how to test
- **Labels**: Appropriate labels (enhancement, bug, documentation)
- **Tests**: Include tests for new functionality
- **Documentation**: Update docs for API changes

## Performance Optimization

### Code Splitting
- Lazy load optional features
- Tree-shake unused dependencies
- Bundle analysis for optimization

### Memory Management
- Avoid memory leaks in long-running processes
- Use streams for large file operations
- Clean up event listeners and timers

### Caching Strategy
- Cache expensive operations
- Invalidate cache appropriately
- Use appropriate cache backends

## Security Considerations

### Input Validation
- Validate all user inputs
- Sanitize file paths and URLs
- Use parameterized queries for database operations

### Authentication & Authorization
- Implement proper access controls
- Use secure token handling
- Validate permissions for operations

### Data Protection
- Encrypt sensitive data at rest
- Use secure communication protocols
- Implement audit logging

## Deployment & Release

### Version Management
- Semantic versioning (MAJOR.MINOR.PATCH)
- Automated changelog generation
- Pre-release versions for testing

### Release Process
```bash
# Bump version
bun run release:patch    # 1.0.0 -> 1.0.1
bun run release:minor    # 1.0.0 -> 1.1.0
bun run release:major    # 1.0.0 -> 2.0.0

# Publish
bun run release:publish
```

### Distribution
- NPM package for CLI installation
- GitHub releases for binaries
- Docker images for containerized deployment

## Monitoring & Debugging

### Logging
- Structured logging with consistent format
- Appropriate log levels (DEBUG, INFO, WARN, ERROR)
- Context preservation across operations

### Debugging
- Source maps for production debugging
- Debug logging for development
- Error stack traces with context

### Performance Monitoring
- Response time tracking
- Memory usage monitoring
- Error rate monitoring

## Documentation Standards

### Code Documentation
- JSDoc comments for public APIs
- TypeScript types as documentation
- Inline comments for complex logic

### External Documentation
- README for project overview
- API documentation for public interfaces
- User guides for common workflows

### Documentation Updates
- Update docs with code changes
- Keep examples current and working
- Review docs in pull requests

## Tooling & Automation

### Development Tools
- **Bun**: Fast JavaScript runtime and bundler
- **TypeScript**: Type-safe JavaScript development
- **ESLint**: Code linting and style enforcement
- **Prettier**: Code formatting
- **Husky**: Git hooks for quality checks

### CI/CD Pipeline
- Automated testing on pull requests
- Code quality checks
- Security scanning
- Automated releases

### Development Scripts
- `bun run sync`: Sync agents and commands
- `bun run validate`: Validate project setup
- `bun run clean`: Clean build artifacts
- `bun run build`: Build for production

This development guide ensures consistent, high-quality contributions to the Codeflow project while maintaining code quality, performance, and security standards.