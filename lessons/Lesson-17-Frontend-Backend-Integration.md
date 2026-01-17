# Lesson 16: Frontend-Backend Integration

## Objective

Replace localStorage persistence with real API calls; implement a typed client for CRUD operations; handle CORS and authentication tokens.

---

## In-Class Materials

### Topics Covered

1. **API Client Implementation**:
   - Typed fetch wrapper for API calls
   - Request/response interceptors
   - Base URL and headers management
2. **CORS (Cross-Origin Resource Sharing)**:
   - Why CORS exists (browser security)
   - Configuring CORS in ASP.NET Core
   - Preflight requests (OPTIONS)
3. **Authentication Integration**:
   - Storing tokens (localStorage, sessionStorage)
   - Sending tokens in Authorization header
   - Handling 401 responses
4. **Error Handling**:
   - Network errors vs API errors
   - Displaying user-friendly messages
   - Retry logic
5. **Loading States**:
   - Disabling buttons during requests
   - Showing spinners/loading indicators
   - Optimistic updates

### Key Concepts

- **API Client**: Abstraction layer for HTTP communication
- **CORS**: Controls which origins can access your API
- **Bearer Token**: JWT sent in Authorization header
- **Error Recovery**: Graceful handling of failures

---

## In-Class Exercise (15–20 minutes)

**Task: Create a typed API client and integrate with frontend**

1. First, enable CORS in the backend `Program.cs`:

   ```csharp
   var builder = WebApplicationBuilder.CreateBuilder(args);

   // Add CORS
   builder.Services.AddCors(options =>
   {
       options.AddPolicy("AllowFrontend", policy =>
       {
           policy.WithOrigins("http://localhost:5173", "https://localhost:5173")
               .AllowAnyMethod()
               .AllowAnyHeader()
               .AllowCredentials();
       });
   });

   // ... rest of configuration ...

   var app = builder.Build();

   app.UseCors("AllowFrontend");
   app.UseHttpsRedirection();
   app.UseAuthentication();
   app.UseAuthorization();
   app.MapControllers();

   app.Run();
   ```

2. Create a frontend API client module (`frontend/modules/api.ts`):

   ```typescript
   interface Task {
     id: number;
     title: string;
     description: string;
     completed: boolean;
     createdAt: string;
   }

   interface ApiError {
     message?: string;
     errors?: Record<string, string[]>;
   }

   const API_BASE = 'https://localhost:7001/api';
   let authToken: string | null = null;

   export function setAuthToken(token: string | null): void {
     authToken = token;
   }

   async function apiCall<T>(
     endpoint: string,
     method: string = 'GET',
     body?: unknown
   ): Promise<T> {
     const headers: HeadersInit = {
       'Content-Type': 'application/json'
     };

     if (authToken) {
       headers['Authorization'] = `Bearer ${authToken}`;
     }

     const options: RequestInit = {
       method,
       headers
     };

     if (body) {
       options.body = JSON.stringify(body);
     }

     const response = await fetch(`${API_BASE}${endpoint}`, options);

     if (response.status === 401) {
       authToken = null;
       throw new Error('Unauthorized. Please login again.');
     }

     if (!response.ok) {
       const error: ApiError = await response.json();
       throw new Error(error.message || `API Error: ${response.statusText}`);
     }

     // Handle 204 No Content
     if (response.status === 204) {
       return undefined as T;
     }

     return response.json();
   }

   export async function getTasks(completed?: boolean): Promise<Task[]> {
     const query = completed !== undefined ? `?completed=${completed}` : '';
     return apiCall<Task[]>(`/tasks${query}`);
   }

   export async function getTask(id: number): Promise<Task> {
     return apiCall<Task>(`/tasks/${id}`);
   }

   export async function createTask(title: string, description: string): Promise<Task> {
     return apiCall<Task>('/tasks', 'POST', { title, description, completed: false });
   }

   export async function updateTask(id: number, task: Partial<Task>): Promise<Task> {
     return apiCall<Task>(`/tasks/${id}`, 'PUT', task);
   }

   export async function deleteTask(id: number): Promise<void> {
     return apiCall<void>(`/tasks/${id}`, 'DELETE');
   }

   export async function register(email: string, password: string): Promise<void> {
     return apiCall('/auth/register', 'POST', { email, password });
   }

   export async function login(email: string, password: string): Promise<{ token?: string }> {
     return apiCall('/auth/login', 'POST', { email, password });
   }
   ```

3. Update `frontend/modules/ui.ts` to use the API client:

   ```typescript
   import * as api from './api';

   export async function loadTasks(filter: 'all' | 'completed'): Promise<void> {
     try {
       const tasks = await api.getTasks(filter === 'completed');
       renderTaskList(tasks);
     } catch (error) {
       console.error('Failed to load tasks:', error);
       alert(`Error: ${(error as Error).message}`);
     }
   }

   function renderTaskList(tasks: api.Task[]): void {
     const taskList = document.getElementById('task-list') as HTMLUListElement;
     taskList.innerHTML = '';

     tasks.forEach(task => {
       const li = document.createElement('li');
       li.className = 'list-group-item d-flex justify-content-between align-items-center';
       const completed = task.completed ? 'text-decoration-line-through' : '';
       li.innerHTML = `
         <div>
           <input type="checkbox" class="form-check-input" ${task.completed ? 'checked' : ''} data-toggle="${task.id}">
           <strong class="${completed}">${task.title}</strong>
         </div>
         <button class="btn btn-sm btn-danger" data-delete="${task.id}">Delete</button>
       `;
       taskList.appendChild(li);
     });

     // Event listeners
     taskList.querySelectorAll('[data-toggle]').forEach(checkbox => {
       checkbox.addEventListener('change', async (e) => {
         const id = parseInt((e.target as HTMLInputElement).getAttribute('data-toggle') || '0', 10);
         const task = tasks.find(t => t.id === id);
         if (task) {
           try {
             await api.updateTask(id, { ...task, completed: !task.completed });
             await loadTasks('all');
           } catch (error) {
             alert(`Error updating task: ${(error as Error).message}`);
           }
         }
       });
     });

     taskList.querySelectorAll('[data-delete]').forEach(btn => {
       btn.addEventListener('click', async (e) => {
         const id = parseInt((e.target as HTMLElement).getAttribute('data-delete') || '0', 10);
         try {
           await api.deleteTask(id);
           await loadTasks('all');
         } catch (error) {
           alert(`Error deleting task: ${(error as Error).message}`);
         }
       });
     });
   }

   export async function addTaskFromForm(title: string, description: string): Promise<void> {
     try {
       await api.createTask(title, description);
       await loadTasks('all');
     } catch (error) {
       alert(`Error creating task: ${(error as Error).message}`);
     }
   }
   ```

4. Update `frontend/app.ts` to use the new API:

   ```typescript
   import { loadTasks, addTaskFromForm } from './modules/ui';
   import { setAuthToken } from './modules/api';

   let currentView: 'all' | 'completed' = 'all';

   async function navigate(view: 'all' | 'completed'): Promise<void> {
     currentView = view;
     window.location.hash = `#${view}`;
     await loadTasks(view);
   }

   function handleHashChange(): void {
     const hash = window.location.hash.slice(1) as 'all' | 'completed';
     if (['all', 'completed'].includes(hash)) {
       navigate(hash);
     }
   }

   // Set up form
   const form = document.getElementById('task-form') as HTMLFormElement;
   form.addEventListener('submit', async (e) => {
     e.preventDefault();
     const title = (document.getElementById('task-input') as HTMLInputElement).value;
     const desc = (document.getElementById('task-desc') as HTMLTextAreaElement).value;
     await addTaskFromForm(title, desc);
     form.reset();
   });

   // Set up navigation
   document.querySelectorAll('a[href^="#"]').forEach(link => {
     link.addEventListener('click', (e) => {
       e.preventDefault();
       const view = (link as HTMLAnchorElement).href.split('#')[1] as 'all' | 'completed';
       navigate(view);
     });
   });

   window.addEventListener('hashchange', handleHashChange);

   // Initialize
   handleHashChange();
   ```

5. Test the integration:
   - Start both frontend and backend
   - Add, update, and delete tasks through the UI
   - Verify data persists in the database

6. Commit your work:

   ```bash
   git add frontend/ backend/
   git commit -m "Integrate frontend with backend API using typed client"
   ```

---

## Homework Assignment

**Reading & Practice**: API Client Patterns

**Tasks**:

1. **Read**: [MDN — Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
2. **Read**: [CORS Explained](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
3. **Read**: [Authentication with Tokens](https://tools.ietf.org/html/rfc6750)
4. **Practice**: Add error handling for network timeouts
5. **Practice**: Test CORS with Postman (from a different origin)

**Optional Challenge**:

- Add a login form and store the auth token
- Implement token refresh mechanism
- Add a loading spinner during API calls

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Fetch API | [MDN — Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) | HTTP requests |
| CORS | [MDN — CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) | Cross-origin requests |
| Bearer Tokens | [RFC 6750](https://tools.ietf.org/html/rfc6750) | Token format |
| ASP.NET CORS | [CORS in ASP.NET Core](https://learn.microsoft.com/aspnet/core/security/cors) | Configuration |

---

## Key Takeaways

✓ Created a typed API client with error handling  
✓ Configured CORS in the backend  
✓ Sent authentication tokens in requests  
✓ Handled API errors gracefully  
✓ Integrated frontend with real backend API  

---

## Next Lesson Preview

**Lesson 17 — Validation & Error Handling**: We'll add comprehensive validation client- and server-side with friendly error messages.
