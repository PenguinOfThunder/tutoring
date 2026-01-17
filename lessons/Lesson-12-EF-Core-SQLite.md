# Lesson 11: Persistence with SQLite (EF Core)

## Objective

Add a persistent SQLite database for tasks using Entity Framework Core; create migrations and apply them to the database.

---

## In-Class Materials

### Topics Covered

1. **Entity Framework Core (EF Core)**:
   - What is an ORM (Object-Relational Mapper)
   - EF Core design patterns
   - DbContext and DbSet
2. **SQLite Basics**:
   - SQLite file-based database
   - Advantages for development (no server needed)
   - Migrations for schema management
3. **Creating a DbContext**:
   - Defining entities (models)
   - Configuration and relationships
   - Connection strings
4. **Migrations**:
   - Creating migrations with `dotnet ef migrations add`
   - Applying migrations with `dotnet ef database update`
   - Rolling back migrations
5. **Querying with EF Core**:
   - LINQ to Entities
   - Filtering, sorting, and projecting data
   - Change tracking and saving

### Key Concepts

- **DbContext**: Represents a session with the database
- **Migration**: Version-controlled database schema changes
- **Change Tracking**: EF knows what changed and only updates those rows
- **LINQ**: Query syntax that reads like C# code

---

## In-Class Exercise (15–20 minutes)

**Task: Set up EF Core with SQLite and migrate the database**

1. Install EF Core packages:

   ```bash
   cd backend/TaskApi
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   dotnet add package Microsoft.EntityFrameworkCore.Tools
   ```

2. Create a DbContext (`Data/TaskContext.cs`):

   ```csharp
   using Microsoft.EntityFrameworkCore;
   using TaskApi.Models;

   namespace TaskApi.Data;

   public class TaskContext : DbContext
   {
       public DbSet<Task> Tasks { get; set; } = null!;

       public TaskContext(DbContextOptions<TaskContext> options) : base(options) { }

       protected override void OnModelCreating(ModelBuilder modelBuilder)
       {
           base.OnModelCreating(modelBuilder);

           modelBuilder.Entity<Task>().HasKey(t => t.Id);
           modelBuilder.Entity<Task>()
               .Property(t => t.Title)
               .IsRequired()
               .HasMaxLength(200);
       }
   }
   ```

3. Update `Program.cs` to register DbContext:

   ```csharp
   using Microsoft.EntityFrameworkCore;
   using TaskApi.Data;

   var builder = WebApplicationBuilder.CreateBuilder(args);

   // Add services
   builder.Services.AddControllers();
   builder.Services.AddDbContext<TaskContext>(options =>
       options.UseSqlite("Data Source=tasks.db"));

   var app = builder.Build();

   // Configure middleware
   app.UseHttpsRedirection();
   app.UseAuthorization();
   app.MapControllers();

   app.Run();
   ```

4. Create and apply migrations:

   ```bash
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

   - Creates `tasks.db` file
   - Creates `Migrations/` folder with migration files

5. Update `TasksController.cs` to use EF Core:

   ```csharp
   using Microsoft.AspNetCore.Mvc;
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
       public IActionResult GetTasks([FromQuery] bool? completed = null)
       {
           var query = _context.Tasks.AsQueryable();
           if (completed.HasValue)
               query = query.Where(t => t.Completed == completed.Value);
           return Ok(query.ToList());
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

6. Run the API and test:

   ```bash
   dotnet run
   ```

   - Tasks are now persisted to `tasks.db`

7. Verify the database:

   ```bash
   # View database with sqlite3 (if installed)
   sqlite3 tasks.db ".tables"
   sqlite3 tasks.db "SELECT * FROM Tasks;"
   ```

8. Commit your work:

   ```bash
   git add backend/
   git commit -m "Add EF Core and SQLite persistence"
   ```

---

## Homework Assignment

**Reading & Practice**: EF Core and Database Migrations

**Tasks**:

1. **Read**: [EF Core Docs — Getting Started](https://learn.microsoft.com/ef/core/get-started/overview/first-app)
2. **Read**: [EF Core — Migrations](https://learn.microsoft.com/ef/core/managing-schemas/migrations/)
3. **Read**: [EF Core — Querying Data](https://learn.microsoft.com/ef/core/querying/)
4. **Practice**: Create a migration that adds a `Priority` field to tasks
5. **Practice**: Test CRUD operations and verify data persists

**Optional Challenge**:

- Use a SQLite GUI viewer to inspect the database
- Add a `DueDate` field and create a migration

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| EF Core Docs | [Microsoft — Entity Framework Core](https://learn.microsoft.com/ef/core/) | Official guide |
| Getting Started | [EF Core — First App](https://learn.microsoft.com/ef/core/get-started/overview/first-app) | Quick start |
| Migrations | [EF Core — Migrations](https://learn.microsoft.com/ef/core/managing-schemas/migrations/) | Schema management |
| LINQ to EF | [EF Core — LINQ Queries](https://learn.microsoft.com/ef/core/querying/) | Query syntax |
| SQLite | [SQLite Official](https://www.sqlite.org/) | Database docs |
| DB Viewer | [DB Browser for SQLite](https://sqlitebrowser.org/) | GUI tool |

---

## Key Takeaways

✓ Set up Entity Framework Core with SQLite  
✓ Created DbContext and configured the database  
✓ Generated and applied migrations  
✓ Converted controller to use EF Core for persistence  
✓ Verified data persistence with SQLite  

---

## Next Lesson Preview

**Lesson 12 — Entity Framework Core Patterns**: We'll explore EF Core patterns like LINQ queries, repository patterns, and advanced configurations.
