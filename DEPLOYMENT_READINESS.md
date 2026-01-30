# Deployment Readiness Guide

This document explains what will be fully functional when deployed online and what needs configuration.

## ✅ Fully Functional (No Changes Needed)

### Website Features
- **All Pages**: Home, About, Download, Emergency, FAQ, Press, Privacy, License, etc.
- **Documentation**: All MDX docs in `web/content/docs/` will be fully functional
  - Getting Started
  - Architecture
  - Security
  - Contributing
- **Internationalization (i18n)**: All 20+ languages will work
- **Responsive Design**: Mobile and desktop layouts
- **GitHub Integration**: Stats, releases, commits, contributors (if repo is public)
- **Navigation**: Categorized menu with dropdowns
- **Styling**: Tailwind CSS, shadcn/ui components

### Static Content
- All markdown/MDX documentation files
- All page components
- All UI components
- Images and assets (if included in repo)

## ⚠️ Requires Configuration

### Environment Variables
Before deploying, set these in your hosting platform (Vercel, Netlify, etc.):

**Required:**
- `NODE_ENV=production`
- `NEXT_PUBLIC_SITE_URL` - Production website URL: [https://lumenlink.org](https://lumenlink.org)

**Optional (for GitHub features):**
- `GITHUB_TOKEN` - Personal access token for higher API rate limits (optional, works without but may hit rate limits)

### GitHub API Integration
The GitHub stats/releases/commits features will work automatically if:
- Repository is **public** at `LumenLink-org/lumenlink`
- No authentication needed for public repos

**If repository is private or you want higher rate limits:**
1. Create a GitHub Personal Access Token
2. Add to environment variables: `GITHUB_TOKEN=ghp_...`
3. Update `web/lib/github.ts` to use the token in headers

### Documentation Files
All MDX files in `web/content/docs/` are included in the build and will work:
- ✅ `getting-started.mdx`
- ✅ `architecture.mdx`
- ✅ `security.mdx`
- ✅ `contributing.mdx`

**To add more docs:**
1. Add `.mdx` file to `web/content/docs/`
2. Include frontmatter:
   ```yaml
   ---
   title: "Your Title"
   description: "Description"
   order: 1
   ---
   ```
3. Rebuild and deploy

### Build & Deploy

**Vercel (Recommended for Next.js):**
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
cd web
vercel
```

**Manual Build:**
```bash
cd web
npm run build
npm start
```

**Docker:**
```bash
docker build -t lumenlink-web ./web
docker run -p 3000:3000 lumenlink-web
```

## 🔧 Post-Deployment Checklist

- [ ] Set `NEXT_PUBLIC_SITE_URL` environment variable
- [ ] Verify all pages load correctly
- [ ] Test documentation navigation
- [ ] Verify GitHub API integration (if repo is public)
- [ ] Test language switching
- [ ] Verify mobile menu works
- [ ] Check all external links (GitHub, social media)
- [ ] Test emergency page accessibility
- [ ] Verify download links work
- [ ] Test search functionality (if implemented)

## 📝 Notes

- **Documentation**: All MDX files are statically built, so they'll be fast and SEO-friendly
- **API Routes**: `/api/github/*` routes work server-side and will function in production
- **Static Generation**: Most pages use static generation for better performance
- **Middleware**: Locale routing middleware works in production
- **No Database Required**: Website is fully static/API-based, no database needed

## 🚀 Quick Deploy Commands

**Vercel:**
```bash
cd web && vercel --prod
```

**Netlify:**
```bash
cd web && netlify deploy --prod
```

**Docker:**
```bash
docker-compose up -d web
```
