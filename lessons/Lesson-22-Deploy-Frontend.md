# Lesson 22: Deploy Frontend (Netlify/Vercel)

## Objective

Deploy static frontend to Netlify or Vercel; configure environment variables and redirects for the SPA.

---

## In-Class Materials

### Topics Covered

1. **Deployment Platforms**:
   - Netlify and Vercel: free tier options
   - Git integration: automatic builds on push
   - Custom domains and HTTPS
2. **Build Configuration**:
   - Build command: `npm run build`
   - Publish directory: `dist/`
   - Environment variables for different stages
3. **SPA Routing**:
   - Hash-based routing (no redirects needed)
   - History-based routing (requires fallback to index.html)
   - Netlify redirects file: `_redirects`
4. **Environment Variables**:
   - API endpoint URL (dev vs production)
   - Feature flags
   - Analytics keys
5. **Pre-deployment Checklist**:
   - Tests passing
   - No console errors
   - Build succeeds
   - Bundle size reasonable

### Key Concepts

- **CDN**: Content Delivery Network for fast asset delivery
- **Static Site**: No server-side processing needed
- **Git Integration**: Automatic deployment on push
- **Environment Parity**: Same config for all environments

---

## In-Class Exercise (15–20 minutes)

**Task: Deploy frontend to Netlify or Vercel**

**Option A: Deploy to Netlify**

1. Create a Netlify account at [netlify.com](https://www.netlify.com/)

2. Create a `netlify.toml` configuration file:

   ```toml
   [build]
   command = "npm run build"
   publish = "dist"

   [build.environment]
   NODE_VERSION = "18"

   [[redirects]]
   from = "/*"
   to = "/index.html"
   status = 200

   [context.production]
   environment = { API_URL = "https://api.example.com" }

   [context.develop]
   environment = { API_URL = "https://api-dev.example.com" }
   ```

3. Connect your GitHub repository:
   - Click "New site from Git"
   - Select your GitHub repo
   - Netlify detects `netlify.toml` automatically

4. Set environment variables:
   - Go to Site Settings → Build & Deploy → Environment
   - Add `VITE_API_URL` for your API endpoint

5. Update frontend to use environment variable (`frontend/modules/api.ts`):

   ```typescript
   const API_BASE = import.meta.env.VITE_API_URL || 'https://localhost:7001/api';
   ```

6. Trigger a deploy by pushing to GitHub:

   ```bash
   git push
   ```

   - Netlify automatically builds and deploys

7. View deployment status at <https://app.netlify.com/>

**Option B: Deploy to Vercel**

1. Create a Vercel account at [vercel.com](https://vercel.com/)

2. Create a `vercel.json` configuration:

   ```json
   {
     "buildCommand": "npm run build",
     "outputDirectory": "dist",
     "env": {
       "VITE_API_URL": "@api_url"
     },
     "rewrites": [
       {
         "source": "/(.*)",
         "destination": "/index.html"
       }
     ]
   }
   ```

3. Import your GitHub repository:
   - Click "Add new..." → "Project"
   - Select your repo
   - Vercel auto-detects configuration

4. Set environment variables:
   - Go to Settings → Environment Variables
   - Add `VITE_API_URL` with your API endpoint

5. Deploy by pushing to GitHub:

   ```bash
   git push
   ```

6. View your live site at the provided Vercel URL

**For Both Platforms:**

1. Create a `.env.production` file (for local testing):

   ```env
   VITE_API_URL=https://api.example.com
   ```

2. Test the deployment:
   - Visit the deployed site
   - Add, update, delete tasks
   - Check browser console for errors
   - Verify API calls go to correct endpoint

3. Set a custom domain (optional):
    - Go to domain settings
    - Point your domain DNS to the platform
    - Enable automatic HTTPS

4. Commit your deployment configuration:

    ```bash
    git add netlify.toml # or vercel.json
    git add .env.production
    git commit -m "Add deployment configuration for Netlify/Vercel"
    git push
    ```

---

## Homework Assignment

**Reading & Practice**: Frontend Deployment

**Tasks**:

1. **Read**: [Netlify Docs — Deploying a Vite App](https://docs.netlify.com/frameworks-and-platforms/frameworks/vite/)
2. **Read**: [Vercel Docs — Deploying a Vite App](https://vercel.com/templates/vue/vite)
3. **Read**: [Environment Variables in Vite](https://vitejs.dev/guide/env-and-mode.html)
4. **Practice**: Deploy your frontend to Netlify or Vercel
5. **Practice**: Test the deployment with real API calls

**Optional Challenge**:

- Set up both Netlify and Vercel deployments
- Configure custom domain and HTTPS
- Set up preview deployments for PRs

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Netlify Docs | [docs.netlify.com](https://docs.netlify.com/) | Deployment platform |
| Vercel Docs | [vercel.com/docs](https://vercel.com/docs) | Alternative platform |
| Vite Environment | [Vite Env Guide](https://vitejs.dev/guide/env-and-mode.html) | Environment variables |
| SPA Routing | [SPA Routing Patterns](https://docs.netlify.com/routing/overview/) | Handling client-side routes |
| DNS Setup | [DNS Documentation](https://support.google.com/domains/answer/3355416) | Custom domains |

---

## Key Takeaways

✓ Deployed frontend to Netlify or Vercel  
✓ Configured build and publish settings  
✓ Set up environment variables per environment  
✓ Handled SPA routing with redirects  
✓ Verified deployment and API integration  

---

## Next Lesson Preview

**Lesson 23 — Dockerize Frontend, Backend & Database**: We'll create Docker images for all services and run them locally.
