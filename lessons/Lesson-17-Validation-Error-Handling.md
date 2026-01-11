# Lesson 17: Validation & Error Handling

## Objective

Validate input client- and server-side; map errors to friendly UI messages; harden endpoints against malformed input.

---

## In-Class Materials

### Topics Covered

1. **Client-Side Validation**:
   - HTML5 form validation (required, type, pattern)
   - JavaScript validation before submission
   - User feedback (error messages, styling)
2. **Server-Side Validation**:
   - Model validation attributes (Data Annotations)
   - Custom validators
   - Throwing validation exceptions
3. **Error Response Format**:
   - Consistent error structures
   - Problem Details (RFC 7807)
   - Field-specific error messages
4. **Global Error Handling**:
   - Exception middleware
   - Catching unhandled exceptions
   - Logging errors
5. **User-Friendly Messages**:
   - Mapping technical errors to user messages
   - Displaying validation errors in the UI
   - Accessibility (ARIA attributes)

### Key Concepts

- **Defense in Depth**: Validate at every layer
- **Fail Secure**: Reject invalid input safely
- **Clear Feedback**: Tell users what went wrong and how to fix it
- **Logging**: Track errors for debugging and monitoring

---

## In-Class Exercise (15–20 minutes)

**Task: Add client and server validation with friendly error messages**

1. Add client-side validation to `frontend/index.html`:

   ```html
   <section class="mb-5">
     <h2>Add a Task</h2>
     <form id="task-form">
       <div class="mb-3">
         <label for="task-input" class="form-label">Task Title *</label>
         <input 
           type="text" 
           class="form-control" 
           id="task-input" 
           name="title" 
           required 
           minlength="1" 
           maxlength="200"
           placeholder="What needs to be done?"
         >
         <div class="invalid-feedback" id="title-error" style="display: none;">
           Title is required and must be between 1-200 characters.
         </div>
       </div>
       
       <div class="mb-3">
         <label for="task-desc" class="form-label">Description (optional)</label>
         <textarea 
           class="form-control" 
           id="task-desc" 
           name="description" 
           rows="3"
           maxlength="1000"
           placeholder="Additional details..."
         ></textarea>
         <small class="form-text text-muted">Max 1000 characters</small>
       </div>
       
       <div id="form-errors" class="alert alert-danger" style="display: none;"></div>
       
       <button type="submit" class="btn btn-primary" id="submit-btn">Add Task</button>
     </form>
   </section>
   ```

2. Create validation utilities (`frontend/modules/validation.ts`):

   ```typescript
   export interface ValidationError {
     field: string;
     message: string;
   }

   export function validateTask(title: string, description: string): ValidationError[] {
     const errors: ValidationError[] = [];

     if (!title || title.trim().length === 0) {
       errors.push({ field: 'title', message: 'Title is required' });
     } else if (title.length > 200) {
       errors.push({ field: 'title', message: 'Title must be 200 characters or less' });
     }

     if (description.length > 1000) {
       errors.push({ field: 'description', message: 'Description must be 1000 characters or less' });
     }

     return errors;
   }

   export function displayValidationErrors(errors: ValidationError[]): void {
     const formErrorDiv = document.getElementById('form-errors') as HTMLDivElement;
     
     if (errors.length === 0) {
       formErrorDiv.style.display = 'none';
       return;
     }

     formErrorDiv.innerHTML = '<strong>Please fix the following errors:</strong>' +
       '<ul>' +
       errors.map(e => `<li>${e.message}</li>`).join('') +
       '</ul>';
     formErrorDiv.style.display = 'block';
   }
   ```

3. Update form submission in `frontend/app.ts`:

   ```typescript
   form.addEventListener('submit', async (e) => {
     e.preventDefault();
     
     const title = (document.getElementById('task-input') as HTMLInputElement).value;
     const desc = (document.getElementById('task-desc') as HTMLTextAreaElement).value;
     
     // Client-side validation
     const errors = validateTask(title, desc);
     if (errors.length > 0) {
       displayValidationErrors(errors);
       return;
     }
     
     // Clear errors and submit
     displayValidationErrors([]);
     const submitBtn = document.getElementById('submit-btn') as HTMLButtonElement;
     submitBtn.disabled = true;
     
     try {
       await addTaskFromForm(title, desc);
       form.reset();
     } catch (error) {
       displayValidationErrors([{
         field: 'form',
         message: (error as Error).message
       }]);
     } finally {
       submitBtn.disabled = false;
     }
   });
   ```

4. Add server-side error handling middleware to `Program.cs`:

   ```csharp
   // Add error handling middleware
   app.UseExceptionHandler(errorApp =>
   {
       errorApp.Run(async context =>
       {
           context.Response.StatusCode = StatusCodes.Status500InternalServerError;
           context.Response.ContentType = "application/json";

           var error = context.Features.Get<IExceptionHandlerFeature>();
           if (error != null)
           {
               var logger = context.RequestServices.GetRequiredService<ILogger<Program>>();
               logger.LogError(error.Error, "Unhandled exception");

               await context.Response.WriteAsJsonAsync(new
               {
                   message = "An error occurred while processing your request.",
                   detail = error.Error.Message
               });
           }
       });
   });
   ```

5. Update Task model with more validation (`Models/Task.cs`):

   ```csharp
   using System.ComponentModel.DataAnnotations;

   namespace TaskApi.Models;

   public class Task
   {
       public int Id { get; set; }
       
       [Required(ErrorMessage = "Title is required")]
       [StringLength(200, MinimumLength = 1, 
           ErrorMessage = "Title must be between 1 and 200 characters")]
       public string Title { get; set; } = string.Empty;
       
       [StringLength(1000, ErrorMessage = "Description must be 1000 characters or less")]
       public string Description { get; set; } = string.Empty;
       
       public bool Completed { get; set; }
       public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
       public string? UserId { get; set; }
   }
   ```

6. Update API client to handle validation errors (`frontend/modules/api.ts`):

   ```typescript
   async function apiCall<T>(
     endpoint: string,
     method: string = 'GET',
     body?: unknown
   ): Promise<T> {
     // ... existing code ...

     if (!response.ok) {
       const error = await response.json();
       
       if (response.status === 400 && error.errors) {
         // ModelState errors from ASP.NET
         const messages = Object.entries(error.errors)
           .flatMap(([field, msgs]: [string, any]) => 
             Array.isArray(msgs) ? msgs : [msgs]
           )
           .join('; ');
         throw new Error(messages || 'Validation failed');
       }
       
       throw new Error(error.message || `API Error: ${response.statusText}`);
     }

     // ... rest of code ...
   }
   ```

7. Test validation:
   - Try submitting an empty form (client-side error)
   - Try submitting with invalid data (server-side validation)
   - Check error messages are displayed correctly

8. Commit your work:

   ```bash
   git add frontend/ backend/
   git commit -m "Add comprehensive client and server-side validation"
   ```

---

## Homework Assignment

**Reading & Practice**: Error Handling and Validation

**Tasks**:

1. **Read**: [ASP.NET Core — Model Validation](https://learn.microsoft.com/aspnet/core/mvc/models/validation)
2. **Read**: [RFC 7807 — Problem Details](https://tools.ietf.org/html/rfc7807)
3. **Read**: [OWASP — Input Validation](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)
4. **Practice**: Add custom validation attribute for a business rule
5. **Practice**: Test all validation scenarios

**Optional Challenge**:

- Implement a custom validation attribute (e.g., NoDuplicateTitles)
- Add field-level error styling in CSS

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Model Validation | [ASP.NET Core Docs](https://learn.microsoft.com/aspnet/core/mvc/models/validation) | Built-in validators |
| Problem Details | [RFC 7807](https://tools.ietf.org/html/rfc7807) | Standard error format |
| Input Validation | [OWASP Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html) | Security best practices |
| FluentValidation | [FluentValidation](https://fluentvalidation.net/) | Alternative validation library |

---

## Key Takeaways

✓ Added HTML5 client-side validation  
✓ Implemented server-side model validation  
✓ Created consistent error response format  
✓ Displayed validation errors in the UI  
✓ Tested validation with invalid inputs  

---

## Next Lesson Preview

**Lesson 18 — Git Workflow & Collaboration**: We'll practice branching, pull requests, and merge conflicts.
