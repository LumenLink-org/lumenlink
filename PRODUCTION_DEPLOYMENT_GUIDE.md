# Production Deployment Guide

## Overview

This guide ensures **all website features work correctly when deployed to production servers**, not just locally. All dynamic features (GitHub API, backend connections, documentation) are production-ready.

## ✅ Production-Ready Features

### 1. GitHub API Integration
- **Status:** ✅ **Fully Production-Ready**
- **API Base:** `https://api.github.com` (production URL)
- **Authentication:** Uses `GITHUB_TOKEN` environment variable (optional)
- **Rate Limits:** 
  - Without token: 60 requests/hour (works for public repos)
  - With token: 5000 requests/hour
- **Caching:** All responses cached for 5 minutes server-side
- **Error Handling:** Graceful fallback to empty states

**All GitHub endpoints work in production:**
- `/api/github/stats` - Repository statistics
- `/api/github/commits` - Recent commits
- `/api/github/pull-requests` - Open PRs
- `/api/github/contributors` - Top contributors
- `/api/github/releases` - Latest releases
- `/api/github/good-first-issues` - Good first issues

### 2. Backend API Integration
- **Status:** ✅ **Production-Ready** (requires environment variable)
- **API Endpoint:** Uses `LUMENLINK_RENDEZVOUS_URL` environment variable
- **Fallback:** `http://localhost:8080` (only for local development)
- **Timeout:** 5 seconds with graceful error handling
- **Caching:** 60 seconds server-side cache

**Backend endpoints:**
- `/api/community/operators` - Fetches from `${LUMENLINK_RENDEZVOUS_URL}/api/v1/gateways`

### 3. Documentation System
- **Status:** ✅ **Production-Ready**
- **Static Mode (Default):** Reads from local files - works everywhere
- **Dynamic Mode (Optional):** Fetches from GitHub API
- **Fallback:** Always falls back to local files if GitHub fails
- **URL Handling:** Uses relative URLs in production (no hardcoded localhost)

### 4. Internationalization (i18n)
- **Status:** ✅ **Production-Ready**
- **20+ Languages:** All fully translated
- **RTL Support:** Persian, Arabic, Urdu
- **Font Loading:** Iran Yekan font loads from CDN (production-ready)
- **Locale Routing:** Works in production via middleware

### 5. All Pages
- **Status:** ✅ **Production-Ready**
- All pages use translation keys (no hardcoded text)
- All components handle errors gracefully
- All API calls have fallbacks

## 🔧 Required Environment Variables

### Production Server Configuration

**File:** Set in your hosting platform (Vercel, Netlify, Railway, etc.)

```bash
# Required: Production website URL
NEXT_PUBLIC_APP_URL=https://lumenlink.org

# Required: Backend API URL (localhost only for development/testing)
LUMENLINK_RENDEZVOUS_URL=https://api.lumenlink.org

# Optional: GitHub Token (for higher rate limits)
GITHUB_TOKEN=ghp_your_personal_access_token_here

# Optional: Enable GitHub docs (default: false, uses local files)
USE_GITHUB_DOCS=false

# Production mode
NODE_ENV=production
```

### Environment Variable Details

#### `NEXT_PUBLIC_APP_URL` (Required)
- **Purpose:** Base URL for the website (used for documentation API calls)
- **Production:** `https://lumenlink.org`
- **Development:** `http://localhost:3000` (test purposes only)
- **Note:** Must include protocol and no trailing slash
- **Used in:** `web/lib/mdx.ts` for GitHub docs feature

#### `LUMENLINK_RENDEZVOUS_URL` (Required for Backend Features)
- **Purpose:** Backend Rendezvous API URL
- **Production:** `https://api.lumenlink.org`
- **Development:** `http://localhost:8080` (test purposes only)
- **Note:** Must be accessible from your production server
- **Used in:** `web/app/api/community/operators/route.ts`

#### `GITHUB_TOKEN` (Optional but Recommended)
- **Purpose:** GitHub Personal Access Token for higher API rate limits
- **How to create:**
  1. Go to: https://github.com/settings/tokens
  2. Generate new token (classic)
  3. Select scope: `public_repo` (read-only)
  4. Copy token and add to environment variables
- **Benefits:**
  - Increases rate limit from 60/hour to 5000/hour
  - Access to private repositories (if needed)
  - More reliable API calls

#### `USE_GITHUB_DOCS` (Optional)
- **Purpose:** Enable dynamic documentation from GitHub
- **Default:** `false` (uses local files)
- **When to use:** If you want documentation to auto-update from GitHub
- **Note:** Requires `NEXT_PUBLIC_APP_URL` to be set

## 🚀 Deployment Steps

### Step 1: Prepare Environment Variables

Before deploying, ensure you have:

1. **Production URL:**
   ```bash
   NEXT_PUBLIC_APP_URL=https://lumenlink.org
   ```

2. **Backend URL:**
   ```bash
   LUMENLINK_RENDEZVOUS_URL=https://api.lumenlink.org
   ```

3. **GitHub Token (Optional):**
   ```bash
   GITHUB_TOKEN=ghp_your_token_here
   ```

### Step 2: Build for Production

```bash
cd web
npm run build
```

This will:
- ✅ Compile all TypeScript
- ✅ Optimize images
- ✅ Generate static pages
- ✅ Bundle all assets
- ✅ Verify all routes work

### Step 3: Deploy to Your Platform

#### Vercel (Recommended for Next.js)

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
cd web
vercel --prod

# Or connect via GitHub for automatic deployments
```

**Environment Variables in Vercel:**
1. Go to Project Settings → Environment Variables
2. Add all required variables
3. Redeploy

#### Netlify

```bash
# Install Netlify CLI
npm i -g netlify-cli

# Deploy
cd web
netlify deploy --prod
```

**Environment Variables in Netlify:**
1. Go to Site Settings → Build & Deploy → Environment
2. Add all required variables
3. Redeploy

#### Docker

```bash
# Build
docker build -t lumenlink-web ./web

# Run with environment variables
docker run -p 3000:3000 \
  -e NEXT_PUBLIC_APP_URL=https://lumenlink.org \
  -e LUMENLINK_RENDEZVOUS_URL=https://api.lumenlink.org \
  -e GITHUB_TOKEN=ghp_your_token \
  lumenlink-web
```

#### Manual Server Deployment

```bash
# Build
cd web
npm run build

# Start production server
npm start

# Or use PM2 for process management
pm2 start npm --name "lumenlink-web" -- start
```

## ✅ Post-Deployment Verification

### 1. Test All Pages

Visit each page and verify:
- ✅ Home page loads
- ✅ About page loads with all sections
- ✅ Community page shows GitHub stats
- ✅ Documentation pages load
- ✅ Emergency page loads
- ✅ Privacy page loads
- ✅ All pages translate correctly

### 2. Test GitHub Integration

```bash
# Test stats API
curl https://lumenlink.org/api/github/stats

# Test commits API
curl https://lumenlink.org/api/github/commits

# Test releases API
curl https://lumenlink.org/api/github/releases
```

**Expected:** JSON responses with data or empty arrays (if repo is new)

### 3. Test Backend Integration

```bash
# Test operators API
curl https://lumenlink.org/api/community/operators
```

**Expected:** JSON with operators array and stats (or empty if backend unavailable)

### 4. Test Language Switching

- ✅ Switch to Persian (fa) - verify RTL layout
- ✅ Switch to Arabic (ar) - verify RTL layout
- ✅ Switch to other languages - verify translations
- ✅ Verify Iran Yekan font loads for Persian

### 5. Test Mobile Responsiveness

- ✅ Test on mobile device or browser dev tools
- ✅ Verify menu works on mobile
- ✅ Verify all pages are responsive

## 🔍 Troubleshooting Production Issues

### Issue: GitHub API Returns Zeros

**Possible Causes:**
1. Repository doesn't exist at `LumenLink-org/lumenlink`
2. Repository is private and no token provided
3. Rate limit exceeded (add `GITHUB_TOKEN`)

**Solution:**
- Verify repository exists and is public
- Add `GITHUB_TOKEN` environment variable
- Check browser console for API errors

### Issue: Backend Connection Fails

**Possible Causes:**
1. `LUMENLINK_RENDEZVOUS_URL` not set or incorrect
2. Backend not accessible from production server
3. CORS issues (if backend is on different domain)
4. Backend not running

**Solution:**
- Verify `LUMENLINK_RENDEZVOUS_URL` is set correctly
- Test backend URL directly: `curl ${LUMENLINK_RENDEZVOUS_URL}/health`
- Check backend CORS configuration
- Verify backend is running and accessible

### Issue: Documentation Not Loading

**Possible Causes:**
1. `USE_GITHUB_DOCS=true` but `NEXT_PUBLIC_APP_URL` not set
2. GitHub API failing
3. Local files missing

**Solution:**
- Set `USE_GITHUB_DOCS=false` to use local files (default)
- Or set `NEXT_PUBLIC_APP_URL` correctly
- Verify local docs exist in `web/content/docs/`

### Issue: Images Not Loading

**Possible Causes:**
1. Image domains not configured in `next.config.js`
2. External images blocked

**Solution:**
- Check `next.config.js` has correct image domains
- Verify external image URLs are accessible

### Issue: Language Switching Not Working

**Possible Causes:**
1. Middleware not configured correctly
2. Translation files missing

**Solution:**
- Verify `middleware.ts` is in root of `web/` directory
- Check all translation files exist in `web/messages/`
- Verify locale routing in `next.config.js`

## 🔒 Security Considerations

### Environment Variables
- ✅ **Never commit** `.env.local` or `.env` files
- ✅ Use hosting platform's environment variable system
- ✅ Rotate `GITHUB_TOKEN` periodically
- ✅ Use different tokens for staging/production

### API Security
- ✅ All API routes are server-side only
- ✅ `GITHUB_TOKEN` is server-side only (not exposed to client)
- ✅ Backend URLs are server-side only
- ✅ No sensitive data in client-side code

### CORS Configuration
If backend is on different domain, ensure backend has CORS configured:

```go
// In backend (Go/Gin example)
router.Use(cors.New(cors.Config{
    AllowOrigins: []string{"https://lumenlink.org"},
    AllowMethods: []string{"GET", "POST"},
    AllowHeaders: []string{"Origin", "Content-Type", "Accept"},
}))
```

## 📊 Performance Optimization

### Already Implemented
- ✅ Server-side caching (5 minutes for GitHub, 60 seconds for backend)
- ✅ Static page generation where possible
- ✅ Image optimization via Next.js Image component
- ✅ Code splitting and lazy loading
- ✅ CDN-ready (Vercel/Netlify provide CDN automatically)

### Recommended
- ✅ Enable compression (gzip/brotli) - usually automatic on hosting platforms
- ✅ Use CDN for static assets
- ✅ Monitor API response times
- ✅ Set up error tracking (Sentry, etc.)

## 🎯 Production Checklist

Before going live, verify:

- [ ] All environment variables set in hosting platform
- [ ] `NEXT_PUBLIC_APP_URL` = https://lumenlink.org
- [ ] `LUMENLINK_RENDEZVOUS_URL` = https://api.lumenlink.org
- [ ] `GITHUB_TOKEN` added (optional but recommended)
- [ ] All pages load correctly
- [ ] GitHub API integration works
- [ ] Backend API integration works (if backend deployed)
- [ ] All languages work correctly
- [ ] Mobile responsiveness verified
- [ ] Documentation pages load
- [ ] No console errors
- [ ] All external links work
- [ ] SSL certificate configured (HTTPS)
- [ ] Domain DNS configured correctly

## 📝 Summary

**All features are production-ready!** The website will work correctly when deployed to production servers. You just need to:

1. ✅ Set environment variables in your hosting platform
2. ✅ Deploy using standard Next.js deployment process
3. ✅ Verify all features work after deployment

**No code changes needed** - everything is already configured to work in production. The localhost URLs are only fallbacks and will be replaced by environment variables in production.

## 🆘 Support

If you encounter issues:
1. Check browser console for errors
2. Check server logs for API errors
3. Verify environment variables are set correctly
4. Test API endpoints directly with `curl`
5. Check network tab in DevTools for failed requests
