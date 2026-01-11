# Lesson 21: Production Build & Logging

## Objective

Produce a production-ready frontend build and backend publish; configure logging for debugging and monitoring.

---

## In-Class Materials

### Topics Covered

1. **Frontend Production Build**:
   - Building with Vite (`npm run build`)
   - Minification and tree-shaking
   - Source maps for debugging
   - Analyzing bundle size
2. **Backend Publishing**:
   - `dotnet publish` to create deployment package
   - Self-contained vs framework-dependent
   - Release vs Debug configuration
3. **Logging Configuration**:
   - Built-in .NET logging
   - Different log levels (Trace, Debug, Info, Warning, Error, Critical)
   - Structured logging (Serilog)
4. **Serving Static Files**:
   - Configuring ASP.NET Core to serve static frontend
   - Asset caching and versioning
   - HTTPS redirects
5. **Performance and Security**:
   - Response compression
   - Security headers
   - Rate limiting

### Key Concepts

- **Build Optimization**: Smaller files, faster downloads
- **Logging**: Record events for debugging and monitoring
- **Structured Logging**: Searchable, queryable logs (JSON format)
- **Deployment Package**: Self-contained app ready to run

---

## In-Class Exercise (15–20 minutes)

**Task: Build frontend, publish backend, and configure logging**

1. Build the frontend:

   ```bash
   cd frontend
   npm run build
   ```

   - Creates `dist/` folder with optimized assets
   - Includes `index.html`, `app.js` (minified), `styles.css`

2. View build output:

   ```bash
   ls -lah dist/
   du -sh dist/
   ```

3. Publish the backend:

   ```bash
   cd backend/TaskApi
   dotnet publish -c Release -o publish/
   ```

   - Creates `publish/` folder with executable and dependencies
   - Much smaller than debug build

4. Set up structured logging with Serilog:

   ```bash
   dotnet add package Serilog.AspNetCore
   dotnet add package Serilog.Sinks.Console
   dotnet add package Serilog.Sinks.File
   ```

5. Configure Serilog in `Program.cs`:

   ```csharp
   using Serilog;

   var builder = WebApplicationBuilder.CreateBuilder(args);

   // Configure Serilog
   Log.Logger = new LoggerConfiguration()
       .MinimumLevel.Debug()
       .WriteTo.Console()
       .WriteTo.File("logs/app-.txt", 
           rollingInterval: RollingInterval.Day,
           retainedFileCountLimit: 7)
       .CreateLogger();

   builder.Host.UseSerilog();

   // ... rest of builder configuration ...

   var app = builder.Build();

   // Log application startup
   app.Logger.LogInformation("Task API starting up at {StartTime}", DateTime.UtcNow);

   // ... rest of middleware ...
   ```

6. Add logging to endpoints:

   ```csharp
   [HttpPost]
   public IActionResult CreateTask([FromBody] Task newTask)
   {
       _logger.LogInformation("Creating task: {Title}", newTask.Title);
       
       // ... task creation logic ...
       
       _logger.LogInformation("Task created with ID: {TaskId}", newTask.Id);
       return CreatedAtAction(nameof(GetTask), new { id = newTask.Id }, newTask);
   }
   ```

7. Configure frontend serving in ASP.NET Core:

   ```csharp
   // Copy built frontend to wwwroot
   // Before publishing, copy dist/* to backend/TaskApi/wwwroot/

   app.UseStaticFiles();

   // Fallback to index.html for SPA routing
   app.MapFallbackToFile("index.html");
   ```

8. Create `appsettings.Production.json`:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Warning",
         "TaskApi": "Information"
       }
     },
     "AllowedHosts": "*.example.com"
   }
   ```

9. Test the production build locally:

   ```bash
   cd backend/TaskApi
   dotnet run -c Release
   ```

10. View logs:

    ```bash
    cat logs/app-*.txt
    ```

11. Commit your work:

    ```bash
    git add backend/Program.cs appsettings.Production.json
    git commit -m "Add production builds and structured logging with Serilog"
    ```

---

## Homework Assignment

**Reading & Practice**: Production Readiness

**Tasks**:

1. **Read**: [ASP.NET Core — Publish and Deploy](https://learn.microsoft.com/aspnet/core/host-and-deploy/)
2. **Read**: [Serilog Documentation](https://serilog.net/)
3. **Read**: [ASP.NET Core — Logging](https://learn.microsoft.com/aspnet/core/fundamentals/logging/)
4. **Practice**: Analyze frontend build output (`npm run build`)
5. **Practice**: Test published backend application locally

**Optional Challenge**:

- Implement Application Insights telemetry
- Set up structured logging to Azure Blob Storage
- Create a health check endpoint

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Publishing | [ASP.NET Core — Publish](https://learn.microsoft.com/aspnet/core/host-and-deploy/) | Deployment guide |
| Serilog | [Serilog Docs](https://serilog.net/) | Structured logging |
| Logging | [ASP.NET Core — Logging](https://learn.microsoft.com/aspnet/core/fundamentals/logging/) | Built-in framework |
| Vite Build | [Vite — Building for Production](https://vitejs.dev/guide/build.html) | Frontend optimization |
| Bundle Analysis | [Bundle Buddy](https://www.npmjs.com/package/vite-plugin-visualizer) | Analyze bundle size |

---

## Key Takeaways

✓ Built frontend for production with Vite  
✓ Published backend with `dotnet publish`  
✓ Configured structured logging with Serilog  
✓ Added logging to application events  
✓ Tested production build locally  

---

## Next Lesson Preview

**Lesson 22 — Deploy Frontend (Netlify/Vercel)**: We'll deploy the static frontend to a CDN platform.
