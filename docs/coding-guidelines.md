# Coding Guidelines

## Overview
This document outlines the coding standards, best practices, and conventions for the TODO application. Following these guidelines ensures consistency, maintainability, and quality across the codebase.

## General Principles

### Code Quality
- **Readability over cleverness**: Write code that's easy to understand, not code that shows off
- **YAGNI (You Aren't Gonna Need It)**: Don't add functionality until it's necessary
- **DRY (Don't Repeat Yourself)**: Extract common logic into reusable functions
- **KISS (Keep It Simple, Stupid)**: Prefer simple solutions over complex ones
- **Boy Scout Rule**: Leave code cleaner than you found it

### Design Principles
- **Single Responsibility**: Each function/class should do one thing well
- **Separation of Concerns**: Keep business logic separate from UI and data access
- **Dependency Injection**: Pass dependencies as parameters rather than creating them internally
- **Composition over Inheritance**: Prefer composing small pieces over large inheritance hierarchies

## JavaScript/Node.js Standards

### Modern JavaScript (ES6+)
- Use `const` by default, `let` when reassignment is needed, never `var`
- Use arrow functions for short callbacks and methods
- Use template literals for string interpolation
- Use destructuring for objects and arrays
- Use spread operator for copying and merging
- Use async/await over raw Promises for asynchronous code

**Example:**
```javascript
// Good
const getUserTodos = async (userId) => {
  const user = await userRepository.findById(userId);
  const todos = await todoRepository.findByUserId(userId);
  return { ...user, todos };
};

// Avoid
var getUserTodos = function(userId) {
  return userRepository.findById(userId).then(function(user) {
    return todoRepository.findByUserId(userId).then(function(todos) {
      return Object.assign({}, user, { todos: todos });
    });
  });
};
```

### Naming Conventions

**Variables and Functions**
- Use camelCase: `getUserTodos`, `todoList`, `isCompleted`
- Use descriptive names that reveal intent
- Boolean variables should read like questions: `isActive`, `hasItems`, `canDelete`
- Avoid abbreviations unless universally understood

**Constants**
- Use UPPER_SNAKE_CASE for true constants: `MAX_TODO_LENGTH`, `API_BASE_URL`
- Use camelCase for configuration objects that don't change

**Classes**
- Use PascalCase: `TodoService`, `UserRepository`, `ListController`

**Files**
- Use kebab-case for file names: `todo-service.js`, `user-repository.js`
- React components use PascalCase: `TodoItem.js`, `ListContainer.js`

**Example:**
```javascript
// Good
const MAX_RETRIES = 3;
const apiConfig = { timeout: 5000 };

class TodoService {
  async createTodo(text) {
    const isValid = this.validateTodoText(text);
    if (!isValid) {
      throw new Error('Invalid todo text');
    }
    return this.repository.save({ text, completed: false });
  }
}

// Avoid
const max_retries = 3;  // Wrong case
const APICONFIG = { timeout: 5000 };  // Wrong case for mutable config

class todoService {  // Wrong case
  async CreateTodo(txt) {  // Wrong case, unclear name
    const v = this.validateTodoText(txt);  // Unclear name
    // ...
  }
}
```

### Function Guidelines

**Function Length**
- Keep functions short (ideally < 20 lines)
- If a function is too long, extract smaller functions
- Each function should do one thing

**Parameters**
- Limit to 3-4 parameters; use an options object for more
- Use destructuring for options objects
- Provide default values where appropriate

**Example:**
```javascript
// Good - Clear, focused function with destructured options
const createTodo = async ({ text, listId, priority = 'normal' }) => {
  validateText(text);
  const todo = { text, listId, priority, completed: false };
  return await repository.save(todo);
};

// Avoid - Too many parameters
const createTodo = async (text, listId, priority, dueDate, tags, category) => {
  // ...
};
```

### Error Handling

**Use Proper Error Handling**
- Always handle errors in async functions
- Use try-catch for synchronous errors
- Create custom error classes for domain-specific errors
- Include meaningful error messages

**Example:**
```javascript
// Good
class TodoNotFoundError extends Error {
  constructor(id) {
    super(`Todo with id ${id} not found`);
    this.name = 'TodoNotFoundError';
    this.statusCode = 404;
  }
}

const getTodo = async (id) => {
  try {
    const todo = await repository.findById(id);
    if (!todo) {
      throw new TodoNotFoundError(id);
    }
    return todo;
  } catch (error) {
    logger.error('Error fetching todo', { id, error });
    throw error;
  }
};

// Avoid - Silent failure
const getTodo = async (id) => {
  const todo = await repository.findById(id);
  return todo;  // Could be null, no error handling
};
```

### Asynchronous Code

**Prefer async/await**
- Use async/await for cleaner asynchronous code
- Handle Promise rejections with try-catch
- Use Promise.all() for parallel operations
- Avoid mixing callbacks and Promises

**Example:**
```javascript
// Good - Clear parallel execution
const loadTodoList = async (listId) => {
  try {
    const [list, todos, users] = await Promise.all([
      listRepository.findById(listId),
      todoRepository.findByListId(listId),
      userRepository.findByListId(listId)
    ]);
    return { list, todos, users };
  } catch (error) {
    throw new Error(`Failed to load list ${listId}: ${error.message}`);
  }
};

// Avoid - Sequential when parallel would work
const loadTodoList = async (listId) => {
  const list = await listRepository.findById(listId);
  const todos = await todoRepository.findByListId(listId);  // Waits unnecessarily
  const users = await userRepository.findByListId(listId);  // Waits unnecessarily
  return { list, todos, users };
};
```

## React Guidelines

### Component Structure

**Functional Components with Hooks**
- Use functional components with hooks (not class components)
- Keep components small and focused
- Extract complex logic into custom hooks
- One component per file

**Component Organization:**
```javascript
// 1. Imports
import React, { useState, useEffect } from 'react';
import PropTypes from 'prop-types';
import { TodoItem } from './TodoItem';

// 2. Component definition
const TodoList = ({ listId, onUpdate }) => {
  // 3. Hooks
  const [todos, setTodos] = useState([]);
  const [loading, setLoading] = useState(true);

  // 4. Effects
  useEffect(() => {
    loadTodos();
  }, [listId]);

  // 5. Event handlers
  const handleToggle = (id) => {
    // ...
  };

  // 6. Helper functions
  const loadTodos = async () => {
    // ...
  };

  // 7. Render
  return (
    <div className="todo-list">
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} onToggle={handleToggle} />
      ))}
    </div>
  );
};

// 8. PropTypes
TodoList.propTypes = {
  listId: PropTypes.string.isRequired,
  onUpdate: PropTypes.func
};

// 9. Export
export default TodoList;
```

### Props and State

**Props**
- Use destructuring in function parameters
- Define PropTypes for type checking
- Mark required props explicitly
- Keep props minimal and focused

**State**
- Keep state as close to where it's used as possible
- Derive values from state rather than storing duplicates
- Use reducer for complex state logic

**Example:**
```javascript
// Good - Minimal, well-defined props
const TodoItem = ({ text, completed, onToggle, onDelete }) => {
  return (
    <div className="todo-item">
      <input type="checkbox" checked={completed} onChange={onToggle} />
      <span className={completed ? 'completed' : ''}>{text}</span>
      <button onClick={onDelete}>Delete</button>
    </div>
  );
};

TodoItem.propTypes = {
  text: PropTypes.string.isRequired,
  completed: PropTypes.bool.isRequired,
  onToggle: PropTypes.func.isRequired,
  onDelete: PropTypes.func.isRequired
};

// Avoid - Passing entire objects, unclear requirements
const TodoItem = ({ todo, actions }) => {
  // Less clear what the component needs
  return <div>...</div>;
};
```

### Event Handlers

**Naming and Structure**
- Prefix with `handle`: `handleClick`, `handleSubmit`, `handleChange`
- Keep handlers simple, extract complex logic
- Use arrow functions to avoid binding issues

**Example:**
```javascript
const TodoForm = ({ onSubmit }) => {
  const [text, setText] = useState('');

  const handleChange = (e) => {
    setText(e.target.value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (text.trim()) {
      onSubmit(text);
      setText('');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={text} onChange={handleChange} />
      <button type="submit">Add</button>
    </form>
  );
};
```

### Styling

**CSS Organization**
- One CSS file per component (co-located)
- Use BEM-like naming: `component-name__element--modifier`
- Prefer CSS classes over inline styles
- Use CSS variables for theming

**Example:**
```css
/* TodoItem.css */
.todo-item {
  display: flex;
  align-items: center;
  padding: var(--spacing-md);
  background: var(--surface-color);
}

.todo-item__checkbox {
  margin-right: var(--spacing-sm);
}

.todo-item__text {
  flex: 1;
}

.todo-item__text--completed {
  text-decoration: line-through;
  color: var(--text-secondary);
}

.todo-item__delete {
  color: var(--error-color);
}
```

## Backend Guidelines (Express/Node.js)

### Application Structure

**Layered Architecture:**
```
src/
├── routes/          # Express route definitions
├── controllers/     # Request/response handling
├── services/        # Business logic
├── repositories/    # Data access layer
├── models/          # Data models/schemas
├── middleware/      # Express middleware
└── utils/           # Helper functions
```

**Separation of Concerns:**
- **Routes**: Define endpoints and map to controllers
- **Controllers**: Handle HTTP concerns (request/response)
- **Services**: Contain business logic
- **Repositories**: Handle data persistence

### Route Definitions

**RESTful Conventions**
- Use proper HTTP methods: GET, POST, PUT, DELETE
- Use plural nouns for resources: `/todos`, `/lists`
- Use nesting for relationships: `/lists/:listId/todos`
- Version your API: `/api/v1/todos`

**Example:**
```javascript
// routes/todos.js
const express = require('express');
const router = express.Router();
const todoController = require('../controllers/todo-controller');

router.get('/', todoController.getAllTodos);
router.post('/', todoController.createTodo);
router.get('/:id', todoController.getTodoById);
router.put('/:id', todoController.updateTodo);
router.delete('/:id', todoController.deleteTodo);

module.exports = router;
```

### Controllers

**Keep Controllers Thin**
- Validate input
- Call service layer
- Format response
- Handle errors

**Example:**
```javascript
// controllers/todo-controller.js
const todoService = require('../services/todo-service');

const createTodo = async (req, res, next) => {
  try {
    const { text, listId } = req.body;
    
    if (!text || !text.trim()) {
      return res.status(400).json({ error: 'Todo text is required' });
    }

    const todo = await todoService.createTodo({ text, listId });
    res.status(201).json(todo);
  } catch (error) {
    next(error);
  }
};

module.exports = { createTodo };
```

### Services

**Business Logic Layer**
- Contains core application logic
- Independent of HTTP concerns
- Easily testable
- Reusable across different controllers

**Example:**
```javascript
// services/todo-service.js
const todoRepository = require('../repositories/todo-repository');

class TodoService {
  async createTodo({ text, listId }) {
    this.validateTodoText(text);
    
    const todo = {
      text: text.trim(),
      listId,
      completed: false,
      createdAt: new Date()
    };
    
    return await todoRepository.create(todo);
  }

  validateTodoText(text) {
    if (!text || text.trim().length === 0) {
      throw new Error('Todo text cannot be empty');
    }
    if (text.length > 500) {
      throw new Error('Todo text too long (max 500 characters)');
    }
  }

  async completeTodo(id) {
    const todo = await todoRepository.findById(id);
    if (!todo) {
      throw new Error(`Todo ${id} not found`);
    }
    return await todoRepository.update(id, { completed: true });
  }
}

module.exports = new TodoService();
```

### Middleware

**Common Middleware Patterns**
- Error handling
- Request logging
- Authentication/authorization
- Input validation

**Example:**
```javascript
// middleware/error-handler.js
const errorHandler = (err, req, res, next) => {
  console.error('Error:', err);

  const statusCode = err.statusCode || 500;
  const message = err.message || 'Internal server error';

  res.status(statusCode).json({
    error: message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  });
};

module.exports = errorHandler;
```

## Testing Standards

### Test Organization

**Test File Structure**
- Co-locate tests with source files or in `__tests__` directory
- Name test files: `*.test.js` or `*.spec.js`
- Mirror the structure of your source code

**Test Structure (AAA Pattern):**
```javascript
describe('TodoService', () => {
  describe('createTodo', () => {
    test('should create a new todo with valid text', () => {
      // Arrange
      const service = new TodoService();
      const text = 'Buy groceries';

      // Act
      const todo = service.createTodo({ text });

      // Assert
      expect(todo.text).toBe(text);
      expect(todo.completed).toBe(false);
    });

    test('should throw error when text is empty', () => {
      // Arrange
      const service = new TodoService();

      // Act & Assert
      expect(() => service.createTodo({ text: '' }))
        .toThrow('Todo text cannot be empty');
    });
  });
});
```

### Test Quality

**Good Tests Are:**
- **Fast**: Run in milliseconds
- **Independent**: Can run in any order
- **Repeatable**: Same result every time
- **Self-validating**: Pass or fail clearly
- **Timely**: Written before or with the code (TDD)

**Test Coverage Focus:**
- Business logic: High coverage (80-90%)
- Edge cases and error handling
- Happy path and unhappy path
- Integration points

## Code Comments

### When to Comment

**Do Comment:**
- Complex algorithms or business logic
- Non-obvious decisions or trade-offs
- Public API documentation
- TODOs and FIXMEs with context

**Don't Comment:**
- What the code does (code should be self-explanatory)
- Obvious statements
- Commented-out code (use version control)

**Example:**
```javascript
// Good - Explains WHY, not WHAT
// Using exponential backoff to prevent overwhelming the API
// when it's under heavy load or experiencing issues
const retryWithBackoff = async (fn, maxRetries = 3) => {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await sleep(Math.pow(2, i) * 1000);
    }
  }
};

// Avoid - Explains WHAT (obvious from code)
// Loop through retries
for (let i = 0; i < maxRetries; i++) {
  // Try to call the function
  try {
    return await fn();
  }
  // ...
}
```

### JSDoc for Public APIs

```javascript
/**
 * Creates a new TODO item
 * @param {Object} options - Todo creation options
 * @param {string} options.text - The text content of the todo
 * @param {string} options.listId - ID of the list to add the todo to
 * @param {string} [options.priority='normal'] - Priority level (low, normal, high)
 * @returns {Promise<Object>} The created todo object
 * @throws {Error} If text is invalid or list doesn't exist
 */
async createTodo({ text, listId, priority = 'normal' }) {
  // ...
}
```

## Git Practices

### Commit Messages

**Format:**
```
<type>: <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style/formatting (no logic change)
- `refactor`: Code restructuring (no behavior change)
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples:**
```
feat: add ability to delete todo items

Implements delete functionality with confirmation dialog
and undo capability via snackbar.

Closes #42
```

```
fix: prevent duplicate todos when submitting rapidly

Added debouncing to form submission to prevent
race condition when user clicks submit multiple times.
```

### Branch Naming

- `feature/todo-delete` - New features
- `fix/form-validation` - Bug fixes
- `refactor/service-layer` - Code refactoring
- `docs/api-documentation` - Documentation

## Code Review Guidelines

### As an Author
- Keep PRs small and focused (< 400 lines)
- Write clear PR descriptions
- Run tests before requesting review
- Respond to feedback promptly

### As a Reviewer
- Be respectful and constructive
- Focus on the code, not the person
- Explain the "why" behind suggestions
- Approve when satisfied, request changes when needed

**Review Checklist:**
- [ ] Code follows style guidelines
- [ ] Tests are included and passing
- [ ] No obvious bugs or edge cases missed
- [ ] Documentation updated if needed
- [ ] No security vulnerabilities
- [ ] Performance considerations addressed

## Performance Considerations

### Frontend
- Memoize expensive computations with `useMemo`
- Prevent unnecessary re-renders with `React.memo`
- Lazy load components with `React.lazy`
- Optimize images and assets
- Minimize bundle size

### Backend
- Use connection pooling for databases
- Implement caching where appropriate
- Index database queries
- Use pagination for large datasets
- Monitor and profile performance

## Security Best Practices

### General
- Never commit secrets or API keys
- Use environment variables for configuration
- Validate and sanitize all user input
- Use HTTPS in production
- Keep dependencies updated

### Backend Specific
- Use parameterized queries (prevent SQL injection)
- Implement rate limiting
- Use CORS appropriately
- Sanitize error messages (don't leak internals)
- Implement proper authentication and authorization

## Documentation

### README Files
- Clear project description
- Setup instructions
- Usage examples
- Contributing guidelines

### API Documentation
- Document all endpoints
- Include request/response examples
- Document error codes and messages
- Keep up to date with code changes

## References

- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [React Best Practices](https://react.dev/learn)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Clean Code by Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
