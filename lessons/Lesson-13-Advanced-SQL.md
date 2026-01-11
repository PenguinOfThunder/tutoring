# Lesson 13: Advanced SQL — Queries, Joins, Views & Indexes

## Objective

Deepen SQL knowledge: SELECT variants, JOINs, GROUP BY, window functions, views, indexes, and query optimization with EXPLAIN plans.

---

## In-Class Materials

### Topics Covered

1. **Advanced SELECT Queries**:
   - WHERE, GROUP BY, HAVING clauses
   - Aggregate functions (COUNT, SUM, AVG, MAX, MIN)
   - CASE expressions
   - Subqueries and CTEs (Common Table Expressions)
2. **JOINs**:
   - INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL OUTER JOIN
   - Self-joins
   - Multiple joins
3. **Window Functions**:
   - ROW_NUMBER, RANK, DENSE_RANK
   - LAG, LEAD for comparing rows
   - OVER clause and partitioning
4. **Views and Materialization**:
   - Creating views (virtual tables)
   - Benefits and drawbacks
   - Updating data through views
5. **Indexes and Query Optimization**:
   - Single-column and composite indexes
   - PRIMARY KEY and UNIQUE constraints
   - EXPLAIN / EXPLAIN PLAN for analyzing queries
   - Index selection strategies

### Key Concepts

- **Joins**: Combine rows from multiple tables based on relationships
- **Aggregation**: Roll up data and produce summaries
- **Views**: Named queries for reusability and encapsulation
- **Indexes**: Data structures that speed up queries at the cost of insert/update overhead

---

## In-Class Exercise (15–20 minutes)

**Task: Create advanced queries and optimize with indexes**

1. First, add a Users table to demonstrate joins. Update `Models/User.cs`:

   ```csharp
   using System.ComponentModel.DataAnnotations;

   namespace TaskApi.Models;

   public class User
   {
       public int Id { get; set; }
       
       [Required]
       [StringLength(100)]
       public string Name { get; set; } = string.Empty;
       
       [Required]
       [EmailAddress]
       public string Email { get; set; } = string.Empty;
   }
   ```

2. Update the Task model to add a foreign key:

   ```csharp
   public class Task
   {
       // ... existing properties ...
       public int? UserId { get; set; }
       public User? User { get; set; }
   }
   ```

3. Update `Data/TaskContext.cs` to include Users:

   ```csharp
   public class TaskContext : DbContext
   {
       public DbSet<Task> Tasks { get; set; } = null!;
       public DbSet<User> Users { get; set; } = null!;

       public TaskContext(DbContextOptions<TaskContext> options) : base(options) { }

       protected override void OnModelCreating(ModelBuilder modelBuilder)
       {
           base.OnModelCreating(modelBuilder);

           // ... existing configuration ...

           // Configure relationship
           modelBuilder.Entity<Task>()
               .HasOne(t => t.User)
               .WithMany()
               .HasForeignKey(t => t.UserId);

           // Create an index on completion status
           modelBuilder.Entity<Task>()
               .HasIndex(t => t.Completed)
               .HasName("IX_Tasks_Completed");
       }
   }
   ```

4. Create and apply migration:

   ```bash
   dotnet ef migrations add AddUsersAndIndexes
   dotnet ef database update
   ```

5. Add advanced query methods to the repository (`Data/TaskRepository.cs`):

   ```csharp
   // Get task count by completion status
   public Dictionary<string, int> GetTaskStats()
   {
       return new Dictionary<string, int>
       {
           { "Total", _context.Tasks.Count() },
           { "Completed", _context.Tasks.Count(t => t.Completed) },
           { "Pending", _context.Tasks.Count(t => !t.Completed) }
       };
   }

   // Get tasks with user information (JOIN)
   public IEnumerable<TaskDto> GetTasksWithUsers()
   {
       return _context.Tasks
           .Include(t => t.User)
           .Select(t => new TaskDto
           {
               Id = t.Id,
               Title = t.Title,
               Completed = t.Completed,
               UserName = t.User != null ? t.User.Name : "Unassigned"
           })
           .ToList();
   }

   // Get tasks grouped by completion status
   public IEnumerable<IGrouping<bool, Task>> GetTasksGroupedByStatus()
   {
       return _context.Tasks
           .GroupBy(t => t.Completed)
           .ToList();
   }
   ```

6. Create a DTO for the response:

   ```csharp
   // Models/TaskDto.cs
   namespace TaskApi.Models;

   public class TaskDto
   {
       public int Id { get; set; }
       public string Title { get; set; } = string.Empty;
       public bool Completed { get; set; }
       public string UserName { get; set; } = string.Empty;
   }
   ```

7. Test with raw SQL (optional, in `appsettings.json` context):

   ```bash
   sqlite3 tasks.db
   ```

   Then run SQL:

   ```sql
   -- Count tasks by completion
   SELECT Completed, COUNT(*) as Count 
   FROM Tasks 
   GROUP BY Completed;

   -- Get tasks with user names (JOIN)
   SELECT t.Id, t.Title, t.Completed, u.Name 
   FROM Tasks t 
   LEFT JOIN Users u ON t.UserId = u.Id;

   -- View task statistics
   SELECT 
       'Total' as Stat, COUNT(*) as Count FROM Tasks
   UNION ALL
   SELECT 'Completed', COUNT(*) FROM Tasks WHERE Completed = 1
   UNION ALL
   SELECT 'Pending', COUNT(*) FROM Tasks WHERE Completed = 0;

   -- Check index usage
   .indexes Tasks
   ```

8. Commit your work:

   ```bash
   git add backend/
   git commit -m "Add advanced SQL queries, JOINs, indexes, and query optimization"
   ```

---

## Homework Assignment

**Reading & Practice**: SQL Optimization and Advanced Queries

**Tasks**:

1. **Read**: [SQLite — SELECT Statement](https://www.sqlite.org/select.html)
2. **Read**: [SQLite — Indexes](https://www.sqlite.org/lang_createindex.html)
3. **Read**: [Use The Index, Luke! — Index Design](https://use-the-index-luke.com/)
4. **Practice**: Write 3–5 advanced SQL queries using GROUP BY, HAVING, and JOINs
5. **Practice**: Analyze query performance with EXPLAIN QUERY PLAN

**Optional Challenge**:

- Create a SQL view for "active tasks" (pending and created in the last 30 days)
- Add a composite index on (UserId, Completed) and measure query time

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| SQLite Docs | [SQLite — Official Docs](https://www.sqlite.org/) | Complete reference |
| SELECT Statement | [SQLite — SELECT](https://www.sqlite.org/select.html) | Query syntax |
| Indexes | [SQLite — CREATE INDEX](https://www.sqlite.org/lang_createindex.html) | Index creation |
| Query Optimization | [Use The Index, Luke!](https://use-the-index-luke.com/) | Performance guide |
| Window Functions | [SQLite — Window Functions](https://www.sqlite.org/windowfunctions.html) | Advanced queries |
| PostgreSQL (optional) | [PostgreSQL Docs](https://www.postgresql.org/docs/) | Production database |

---

## Key Takeaways

✓ Wrote advanced SELECT queries with GROUP BY and aggregates  
✓ Used JOINs to combine data from multiple tables  
✓ Created indexes to optimize query performance  
✓ Analyzed queries with EXPLAIN PLAN  
✓ Understood relationships and foreign keys  

---

## Next Lesson Preview

**Lesson 14 — User Accounts & Passwords**: We'll add authentication using ASP.NET Core Identity with hashed passwords.
