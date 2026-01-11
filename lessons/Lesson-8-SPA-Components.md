# Lesson 8: Intro to Component Thinking & SPA Concepts

## Objective

Understand componentization and single-page app (SPA) patterns; implement view switching using React + TypeScript for a modern component-based architecture.

---

## In-Class Materials

### Topics Covered

1. **Component-Based Architecture**:
   - What is a component (self-contained UI + logic)
   - Component composition (combining smaller components)
   - Props and state (data flow)
2. **Single-Page Applications (SPAs)**:
   - How SPAs differ from traditional multi-page apps
   - Client-side routing (switching views without reload)
   - URL management with `window.history`
3. **React Fundamentals**:
   - React components as TypeScript functions
   - JSX syntax for writing UI
   - Props for passing data to components
   - Hooks (`useState`, `useEffect`) for state and side effects
4. **Routing Basics**:
   - Hash-based routing (`#all`, `#completed`)
   - Updating component state when route changes
   - Browser back/forward support
5. **Component State Management**:
   - Local component state with `useState`
   - Passing data between components via props
   - Event handlers for user interactions

### Key Concepts

- **Encapsulation**: Components hide internal details and expose interfaces
- **Reusability**: Components can be used in multiple places
- **Routing**: Navigate between views while staying on one HTML page
- **Hooks**: Functions to add state and side effects to function components
- **JSX**: Syntax extension for writing React components

---

## In-Class Exercise (15–20 minutes)

**Task: Build a task app using React components with routing**

### Part 1: Set up React with Vite

React is already installed if you've been following the course. Update your `frontend/index.html` to use React:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Task Manager</title>
</head>
<body>
  <div id="root"></div>
  <script type="module" src="app.tsx"></script>
</body>
</html>
```

### Part 2: Create React Components

**`frontend/types.ts`** — Type definitions:

```typescript
export interface Task {
  id: number;
  title: string;
  description: string;
  completed: boolean;
  createdAt: Date;
}

export type Filter = 'all' | 'completed';
```

**`frontend/components/TaskList.tsx`** — Task list component:

```typescript
import React from 'react';
import { Task, Filter } from '../types';

interface TaskListProps {
  tasks: Task[];
  filter: Filter;
  onToggle: (id: number) => void;
  onDelete: (id: number) => void;
}

export const TaskList: React.FC<TaskListProps> = ({ tasks, filter, onToggle, onDelete }) => {
  const filteredTasks = filter === 'completed' 
    ? tasks.filter(t => t.completed) 
    : tasks;

  return (
    <section>
      <h2>Your Tasks</h2>
      <ul className="list-group">
        {filteredTasks.length === 0 ? (
          <li className="list-group-item">No tasks yet</li>
        ) : (
          filteredTasks.map(task => (
            <li key={task.id} className="list-group-item d-flex justify-content-between align-items-center">
              <div>
                <input
                  type="checkbox"
                  className="form-check-input"
                  checked={task.completed}
                  onChange={() => onToggle(task.id)}
                />
                <strong className={task.completed ? 'text-decoration-line-through' : ''}>
                  {task.title}
                </strong>
              </div>
              <button
                className="btn btn-sm btn-danger"
                onClick={() => onDelete(task.id)}
              >
                Delete
              </button>
            </li>
          ))
        )}
      </ul>
    </section>
  );
};
```

**`frontend/components/TaskForm.tsx`** — Form component:

```typescript
import React, { useState } from 'react';

interface TaskFormProps {
  onAddTask: (title: string, description: string) => void;
}

export const TaskForm: React.FC<TaskFormProps> = ({ onAddTask }) => {
  const [title, setTitle] = useState('');
  const [description, setDescription] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (title.trim()) {
      onAddTask(title, description);
      setTitle('');
      setDescription('');
    }
  };

  return (
    <section className="mb-5">
      <h2>Add a Task</h2>
      <form onSubmit={handleSubmit}>
        <div className="mb-3">
          <label htmlFor="task-input" className="form-label">
            Task Title
          </label>
          <input
            type="text"
            className="form-control"
            id="task-input"
            value={title}
            onChange={(e) => setTitle(e.target.value)}
            required
          />
        </div>
        <div className="mb-3">
          <label htmlFor="task-desc" className="form-label">
            Description (optional)
          </label>
          <textarea
            className="form-control"
            id="task-desc"
            value={description}
            onChange={(e) => setDescription(e.target.value)}
            rows={3}
          />
        </div>
        <button type="submit" className="btn btn-primary">
          Add Task
        </button>
      </form>
    </section>
  );
};
```

**`frontend/components/Navigation.tsx`** — Navigation component:

```typescript
import React from 'react';
import { Filter } from '../types';

interface NavigationProps {
  currentFilter: Filter;
  onFilterChange: (filter: Filter) => void;
}

export const Navigation: React.FC<NavigationProps> = ({ currentFilter, onFilterChange }) => {
  return (
    <nav className="navbar navbar-expand-lg navbar-dark bg-dark mb-4">
      <div className="container-fluid">
        <span className="navbar-brand">📝 Task Manager</span>
        <div className="navbar-nav ms-auto">
          <button
            className={`nav-link btn btn-link ${currentFilter === 'all' ? 'active' : ''}`}
            onClick={() => onFilterChange('all')}
          >
            All
          </button>
          <button
            className={`nav-link btn btn-link ${currentFilter === 'completed' ? 'active' : ''}`}
            onClick={() => onFilterChange('completed')}
          >
            Completed
          </button>
        </div>
      </div>
    </nav>
  );
};
```

**`frontend/app.tsx`** — Main app component with state management:

```typescript
import React, { useState, useEffect } from 'react';
import ReactDOM from 'react-dom/client';
import { Task, Filter } from './types';
import { TaskForm } from './components/TaskForm';
import { TaskList } from './components/TaskList';
import { Navigation } from './components/Navigation';
import 'bootstrap/dist/css/bootstrap.css';

const App: React.FC = () => {
  const [tasks, setTasks] = useState<Task[]>([]);
  const [filter, setFilter] = useState<Filter>('all');
  const [nextId, setNextId] = useState(1);

  // Load tasks from localStorage on mount
  useEffect(() => {
    const saved = localStorage.getItem('tasks');
    if (saved) {
      const parsedTasks = JSON.parse(saved).map((t: any) => ({
        ...t,
        createdAt: new Date(t.createdAt)
      }));
      setTasks(parsedTasks);
      setNextId(Math.max(...parsedTasks.map((t: Task) => t.id)) + 1);
    }

    // Handle URL hash for routing
    const handleHashChange = () => {
      const hash = window.location.hash.slice(1) as Filter;
      if (['all', 'completed'].includes(hash)) {
        setFilter(hash);
      }
    };

    handleHashChange();
    window.addEventListener('hashchange', handleHashChange);
    return () => window.removeEventListener('hashchange', handleHashChange);
  }, []);

  // Save tasks to localStorage whenever they change
  useEffect(() => {
    localStorage.setItem('tasks', JSON.stringify(tasks));
  }, [tasks]);

  const addTask = (title: string, description: string) => {
    const newTask: Task = {
      id: nextId,
      title,
      description,
      completed: false,
      createdAt: new Date()
    };
    setTasks([...tasks, newTask]);
    setNextId(nextId + 1);
  };

  const deleteTask = (id: number) => {
    setTasks(tasks.filter(t => t.id !== id));
  };

  const toggleTask = (id: number) => {
    setTasks(tasks.map(t =>
      t.id === id ? { ...t, completed: !t.completed } : t
    ));
  };

  const handleFilterChange = (newFilter: Filter) => {
    setFilter(newFilter);
    window.location.hash = `#${newFilter}`;
  };

  return (
    <div>
      <Navigation currentFilter={filter} onFilterChange={handleFilterChange} />
      <div className="container mt-5">
        <div className="row">
          <div className="col-md-8 offset-md-2">
            <TaskForm onAddTask={addTask} />
            <TaskList
              tasks={tasks}
              filter={filter}
              onToggle={toggleTask}
              onDelete={deleteTask}
            />
          </div>
        </div>
      </div>
    </div>
  );
};

const root = ReactDOM.createRoot(document.getElementById('root')!);
root.render(<App />);
```

### Part 3: Install Dependencies

```bash
cd frontend
npm install react react-dom
npm install --save-dev @types/react @types/react-dom
npm run dev
```

### Part 4: Test the App

- Open http://localhost:5173
- Add tasks using the form
- Click "All" and "Completed" to filter
- Toggle completion status
- Delete tasks
- Refresh the page (tasks persist in localStorage)

### Part 5: Commit Your Work

```bash
git add frontend/app.tsx frontend/components/ frontend/types.ts
git commit -m "Refactor to React components with routing and state management"
```

---

## Homework Assignment

**Reading & Practice**: React Hooks and Component Composition

**Tasks**:

1. **Read**: [React — Hooks](https://react.dev/reference/react/hooks) (focus on `useState` and `useEffect`)
2. **Read**: [React — Components & Props](https://react.dev/learn/describing-the-ui#components)
3. **Practice**: Add a "tags" feature to the TaskList component
   - Modify the `Task` interface to include `tags: string[]`
   - Add a tags input field to TaskForm (comma-separated, split on blur)
   - Display tags as Bootstrap badges under each task
   - Create a tag filter using a new `useState` hook
4. **Practice**: Implement task editing
   - Add an Edit button to each task item
   - Use `useState` to toggle edit mode
   - Show a form when editing, display when not editing
5. **Stretch Challenge**: Add a "due date" field to tasks
   - Add date picker input to the form
   - Display due dates formatted nicely (use `toLocaleDateString()`)
   - Sort tasks by due date (soonest first)

**Reflection Questions**:
- How does React's declarative approach differ from the vanilla DOM manipulation we did before?
- When would you lift state up from a child component to the parent?
- How do you prevent unnecessary re-renders in React components?

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| React Components | [React Docs — Components](https://react.dev/learn/describing-the-ui#components) | Function components and JSX |
| React Hooks | [React Docs — Hooks API](https://react.dev/reference/react/hooks) | useState, useEffect, custom hooks |
| React State | [React Docs — State](https://react.dev/learn/state-a-components-memory) | Managing state with hooks |
| Client-side Routing | [MDN — History API](https://developer.mozilla.org/en-US/docs/Web/API/History_API) | Browser navigation |
| TypeScript with React | [React Docs — TypeScript](https://react.dev/learn/typescript) | Typing props and state |
| SPA Concepts | [MDN — SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA) | What is an SPA |

---

## Key Takeaways

✓ Built React functional components with TypeScript  
✓ Used hooks (`useState`, `useEffect`) for state and side effects  
✓ Managed client-side routing with hash navigation  
✓ Passed data between components via props  
✓ Persisted application state to localStorage  
✓ Leveraged React's declarative, component-based approach

---

## Next Lesson Preview

**Lesson 9 — ASP.NET Core Intro**: We'll shift to the backend and create a REST API using ASP.NET Core.
