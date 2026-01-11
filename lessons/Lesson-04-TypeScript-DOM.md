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

1. **Read**: [TypeScript Handbook — Basic Types](https://www.typescriptlang.org/docs/handbook/2/basic-types.html)
2. **Read**: [MDN — Web Storage (localStorage)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
3. **Practice**: Add localStorage persistence to save tasks between page reloads
   - Save tasks when added or deleted
   - Load tasks on page load
4. **Read**: [MDN — DOM Manipulation](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)

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
