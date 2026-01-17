# Lesson 4: DOM Manipulation & Events

## Objective

Learn DOM manipulation and event handling fundamentals using vanilla JavaScript; add interactivity to the task app by responding to user actions.

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
3. **Best Practices**:
   - Using `preventDefault()` to control form behavior
   - Event delegation for efficient handling
   - Organizing code with clear separation of data and UI

### Key Concepts

- **DOM as API**: The Document Object Model provides ways to interact with HTML
- **Event-Driven**: Respond to user actions (form submit, button click)
- **Interactivity**: JavaScript makes static HTML pages interactive

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

```javascript
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

### Creating and Modifying Elements

You can dynamically create new HTML elements and add them to the page:

```javascript
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
const taskList = document.getElementById('task-list');
taskList.appendChild(listItem);

// Remove from the page
listItem.remove();
```

**Important**: Use `textContent` for user-provided data to prevent security attacks. Only use `innerHTML` for trusted content you control.

### Events and Event Listeners

**Events** are actions users take (clicks, form submissions, typing, etc.). You respond to events by adding **event listeners**:

```javascript
// Button click
const button = document.querySelector('button');
button?.addEventListener('click', (event) => {
  console.log('Button clicked!');
});

// Form submission
const form = document.getElementById('task-form');
form.addEventListener('submit', (event) => {
  event.preventDefault(); // Prevent page reload
  console.log('Form submitted');
});

// Input changes
const input = document.getElementById('task-input');
input.addEventListener('change', (event) => {
  const target = event.target;
  console.log('New value:', target.value);
});

// Keyboard input (fires repeatedly while typing)
input.addEventListener('input', (event) => {
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

```javascript
const form = document.getElementById('task-form');

form.addEventListener('submit', (event) => {
  event.preventDefault(); // Don't reload the page!
  
  // Get values from form inputs
  const titleInput = document.getElementById('task-input');
  const descInput = document.getElementById('task-desc');
  
  // Process the data (add task, send to server, etc.)
  addTask(titleInput.value, descInput.value);
  
  // Clear the form for next entry
  form.reset();
});
```

### Event Delegation

When you have many similar elements (like task items in a list), adding listeners to each is inefficient. **Event Delegation** listens to the parent and checks what was clicked:

```javascript
// Instead of adding a listener to each task...
tasks.forEach(task => {
  deleteButton.addEventListener('click', () => deleteTask(task.id));
});

// Add ONE listener to the parent container
document.getElementById('task-list').addEventListener('click', (event) => {
  const target = event.target;
  
  // Check if the clicked element is a delete button
  if (target.classList.contains('btn-danger')) {
    const taskId = target.getAttribute('data-id');
    console.log('Delete task', taskId);
  }
});
```

This is more efficient and works even for tasks added later.

### Using JSDoc Comments for Type Hints

Even without TypeScript, you can add type hints using **JSDoc comments**. This helps you remember what types to expect and provides IDE autocompletion:

```javascript
/**
 * Adds a new task to the task list
 * @param {string} title - The task title
 * @param {string} description - The task description
 * @returns {void}
 */
function addTask(title, description) {
  // Function body
}

/**
 * @typedef {Object} Task
 * @property {number} id - Unique task ID
 * @property {string} title - Task title
 * @property {string} description - Task description
 * @property {boolean} completed - Whether task is done
 * @property {Date} createdAt - When task was created
 */

/** @type {Task[]} */
let tasks = [];

/**
 * Renders all tasks to the DOM
 * @returns {void}
 */
function renderTasks() {
  const taskList = document.getElementById('task-list');
  taskList.innerHTML = '';
  
  tasks.forEach(task => {
    // Create and append list items
  });
}
```

JSDoc provides lightweight type documentation without needing TypeScript compilation. In Lesson 5, we'll move this to full TypeScript for stricter type checking.

---

## In-Class Exercise (15–20 minutes)

**Task: Add JavaScript to handle task creation and deletion with DOM manipulation**

1. Create `frontend/app.js` with DOM interaction:

   ```javascript
   // Define the shape of a Task using JSDoc
   /**
    * @typedef {Object} Task
    * @property {number} id
    * @property {string} title
    * @property {string} description
    * @property {boolean} completed
    * @property {Date} createdAt
    */

   let tasks = [];
   let nextId = 1;

   /**
    * Re-render all tasks in the DOM
    * @returns {void}
    */
   function renderTasks() {
     const taskList = document.getElementById('task-list');
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

   /**
    * Add a new task
    * @param {string} title - The task title
    * @param {string} description - The task description
    * @returns {void}
    */
   function addTask(title, description) {
     const task = {
       id: nextId++,
       title,
       description,
       completed: false,
       createdAt: new Date()
     };
     tasks.push(task);
     renderTasks();
   }

   /**
    * Delete a task by ID
    * @param {number} id - The task ID to delete
    * @returns {void}
    */
   function deleteTask(id) {
     tasks = tasks.filter(task => task.id !== id);
     renderTasks();
   }

   // Set up event listeners
   const form = document.getElementById('task-form');
   form.addEventListener('submit', (event) => {
     event.preventDefault();

     const titleInput = document.getElementById('task-input');
     const descInput = document.getElementById('task-desc');

     addTask(titleInput.value, descInput.value);

     form.reset();
   });

   // Event delegation for delete buttons
   document.getElementById('task-list').addEventListener('click', (event) => {
     const target = event.target;
     if (target.classList.contains('btn-danger')) {
       const id = parseInt(target.getAttribute('data-id'), 10);
       deleteTask(id);
     }
   });

   // Initialize
   renderTasks();
   ```

2. Update `frontend/index.html` to reference the script:

   ```html
   <script src="app.js"></script>
   ```

3. Test the form by adding and deleting tasks
4. Commit your work:

   ```bash
   git add frontend/app.js frontend/index.html
   git commit -m "Add DOM manipulation for task management"
   ```

---

## Homework Assignment

**Reading & Practice**: DOM APIs and Event Handling

**Tasks**:

1. **Read**: [MDN — DOM Manipulation](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)
2. **Read**: [MDN — Events](https://developer.mozilla.org/en-US/docs/Web/API/Event)
3. **Read**: [MDN — Web Storage (localStorage)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
4. **Practice**: Add localStorage persistence to save tasks between page reloads
   - Save tasks when added or deleted
   - Load tasks on page load

**Optional Challenge**:

- Add an "Edit" button that allows inline editing of task titles
- Experiment with JSDoc comments to document your functions

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| DOM API | [MDN — DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) | Element selection and manipulation |
| Events | [MDN — Events](https://developer.mozilla.org/en-US/docs/Web/API/Event) | Event handling |
| localStorage | [MDN — Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) | Client-side persistence |
| Event Delegation | [JavaScript.info — Event Delegation](https://javascript.info/event-delegation) | Efficient event handling |
| JSDoc | [JSDoc Reference](https://jsdoc.app/) | Type hints in JavaScript comments |
| JSDoc & TypeScript | [TypeScript JSDoc Reference](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html) | JSDoc types supported by TypeScript |

---

## Key Takeaways

✓ Selected elements from the DOM  
✓ Created and modified HTML elements  
✓ Added event listeners for user interactions  
✓ Implemented add and delete functionality  
✓ Used event delegation for efficiency  
✓ Added JSDoc comments for type documentation  

---

## Next Lesson Preview

**Lesson 5 — TypeScript Advanced**: We'll set up TypeScript, convert your JavaScript to typed code, and learn about interfaces and type assertions.
