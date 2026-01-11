# Lesson 7: Package Management & Vite

## Objective

Initialize `package.json`, set up Vite with TypeScript, and run the local dev server with hot module replacement (HMR).

---

## In-Class Materials

### Topics Covered

1. **npm Fundamentals**:
   - `package.json` structure and purpose
   - Installing dependencies (`npm install`)
   - `npm scripts` for common tasks
   - `node_modules` and `.gitignore`
2. **Vite Overview**:
   - Why use a bundler (code splitting, minification, HMR)
   - Vite vs Webpack: speed and developer experience
   - Vite configuration (`vite.config.ts`)
3. **Development Workflow**:
   - `npm run dev` to start the dev server
   - Hot Module Replacement (HMR) for fast feedback
   - Building for production with `npm run build`
4. **TypeScript with Vite**:
   - TypeScript compilation in the build pipeline
   - `tsconfig.json` configuration
   - Type-safe environment variables

### Key Concepts

- **Build Tools**: Automate compilation, bundling, and optimization
- **HMR**: See code changes instantly without full page reload
- **Dependency Management**: npm tracks and manages project dependencies
- **Development vs Production**: Different build outputs for dev and production

---

## In-Class Exercise (15–20 minutes)

**Task: Set up Vite project and run the dev server**

1. Initialize npm in the `frontend` directory:

   ```bash
   cd frontend
   npm init -y
   ```

2. Install Vite and TypeScript:

   ```bash
   npm install --save-dev vite typescript
   npm install --save-dev @types/node
   ```

3. Create `frontend/vite.config.ts`:

   ```typescript
   import { defineConfig } from 'vite'
   
   export default defineConfig({
     root: '.',
     build: {
       outDir: 'dist',
       minify: 'terser'
     },
     server: {
       port: 5173,
       open: true
     }
   })
   ```

4. Create `frontend/tsconfig.json`:

   ```json
   {
     "compilerOptions": {
       "target": "ES2020",
       "useDefineForClassFields": true,
       "lib": ["ES2020", "DOM", "DOM.Iterable"],
       "module": "ESNext",
       "skipLibCheck": true,
       "esModuleInterop": true,
       "strict": true,
       "forceConsistentCasingInFileNames": true,
       "resolveJsonModule": true,
       "moduleResolution": "bundler",
       "allowImportingTsExtensions": true,
       "noEmit": true
     },
     "include": ["*.ts"],
     "exclude": ["node_modules"]
   }
   ```

5. Update `frontend/package.json` with scripts:

   ```json
   {
     "scripts": {
       "dev": "vite",
       "build": "vite build",
       "preview": "vite preview"
     }
   }
   ```

6. Update `frontend/index.html` to reference `app.ts` directly:

   ```html
   <script type="module" src="app.ts"></script>
   ```

   (Remove the compiled `.js` reference)

7. Run the dev server:

   ```bash
   npm run dev
   ```

   - Browser should open at `http://localhost:5173`
   - Try editing `app.ts` and see HMR reload instantly

8. Test the production build:

   ```bash
   npm run build
   ```

   - Outputs to `dist/` directory

9. Commit your work:

   ```bash
   git add frontend/package.json frontend/vite.config.ts frontend/tsconfig.json
   git commit -m "Set up Vite and npm for TypeScript development"
   ```

---

## Homework Assignment

**Reading & Practice**: npm and Build Tools

**Tasks**:

1. **Read**: [npm Docs — package.json](https://docs.npmjs.com/cli/v9/configuring-npm/package-json)
2. **Read**: [Vite Guide — Getting Started](https://vitejs.dev/guide/)
3. **Read**: [Vite Guide — Features](https://vitejs.dev/guide/features.html)
4. **Practice**: Explore the `node_modules` directory and understand what dependencies are installed
5. **Practice**: Update `.gitignore` to exclude `node_modules`, `dist`, and `*.log`

**Optional Challenge**:

- Install a utility library (e.g., `lodash-es`) and use it in your code via `import`

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| npm Docs | [npm Documentation](https://docs.npmjs.com/) | Official npm reference |
| package.json | [npm — package.json](https://docs.npmjs.com/cli/v9/configuring-npm/package-json) | Configuration file |
| Vite Guide | [Vite Documentation](https://vitejs.dev/guide/) | Complete guide |
| Vite Features | [Vite — Features](https://vitejs.dev/guide/features.html) | HMR, plugins, etc. |
| TypeScript Config | [TypeScript — tsconfig.json](https://www.typescriptlang.org/tsconfig) | Compiler options |

---

## Key Takeaways

✓ Initialized npm project and package.json  
✓ Set up Vite with TypeScript configuration  
✓ Ran dev server with HMR  
✓ Built production-optimized bundle  
✓ Created npm scripts for common tasks  

---

## Next Lesson Preview

**Lesson 8 — Intro to Component Thinking / SPA Concepts**: We'll explore single-page app patterns and component architecture.
