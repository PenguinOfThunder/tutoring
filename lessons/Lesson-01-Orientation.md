# Lesson 1: Orientation & Tooling

## Objective

Set expectations for the tutoring course; install essential developer tools (`git`, VS Code, Node.js); and create a repository skeleton for the full-stack task/notes web app.

---

## In-Class Materials

### Topics Covered

1. **Course Overview**: Full-stack web development roadmap (frontend → backend → database → deployment)
2. **Development Environment**:
   - VS Code installation and essential extensions
   - WSL2 (Windows Subsystem for Linux 2) and Docker basics
   - Installing Docker Desktop with WSL2 integration
   - `.NET` CLI and creating a dev container
   - Opening and working in dev containers with VS Code
   - `git` basics (clone, commit, push)
   - Note: Node.js and npm will be introduced in Lesson 6 for frontend tooling
3. **Repository Setup**:
   - Initialize GitHub repository
   - Create project structure (backend/, frontend/, docs/)
   - Add `.devcontainer/` configuration
   - Add `.gitignore` for .NET and Docker
   - Write initial README with project goals and tech stack

### Key Concepts

- **Version Control**: Why we use Git and GitHub
- **Containerization**: Why Docker and dev containers ensure consistency
- **Dev Containers**: Reproducible development environments via Docker
- **Project Structure**: Organizing frontend and backend code separately
- **Development Workflow**: Commit messages, branching (optional for first lesson)

---

## In-Class Exercise (15–20 minutes)

**Task: Set up the project skeleton with Git, Docker, and dev container**

### Part 1: Install Docker in WSL2

If you haven't already, install Docker Desktop with WSL2 integration:

1. **Download Docker Desktop**:
   - Visit [Docker's official download page](https://www.docker.com/products/docker-desktop)
   - Choose the installer for Windows
   - Run the installer and follow prompts
   - Enable "WSL 2 based engine" during setup (recommended)

2. **Verify Docker is installed**:

   ```bash
   # Open PowerShell or WSL2 terminal
   docker --version
   docker run hello-world
   ```

   - Should output Docker version and run a test container

3. **Enable WSL2 integration** (if not already enabled):
   - Open Docker Desktop
   - Go to Settings → Resources → WSL Integration
   - Enable integration for your WSL2 distribution
   - Click "Apply & Restart"

4. **Verify WSL2 has Docker access**:

   ```bash
   # From within WSL2
   docker ps
   docker --version
   ```

### Part 2: Create Project Structure

1. Create a directory for the project:

   ```bash
   mkdir my-task-app
   cd my-task-app
   ```

2. Initialize a Git repository:

   ```bash
   git init
   ```

3. Create the folder structure:

   ```bash
   mkdir backend frontend docs .devcontainer
   ```

4. Your folder structure should look like:

   ```
   my-task-app/
   ├── backend/
   ├── frontend/
   ├── docs/
   ├── .devcontainer/
   ├── .gitignore
   └── README.md
   ```

### Part 3: Create Dev Container Configuration

1. Create `.devcontainer/Dockerfile`:

   ```dockerfile
   FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

   # Install .NET SDK
   RUN apt-get update && apt-get install -y \
       dotnet-sdk-7.0 \
       git \
       curl \
       && rm -rf /var/lib/apt/lists/*

   # Optional: Install Node.js for frontend work (Lesson 6+)
   # RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
   #     && apt-get install -y nodejs

   WORKDIR /workspace
   ```

2. Create `.devcontainer/devcontainer.json`:

   ```json
   {
     "name": "Task App Dev Container",
     "dockerFile": "Dockerfile",
     "context": "..",
     "remoteUser": "vscode",
     "workspaceFolder": "/workspace",
     "mounts": [
       "source=${localEnv:HOME}${localEnv:USERPROFILE}/.ssh,target=/home/vscode/.ssh,readonly"
     ],
     "customizations": {
       "vscode": {
         "extensions": [
           "ms-vscode-remote.remote-containers",
           "ms-dotnettools.csharp",
           "ms-dotnettools.vscode-dotnet-runtime",
           "ms-vscode.makefile-tools",
           "github.copilot"
         ]
       }
     },
     "postCreateCommand": "dotnet --version && git --version",
     "forwardPorts": [5000, 5173],
     "forwardPortsActivity": {
       "5000": "silent",
       "5173": "silent"
     }
   }
   ```

### Part 4: Open in Dev Container

**Prerequisites**: You must have the **Dev Containers** extension installed in VS Code before opening a dev container.

1. **Install the Dev Containers extension** (if you haven't already):
   - Open VS Code
   - Click the Extensions icon in the left sidebar (or press `Ctrl+Shift+X`)
   - Search for "Dev Containers"
   - Install the official extension by Microsoft (`ms-vscode-remote.remote-containers`)

2. **Open the project in VS Code**:

   ```bash
   code .
   ```

3. **Open in dev container**:
   - Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on macOS) to open Command Palette
   - Type "Dev Containers: Reopen in Container"
   - Select it and wait for the container to build
   - VS Code will reload inside the container environment

4. **Verify you're in the container**:
   - Look at the bottom-left corner of VS Code
   - Should show "Dev Container" indicator
   - Open an integrated terminal and run:

     ```bash
     dotnet --version
     git --version
     ```

### Part 5: Set up Version Control

1. Create a README.md describing the project:

   ```markdown
   # My Task Manager App

   A full-stack task/notes manager built with modern web technologies.

   ## Tech Stack
   - **Frontend**: TypeScript + Vite + Bootstrap
   - **Backend**: ASP.NET Core + Entity Framework Core
   - **Database**: SQLite (development) / PostgreSQL (production)
   - **Runtime**: Docker + WSL2
   - **Version Control**: Git + GitHub

   ## Getting Started

   ### Prerequisites
   - Docker Desktop with WSL2 enabled
   - VS Code with Dev Containers extension
   - Git installed in WSL2

   ### Development Setup

   1. Clone the repository:
      ```bash
      git clone <repo-url>
      cd my-task-app
      ```

   1. Open in dev container:
      - Open in VS Code
      - Press `Ctrl+Shift+P` → "Dev Containers: Reopen in Container"
      - Wait for container to build

   2. Start development:

      ```bash
      cd backend
      dotnet run
      ```

   ## Project Structure

   - `backend/` — ASP.NET Core REST API
   - `frontend/` — TypeScript + Vite single-page app
   - `docs/` — Documentation and notes
   - `.devcontainer/` — Dev container configuration

   ## Next Steps

   - Lesson 2: Build HTML structure
   - Lesson 3: Add styling with Bootstrap
   - Lesson 9: Create REST API endpoints

   ```

2. Create a basic `.gitignore` for .NET and Docker:

   ```
   # Build results
   bin/
   obj/
   dist/
   
   # User-specific
   *.user
   *.userprefs
   
   # OS
   .DS_Store
   Thumbs.db
   
   # Editor
   .vscode/
   .idea/
   *.swp
   
   # Environment
   .env
   .env.local
   
   # Dependencies
   node_modules/
   
   # Database
   *.db
   *.sqlite
   
   # Dev container
   .devcontainer/.env
   
   # Logs
   logs/
   *.log
   ```

3. Make your first commit:

   ```bash
   git add .
   git commit -m "Initial project setup with dev container"
   ```

### Part 6: Verify Everything Works

1. Confirm dev container is running:
   - Terminal should show you're in `/workspace`
   - Running `pwd` should show `/workspace` or similar
   - `dotnet --version` should output a version number

2. Create a test .NET app to verify setup:

   ```bash
   cd backend
   dotnet new console -n TestApp
   cd TestApp
   dotnet run
   ```

3. Clean up test app (optional):

   ```bash
   cd ../..
   rm -rf backend/TestApp
   git add -A && git commit -m "Remove test app"
   ```

4. **(Optional) Push to GitHub**:

   ```bash
   git remote add origin https://github.com/your-username/my-task-app.git
   git branch -M main
   git push -u origin main
   ```

---

## Homework Assignment

**Reading**: Introduction to HTTP, Client-Server Architecture, Docker, and Dev Containers

**Tasks**:

1. **Read**: [MDN — An overview of HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
2. **Read**: [MDN — Client-Server Overview](https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Client-Server_overview)
3. **Read**: [Docker — What is a Container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)
4. **Read**: [VS Code — Dev Containers](https://code.visualstudio.com/docs/devcontainers/containers)
5. **Reflection**: Write 3–5 sentences in your repo (e.g., in `docs/notes.md`) explaining:
   - What a "client" and "server" do
   - Why we use Docker for development
   - Benefits of dev containers for team consistency and security

**Practice**:

1. Get comfortable navigating in the dev container terminal
2. Try running `dotnet new` to create a test project
3. Explore the `.devcontainer/` configuration files

**Optional Challenge**:

- Look up some basic Linux commands (`cd`, `ls`, `mv`, `cat`) and learn what they do. Learn about pipes (`|`) and redirects (`>`, `<`)
- Add Node.js to the Dockerfile and rebuild the container (Lesson 6+)
- Share your dev container setup with a peer and verify they can open it successfully

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| HTTP Overview | [MDN HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP) | Foundational concept |
| Client-Server Model | [MDN Client-Server](https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Client-Server_overview) | How the web works |
| Docker & Containers | [Docker Docs — What is a Container?](https://www.docker.com/resources/what-is-container/) | Containerization basics |
| Docker Desktop | [Docker Desktop Download](https://www.docker.com/products/docker-desktop) | Windows/Mac installer |
| WSL2 Setup | [Microsoft — Install WSL2](https://learn.microsoft.com/en-us/windows/wsl/install) | Development environment |
| Dev Containers | [VS Code — Dev Containers](https://code.visualstudio.com/docs/devcontainers/containers) | Complete guide |
| Dev Container JSON | [Dev Container Config Reference](https://containers.dev/implementors/json_reference/) | devcontainer.json syntax |
| Git Basics | [Git Handbook](https://guides.github.com/introduction/git-handbook/) | Version control fundamentals |
| VS Code Tips | [VS Code Docs](https://code.visualstudio.com/docs) | Editor and keyboard shortcuts |

---

## Key Takeaways

✓ Installed Docker Desktop with WSL2 integration  
✓ Created a project structure with version control  
✓ Set up a dev container for reproducible development  
✓ Configured VS Code to work inside the container  
✓ Understood the client-server model  
✓ Made your first Git commit  

---

## Next Lesson Preview

**Lesson 2 — HTML Basics**: We'll build the structure of our task app using semantic HTML.
