# Lesson 19: Basic Testing for Backend

## Objective

Write unit and integration tests for the API using xUnit and `WebApplicationFactory`; establish testing practices.

---

## In-Class Materials

### Topics Covered

1. **Testing Types**:
   - Unit tests (test individual functions/methods)
   - Integration tests (test API endpoints with database)
   - E2E tests (test full user workflows)
2. **Test Framework (xUnit)**:
   - Test methods and assertions
   - Test fixtures and setup/teardown
   - Test organization and naming
3. **Testing HTTP APIs**:
   - `WebApplicationFactory` to host API in-memory
   - Making requests and asserting responses
   - Testing status codes and response bodies
4. **Mocking and Isolation**:
   - In-memory databases for testing
   - Mocking dependencies
   - Test data setup
5. **Test Driven Development (TDD)**:
   - Write test first, then code
   - Red-green-refactor cycle
   - Benefits: better design, fewer bugs

### Key Concepts

- **Assertion**: Verify expected vs actual behavior
- **Fixture**: Setup and teardown for each test
- **Test Isolation**: Each test is independent
- **Code Coverage**: Percentage of code tested

---

## In-Class Exercise (15–20 minutes)

**Task: Write integration tests for the Tasks API**

1. Create a new test project:

   ```bash
   cd backend
   dotnet new xunit -n TaskApi.Tests
   cd TaskApi.Tests
   ```

2. Add project references:

   ```bash
   dotnet add reference ../TaskApi/TaskApi.csproj
   dotnet add package Microsoft.AspNetCore.Mvc.Testing
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

3. Create test fixtures (`TaskApiFactory.cs`):

   ```csharp
   using Microsoft.AspNetCore.Mvc.Testing;
   using Microsoft.EntityFrameworkCore;
   using Microsoft.Extensions.DependencyInjection;
   using TaskApi.Data;

   namespace TaskApi.Tests;

   public class TaskApiFactory : WebApplicationFactory<Program>
   {
       protected override void ConfigureWebHost(IWebHostBuilder builder)
       {
           builder.ConfigureServices(services =>
           {
               // Remove the existing DbContext
               var descriptor = services.SingleOrDefault(d =>
                   d.ServiceType == typeof(DbContextOptions<TaskContext>));
               if (descriptor != null)
                   services.Remove(descriptor);

               // Add in-memory database for testing
               services.AddDbContext<TaskContext>(options =>
               {
                   options.UseInMemoryDatabase("TestDb");
               });

               // Ensure database is created
               var sp = services.BuildServiceProvider();
               using (var scope = sp.CreateScope())
               {
                   var db = scope.ServiceProvider.GetRequiredService<TaskContext>();
                   db.Database.EnsureCreated();
               }
           });
       }
   }
   ```

4. Create task endpoint tests (`TasksControllerTests.cs`):

   ```csharp
   using System.Net;
   using System.Text;
   using System.Text.Json;
   using TaskApi.Models;
   using Xunit;

   namespace TaskApi.Tests;

   public class TasksControllerTests : IAsyncLifetime
   {
       private readonly TaskApiFactory _factory;
       private readonly HttpClient _client;

       public TasksControllerTests()
       {
           _factory = new TaskApiFactory();
           _client = _factory.CreateClient();
       }

       public async Task InitializeAsync()
       {
           // Setup test data if needed
           await Task.CompletedTask;
       }

       public async Task DisposeAsync()
       {
           _client.Dispose();
           await _factory.DisposeAsync();
       }

       [Fact]
       public async Task GetTasks_ReturnsUnauthorized_WhenNotAuthenticated()
       {
           // Act
           var response = await _client.GetAsync("/api/tasks");

           // Assert
           Assert.Equal(HttpStatusCode.Unauthorized, response.StatusCode);
       }

       [Fact]
       public async Task CreateTask_Returns201Created_WithValidData()
       {
           // Arrange
           var task = new Task
           {
               Title = "Test Task",
               Description = "Test Description",
               Completed = false
           };
           var json = JsonSerializer.Serialize(task);
           var content = new StringContent(json, Encoding.UTF8, "application/json");

           // Act
           var response = await _client.PostAsync("/api/tasks", content);

           // Assert
           Assert.Equal(HttpStatusCode.Created, response.StatusCode);
           var createdTask = JsonSerializer.Deserialize<Task>(
               await response.Content.ReadAsStringAsync(),
               new JsonSerializerOptions { PropertyNameCaseInsensitive = true }
           );
           Assert.NotNull(createdTask);
           Assert.Equal("Test Task", createdTask!.Title);
       }

       [Fact]
       public async Task CreateTask_ReturnsBadRequest_WithMissingTitle()
       {
           // Arrange
           var json = JsonSerializer.Serialize(new { Description = "No title" });
           var content = new StringContent(json, Encoding.UTF8, "application/json");

           // Act
           var response = await _client.PostAsync("/api/tasks", content);

           // Assert
           Assert.Equal(HttpStatusCode.BadRequest, response.StatusCode);
       }

       [Fact]
       public async Task DeleteTask_ReturnsNotFound_WithInvalidId()
       {
           // Act
           var response = await _client.DeleteAsync("/api/tasks/99999");

           // Assert
           Assert.Equal(HttpStatusCode.NotFound, response.StatusCode);
       }
   }
   ```

5. Run the tests:

   ```bash
   dotnet test
   ```

6. View test coverage (optional):

   ```bash
   dotnet add package coverlet.collector
   dotnet test /p:CollectCoverage=true
   ```

7. Commit your work:

   ```bash
   git add backend/
   git commit -m "Add xUnit integration tests for API endpoints"
   ```

---

## Homework Assignment

**Reading & Practice**: Testing Best Practices

**Tasks**:

1. **Read**: [xUnit Documentation](https://xunit.net/docs/getting-started/netcore)
2. **Read**: [Microsoft — Integration Tests in ASP.NET Core](https://learn.microsoft.com/aspnet/core/test/integration-tests)
3. **Read**: [Testing Best Practices](https://learn.microsoft.com/dotnet/core/testing/)
4. **Practice**: Write tests for all CRUD endpoints
5. **Practice**: Aim for >80% code coverage

**Optional Challenge**:

- Write unit tests for the Task model validation
- Test error handling and edge cases
- Create fixtures for common test data

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| xUnit | [xUnit.net Docs](https://xunit.net/) | Test framework |
| Integration Tests | [Microsoft Docs](https://learn.microsoft.com/aspnet/core/test/integration-tests) | API testing |
| Testing Best Practices | [Microsoft Guide](https://learn.microsoft.com/dotnet/core/testing/) | Principles |
| WebApplicationFactory | [Microsoft Docs](https://learn.microsoft.com/aspnet/core/test/integration-tests#basic-tests-with-the-default-webapplicationfactory) | In-memory testing |
| Unit Testing | [Unit Testing Best Practices](https://stackoverflow.com/questions/119169/significant-new-inventions-in-computing-since-1980) | General guide |

---

## Key Takeaways

✓ Created test project with xUnit  
✓ Used WebApplicationFactory for integration tests  
✓ Tested API endpoints with various scenarios  
✓ Verified status codes and response bodies  
✓ Ran tests and verified coverage  

---

## Next Lesson Preview

**Lesson 20 — Environment & Secrets**: We'll manage configuration for different environments and keep secrets secure.
