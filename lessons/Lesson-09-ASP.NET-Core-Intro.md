# Lesson 9: ASP.NET Core Intro

## Objective

Create a minimal ASP.NET Core Web API that returns JSON; understand the basics of backend development and RESTful services.

---

## In-Class Materials

### Topics Covered

1. **ASP.NET Core Fundamentals**:
   - What is ASP.NET Core (cross-platform, open-source framework)
   - Running under WSL2 and Docker
   - Project structure: Controllers, Models, `Program.cs`
2. **REST API Basics**:
   - HTTP methods (GET, POST, PUT, DELETE)
   - Status codes (200, 201, 400, 404, 500)
   - JSON request/response bodies
3. **Creating a Web API Project**:
   - Using `dotnet new webapi` template
   - Understanding middleware and dependency injection
   - Running the API with `dotnet run`
4. **Controllers and Routing**:
   - `[ApiController]` and `[Route]` attributes
   - Action methods for CRUD operations
   - Binding parameters from URL, query, and body
5. **Testing the API**:
   - Using `curl` command-line tool
   - Using Postman or Insomnia (GUI tools)
   - Inspecting requests and responses

### Key Concepts

- **API**: Application Programming Interface — contract between client and server
- **RESTful**: Uses HTTP methods and URLs semantically
- **Stateless**: Each request is independent; no session state
- **JSON**: Lightweight data format for requests/responses

---

## In-Class Exercise (15–20 minutes)

**Task: Create a minimal ASP.NET Core API with a GET endpoint**

1. Create a new Web API project in the `backend` folder:

   ```bash
   cd backend
   dotnet new webapi -n TaskApi
   cd TaskApi
   ```

2. Review the project structure:
   - `Program.cs` — app configuration
   - `Controllers/` — API endpoints
   - `appsettings.json` — configuration

3. Create a Task model (`Models/Task.cs`):

   ```csharp
   namespace TaskApi.Models;

   public class Task
   {
       public int Id { get; set; }
       public string Title { get; set; } = string.Empty;
       public string Description { get; set; } = string.Empty;
       public bool Completed { get; set; }
       public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
   }
   ```

4. Create a Tasks controller (`Controllers/TasksController.cs`):

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

       [HttpGet]
       public IActionResult GetTasks()
       {
           return Ok(tasks);
       }

       [HttpGet("{id}")]
       public IActionResult GetTask(int id)
       {
           var task = tasks.FirstOrDefault(t => t.Id == id);
           if (task == null)
               return NotFound();
           return Ok(task);
       }
   }
   ```

5. Run the API:

   ```bash
   dotnet run
   ```

   - API runs at `https://localhost:7001` (or another port)

6. Test the API using `curl`:

   ```bash
   # Get all tasks
   curl https://localhost:7001/api/tasks -k
   
   # Get a specific task
   curl https://localhost:7001/api/tasks/1 -k
   ```

   - `-k` flag ignores HTTPS certificate warnings in development

7. Commit your work:

   ```bash
   git add backend/
   git commit -m "Create initial ASP.NET Core Web API with GET endpoints"
   ```

---

## Homework Assignment

**Reading & Practice**: ASP.NET Core Fundamentals

**Tasks**:

1. **Read**: [ASP.NET Core — Getting Started](https://learn.microsoft.com/aspnet/core/getting-started/)
2. **Read**: [ASP.NET Core — RESTful API Design](https://learn.microsoft.com/aspnet/core/web-api/)
3. **Read**: [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
4. **Practice**: Add a third sample task to the API and fetch it using `curl`
5. **Read**: [ASP.NET Core — Dependency Injection](https://learn.microsoft.com/aspnet/core/fundamentals/dependency-injection)

**Optional Challenge**:

- Test the API with both `curl` and Postman
- Inspect the response headers and content-type

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| ASP.NET Core Docs | [ASP.NET Core Docs](https://learn.microsoft.com/aspnet/core/) | Official documentation |
| Getting Started | [ASP.NET Core — Getting Started](https://learn.microsoft.com/aspnet/core/getting-started/) | First steps |
| Web API Guide | [ASP.NET Core — Web API](https://learn.microsoft.com/aspnet/core/web-api/) | Building APIs |
| HTTP Status Codes | [MDN — HTTP Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) | Response codes |
| curl Manual | [curl Docs](https://curl.se/docs/manual.html) | Command-line HTTP client |

---

## Key Takeaways

✓ Created an ASP.NET Core Web API project  
✓ Defined a Task model  
✓ Implemented GET endpoints  
✓ Ran the API and tested with curl  
✓ Understood HTTP status codes and REST basics  

---

## Next Lesson Preview

**Lesson 10 — Build RESTful CRUD Endpoints**: We'll complete the REST API by implementing POST, PUT, and DELETE operations.
