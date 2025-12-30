# Known Limitations with GitHub Pages Static Export

## API Routes Issue

The frontend currently contains Next.js API routes in the `app/api/` directory that **will not work** with static export to GitHub Pages. These routes require a server runtime to execute.

### Affected Routes

The following API routes are currently in the codebase:

1. **Cookie Management Routes** (won't work on GitHub Pages):
   - `/api/media-title-language` - Sets media title language preference
   - `/api/subtitle` - Sets subtitle language preference
   - `/api/adult-content` - Sets adult content filter
   - `/api/wrong-media-enabled` - Sets wrong media playback option
   - `/api/anilist` - Handles Anilist OAuth token storage

2. **Data Fetching Routes** (these work via backend server):
   - `/api/mediaInfo/*` - Proxies to backend server ✅
   - `/api/episodes/*` - Proxies to backend server ✅
   - `/api/search/*` - Proxies to backend server ✅

### Current Status

With the current configuration:
- ✅ The site will build successfully (Next.js allows API routes in static export)
- ❌ API routes that handle cookies will return 404 errors when called
- ✅ Data fetching routes already proxy to the backend server and work fine

### Solutions

#### Option 1: Move Cookie Routes to Backend (Recommended)

Move the cookie-setting API routes to the backend server:

1. Add these endpoints to `/backend/routes/`:
   ```
   POST /api/user/settings/media-title-language
   POST /api/user/settings/subtitle
   POST /api/user/settings/adult-content
   POST /api/user/settings/wrong-media-enabled
   POST /api/auth/anilist/token
   ```

2. Update frontend calls in:
   - `app/api/cookie/userCookieSettingsActions.ts`
   - `app/lib/user/anilistUserLoginOptions.ts`
   
   Change from:
   ```typescript
   ${process.env.NEXT_PUBLIC_WEBSITE_ORIGIN_URL}/api/...
   ```
   
   To:
   ```typescript
   ${process.env.NEXT_PUBLIC_BACKEND_URL}/api/user/settings/...
   ```

3. Ensure backend server supports CORS from your GitHub Pages domain

#### Option 2: Client-Side Cookie Management

Replace server-side cookie setting with client-side solutions:

1. Use `js-cookie` or similar library
2. Store preferences in localStorage
3. Update the cookie-setting functions to work client-side

```typescript
// Example with js-cookie
import Cookies from 'js-cookie';

export function setMediaTitleLanguage(language: string) {
  Cookies.set('media_title_language', language, { expires: 365 });
}
```

**Pros**: No backend changes needed  
**Cons**: Less secure for sensitive data, no HTTP-only cookies

#### Option 3: Accept the Limitation

For a quick deployment, you can:
1. Deploy as-is to GitHub Pages
2. Cookie-related features won't work initially
3. Add a note that certain preferences may not persist
4. Implement proper solution later

### Recommended Action

**For production deployment**: Implement Option 1 (move to backend)
**For testing/demo**: Option 3 is acceptable

### Testing Cookie Routes

To test if cookie routes are working after migration:

```bash
# Test media title language
curl -X POST https://your-backend.com/api/user/settings/media-title-language \
  -H "Content-Type: application/json" \
  -d '{"titleLanguage": "romaji"}'

# Test subtitle setting
curl -X POST https://your-backend.com/api/user/settings/subtitle \
  -H "Content-Type: application/json" \
  -d '{"subtitleLanguage": "en"}'
```

### Impact Assessment

| Feature | Works on GitHub Pages? | Workaround |
|---------|----------------------|------------|
| Browse anime/manga | ✅ Yes | None needed |
| Search | ✅ Yes | Already uses backend |
| Watch episodes | ✅ Yes | Already uses backend |
| Read manga | ✅ Yes | Already uses backend |
| User authentication | ⚠️ Partial | Anilist token route needs backend migration |
| User preferences | ❌ No | Needs Option 1 or 2 |
| Media title language | ❌ No | Needs Option 1 or 2 |
| Subtitle preferences | ❌ No | Needs Option 1 or 2 |
| Adult content filter | ❌ No | Needs Option 1 or 2 |

### Priority

- **High Priority**: Anilist OAuth token handling (affects authentication)
- **Medium Priority**: User preferences (affects UX but not core functionality)
- **Low Priority**: Other cookie settings (nice to have)

## Other Static Export Limitations

1. **No Server-Side Redirects**: 
   - Removed from `next.config.mjs`
   - Redirects must be handled client-side or via GitHub Pages configuration

2. **No Incremental Static Regeneration (ISR)**:
   - All pages generated at build time
   - No automatic rebuilds on data changes
   - Need to rebuild and redeploy to update content

3. **No Middleware**:
   - `middleware.ts` will not run on GitHub Pages
   - Any auth/redirect logic in middleware won't work

## Deployment Checklist

Before deploying to production:

- [ ] Migrate cookie API routes to backend server
- [ ] Update frontend API calls to use backend URLs
- [ ] Test all authentication flows
- [ ] Test user preference settings
- [ ] Verify CORS is configured on backend
- [ ] Test the complete user journey
- [ ] Monitor for 404 errors on `/api/*` routes

## Questions?

If you need help implementing any of these solutions, please:
1. Check the backend README for API endpoint examples
2. Review the frontend code for similar patterns
3. Test locally before deploying to GitHub Pages
