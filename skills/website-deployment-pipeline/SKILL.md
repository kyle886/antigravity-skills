---
name: website-deployment-pipeline
description: Design and implement end-to-end website deployment pipelines including build, test, deploy, CDN purge, smoke test, and sitemap submission. Use when setting up CI/CD for static sites, SPAs, or JAMstack applications targeting platforms like Cloudflare Pages, Vercel, Netlify, or traditional hosting.
---

# Website Deployment Pipeline

End-to-end deployment pipeline design for modern web applications.

## Use this skill when

- Setting up CI/CD for a website or web application
- Migrating between hosting platforms
- Adding automated testing to a deployment pipeline
- Implementing blue-green or canary deployments
- Setting up preview deployments for pull requests

## Do not use this skill when

- Deploying backend services or APIs (use `deployment-engineer` instead)
- Setting up infrastructure (use `cloud-architect` instead)
- You only need to run a single deploy command

## Instructions

### Phase 1: Assess Current State

1. Identify the build system (Vite, Next.js, CRA, etc.)
2. Identify the hosting target and current deployment method
3. Check for existing CI/CD pipelines (GitHub Actions, GitLab CI, etc.)
4. Document environment variables required for production build
5. Check for pre-build steps (SEO generation, image optimization, type checking)

### Phase 2: Design Pipeline

6. Design the pipeline stages:

```
[Commit] → [Lint/Type Check] → [Test] → [Build] → [Preview] → [Deploy] → [Smoke Test] → [Post-Deploy]
```

**Lint/Type Check Stage:**

- Run ESLint, TypeScript compiler, Prettier
- Fail fast on errors

**Test Stage:**

- Unit tests (Vitest, Jest)
- E2E tests (Playwright, Cypress)
- Accessibility tests (axe-core)

**Build Stage:**

- Generate SEO files (sitemap, robots.txt)
- Optimize images
- Run production build
- Verify build output size

**Deploy Stage:**

- Upload dist/ to hosting platform
- Configure caching headers
- Set up redirects (SPA fallback for client-side routing)

**Smoke Test Stage:**

- Verify homepage loads (HTTP 200)
- Check critical pages respond
- Verify meta tags are present
- Check for JavaScript errors

**Post-Deploy Stage:**

- Submit sitemap to Google Search Console
- Purge CDN cache
- Notify team (Slack, email)
- Monitor error rates for 30 minutes

### Phase 3: Platform-Specific Setup

7. **SPA on any static host** — CRITICAL: Configure SPA fallback routing

```
# For Cloudflare Pages (_redirects file):
/*  /index.html  200

# For Netlify (_redirects file):
/*  /index.html  200

# For Vercel (vercel.json):
{ "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }

# For GoDaddy / Apache (.htaccess):
RewriteEngine On
RewriteBase /
RewriteRule ^index\.html$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.html [L]

# For GoDaddy / Nginx:
location / {
    try_files $uri $uri/ /index.html;
}
```

8. **GitHub Actions** example workflow:

```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck
      - run: npm run build
      # Add platform-specific deploy step here
```

### Phase 4: Rollback Strategy

9. Document rollback procedure:
   - How to revert to previous deployment
   - How long CDN caches take to expire
   - Whether database migrations are involved (if so, use migration-workflow)

## Safety

- Never deploy without running lint and type check first
- Always test SPA routing (refresh on sub-pages) after deployment
- Keep environment variables out of build logs
- Verify HTTPS is configured on the production domain
- Test redirects: www → non-www (or vice versa), HTTP → HTTPS
