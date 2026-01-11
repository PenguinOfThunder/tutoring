# Lesson 12: Entity Framework Core Patterns

## Objective

Introduce EF Core patterns: migrations, LINQ queries, filtering, sorting, and repository patterns (optional).

---

## In-Class Materials

### Topics Covered

1. **LINQ Fundamentals**:
   - LINQ syntax (method vs query syntax)
   - Filtering (`Where`), sorting (`OrderBy`), projecting (`Select`)
   - Aggregation (`Count`, `Sum`, `Max`)
2. **Advanced EF Core Queries**:
   - Eager loading (`Include`) vs lazy loading
   - Deferred execution
   - Query composition and optimization
3. **Change Tracking**:
   - How EF Core tracks changes
   - `SaveChanges()` and transaction handling
   - Detaching entities
4. **Repository Pattern** (optional):
   - Abstracting data access
   - Dependency injection with repositories
   - Benefits: testability, loose coupling
5. **Common Patterns**:
   - Filtering by date range
   - Pagination
   - Soft deletes (mark as deleted instead of removing)
   - Timestamps (CreatedAt, UpdatedAt)

### Key Concepts

- **LINQ**: Query syntax that works with any data source
- **Deferred Execution**: Queries execute when enumerated, not when defined
- **Repository Pattern**: Interface-based data access layer
- **Change Tracking**: EF's mechanism for knowing what changed

---

## In-Class Exercise (15–20 minutes)

**Task: Implement advanced querying and add a repository pattern**

1. Add filtering and sorting to the TasksController (`Controllers/TasksController.cs`):

   ```csharp
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.EntityFrameworkCore;
   using TaskApi.Data;
   using TaskApi.Models;

   namespace TaskApi.Controllers;

   [ApiController]
   [Route("api/[controller]")]
   public class TasksController : ControllerBase
   {
       private readonly TaskContext _context;

       public TasksController(TaskContext context)
       {
           _context = context;
       }

       [HttpGet]
       public IActionResult GetTasks(
           [FromQuery] bool? completed = null,
           [FromQuery] string? sortBy = "createdAt",
           [FromQuery] int page = 1,
           [FromQuery] int pageSize = 10)
       {
           IQueryable<Task> query = _context.Tasks;

           // Filter
           if (completed.HasValue)
               query = query.Where(t => t.Completed == completed.Value);

           // Sort
           query = sortBy?.ToLower() switch
           {
               "title" => query.OrderBy(t => t.Title),
               "completed" => query.OrderBy(t => t.Completed),
               _ => query.OrderByDescending(t => t.CreatedAt)
           };

           // Pagination
           var totalCount = query.Count();
           var tasks = query
               .Skip((page - 1) * pageSize)
               .Take(pageSize)
               .ToList();

           return Ok(new
           {
               data = tasks,
               totalCount,
               page,
               pageSize
           });
       }

       [HttpGet("{id}")]
       public IActionResult GetTask(int id)
       {
           var task = _context.Tasks.Find(id);
           if (task == null)
               return NotFound(new { message = "Task not found" });
           return Ok(task);
       }

       [HttpPost]
       public IActionResult CreateTask([FromBody] Task newTask)
       {
           if (!ModelState.IsValid)
               return BadRequest(ModelState);

           newTask.CreatedAt = DateTime.UtcNow;
           _context.Tasks.Add(newTask);
           _context.SaveChanges();

           return CreatedAtAction(nameof(GetTask), new { id = newTask.Id }, newTask);
       }

       [HttpPut("{id}")]
       public IActionResult UpdateTask(int id, [FromBody] Task updatedTask)
       {
           var task = _context.Tasks.Find(id);
           if (task == null)
               return NotFound(new { message = "Task not found" });

           task.Title = updatedTask.Title;
           task.Description = updatedTask.Description;
           task.Completed = updatedTask.Completed;

           _context.SaveChanges();
           return Ok(task);
       }

       [HttpDelete("{id}")]
       public IActionResult DeleteTask(int id)
       {
           var task = _context.Tasks.Find(id);
           if (task == null)
               return NotFound(new { message = "Task not found" });

           _context.Tasks.Remove(task);
           _context.SaveChanges();
           return NoContent();
       }
   }
   ```

2. (Optional) Create a repository pattern (`Data/ITaskRepository.cs`):

   ```csharp
   using TaskApi.Models;

   namespace TaskApi.Data;

   public interface ITaskRepository
   {
       IEnumerable<Task> GetTasks(bool? completed = null, string sortBy = "createdAt");
       Task? GetTaskById(int id);
       void AddTask(Task task);
       void UpdateTask(Task task);
       void DeleteTask(int id);
       void SaveChanges();
   }
   ```

3. Implement the repository (`Data/TaskRepository.cs`):

   ```csharp
   using TaskApi.Models;
   using Microsoft.EntityFrameworkCore;

   namespace TaskApi.Data;

   public class TaskRepository : ITaskRepository
   {
       private readonly TaskContext _context;

       public TaskRepository(TaskContext context)
       {
           _context = context;
       }

       public IEnumerable<Task> GetTasks(bool? completed = null, string sortBy = "createdAt")
       {
           IQueryable<Task> query = _context.Tasks;

           if (completed.HasValue)
               query = query.Where(t => t.Completed == completed.Value);

           query = sortBy.ToLower() switch
           {
               "title" => query.OrderBy(t => t.Title),
               "completed" => query.OrderBy(t => t.Completed),
               _ => query.OrderByDescending(t => t.CreatedAt)
           };

           return query.ToList();
       }

       public Task? GetTaskById(int id) => _context.Tasks.Find(id);

       public void AddTask(Task task)
       {
           task.CreatedAt = DateTime.UtcNow;
           _context.Tasks.Add(task);
       }

       public void UpdateTask(Task task) => _context.Tasks.Update(task);

       public void DeleteTask(int id)
       {
           var task = _context.Tasks.Find(id);
           if (task != null)
               _context.Tasks.Remove(task);
       }

       public void SaveChanges() => _context.SaveChanges();
   }
   ```

4. Register repository in `Program.cs`:

   ```csharp
   builder.Services.AddScoped<ITaskRepository, TaskRepository>();
   ```

5. Test filtering and pagination:

   ```bash
   # Get completed tasks only
   curl "https://localhost:7001/api/tasks?completed=true" -k

   # Sort by title
   curl "https://localhost:7001/api/tasks?sortBy=title" -k

   # Paginate (page 2, 5 items per page)
   curl "https://localhost:7001/api/tasks?page=2&pageSize=5" -k
   ```

6. Commit your work:

   ```bash
   git add backend/
   git commit -m "Add LINQ filtering, sorting, pagination, and repository pattern"
   ```

---

## Homework Assignment

**Reading & Practice**: EF Core Advanced Patterns

**Tasks**:

1. **Read**: [EF Core — Query Documentation](https://learn.microsoft.com/ef/core/querying/)
2. **Read**: [EF Core — Relationships](https://learn.microsoft.com/ef/core/modeling/relationships)
3. **Read**: [EF Core — Change Tracking](https://learn.microsoft.com/ef/core/change-tracking/)
4. **Practice**: Add a filter for tasks created in the last 7 days
5. **Practice**: Implement pagination in the UI (optional)

**Optional Challenge**:

- Add a `Priority` enum field and filter by priority
- Implement the repository pattern in the controller

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| LINQ Queries | [EF Core — LINQ](https://learn.microsoft.com/ef/core/querying/) | Query syntax |
| Repository Pattern | [Microsoft — Repository Pattern](https://learn.microsoft.com/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) | Design pattern |
| Change Tracking | [EF Core — Change Tracking](https://learn.microsoft.com/ef/core/change-tracking/) | How EF tracks changes |
| Relationships | [EF Core — Relationships](https://learn.microsoft.com/ef/core/modeling/relationships) | One-to-many, etc. |
| Pagination | [Web Dev — Pagination](https://www.w3schools.com/howto/howto_css_pagination.asp) | Best practices |

---

## Key Takeaways

✓ Used LINQ to filter and sort queries  
✓ Implemented pagination for large datasets  
✓ Applied the repository pattern for cleaner architecture  
✓ Composed queries efficiently with EF Core  
✓ Used dependency injection for repositories  

---

## Next Lesson Preview

**Lesson 13 — Advanced SQL**: We'll deepen our SQL knowledge with joins, aggregates, views, and indexes.
