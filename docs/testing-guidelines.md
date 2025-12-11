# Testing Guidelines

## Overview
This document outlines the testing philosophy and practices for the TODO application, following Test-Driven Development (TDD) principles as advocated by Kent Beck.

## Test-Driven Development (TDD)

### The Red-Green-Refactor Cycle

TDD follows a simple, iterative three-step process:

**1. Red - Write a Failing Test**
- Write a test for the next bit of functionality you want to add
- The test should fail because the functionality doesn't exist yet
- This validates that the test is actually testing something and can fail
- Run the test suite to confirm the new test fails for the expected reason

**2. Green - Make the Test Pass**
- Write the simplest code possible to make the test pass
- Don't worry about code quality or elegance at this stage
- Focus only on getting the test to pass
- Avoid the temptation to add extra functionality "while you're at it"
- Run the test suite to confirm all tests now pass

**3. Refactor - Improve the Code**
- Clean up the code while keeping all tests passing
- Remove duplication
- Improve names and structure
- Enhance readability and maintainability
- Run tests frequently during refactoring to ensure nothing breaks
- Commit when tests are green

### Benefits of TDD
- **Confidence**: Every feature has a test validating its behavior
- **Documentation**: Tests serve as living documentation of how code should behave
- **Design feedback**: Difficult-to-test code often indicates design problems
- **Regression protection**: Tests catch bugs when making changes
- **Simplified debugging**: When a test fails, you know exactly what broke

## Unit Testing Philosophy

### Black Box Testing Approach

Unit tests should treat the code under test as a **black box**:

- **Test behavior, not implementation**: Focus on what the code does, not how it does it
- **Test through the public interface**: Only interact with public methods and properties
- **Verify outcomes**: Assert on return values, state changes, or observable effects
- **Avoid implementation coupling**: Tests should not break when internal implementation changes

**Example - Good Black Box Test:**
```javascript
test('should add a new TODO item to the list', () => {
  const list = new TodoList();
  list.addItem('Buy groceries');
  
  expect(list.getItems()).toHaveLength(1);
  expect(list.getItems()[0].text).toBe('Buy groceries');
});
```

**Example - Poor White Box Test (Avoid):**
```javascript
test('should call internal _validateItem method', () => {
  const list = new TodoList();
  const validateSpy = jest.spyOn(list, '_validateItem');
  
  list.addItem('Buy groceries');
  
  expect(validateSpy).toHaveBeenCalled(); // Testing internal implementation
});
```

### No Mocks in Unit Tests

**Unit tests should NOT use mocks** for the following reasons:

1. **Tests become brittle**: Mocks couple tests to implementation details
2. **False confidence**: Tests can pass even when actual integration fails
3. **Refactoring difficulty**: Changing implementation requires updating mocks
4. **Missed bugs**: Mocks don't catch issues in real interactions between components

**Instead of mocking:**
- Use real implementations of dependencies when possible
- Use simple, lightweight test doubles (e.g., in-memory databases instead of mocking database calls)
- If a dependency is too complex, consider if your design needs improvement
- Reserve mocks for integration/end-to-end tests where you're testing boundaries with external systems

**Example - Avoid This (Using Mocks):**
```javascript
test('should save TODO to database', () => {
  const mockDb = { save: jest.fn() };
  const service = new TodoService(mockDb);
  
  service.createTodo('Buy groceries');
  
  expect(mockDb.save).toHaveBeenCalledWith({ text: 'Buy groceries' });
});
```

**Example - Prefer This (Real Implementation):**
```javascript
test('should save and retrieve TODO from database', () => {
  const db = new InMemoryDatabase(); // Real, lightweight implementation
  const service = new TodoService(db);
  
  const id = service.createTodo('Buy groceries');
  const todo = service.getTodo(id);
  
  expect(todo.text).toBe('Buy groceries');
});
```

### What to Test

**Do Test:**
- Public API behavior and contracts
- Edge cases and boundary conditions
- Error handling and validation
- State transitions and side effects
- Return values and output
- Integration between units within your codebase

**Don't Test:**
- Private methods directly (test them through public methods)
- Implementation details (internal data structures, helper functions)
- Third-party libraries (assume they work)
- Language features (assume JavaScript/Node.js works)
- Getters/setters with no logic

## Test Structure

### Arrange-Act-Assert (AAA) Pattern

Structure tests using the AAA pattern for clarity:

```javascript
test('should mark TODO item as completed', () => {
  // Arrange - Set up test data and dependencies
  const list = new TodoList();
  list.addItem('Buy groceries');
  
  // Act - Perform the action being tested
  list.completeItem(0);
  
  // Assert - Verify the expected outcome
  expect(list.getItems()[0].completed).toBe(true);
});
```

### Test Naming

Use descriptive test names that explain the behavior being tested:

- **Format**: "should [expected behavior] when [condition]"
- **Be specific**: Clearly state what's being tested
- **Use plain language**: Avoid technical jargon when possible

**Good examples:**
- `should add item to empty list`
- `should throw error when adding empty TODO text`
- `should preserve existing items when adding new item`

**Avoid:**
- `test1`, `testAddItem`, `addItemWorks`

## Testing Scope

### Unit Tests
- **Purpose**: Test individual functions or classes in isolation
- **Scope**: Single unit of code (function, class, module)
- **Speed**: Fast (milliseconds)
- **No mocks**: Use real implementations
- **Coverage**: Aim for high coverage of business logic

### Integration Tests
- **Purpose**: Test how multiple units work together
- **Scope**: Multiple modules, database interactions, API calls
- **Speed**: Moderate (seconds)
- **Test doubles**: Use lightweight in-memory alternatives (e.g., in-memory DB)
- **Coverage**: Focus on critical integration points

### End-to-End (E2E) Tests
- **Purpose**: Test complete user workflows
- **Scope**: Full application stack
- **Speed**: Slow (seconds to minutes)
- **External services**: May use mocks for external APIs
- **Coverage**: Cover main user journeys only

## Best Practices

### General Guidelines

1. **One assertion per test**: Each test should verify one specific behavior
2. **Tests should be independent**: No test should depend on another test's execution
3. **Fast feedback**: Unit tests should run in milliseconds
4. **Deterministic**: Tests should always produce the same result
5. **Readable**: Tests should be easy to understand and maintain
6. **No conditional logic**: Avoid if statements and loops in tests

### Code Coverage

- Aim for **80-90% code coverage** for business logic
- Don't obsess over 100% coverage - focus on valuable tests
- Coverage is a metric, not a goal
- Uncovered code should be either:
  - Truly unreachable (dead code to remove)
  - Not worth testing (simple getters, configuration)
  - Tested in integration/E2E tests instead

### Test Maintenance

- **Keep tests simple**: Simpler than the code they test
- **Remove duplication**: Extract common setup into helper functions
- **Update tests when requirements change**: Tests are first-class code
- **Delete tests for removed features**: Don't keep obsolete tests
- **Refactor tests**: Apply the same quality standards as production code

## Testing in This Project

### Frontend Testing (React)

**Test React components as black boxes:**
- Render components with props
- Simulate user interactions (clicks, typing)
- Assert on rendered output (what user sees)
- Don't test React internals (state, lifecycle methods)

```javascript
test('should display TODO item text', () => {
  render(<TodoItem text="Buy groceries" completed={false} />);
  
  expect(screen.getByText('Buy groceries')).toBeInTheDocument();
});
```

### Backend Testing (Express/Node.js)

**Test API endpoints and business logic:**
- Test route handlers with actual requests
- Test business logic functions with real data
- Use in-memory data structures instead of mocking

```javascript
test('should create new TODO item', () => {
  const todos = new TodoRepository();
  const service = new TodoService(todos);
  
  const item = service.createTodo({ text: 'Buy groceries' });
  
  expect(item.id).toBeDefined();
  expect(item.text).toBe('Buy groceries');
  expect(item.completed).toBe(false);
});
```

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode (re-run on file changes)
npm test -- --watch

# Run tests with coverage
npm test -- --coverage

# Run tests for specific package
npm test --workspace=packages/backend
```

## Common Pitfalls to Avoid

1. **Testing implementation details**: Focus on behavior, not internals
2. **Overusing mocks**: Use real implementations when practical
3. **Writing tests after code**: Follow the red-green-refactor cycle
4. **Skipping refactor step**: Clean code is just as important as working code
5. **Large, complex tests**: Keep tests small and focused
6. **Testing everything**: Focus on valuable tests over coverage percentage
7. **Brittle tests**: Tests shouldn't break when refactoring implementation

## References

- Kent Beck - "Test-Driven Development: By Example"
- Martin Fowler - "Mocks Aren't Stubs"
- Robert C. Martin - "The Clean Coder"
