# Lesson 14: User Accounts & Passwords (ASP.NET Identity)

## Objective

Add user model and hashed passwords using ASP.NET Core Identity; implement registration and login endpoints.

---

## In-Class Materials

### Topics Covered

1. **Authentication vs Authorization**:
   - Authentication: Verifying identity (who are you?)
   - Authorization: Checking permissions (what can you do?)
2. **ASP.NET Core Identity**:
   - Built-in user management system
   - IdentityUser model and IdentityRole
   - UserManager<T> for user operations
3. **Password Security**:
   - Hashing vs encryption
   - Salting and bcrypt
   - OWASP password guidelines
4. **Identity Configuration**:
   - Password complexity rules
   - Lockout policies
   - Role-based access control
5. **User Registration and Login**:
   - Creating users securely
   - Verifying passwords
   - Token generation (JWT)

### Key Concepts

- **Hashing**: One-way function to securely store passwords
- **Salt**: Random data added to hash to prevent rainbow tables
- **Identity**: ASP.NET Core's framework for user management
- **Tokens**: Credentials for subsequent requests

---

## In-Class Exercise (15–20 minutes)

**Task: Set up ASP.NET Core Identity with registration and login**

1. Install Identity packages:

   ```bash
   cd backend/TaskApi
   dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
   dotnet add package Microsoft.IdentityModel.Tokens
   dotnet add package System.IdentityModel.Tokens.Jwt
   ```

2. Update `Data/TaskContext.cs` to use IdentityDbContext:

   ```csharp
   using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
   using Microsoft.EntityFrameworkCore;
   using TaskApi.Models;

   namespace TaskApi.Data;

   public class TaskContext : IdentityDbContext<IdentityUser>
   {
       public DbSet<Task> Tasks { get; set; } = null!;

       public TaskContext(DbContextOptions<TaskContext> options) : base(options) { }

       protected override void OnModelCreating(ModelBuilder modelBuilder)
       {
           base.OnModelCreating(modelBuilder);

           modelBuilder.Entity<Task>()
               .HasIndex(t => t.Completed)
               .HasName("IX_Tasks_Completed");
       }
   }
   ```

3. Create Auth DTOs (`Models/AuthDto.cs`):

   ```csharp
   namespace TaskApi.Models;

   public class RegisterRequest
   {
       public string Email { get; set; } = string.Empty;
       public string Password { get; set; } = string.Empty;
   }

   public class LoginRequest
   {
       public string Email { get; set; } = string.Empty;
       public string Password { get; set; } = string.Empty;
   }

   public class AuthResponse
   {
       public bool Success { get; set; }
       public string? Token { get; set; }
       public string? Message { get; set; }
   }
   ```

4. Configure Identity in `Program.cs`:

   ```csharp
   using Microsoft.AspNetCore.Identity;
   using Microsoft.EntityFrameworkCore;
   using Microsoft.IdentityModel.Tokens;
   using System.Text;
   using TaskApi.Data;

   var builder = WebApplicationBuilder.CreateBuilder(args);

   builder.Services.AddControllers();
   builder.Services.AddDbContext<TaskContext>(options =>
       options.UseSqlite("Data Source=tasks.db"));

   // Add Identity
   builder.Services
       .AddIdentity<IdentityUser, IdentityRole>()
       .AddEntityFrameworkStores<TaskContext>();

   // Configure Identity options
   builder.Services.Configure<IdentityOptions>(options =>
   {
       options.Password.RequireDigit = true;
       options.Password.RequiredLength = 8;
       options.Password.RequireNonAlphanumeric = false;
   });

   var app = builder.Build();

   app.UseHttpsRedirection();
   app.UseAuthentication();
   app.UseAuthorization();
   app.MapControllers();

   app.Run();
   ```

5. Create Auth controller (`Controllers/AuthController.cs`):

   ```csharp
   using Microsoft.AspNetCore.Identity;
   using Microsoft.AspNetCore.Mvc;
   using TaskApi.Models;

   namespace TaskApi.Controllers;

   [ApiController]
   [Route("api/[controller]")]
   public class AuthController : ControllerBase
   {
       private readonly UserManager<IdentityUser> _userManager;
       private readonly SignInManager<IdentityUser> _signInManager;

       public AuthController(UserManager<IdentityUser> userManager, SignInManager<IdentityUser> signInManager)
       {
           _userManager = userManager;
           _signInManager = signInManager;
       }

       [HttpPost("register")]
       public async Task<IActionResult> Register([FromBody] RegisterRequest request)
       {
           var user = new IdentityUser { UserName = request.Email, Email = request.Email };
           var result = await _userManager.CreateAsync(user, request.Password);

           if (!result.Succeeded)
               return BadRequest(new { errors = result.Errors.Select(e => e.Description) });

           return Ok(new { message = "User registered successfully" });
       }

       [HttpPost("login")]
       public async Task<IActionResult> Login([FromBody] LoginRequest request)
       {
           var user = await _userManager.FindByEmailAsync(request.Email);
           if (user == null)
               return Unauthorized(new { message = "Invalid email or password" });

           var result = await _signInManager.CheckPasswordSignInAsync(user, request.Password, false);
           if (!result.Succeeded)
               return Unauthorized(new { message = "Invalid email or password" });

           // TODO: Generate JWT token in next steps
           return Ok(new { message = "Login successful" });
       }
   }
   ```

6. Create and apply migration:

   ```bash
   dotnet ef migrations add AddIdentity
   dotnet ef database update
   ```

7. Test registration and login:

   ```bash
   # Register
   curl -X POST https://localhost:7001/api/auth/register -k \
     -H "Content-Type: application/json" \
     -d '{"email":"user@example.com","password":"SecurePass123"}'

   # Login
   curl -X POST https://localhost:7001/api/auth/login -k \
     -H "Content-Type: application/json" \
     -d '{"email":"user@example.com","password":"SecurePass123"}'
   ```

8. Commit your work:

   ```bash
   git add backend/
   git commit -m "Add ASP.NET Core Identity with registration and login"
   ```

---

## Homework Assignment

**Reading & Practice**: Identity and Security

**Tasks**:

1. **Read**: [ASP.NET Core Identity Docs](https://learn.microsoft.com/aspnet/core/security/authentication/identity)
2. **Read**: [OWASP — Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
3. **Read**: [JWT Introduction](https://jwt.io/introduction)
4. **Practice**: Test password validation by trying weak passwords
5. **Practice**: Inspect the database to see hashed passwords

**Optional Challenge**:

- Implement JWT token generation in the login endpoint
- Add email confirmation flow

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| ASP.NET Identity | [Microsoft Docs](https://learn.microsoft.com/aspnet/core/security/authentication/identity) | User management |
| Password Security | [OWASP Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) | Best practices |
| JWT | [JWT.io](https://jwt.io/introduction) | Token format |
| bcrypt | [bcrypt Official](https://bcrypt.online/) | Password hashing |

---

## Key Takeaways

✓ Set up ASP.NET Core Identity framework  
✓ Configured password complexity rules  
✓ Implemented registration endpoint  
✓ Implemented login endpoint  
✓ Verified password hashing in the database  

---

## Next Lesson Preview

**Lesson 15 — Protecting Routes / Middleware**: We'll require authentication for sensitive operations using [Authorize] attributes.
