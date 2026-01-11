# Lesson 15: Protecting Routes & Middleware

## Objective

Require authentication for create/update/delete operations; use [Authorize] attribute and implement middleware for access control.

---

## In-Class Materials

### Topics Covered

1. **Authorization in ASP.NET Core**:
   - [Authorize] and [AllowAnonymous] attributes
   - Authorization policies and claims
   - Role-based and policy-based authorization
2. **Middleware Pipeline**:
   - Request flow through middleware
   - Authentication and authorization middleware
   - Custom middleware
3. **Protecting Endpoints**:
   - Public vs private endpoints
   - User context in controllers
   - Associating resources with users
4. **Cookie and JWT Authentication**:
   - Cookies for web apps (session-based)
   - JWT for APIs (token-based)
   - Token validation
5. **Error Handling**:
   - 401 Unauthorized (not authenticated)
   - 403 Forbidden (authenticated but no permission)

### Key Concepts

- **Claims**: Information about the authenticated user
- **Principal**: Authenticated user entity
- **Middleware**: Components that process requests/responses
- **Token Validation**: Verifying JWT integrity and expiration

---

## In-Class Exercise (15–20 minutes)

**Task: Protect task endpoints and associate tasks with users**

1. Update Task model to include UserId (`Models/Task.cs`):

   ```csharp
   using System.ComponentModel.DataAnnotations;

   namespace TaskApi.Models;

   public class Task
   {
       public int Id { get; set; }
       
       [Required]
       [StringLength(200)]
       public string Title { get; set; } = string.Empty;
       
       [StringLength(1000)]
       public string Description { get; set; } = string.Empty;
       
       public bool Completed { get; set; }
       public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
       
       // Add UserId to associate tasks with users
       public string? UserId { get; set; }
   }
   ```

2. Create and apply migration:

   ```bash
   dotnet ef migrations add AddUserIdToTasks
   dotnet ef database update
   ```

3. Update `Controllers/TasksController.cs` with authorization:

   ```csharp
   using Microsoft.AspNetCore.Authorization;
   using Microsoft.AspNetCore.Identity;
   using Microsoft.AspNetCore.Mvc;
   using TaskApi.Data;
   using TaskApi.Models;

   namespace TaskApi.Controllers;

   [ApiController]
   [Route("api/[controller]")]
   public class TasksController : ControllerBase
   {
       private readonly TaskContext _context;
       private readonly UserManager<IdentityUser> _userManager;

       public TasksController(TaskContext context, UserManager<IdentityUser> userManager)
       {
           _context = context;
           _userManager = userManager;
       }

       // Get user's own tasks
       [Authorize]
       [HttpGet]
       public IActionResult GetTasks([FromQuery] bool? completed = null)
       {
           var userId = _userManager.GetUserId(User);
           var query = _context.Tasks.Where(t => t.UserId == userId);

           if (completed.HasValue)
               query = query.Where(t => t.Completed == completed.Value);

           return Ok(query.OrderByDescending(t => t.CreatedAt).ToList());
       }

       [Authorize]
       [HttpGet("{id}")]
       public IActionResult GetTask(int id)
       {
           var userId = _userManager.GetUserId(User);
           var task = _context.Tasks.FirstOrDefault(t => t.Id == id && t.UserId == userId);
           
           if (task == null)
               return NotFound(new { message = "Task not found" });
           
           return Ok(task);
       }

       [Authorize]
       [HttpPost]
       public IActionResult CreateTask([FromBody] Task newTask)
       {
           if (!ModelState.IsValid)
               return BadRequest(ModelState);

           var userId = _userManager.GetUserId(User);
           newTask.UserId = userId;
           newTask.CreatedAt = DateTime.UtcNow;
           
           _context.Tasks.Add(newTask);
           _context.SaveChanges();

           return CreatedAtAction(nameof(GetTask), new { id = newTask.Id }, newTask);
       }

       [Authorize]
       [HttpPut("{id}")]
       public IActionResult UpdateTask(int id, [FromBody] Task updatedTask)
       {
           var userId = _userManager.GetUserId(User);
           var task = _context.Tasks.FirstOrDefault(t => t.Id == id && t.UserId == userId);
           
           if (task == null)
               return NotFound(new { message = "Task not found" });

           task.Title = updatedTask.Title;
           task.Description = updatedTask.Description;
           task.Completed = updatedTask.Completed;

           _context.SaveChanges();
           return Ok(task);
       }

       [Authorize]
       [HttpDelete("{id}")]
       public IActionResult DeleteTask(int id)
       {
           var userId = _userManager.GetUserId(User);
           var task = _context.Tasks.FirstOrDefault(t => t.Id == id && t.UserId == userId);
           
           if (task == null)
               return NotFound(new { message = "Task not found" });

           _context.Tasks.Remove(task);
           _context.SaveChanges();
           return NoContent();
       }
   }
   ```

4. Update Auth controller to generate JWT token:

   ```csharp
   [HttpPost("login")]
   public async Task<IActionResult> Login([FromBody] LoginRequest request)
   {
       var user = await _userManager.FindByEmailAsync(request.Email);
       if (user == null)
           return Unauthorized(new { message = "Invalid email or password" });

       var result = await _signInManager.CheckPasswordSignInAsync(user, request.Password, false);
       if (!result.Succeeded)
           return Unauthorized(new { message = "Invalid email or password" });

       // For now, return success message
       // TODO: Generate JWT token
       return Ok(new { message = "Login successful", userId = user.Id });
   }
   ```

5. Test authorization:

   ```bash
   # This should now fail without authentication
   curl https://localhost:7001/api/tasks -k
   
   # Should return 401 Unauthorized
   ```

6. Commit your work:

   ```bash
   git add backend/
   git commit -m "Add authorization to task endpoints, associate tasks with users"
   ```

---

## Homework Assignment

**Reading & Practice**: Authorization and Security

**Tasks**:

1. **Read**: [ASP.NET Core — Authorization](https://learn.microsoft.com/aspnet/core/security/authorization/introduction)
2. **Read**: [ASP.NET Core — Claims-based Authorization](https://learn.microsoft.com/aspnet/core/security/authorization/claims)
3. **Read**: [JWT Claims](https://jwt.io/introduction#what-is-jwt-structure)
4. **Practice**: Test protected endpoints without authentication (should get 401)
5. **Practice**: Attempt to access another user's tasks (should get 404)

**Optional Challenge**:

- Implement JWT token generation in the login endpoint
- Add a policy-based authorization requirement

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Authorization | [ASP.NET Core — Authorization](https://learn.microsoft.com/aspnet/core/security/authorization/introduction) | Access control |
| Claims-based | [Claims-based Authorization](https://learn.microsoft.com/aspnet/core/security/authorization/claims) | Advanced patterns |
| JWT Claims | [JWT.io](https://jwt.io/introduction#what-is-jwt-structure) | Token structure |
| Middleware | [ASP.NET Core — Middleware](https://learn.microsoft.com/aspnet/core/fundamentals/middleware) | Request pipeline |

---

## Key Takeaways

✓ Added [Authorize] attribute to protect endpoints  
✓ Associated tasks with users  
✓ Extracted user ID from claims  
✓ Tested 401 Unauthorized responses  
✓ Implemented access control logic  

---

## Next Lesson Preview

**Lesson 16 — Frontend-Backend Integration**: We'll connect the UI to the real API with TypeScript fetch calls and handle responses.
