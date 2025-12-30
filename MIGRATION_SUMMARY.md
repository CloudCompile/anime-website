# GitHub Pages Migration Summary

## What Changed

This repository has been configured to support deployment to GitHub Pages as a static website.

### Files Modified

1. **`frontend/next.config.mjs`**
   - Added `output: 'export'` to enable static site generation
   - Removed `async redirects()` function (not compatible with static export)
   - Added comment explaining redirect limitation

2. **`README.md`**
   - Added new "GitHub Pages" section under "Quick Deploy"
   - Includes step-by-step deployment instructions
   - Lists all required GitHub Secrets
   - Notes about backend server requirement

### Files Created

1. **`.github/workflows/deploy.yml`**
   - GitHub Actions workflow for automated deployment
   - Triggers on push to `main` branch
   - Builds the Next.js app with all environment variables
   - Deploys to GitHub Pages automatically

2. **`GITHUB_PAGES_SETUP.md`**
   - Comprehensive deployment guide
   - Detailed setup instructions for:
     - GitHub Pages configuration
     - Repository secrets
     - Backend deployment options
     - Firebase configuration
     - Anilist OAuth setup
   - Troubleshooting section

3. **`frontend/public/.nojekyll`**
   - Empty file to prevent GitHub Pages from using Jekyll
   - Ensures all Next.js files are served correctly

## How It Works

### Build Process

1. When code is pushed to the `main` branch, GitHub Actions triggers
2. The workflow installs dependencies in the `frontend` folder
3. Next.js builds a static site to the `frontend/out` directory
4. The static files are uploaded to GitHub Pages
5. Your site is automatically deployed

### Static Export Limitations

Next.js static export (`output: 'export'`) has some limitations:

- **Server-side features are not available:**
  - API Routes (use external backend instead)
  - Incremental Static Regeneration
  - Server-side redirects
  - Middleware

- **Handled by this setup:**
  - Images already set to `unoptimized: true`
  - External backend server for API calls
  - Client-side routing for navigation

### Backend Requirement

The backend server in the `/backend` folder **must be deployed separately** because:
- GitHub Pages only hosts static files
- The backend requires Node.js and Redis runtime
- Suggested hosting options: Vercel, Render, Heroku, Railway

## Next Steps for Users

To deploy your fork to GitHub Pages:

1. **Read the setup guide**: `GITHUB_PAGES_SETUP.md`

2. **Enable GitHub Pages**:
   - Go to Settings → Pages
   - Set source to "GitHub Actions"

3. **Add repository secrets** (Settings → Secrets and variables → Actions):
   - All Firebase credentials
   - Anilist OAuth credentials
   - Backend server URL
   - Your GitHub Pages URL
   - API URLs

4. **Deploy the backend** to Vercel/Render

5. **Push to main branch** to trigger deployment

## Important Notes

### Environment Variables

All environment variables with `NEXT_PUBLIC_` prefix are embedded into the static build at build time. Make sure to:
- Set them as GitHub Secrets
- Update them and rebuild if they change
- Don't commit them to the repository

### URL Configuration

Set `NEXT_PUBLIC_WEBSITE_ORIGIN_URL` to your actual GitHub Pages URL:
- Format: `https://username.github.io/repo-name`
- Also update in Firebase authorized domains
- And in Anilist OAuth redirect URIs

### First Deployment

The first deployment may take 5-10 minutes to complete and propagate.

### Updates

After initial setup, any push to `main` will automatically rebuild and redeploy.

## Testing

To test the static build locally:

```bash
cd frontend
npm install
npm run build
npx serve out
```

This will serve the static site at http://localhost:3000

## Troubleshooting

If deployment fails:
1. Check the Actions tab for error logs
2. Verify all secrets are set correctly
3. Ensure secret names match exactly (case-sensitive)
4. Check that backend server is accessible

## Differences from Vercel Deployment

| Feature | Vercel | GitHub Pages |
|---------|--------|--------------|
| API Routes | ✅ Supported | ❌ Not supported (use external backend) |
| Server-side redirects | ✅ Supported | ❌ Not supported |
| Automatic HTTPS | ✅ Yes | ✅ Yes |
| Custom domains | ✅ Free | ✅ Free |
| Build time | ~2-3 min | ~3-5 min |
| Cost | Free tier | Completely free |
| Backend hosting | ✅ Can host both | ❌ Static only |

## Compatibility

This configuration maintains compatibility with:
- Vercel deployment (can still deploy to Vercel)
- Local development (`npm run dev`)
- The existing backend server

You can deploy to both GitHub Pages and Vercel simultaneously if desired.
