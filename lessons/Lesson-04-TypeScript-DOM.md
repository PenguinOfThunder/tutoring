# Lesson 4: TypeScript & DOM

## Objective

DOM manipulation and event handling using TypeScript types; add interactivity to the task app with type-safe code.

---

## In-Class Materials

### Topics Covered

1. **DOM Basics**:
   - Document object and element selection (`getElementById`, `querySelector`)
   - Creating and modifying elements
   - Adding and removing classes
2. **Events**:
   - Event listeners and handlers
   - Form submission events
   - Click events for delete/edit actions
3. **TypeScript for DOM Work**:
   - Types for DOM elements (`HTMLInputElement`, `HTMLFormElement`)
   - Type assertions and casting
   - Interfaces for app data (Task model)
4. **In-Memory Task Storage**:
   - Array of task objects
   - Adding and removing tasks from memory
   - Re-rendering the list

### Key Concepts

- **Type Safety**: Using TypeScript types prevents bugs with DOM manipulation
- **Event-Driven**: Respond to user actions (form submit, button click)
- **Separation of Concerns**: Keep data (tasks array) separate from UI (DOM)

---

## Deep Dive: Key Topics

### DOM Basics: What is the DOM?

The **DOM (Document Object Model)** is a JavaScript representation of your HTML page. It's a tree structure where each HTML element is a node that you can select, modify, create, or delete.

```
Document
  └─ html
      ├─ head
      │   └─ title
      └─ body
          ├─ header
          └─ main
              └─ section
                  └─ ul#task-list
                      └─ li
```

JavaScript lets you interact with this tree to make pages interactive. When you change the DOM, the browser automatically updates what the user sees.

### Selecting Elements from the DOM

The first step in DOM manipulation is finding the elements you want to work with:

```typescript
// Select by ID (unique identifier) - returns single element or null
const form = document.getElementById('task-form');

// Select by class name - returns a collection
const buttons = document.getElementsByClassName('btn');

// Select by CSS selector - most flexible and recommended
const taskList = document.querySelector('#task-list'); // First match
const allButtons = document.querySelectorAll('.btn'); // All matches

// Always check if element exists before using it
if (taskList) {
  taskList.innerHTML = 'Updated!';
}

// Or use optional chaining (?.) for cleaner code
document.getElementById('task-list')?.addEventListener('click', handleClick);
```

**Best practice**: Use `querySelector` and `querySelectorAll` — they support any CSS selector and are the most flexible.

### Type Assertions: Telling TypeScript About DOM Elements

When you select a DOM element, TypeScript doesn't automatically know what *type* of element it is. You need to tell it using **type assertions**:

```typescript
// Without type assertion - TypeScript sees it as generic Element
const input = document.getElementById('task-input');
// input.value -> Error! Element type doesn't have a 'value' property

// With type assertion using 'as' keyword
const input = document.getElementById('task-input') as HTMLInputElement;
input.value = 'Hello'; // Now TypeScript knows value exists

// You can also use angle brackets (older syntax, less common)
const input = <HTMLInputElement>document.getElementById('task-input');
```

Common DOM element types you'll use:
- `HTMLInputElement` — Text, checkbox, radio, hidden inputs
- `HTMLTextAreaElement` — Multi-line text
- `HTMLFormElement` — Form containers
- `HTMLButtonElement` — Buttons
- `HTMLUListElement` / `HTMLOListElement` — Lists
- `HTMLElement` — Generic element (any HTML tag)

### Creating and Modifying Elements

You can dynamically create new HTML elements and add them to the page:

```typescript
// Create a new element
const listItem = document.createElement('li');

// Set text content (safe - no HTML parsing)
listItem.textContent = 'Buy milk';

// Set CSS classes
listItem.className = 'list-group-item';

// Or add classes one at a time
listItem.classList.add('active', 'highlight');
listItem.classList.remove('highlight');

// Set HTML (careful with user input - can create security issues!)
listItem.innerHTML = '<strong>Buy milk</strong>';

// Set attributes
listItem.setAttribute('data-id', '123');
listItem.id = 'task-1';

// Add the element to the page
const taskList = document.getElementById('task-list') as HTMLUListElement;
taskList.appendChild(listItem);

// Remove from the page
listItem.remove();
```

**Important**: Use `textContent` for user-provided data to prevent security attacks. Only use `innerHTML` for trusted content you control.

### Events and Event Listeners

**Events** are actions users take (clicks, form submissions, typing, etc.). You respond to events by adding **event listeners**:

```typescript
// Button click
const button = document.querySelector('button');
button?.addEventListener('click', (event: Event) => {
  console.log('Button clicked!');
});

// Form submission
const form = document.getElementById('task-form') as HTMLFormElement;
form.addEventListener('submit', (event: Event) => {
  event.preventDefault(); // Prevent page reload
  console.log('Form submitted');
});

// Input changes
const input = document.getElementById('task-input') as HTMLInputElement;
input.addEventListener('change', (event: Event) => {
  const target = event.target as HTMLInputElement;
  console.log('New value:', target.value);
});

// Keyboard input (fires repeatedly while typing)
input.addEventListener('input', (event: Event) => {
  console.log('User typing...');
});
```

Common events:
- `click` — User clicked an element
- `submit` — Form was submitted
- `change` — Input value changed and focus lost
- `input` — User typed something (fires continuously)
- `keydown` / `keyup` — Keyboard key pressed or released

### Understanding preventDefault

By default, HTML forms reload the page when submitted. In single-page apps, you prevent this:

```typescript
const form = document.getElementById('task-form') as HTMLFormElement;

form.addEventListener('submit', (event: Event) => {
  event.preventDefault(); // Don't reload the page!
  
  // Get values from form inputs
  const titleInput = document.getElementById('task-input') as HTMLInputElement;
  const descInput = document.getElementById('task-desc') as HTMLTextAreaElement;
  
  // Process the data (add task, send to server, etc.)
  addTask(titleInput.value, descInput.value);
  
  // Clear the form for next entry
  form.reset();
});
```

### TypeScript Interfaces for Type-Safe Data

**Interfaces** define the shape of your data — they're like blueprints. They make code self-documenting and prevent bugs:

```typescript
// Define what a Task should look like
interface Task {
  id: number;
  title: string;
  description: string;
  completed: boolean;
  createdAt: Date;
}

// Now you can create tasks with guaranteed structure
const newTask: Task = {
  id: 1,
  title: 'Buy milk',
  description: 'At the grocery store',
  completed: false,
  createdAt: new Date()
};

// TypeScript will error if you forget properties or use wrong types
const badTask: Task = {
  id: 'one', // Error: string instead of number
  title: 'Buy milk'
  // Error: missing description, completed, createdAt
};
```

### In-Memory Storage: Keeping Tasks in an Array

During the app's session, you store tasks in a JavaScript array. This data persists only while the page is open (not after refresh):

```typescript
let tasks: Task[] = []; // Array of Tasks
let nextId = 1; // Counter for unique IDs

// Add a task to memory
function addTask(title: string, description: string): void {
  const task: Task = {
    id: nextId++, // Increment after use
    title,
    description,
    completed: false,
    createdAt: new Date()
  };
  tasks.push(task); // Add to array
  renderTasks(); // Update the UI
}

// Remove a task from memory
function deleteTask(id: number): void {
  tasks = tasks.filter(task => task.id !== id); // Keep all except the one to delete
  renderTasks(); // Update the UI
}
```

### Separation of Concerns: Data Layer vs. UI Layer

A key principle is keeping **data** (your tasks array) separate from **UI** (the DOM):

```typescript
// Data Layer - stores information
let tasks: Task[] = [];

// UI Layer - displays information
function renderTasks(): void {
  const taskList = document.getElementById('task-list') as HTMLUListElement;
  taskList.innerHTML = ''; // Clear existing items
  
  // Loop through data and create corresponding DOM elements
  tasks.forEach(task => {
    const li = document.createElement('li');
    li.textContent = task.title;
    taskList.appendChild(li);
  });
}

// Logic Layer - performs operations
function addTask(title: string, description: string): void {
  // Modify data
  tasks.push({ id: nextId++, title, description, completed: false, createdAt: new Date() });
  // Update UI to reflect data changes
  renderTasks();
}
```

**Why separate?** It makes code:
- **Testable** — Test data logic without worrying about the DOM
- **Debuggable** — Data is independent of how it's displayed
- **Maintainable** — Change how you render without touching data logic

---

## Setting Up TypeScript in Your Project

Before you start writing TypeScript, you need to configure your project to compile `.ts` files to JavaScript. Here's how:

### Step 1: Install TypeScript

Open your terminal in the project root and install TypeScript as a development dependency:

```bash
npm install --save-dev typescript
```

This installs the TypeScript compiler. The `--save-dev` flag means it's only needed for development, not in production.

### Step 2: Initialize TypeScript Configuration

Create a `tsconfig.json` file in your project root. This tells TypeScript how to compile your code:

```bash
npx tsc --init
```

This generates a `tsconfig.json` with default settings. You can customize it, but here's a minimal recommended configuration for your task app:

```json
{
  "compilerOptions": {
    "target": "ES2020",                    // Compile to modern JavaScript
    "module": "ES2020",                    // Use ES modules
    "lib": ["ES2020", "DOM"],              // Include DOM APIs
    "outDir": "./frontend",                // Where compiled .js goes
    "rootDir": "./frontend",               // Where .ts files are
    "strict": true,                        // Enable strict type checking
    "esModuleInterop": true,               // Better CommonJS compatibility
    "skipLibCheck": true,                  // Skip type checking of declaration files
    "forceConsistentCasingInFileNames": true // Enforce consistent naming
  },
  "include": ["frontend/**/*.ts"],         // Compile all .ts files in frontend
  "exclude": ["node_modules"]              // Don't compile dependencies
}
```

### Step 3: Create Your TypeScript File

Create `frontend/app.ts` (note the `.ts` extension):

```typescript
// Your TypeScript code goes here
console.log('TypeScript is working!');
```

### Step 4: Compile TypeScript to JavaScript

Run the TypeScript compiler:

```bash
npx tsc
```

This compiles all `.ts` files to `.js` files in the output directory. You should see `frontend/app.js` created.

**Note**: You'll need to run `npx tsc` after every change to your `.ts` files. For development, you can use watch mode:

```bash
npx tsc --watch
```

This automatically recompiles when you save a `.ts` file.

### Step 5: Link the Compiled JavaScript in HTML

Update your `frontend/index.html` to reference the compiled JavaScript file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Task Manager</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <!-- Your HTML here -->
  
  <!-- Link the compiled JavaScript at the bottom -->
  <script src="app.js"></script>
</body>
</html>
```

**Important**: The browser runs `.js` files, not `.ts` files. TypeScript compiles to JavaScript behind the scenes.

### Development Workflow

Here's how you'll work with TypeScript:

```
1. Edit TypeScript file (.ts)
   ↓
2. Save the file
   ↓
3. TypeScript compiler automatically recompiles (if using --watch)
   ↓
4. Refresh your browser to see changes
   ↓
5. Debug in DevTools (Browser uses .js file)
```

**Tip**: Use Live Server (from Lesson 2) to auto-refresh your browser when files change. Combined with `tsc --watch`, this creates a smooth development experience.

### Project Structure After Setup

After setting up TypeScript, your project structure looks like:

```
frontend/
  ├── index.html          (HTML template)
  ├── styles.css          (CSS styles)
  ├── app.ts              (TypeScript source - you edit this)
  ├── app.js              (Compiled JavaScript - auto-generated)
  └── tsconfig.json       (TypeScript configuration)
```

---

## In-Class Exercise (15–20 minutes)

**Task: Add TypeScript to handle task creation and deletion**

1. Create `frontend/app.ts` with typed task handling:

   ```typescript
   interface Task {
     id: number;
     title: string;
     description: string;
     completed: boolean;
     createdAt: Date;
   }
   
   let tasks: Task[] = [];
   let nextId = 1;
   
   function renderTasks(): void {
     const taskList = document.getElementById('task-list') as HTMLUListElement;
     taskList.innerHTML = '';
     
     tasks.forEach(task => {
       const li = document.createElement('li');
       li.className = 'list-group-item d-flex justify-content-between align-items-center';
       li.innerHTML = `
         <div>
           <strong>${task.title}</strong>
           <p class="mb-0 text-muted">${task.description}</p>
         </div>
         <button class="btn btn-sm btn-danger" data-id="${task.id}">Delete</button>
       `;
       taskList.appendChild(li);
     });
   }
   
   function addTask(title: string, description: string): void {
     const task: Task = {
       id: nextId++,
       title,
       description,
       completed: false,
       createdAt: new Date()
     };
     tasks.push(task);
     renderTasks();
   }
   
   function deleteTask(id: number): void {
     tasks = tasks.filter(task => task.id !== id);
     renderTasks();
   }
   
   // Set up event listeners
   const form = document.getElementById('task-form') as HTMLFormElement;
   form.addEventListener('submit', (event: Event) => {
     event.preventDefault();
     
     const titleInput = document.getElementById('task-input') as HTMLInputElement;
     const descInput = document.getElementById('task-desc') as HTMLTextAreaElement;
     
     addTask(titleInput.value, descInput.value);
     
     form.reset();
   });
   
   // Event delegation for delete buttons
   document.getElementById('task-list')?.addEventListener('click', (event: Event) => {
     const target = event.target as HTMLElement;
     if (target.classList.contains('btn-danger')) {
       const id = parseInt(target.getAttribute('data-id') || '0', 10);
       deleteTask(id);
     }
   });
   
   // Initialize
   renderTasks();
   ```

2. Update `frontend/index.html` to reference the compiled script (placeholder for now):

   ```html
   <script src="app.js"></script>
   ```

3. Test the form by adding and deleting tasks
4. Commit your work:

   ```bash
   git add frontend/app.ts frontend/index.html
   git commit -m "Add TypeScript DOM manipulation for task management"
   ```

---

## Homework Assignment

**Reading & Practice**: TypeScript Basics and localStorage Persistence

**Tasks**:

1. **Read**: [MDN — DOM Manipulation](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)
2. **Read**: [TypeScript Handbook — Basic Types](https://www.typescriptlang.org/docs/handbook/2/basic-types.html)
3. **Read**: [MDN — Web Storage (localStorage)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
4. **Practice**: Add localStorage persistence to save tasks between page reloads
   - Save tasks when added or deleted
   - Load tasks on page load

**Optional Challenge**:

- Add an "Edit" button that allows inline editing of task titles

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| TypeScript Basics | [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/2/basic-types.html) | Type fundamentals |
| DOM API | [MDN — DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) | Element selection and manipulation |
| Events | [MDN — Events](https://developer.mozilla.org/en-US/docs/Web/API/Event) | Event handling |
| localStorage | [MDN — Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) | Client-side persistence |
| TypeScript DOM | [TypeScript — DOM Manipulation](https://www.typescriptlang.org/docs/handbook/dom-manipulation.html) | Typed DOM work |

---

## Key Takeaways

✓ Used TypeScript interfaces for type-safe data  
✓ Added event listeners and form handling  
✓ Rendered list items dynamically from data  
✓ Implemented add and delete functionality  

---

## Next Lesson Preview

**Lesson 5 — Debugging & DevTools**: We'll learn to debug and profile our code using browser tools.
