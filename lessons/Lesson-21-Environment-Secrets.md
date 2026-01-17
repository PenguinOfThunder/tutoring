# Lesson 20: Environment & Secrets

## Objective

Use environment variables and `.dotnet user-secrets` for local development; manage configuration for different environments (dev, test, production).

---

## In-Class Materials

### Topics Covered

1. **Configuration Providers**:
   - `appsettings.json` (shared configuration)
   - `appsettings.{Environment}.json` (environment-specific)
   - Environment variables
   - User secrets (local development)
2. **Secrets Management**:
   - Never commit secrets to version control
   - `.gitignore` for sensitive files
   - `dotnet user-secrets` tool
3. **Environment Variables**:
   - `ASPNETCORE_ENVIRONMENT` for selecting environment
   - Custom environment variables
   - Docker environment variables
4. **Connection Strings**:
   - Configuring per environment
   - Different databases (SQLite dev, Postgres production)
5. **Dependency Injection**:
   - Reading configuration in services
   - `IConfiguration` interface
   - Options pattern (`IOptions<T>`)

### Key Concepts

- **Secrets**: Credentials, API keys, connection strings (never commit)
- **Configuration**: Settings that change per environment
- **Options Pattern**: Strongly-typed configuration classes
- **Twelve-Factor App**: Best practices for configuration

---

## In-Class Exercise (15–20 minutes)

**Task: Set up environment-specific configuration and use user secrets**

1. Initialize user secrets (run in project directory):

   ```bash
   cd backend/TaskApi
   dotnet user-secrets init
   ```

   - Creates a `secrets.json` file in user home directory
   - Never committed to version control

2. Add a secret (e.g., JWT signing key):

   ```bash
   dotnet user-secrets set "Jwt:SigningKey" "your-very-secret-key-min-256-bits-long-xxxxx"
   dotnet user-secrets set "Jwt:Issuer" "https://localhost:7001"
   dotnet user-secrets set "Jwt:Audience" "TaskAppClient"
   ```

3. View all secrets:

   ```bash
   dotnet user-secrets list
   ```

4. Create environment-specific config files:

   **`appsettings.Development.json`**:

   ```json
   {
     "ConnectionStrings": {
       "DefaultConnection": "Data Source=tasks.db"
     },
     "Logging": {
       "LogLevel": {
         "Default": "Debug"
       }
     },
     "AllowedHosts": "*"
   }
   ```

   **`appsettings.Production.json`**:

   ```json
   {
     "ConnectionStrings": {
       "DefaultConnection": "Server=prod-db-server;Database=TaskDb;User Id=sa;Password=${DB_PASSWORD}"
     },
     "Logging": {
       "LogLevel": {
         "Default": "Error"
       }
     },
     "AllowedHosts": "app.example.com"
   }
   ```

5. Create a configuration class (`Models/JwtOptions.cs`):

   ```csharp
   namespace TaskApi.Models;

   public class JwtOptions
   {
       public string SigningKey { get; set; } = string.Empty;
       public string Issuer { get; set; } = string.Empty;
       public string Audience { get; set; } = string.Empty;
       public int ExpirationMinutes { get; set; } = 60;
   }
   ```

6. Register options in `Program.cs`:

   ```csharp
   var builder = WebApplicationBuilder.CreateBuilder(args);

   // Bind JWT options from configuration
   var jwtOptions = new JwtOptions();
   builder.Configuration.GetSection("Jwt").Bind(jwtOptions);
   builder.Services.AddSingleton(jwtOptions);

   // Or use IOptions pattern:
   builder.Services.Configure<JwtOptions>(
       builder.Configuration.GetSection("Jwt"));

   // ... rest of configuration
   ```

7. Use configuration in a service:

   ```csharp
   public class TokenService
   {
       private readonly JwtOptions _jwtOptions;

       public TokenService(IOptions<JwtOptions> options)
       {
           _jwtOptions = options.Value;
       }

       public string GenerateToken(IdentityUser user)
       {
           var key = new SymmetricSecurityKey(
               Encoding.UTF8.GetBytes(_jwtOptions.SigningKey));
           
           var credentials = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);
           
           var token = new JwtSecurityToken(
               issuer: _jwtOptions.Issuer,
               audience: _jwtOptions.Audience,
               expires: DateTime.UtcNow.AddMinutes(_jwtOptions.ExpirationMinutes),
               signingCredentials: credentials
           );

           return new JwtSecurityTokenHandler().WriteToken(token);
       }
   }
   ```

8. Set environment and run:

   ```bash
   # Set environment (defaults to Development)
   export ASPNETCORE_ENVIRONMENT=Development
   dotnet run

   # Or run in Production
   export ASPNETCORE_ENVIRONMENT=Production
   dotnet run
   ```

9. Verify secrets are not in source control:

   ```bash
   # Ensure user-secrets directory is in .gitignore
   echo "**/UserSecretsId" >> .gitignore
   ```

10. Commit your work:

    ```bash
    git add appsettings.*.json Program.cs
    git commit -m "Add environment-specific configuration and user secrets"
    ```

---

## Homework Assignment

**Reading & Practice**: Configuration Management

**Tasks**:

1. **Read**: [ASP.NET Core — Configuration](https://learn.microsoft.com/aspnet/core/fundamentals/configuration/)
2. **Read**: [ASP.NET Core — User Secrets](https://learn.microsoft.com/aspnet/core/security/app-secrets)
3. **Read**: [Twelve-Factor App — Config](https://12factor.net/config)
4. **Practice**: Set up multiple environment configurations
5. **Practice**: Test that secrets don't appear in logs or error messages

**Optional Challenge**:

- Use environment variables for database connection strings
- Implement environment-specific feature flags
- Create a configuration validation class

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Configuration | [Microsoft Docs](https://learn.microsoft.com/aspnet/core/fundamentals/configuration/) | Complete guide |
| User Secrets | [User Secrets Tool](https://learn.microsoft.com/aspnet/core/security/app-secrets) | Local development |
| Twelve-Factor App | [12factor.net](https://12factor.net/config) | Best practices |
| Options Pattern | [Options Pattern](https://learn.microsoft.com/aspnet/core/fundamentals/configuration/options) | Strongly-typed config |
| Environment Vars | [Environment Variables](https://en.wikipedia.org/wiki/Environment_variable) | General concept |

---

## Key Takeaways

✓ Initialized user secrets for local development  
✓ Created environment-specific config files  
✓ Used IOptions pattern for type-safe configuration  
✓ Managed secrets securely without committing them  
✓ Tested configuration in different environments  

---

## Next Lesson Preview

**Lesson 21 — Production Build & Logging**: We'll create production-ready builds and set up comprehensive logging.
