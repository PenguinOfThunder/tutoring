# Lesson 10: Build RESTful CRUD Endpoints

## Objective

Implement POST, PUT/PATCH, and DELETE endpoints; use model binding and validation; return appropriate HTTP status codes.

---

## In-Class Materials

### Topics Covered

1. **CRUD Operations**:
   - CREATE (POST) — Add a new resource
   - READ (GET) — Retrieve resources (covered in Lesson 9)
   - UPDATE (PUT/PATCH) — Modify an existing resource
   - DELETE — Remove a resource
2. **Model Binding**:
   - Binding from URL route (`{id}`)
   - Binding from query string (`?completed=true`)
   - Binding from request body (JSON)
3. **Data Validation**:
   - `[Required]`, `[StringLength]`, `[Range]` attributes
   - Model state validation
   - Custom validation logic
4. **HTTP Status Codes**:
   - 200 OK — Successful GET
   - 201 Created — Successful POST
   - 204 No Content — Successful DELETE/PATCH
   - 400 Bad Request — Invalid input
   - 404 Not Found — Resource doesn't exist
5. **Testing CRUD Operations**:
   - Using Postman/Insomnia to test POST, PUT, DELETE
   - Verifying response bodies and status codes

### Key Concepts

- **Idempotency**: GET and DELETE should not have side effects
- **Status Codes**: Tell the client what happened
- **Validation**: Check data before processing
- **RESTful Design**: Use HTTP semantically

---

## In-Class Exercise (15–20 minutes)

**Task: Add POST, PUT, and DELETE endpoints to TasksController**

1. Update the Task model with validation (`Models/Task.cs`):

   ```csharp
   using System.ComponentModel.DataAnnotations;

   namespace TaskApi.Models;

   public class Task
   {
       public int Id { get; set; }
       
       [Required(ErrorMessage = "Title is required")]
       [StringLength(200, MinimumLength = 1)]
       public string Title { get; set; } = string.Empty;
       
       [StringLength(1000)]
       public string Description { get; set; } = string.Empty;
       
       public bool Completed { get; set; }
       public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
   }
   ```

2. Expand `Controllers/TasksController.cs` with CRUD operations:

   ```csharp
   using Microsoft.AspNetCore.Mvc;
   using TaskApi.Models;

   namespace TaskApi.Controllers;

   [ApiController]
   [Route("api/[controller]")]
   public class TasksController : ControllerBase
   {
       private static List<Task> tasks = new()
       {
           new Task { Id = 1, Title = "Learn ASP.NET Core", Description = "Complete the tutorial", Completed = false },
           new Task { Id = 2, Title = "Build a REST API", Description = "Implement CRUD endpoints", Completed = false }
       };
       private static int nextId = 3;

       // GET /api/tasks
       [HttpGet]
       public IActionResult GetTasks([FromQuery] bool? completed = null)
       {
           var result = tasks.AsEnumerable();
           if (completed.HasValue)
               result = result.Where(t => t.Completed == completed.Value);
           return Ok(result.ToList());
       }

       // GET /api/tasks/{id}
       [HttpGet("{id}")]
       public IActionResult GetTask(int id)
       {
           var task = tasks.FirstOrDefault(t => t.Id == id);
           if (task == null)
               return NotFound(new { message = "Task not found" });
           return Ok(task);
       }

       // POST /api/tasks
       [HttpPost]
       public IActionResult CreateTask([FromBody] Task newTask)
       {
           if (!ModelState.IsValid)
               return BadRequest(ModelState);

           newTask.Id = nextId++;
           newTask.CreatedAt = DateTime.UtcNow;
           tasks.Add(newTask);

           return CreatedAtAction(nameof(GetTask), new { id = newTask.Id }, newTask);
       }

       // PUT /api/tasks/{id}
       [HttpPut("{id}")]
       public IActionResult UpdateTask(int id, [FromBody] Task updatedTask)
       {
           var task = tasks.FirstOrDefault(t => t.Id == id);
           if (task == null)
               return NotFound(new { message = "Task not found" });

           if (!ModelState.IsValid)
               return BadRequest(ModelState);

           task.Title = updatedTask.Title;
           task.Description = updatedTask.Description;
           task.Completed = updatedTask.Completed;

           return Ok(task);
       }

       // DELETE /api/tasks/{id}
       [HttpDelete("{id}")]
       public IActionResult DeleteTask(int id)
       {
           var task = tasks.FirstOrDefault(t => t.Id == id);
           if (task == null)
               return NotFound(new { message = "Task not found" });

           tasks.Remove(task);
           return NoContent();
       }
   }
   ```

3. Run the API:

   ```bash
   dotnet run
   ```

4. Test each endpoint using curl or Postman:

   ```bash
   # Create a task
   curl -X POST https://localhost:7001/api/tasks -k \
     -H "Content-Type: application/json" \
     -d '{"title":"New Task","description":"Test","completed":false}'
   
   # Get all tasks
   curl https://localhost:7001/api/tasks -k
   
   # Get completed tasks only
   curl "https://localhost:7001/api/tasks?completed=true" -k
   
   # Update a task
   curl -X PUT https://localhost:7001/api/tasks/1 -k \
     -H "Content-Type: application/json" \
     -d '{"title":"Updated Title","description":"","completed":true}'
   
   # Delete a task
   curl -X DELETE https://localhost:7001/api/tasks/1 -k
   ```

5. Commit your work:

   ```bash
   git add backend/
   git commit -m "Add POST, PUT, DELETE endpoints with validation"
   ```

---

## Homework Assignment

**Reading & Practice**: REST API Design and Testing

**Tasks**:

1. **Read**: [Microsoft — Build Web APIs with ASP.NET Core](https://learn.microsoft.com/aspnet/core/web-api/)
2. **Read**: [MDN — Designing Robust Web APIs for Longevity](https://www.youtube.com/watch?v=QCW2F_gvmzs)
3. **Practice**: Test all CRUD operations with Postman or curl
4. **Practice**: Test validation by sending invalid data (missing title, too long, etc.)
5. **Read**: [Data Annotations — Validation](https://learn.microsoft.com/aspnet/core/mvc/models/validation)

**Optional Challenge**:

- Add unit tests for one endpoint using xUnit
- Add a `completedAt` timestamp field

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| ASP.NET Core — Web API | [Microsoft Docs](https://learn.microsoft.com/aspnet/core/web-api/) | API design guide |
| Model Validation | [Microsoft — Data Annotations](https://learn.microsoft.com/aspnet/core/mvc/models/validation) | Validation attributes |
| Status Codes | [MDN — HTTP Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) | Full reference |
| Postman Docs | [Postman Learning](https://learning.postman.com/) | API testing |
| curl Cookbook | [curl Cookbook](https://catonmat.net/curl-tutorial) | Command-line examples |

---

## Key Takeaways

✓ Implemented full CRUD operations (POST, PUT, DELETE)  
✓ Added data validation to the model  
✓ Returned appropriate HTTP status codes  
✓ Used model binding from body and query  
✓ Tested all endpoints with curl and Postman  

---

## Next Lesson Preview

**Lesson 11 — Persistence with SQLite**: We'll add a persistent database using Entity Framework Core and SQLite.
