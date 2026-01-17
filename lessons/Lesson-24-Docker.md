# Lesson 23: Dockerize Frontend, Backend & Database

## Objective

Create Dockerfiles for the frontend, backend, and database; run a multi-container application with Docker.

---

## In-Class Materials

### Topics Covered

1. **Docker Basics**:
   - Images (blueprints) vs containers (running instances)
   - Dockerfile syntax
   - Building images and running containers
2. **Frontend Dockerfile**:
   - Multi-stage build (node build stage → nginx runtime)
   - Static file serving
   - Production-optimized image
3. **Backend Dockerfile**:
   - Multi-stage build (SDK build → runtime)
   - ASP.NET Core runtime
   - Working directory and entry points
4. **Database Container**:
   - Running PostgreSQL in Docker
   - Volumes for persistent data
   - Connection strings for services
5. **Docker Compose**:
   - Orchestrating multiple services
   - Networks and service discovery
   - Environment variables and volumes

### Key Concepts

- **Image**: Template for containers (like a snapshot)
- **Container**: Running instance of an image
- **Layer**: Part of image built from Dockerfile instructions
- **Volume**: Persistent storage outside container

---

## In-Class Exercise (15–20 minutes)

**Task: Create Dockerfiles and run services with Docker Compose**

1. Create frontend Dockerfile (`frontend/Dockerfile`):

   ```dockerfile
   # Build stage
   FROM node:18-alpine as builder
   WORKDIR /app
   COPY package*.json ./
   RUN npm ci
   COPY . .
   RUN npm run build

   # Runtime stage
   FROM nginx:1.23-alpine
   COPY --from=builder /app/dist /usr/share/nginx/html
   COPY nginx.conf /etc/nginx/conf.d/default.conf
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```

2. Create frontend nginx config (`frontend/nginx.conf`):

   ```nginx
   server {
     listen 80;
     location / {
       root /usr/share/nginx/html;
       index index.html;
       try_files $uri $uri/ /index.html;
     }
   }
   ```

3. Create backend Dockerfile (`backend/TaskApi/Dockerfile`):

   ```dockerfile
   # Build stage
   FROM mcr.microsoft.com/dotnet/sdk:7.0 as builder
   WORKDIR /src
   COPY ["TaskApi/TaskApi.csproj", "TaskApi/"]
   RUN dotnet restore "TaskApi/TaskApi.csproj"
   COPY . .
   WORKDIR "/src/TaskApi"
   RUN dotnet build "TaskApi.csproj" -c Release -o /app/build

   FROM builder as publish
   RUN dotnet publish "TaskApi.csproj" -c Release -o /app/publish

   # Runtime stage
   FROM mcr.microsoft.com/dotnet/aspnet:7.0
   WORKDIR /app
   COPY --from=publish /app/publish .
   EXPOSE 80 443
   ENTRYPOINT ["dotnet", "TaskApi.dll"]
   ```

4. Create `docker-compose.yml`:

   ```yaml
   version: '3.8'

   services:
     frontend:
       build:
         context: ./frontend
         dockerfile: Dockerfile
       ports:
         - "80:80"
       environment:
         - VITE_API_URL=http://localhost:5000
       depends_on:
         - backend

     backend:
       build:
         context: ./backend
         dockerfile: TaskApi/Dockerfile
       ports:
         - "5000:80"
       environment:
         - ASPNETCORE_ENVIRONMENT=Production
         - ConnectionStrings__DefaultConnection=Server=db;Database=TaskDb;User Id=sa;Password=YourPassword123!;
       depends_on:
         - db
       healthcheck:
         test: ["CMD", "curl", "-f", "http://localhost:80/health"]
         interval: 10s
         timeout: 3s
         retries: 3

     db:
       image: postgres:15-alpine
       environment:
         POSTGRES_DB: TaskDb
         POSTGRES_USER: sa
         POSTGRES_PASSWORD: YourPassword123!
       volumes:
         - postgres_data:/var/lib/postgresql/data
       ports:
         - "5432:5432"
       healthcheck:
         test: ["CMD-SHELL", "pg_isready -U sa"]
         interval: 10s
         timeout: 3s
         retries: 3

   volumes:
     postgres_data:
   ```

5. Build and run the stack:

   ```bash
   docker-compose up --build
   ```

6. Verify services are running:

   ```bash
   docker-compose ps
   ```

7. Test the application:
   - Frontend: <http://localhost>
   - Backend API: <http://localhost:5000/api/tasks>
   - Database: localhost:5432

8. View logs:

   ```bash
   docker-compose logs -f backend
   docker-compose logs -f frontend
   ```

9. Stop services:

   ```bash
   docker-compose down
   ```

10. Commit your work:

    ```bash
    git add Dockerfile docker-compose.yml frontend/nginx.conf
    git commit -m "Add Docker support with multi-service compose"
    ```

---

## Homework Assignment

**Reading & Practice**: Docker and Containerization

**Tasks**:

1. **Read**: [Docker — Getting Started](https://docs.docker.com/get-started/)
2. **Read**: [Docker — Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)
3. **Read**: [Docker Compose — Overview](https://docs.docker.com/compose/)
4. **Practice**: Build images individually and run containers
5. **Practice**: Inspect running containers with `docker ps`, `docker logs`, `docker exec`

**Optional Challenge**:

- Add Redis for caching
- Implement health checks for all services
- Use environment file (`.env`) for secrets

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Docker Docs | [docs.docker.com](https://docs.docker.com/) | Official docs |
| Dockerfile | [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/) | Syntax guide |
| Docker Compose | [Compose Docs](https://docs.docker.com/compose/) | Multi-container |
| Best Practices | [Docker Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) | Optimization |
| ASP.NET Images | [Microsoft — Docker Images](https://mcr.microsoft.com/) | Official images |

---

## Key Takeaways

✓ Created multi-stage Dockerfiles for frontend and backend  
✓ Defined PostgreSQL container for database  
✓ Orchestrated services with Docker Compose  
✓ Verified services communicate correctly  
✓ Used health checks for reliability  

---

## Next Lesson Preview

**Lesson 24 — Local HTTPS, Reverse Proxy & Monitoring**: We'll add TLS certificates, reverse proxy routing, and monitoring.
