# Lesson 5: TypeScript Advanced & Type-Safe DOM

## Objective

Set up TypeScript in the project; use TypeScript types and interfaces to write type-safe code for DOM manipulation and data management.

---

## In-Class Materials

### Topics Covered

1. **TypeScript Setup**:
   - Installing TypeScript and configuring `tsconfig.json`
   - Compiling TypeScript to JavaScript
   - Watch mode for development
2. **TypeScript for DOM Work**:
   - Types for DOM elements (`HTMLInputElement`, `HTMLFormElement`)
   - Type assertions and casting
   - Interfaces for app data (Task model)
3. **Interfaces and Type Safety**:
   - Defining custom interfaces
   - Ensuring data consistency with types
   - Preventing type-related bugs
4. **In-Memory Task Storage**:
   - Array of task objects with type safety
   - Adding and removing tasks from memory
   - Re-rendering the list

### Key Concepts

- **Type Safety**: Using TypeScript types prevents bugs with DOM manipulation
- **Compilation**: TypeScript compiles to JavaScript for browser execution
- **Interfaces**: Define the shape of data to ensure consistency
- **Separation of Concerns**: Keep data (tasks array) separate from UI (DOM)

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

## Deep Dive: TypeScript Concepts

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

### Interfaces: Defining Data Shapes

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

During the app's session, you store tasks in a TypeScript array. This data persists only while the page is open (not after refresh):

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

## In-Class Exercise (15–20 minutes)

**Task: Set up TypeScript and add type-safe task management**

1. **Initialize TypeScript**:
   ```bash
   npm install --save-dev typescript
   npx tsc --init
   ```

2. Create `frontend/app.ts` with typed task handling:

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

3. Compile TypeScript and verify the `.js` file is created:
   ```bash
   npx tsc
   ```

4. Make sure `frontend/index.html` links to `app.js`

5. Test the form by adding and deleting tasks

6. Commit your work:

   ```bash
   git add frontend/app.ts tsconfig.json frontend/index.html
   git commit -m "Add TypeScript setup and type-safe task management"
   ```

---

## Homework Assignment

**Reading & Practice**: TypeScript Advanced Types and localStorage Persistence

**Tasks**:

1. **Read**: [TypeScript Handbook — Basic Types](https://www.typescriptlang.org/docs/handbook/2/basic-types.html)
2. **Read**: [TypeScript Handbook — Interfaces](https://www.typescriptlang.org/docs/handbook/2/objects.html)
3. **Read**: [MDN — Web Storage (localStorage)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
4. **Practice**: Add localStorage persistence to save tasks between page reloads
   - Save tasks when added or deleted
   - Load tasks on page load

**Optional Challenge**:

- Add an "Edit" button that allows inline editing of task titles
- Use optional properties in your Task interface (e.g., `priority?: string`)

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| TypeScript Basics | [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/2/basic-types.html) | Type fundamentals |
| TypeScript Interfaces | [TypeScript Handbook — Interfaces](https://www.typescriptlang.org/docs/handbook/2/objects.html) | Defining object shapes |
| DOM Types | [TypeScript — DOM Manipulation](https://www.typescriptlang.org/docs/handbook/dom-manipulation.html) | Typed DOM work |
| localStorage | [MDN — Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) | Client-side persistence |
| tsconfig.json | [TypeScript — tsconfig.json](https://www.typescriptlang.org/tsconfig) | Configuration reference |

---

## Key Takeaways

✓ Set up TypeScript with tsconfig.json  
✓ Compiled TypeScript to JavaScript  
✓ Used TypeScript interfaces for type-safe data  
✓ Added event listeners with type assertions  
✓ Implemented type-safe task management  

---

## Next Lesson Preview

**Lesson 6 — Debugging & DevTools**: We'll learn to debug and profile our code using browser tools.
