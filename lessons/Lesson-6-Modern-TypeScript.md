# Lesson 6: Modern TypeScript

## Objective

Use modern TypeScript features (interfaces, async/await, fetch, type inference) to build a more robust frontend; fetch data from a public API.

---

## In-Class Materials

### Topics Covered

1. **TypeScript Advanced Types**:
   - `interface` vs `type`
   - Union types and type guards
   - Generics and constraints
   - `readonly` and `partial` utility types
2. **Modern JavaScript/TypeScript**:
   - `let` and `const` (block scoping)
   - Arrow functions and `this` binding
   - Template literals
   - Destructuring (objects and arrays)
3. **Asynchronous Programming**:
   - Promises and `.then()`
   - `async`/`await` syntax
   - Error handling with `try/catch`
4. **Fetch API**:
   - Making HTTP GET requests
   - Parsing JSON responses
   - Type-safe fetch with typed responses
5. **Integrating External Data**:
   - Fetching from a public API (e.g., JSONPlaceholder)
   - Mapping API data to local types
   - Handling errors gracefully

### Key Concepts

- **Type Safety**: Use TypeScript to catch errors at compile time
- **Async/Await**: Write asynchronous code that reads like synchronous
- **API Integration**: Fetch and display data from external sources
- **Error Handling**: Gracefully handle network and parsing errors

---

## In-Class Exercise (15–20 minutes)

**Task: Fetch sample tasks from a public API and display them**

1. Update `frontend/app.ts` to use async/await and fetch:

   ```typescript
   interface Task {
     id: number;
     title: string;
     description: string;
     completed: boolean;
     createdAt: Date;
   }
   
   interface ApiTask {
     id: number;
     title: string;
     completed: boolean;
   }
   
   let tasks: Task[] = [];
   let nextId = 1;
   
   // Fetch sample tasks from JSONPlaceholder API
   async function loadSampleTasks(): Promise<void> {
     try {
       const response = await fetch('https://jsonplaceholder.typicode.com/todos?_limit=5');
       if (!response.ok) {
         throw new Error(`HTTP error! status: ${response.status}`);
       }
       const apiTasks: ApiTask[] = await response.json();
       
       tasks = apiTasks.map((apiTask: ApiTask) => ({
         id: apiTask.id,
         title: apiTask.title,
         description: `(from API)`,
         completed: apiTask.completed,
         createdAt: new Date()
       }));
       
       nextId = Math.max(...tasks.map(t => t.id)) + 1;
       renderTasks();
     } catch (error) {
       console.error('Failed to load tasks:', error);
       alert('Could not load sample tasks. Using empty list.');
     }
   }
   
   function renderTasks(): void {
     const taskList = document.getElementById('task-list') as HTMLUListElement;
     taskList.innerHTML = '';
     
     tasks.forEach(task => {
       const li = document.createElement('li');
       li.className = 'list-group-item d-flex justify-content-between align-items-center';
       const completed = task.completed ? 'text-decoration-line-through text-muted' : '';
       li.innerHTML = `
         <div>
           <strong class="${completed}">${task.title}</strong>
           <p class="mb-0 text-muted small">${task.description}</p>
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
   
   // Event listeners
   const form = document.getElementById('task-form') as HTMLFormElement;
   form.addEventListener('submit', (event: Event) => {
     event.preventDefault();
     const titleInput = document.getElementById('task-input') as HTMLInputElement;
     const descInput = document.getElementById('task-desc') as HTMLTextAreaElement;
     addTask(titleInput.value, descInput.value);
     form.reset();
   });
   
   document.getElementById('task-list')?.addEventListener('click', (event: Event) => {
     const target = event.target as HTMLElement;
     if (target.classList.contains('btn-danger')) {
       const id = parseInt(target.getAttribute('data-id') || '0', 10);
       deleteTask(id);
     }
   });
   
   // Initialize by loading sample tasks
   loadSampleTasks();
   ```

2. Test the app and observe tasks loaded from the API
3. Commit your work:

   ```bash
   git add frontend/app.ts
   git commit -m "Add fetch API integration and async/await"
   ```

---

## Homework Assignment

**Reading & Practice**: TypeScript Fundamentals and API Integration

**Tasks**:

1. **Read**: [TypeScript Handbook — Interfaces](https://www.typescriptlang.org/docs/handbook/2/objects.html)
2. **Read**: [TypeScript Handbook — Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
3. **Read**: [MDN — Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
4. **Read**: [MDN — async/await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Promises#async_and_await)
5. **Practice**: Create a typed generic function to fetch data from any API endpoint

**Optional Challenge**:

- Fetch data from a different public API (e.g., Star Wars API, Pokémon API) and map it to your Task type

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| TypeScript Handbook | [TypeScript Docs](https://www.typescriptlang.org/docs/) | Official reference |
| Interfaces | [TypeScript — Interfaces](https://www.typescriptlang.org/docs/handbook/2/objects.html) | Object type definitions |
| Generics | [TypeScript — Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html) | Reusable types |
| Fetch API | [MDN — Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) | Making HTTP requests |
| async/await | [MDN — async/await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Promises#async_and_await) | Async syntax |
| Error Handling | [MDN — Error Handling](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling#exception_handling_statements) | try/catch patterns |

---

## Key Takeaways

✓ Used async/await for clean asynchronous code  
✓ Fetched and parsed JSON from an API  
✓ Created typed interfaces for API responses  
✓ Handled errors gracefully with try/catch  
✓ Integrated external data into the app  

---

## Next Lesson Preview

**Lesson 7 — Package Management & Vite**: We'll set up npm and Vite to manage dependencies and compile TypeScript automatically.
