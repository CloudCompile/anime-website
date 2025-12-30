# GitHub Pages Deployment Guide

This guide will help you deploy the AniProject website to GitHub Pages.

## Prerequisites

Before deploying to GitHub Pages, you need to:

1. Have a GitHub account
2. Fork or clone this repository
3. Set up external services (Firebase, Anilist OAuth)
4. Deploy the backend server separately (Vercel, Render, etc.)

## Step-by-Step Deployment

### 1. Enable GitHub Pages

1. Go to your repository on GitHub
2. Click on **Settings** → **Pages** (in the left sidebar)
3. Under "Build and deployment":
   - Source: Select **GitHub Actions**
   - This enables the automated deployment workflow

### 2. Configure Repository Secrets

The website requires several environment variables to function. You need to add these as GitHub repository secrets:

1. Go to **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret** and add each of the following:

#### Required Secrets

| Secret Name | Description | Example |
|------------|-------------|---------|
| `NEXT_PUBLIC_BACKEND_URL` | URL to your backend server (must be deployed separately) | `https://your-backend.onrender.com` |
| `NEXT_PUBLIC_WEBSITE_ORIGIN_URL` | Your GitHub Pages URL | `https://yourusername.github.io/anime-website` |
| `NEXT_PUBLIC_ANILIST_CLIENT_ID` | Anilist OAuth client ID | Get from [Anilist Developer Settings](https://anilist.co/settings/developer) |
| `ANILIST_CLIENT_SECRET` | Anilist OAuth client secret | Get from [Anilist Developer Settings](https://anilist.co/settings/developer) |
| `NEXT_PUBLIC_FIREBASE_API_KEY` | Firebase API key | From Firebase Console |
| `NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN` | Firebase auth domain | From Firebase Console |
| `NEXT_PUBLIC_FIREBASE_PROJECT_ID` | Firebase project ID | From Firebase Console |
| `NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET` | Firebase storage bucket | From Firebase Console |
| `NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER` | Firebase messaging sender ID | From Firebase Console |
| `NEXT_PUBLIC_FIREBASE_APP_ID` | Firebase app ID | From Firebase Console |
| `NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID` | Firebase measurement ID | From Firebase Console |
| `NEXT_PUBLIC_FIREBASE_DATABASE_URL` | Firebase database URL | From Firebase Console |
| `NEXT_PUBLIC_ANIWATCH_API_URL` | Aniwatch API endpoint | e.g., `https://your-aniwatch-api.com` |
| `NEXT_PUBLIC_CONSUMET_API_URL` | Consumet API endpoint | e.g., `https://your-consumet-api.com` |

#### Optional Secrets

| Secret Name | Description |
|------------|-------------|
| `NEXT_PUBLIC_MEASUREMENT_ID` | Google Analytics measurement ID (optional) |

### 3. Deploy the Backend Server

**Important**: GitHub Pages only hosts static websites. The backend server must be deployed separately.

#### Option A: Deploy to Render

1. Create an account on [Render](https://render.com)
2. Create a new **Web Service**
3. Connect your GitHub repository
4. Set the following:
   - **Root Directory**: `backend`
   - **Build Command**: `npm install`
   - **Start Command**: `node server.js`
5. Add environment variables (if needed by the backend)
6. Deploy and copy the service URL
7. Use this URL as your `NEXT_PUBLIC_BACKEND_URL` secret

#### Option B: Deploy to Vercel

1. Create an account on [Vercel](https://vercel.com)
2. Import your GitHub repository
3. Configure:
   - **Framework Preset**: Other
   - **Root Directory**: `backend`
   - **Build Command**: (leave empty)
   - **Output Directory**: (leave empty)
4. Deploy and copy the deployment URL
5. Use this URL as your `NEXT_PUBLIC_BACKEND_URL` secret

### 4. Configure Firebase

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Create a new project or use existing one
3. Enable Authentication:
   - Go to **Authentication** → **Sign-in method**
   - Enable: Google, Email/Password, Anonymous
4. Enable Firestore Database:
   - Go to **Firestore Database** → **Create database**
   - Start in production mode
   - Update security rules (see frontend/README.md)
5. Add your GitHub Pages domain to authorized domains:
   - **Authentication** → **Settings** → **Authorized domains**
   - Add: `yourusername.github.io`

### 5. Configure Anilist OAuth

1. Log in to [Anilist](https://anilist.co)
2. Go to **Settings** → **Developer**
3. Click **Create New Client**
4. Fill in:
   - **Name**: Your app name
   - **Redirect URI**: `https://yourusername.github.io/your-repo-name/api/auth`
5. Save and copy the Client ID and Secret
6. Add these as GitHub secrets

### 6. Deploy

1. Push your code to the `main` branch:
   ```bash
   git add .
   git commit -m "Configure for GitHub Pages"
   git push origin main
   ```

2. The GitHub Actions workflow will automatically:
   - Install dependencies
   - Build the static site
   - Deploy to GitHub Pages

3. Monitor the deployment:
   - Go to **Actions** tab in your repository
   - Watch the "Deploy to GitHub Pages" workflow

4. Once complete, your site will be live at:
   ```
   https://yourusername.github.io/repo-name
   ```

## Troubleshooting

### Build Fails

- Check the **Actions** tab for error details
- Ensure all required secrets are set correctly
- Verify environment variable names match exactly

### 404 Error on Deployed Site

- Ensure GitHub Pages is enabled with "GitHub Actions" as source
- Check that the workflow completed successfully
- Wait a few minutes for GitHub Pages to update

### Authentication Issues

- Verify Firebase authorized domains include your GitHub Pages domain
- Check Anilist OAuth redirect URI matches your deployment URL
- Ensure all Firebase secrets are set correctly

### Backend Connection Issues

- Verify backend server is running and accessible
- Check `NEXT_PUBLIC_BACKEND_URL` is set correctly
- Ensure backend allows CORS from your GitHub Pages domain

## Updating the Site

After the initial setup, updating is simple:

1. Make your changes locally
2. Commit and push to the `main` branch
3. GitHub Actions will automatically rebuild and redeploy

## Custom Domain (Optional)

To use a custom domain with GitHub Pages:

1. Go to **Settings** → **Pages**
2. Under "Custom domain", enter your domain
3. Follow GitHub's instructions to configure DNS
4. Update secrets:
   - `NEXT_PUBLIC_WEBSITE_ORIGIN_URL` → your custom domain
5. Update Firebase and Anilist OAuth redirect URIs

## Notes

- The first deployment may take 5-10 minutes
- Static export means some dynamic Next.js features are not available
- Server-side redirects are replaced with client-side routing
- The backend must remain hosted separately for full functionality
