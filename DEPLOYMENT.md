# Deployment Guide - Dr.C Mandelbrot Explorer

## Overview

The Dr.C - Mandelbrot Explorer is a **fully self-contained web application** that requires no build process, backend server, or database. It consists of static HTML files that can be deployed to any web hosting service.

## What You're Deploying

The application consists of these files:

```
Dr.C-Mandelbrot-Explorer/
├── index.html           # Landing page (entry point)
├── app-v2.1.html       # Main application
├── quickstart.html     # Quick start guide
├── users-guide.md      # User's guide (Markdown)
└── technical-guide.md  # Technical guide (Markdown)
```

**Total size:** ~1.2 MB (all files combined)

## Requirements

- ✅ **No build step required** - files are ready to deploy as-is
- ✅ **No server-side code** - pure client-side application
- ✅ **No database** - uses browser localStorage for presets
- ✅ **No Node.js/npm** - Csound WASM loaded from CDN
- ✅ **Works offline** - after first load (CDN assets cached)

## Browser Requirements

**Minimum browser versions:**

- Chrome 66+ (April 2018)
- Firefox 60+ (May 2018)
- Safari 12+ (September 2018)
- Edge 79+ (January 2020)

**Required browser features:**

- Web Audio API
- WebAssembly
- Canvas API
- localStorage
- ES6 JavaScript

## Deployment Options

### Option 1: GitHub Pages (Recommended - FREE)

**Best for:** Personal projects, open source, free hosting with custom domain support.

#### Steps:

1. **Create a GitHub repository:**

   ```bash
   cd /Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer
   git init
   git add index.html app-v2.1.html quickstart.html users-guide.md technical-guide.md
   git commit -m "Initial commit: Dr.C Mandelbrot Explorer"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/mandelbrot-explorer.git
   git push -u origin main
   ```

2. **Enable GitHub Pages:**
   - Go to repository Settings → Pages
   - Source: Deploy from a branch
   - Branch: `main` / `root`
   - Click Save

3. **Access your site:**
   - URL: `https://YOUR-USERNAME.github.io/mandelbrot-explorer/`
   - Ready in 1-2 minutes

#### Custom Domain (Optional):

1. Add `CNAME` file to repository:

   ```
   mandelbrot.yourdomain.com
   ```

2. Configure DNS:
   - Add CNAME record pointing to `YOUR-USERNAME.github.io`

3. Enable HTTPS in GitHub Pages settings

---

### Option 2: Netlify (Recommended - FREE)

**Best for:** Drag-and-drop simplicity, instant deploys, automatic HTTPS.

#### Steps:

1. **Sign up:** Visit [netlify.com](https://netlify.com) (free account)

2. **Deploy via drag-and-drop:**
   - Click "Add new site" → "Deploy manually"
   - Drag the entire `Dr.C-Mandelbrot-Explorer` folder into browser
   - Done! Site is live in ~30 seconds

3. **Or deploy via CLI:**

   ```bash
   npm install -g netlify-cli
   cd /Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer
   netlify deploy --prod
   ```

4. **Access your site:**
   - Netlify provides a URL: `https://random-name-12345.netlify.app`
   - Can customize subdomain or add custom domain

#### Features:

- ✅ Automatic HTTPS
- ✅ Global CDN
- ✅ Continuous deployment from Git
- ✅ Instant rollbacks
- ✅ Free custom domain support

---

### Option 3: Vercel (FREE)

**Best for:** Modern hosting with edge network, great performance.

#### Steps:

1. **Sign up:** Visit [vercel.com](https://vercel.com) (free account)

2. **Deploy via CLI:**

   ```bash
   npm install -g vercel
   cd /Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer
   vercel --prod
   ```

3. **Or deploy via Git:**
   - Connect GitHub repository
   - Vercel auto-deploys on push

4. **Access your site:**
   - Vercel provides URL: `https://mandelbrot-explorer.vercel.app`

---

### Option 4: Cloudflare Pages (FREE)

**Best for:** Maximum performance, global edge network, DDoS protection.

#### Steps:

1. **Sign up:** Visit [pages.cloudflare.com](https://pages.cloudflare.com)

2. **Connect Git repository:**
   - Click "Create a project"
   - Connect GitHub/GitLab
   - Select repository

3. **Build settings:**
   - Framework preset: None
   - Build command: (leave empty)
   - Build output directory: `/`

4. **Deploy:**
   - Click "Save and Deploy"
   - Live in 1-2 minutes

---

### Option 5: Your Own Server

**Best for:** Full control, existing infrastructure.

#### Apache:

1. Copy files to web root:

   ```bash
   cp -r /Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer/* /var/www/html/mandelbrot/
   ```

2. Ensure MIME types are set (usually default):

   ```apache
   AddType text/html .html
   AddType text/markdown .md
   AddType application/javascript .js
   ```

3. Enable HTTPS (Let's Encrypt):
   ```bash
   certbot --apache -d yourdomain.com
   ```

#### Nginx:

1. Copy files to web root:

   ```bash
   cp -r /Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer/* /usr/share/nginx/html/mandelbrot/
   ```

2. Configure server block:

   ```nginx
   server {
       listen 80;
       server_name yourdomain.com;
       root /usr/share/nginx/html/mandelbrot;
       index index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

3. Enable HTTPS:
   ```bash
   certbot --nginx -d yourdomain.com
   ```

---

### Option 6: Amazon S3 + CloudFront

**Best for:** AWS infrastructure, scalability.

#### Steps:

1. **Create S3 bucket:**

   ```bash
   aws s3 mb s3://mandelbrot-explorer
   aws s3 sync /Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer s3://mandelbrot-explorer --acl public-read
   ```

2. **Enable static website hosting:**
   - S3 Console → Bucket → Properties → Static website hosting
   - Index document: `index.html`

3. **Create CloudFront distribution:**
   - Origin: S3 bucket
   - Viewer Protocol: Redirect HTTP to HTTPS
   - Default Root Object: `index.html`

4. **Access via CloudFront URL:**
   - `https://d123456789.cloudfront.net`

---

## Post-Deployment Checklist

After deploying, verify these features work:

- [ ] Landing page loads (`index.html`)
- [ ] App launches (`app-v2.1.html`)
- [ ] Quick start guide opens (`quickstart.html`)
- [ ] User's guide renders (Markdown viewer or raw `.md`)
- [ ] Technical guide renders
- [ ] Audio plays when "Start Audio" clicked
- [ ] Fractal renders and updates
- [ ] Presets load (45 factory presets appear)
- [ ] Preset save/load works (localStorage)
- [ ] All 11 synthesis engines work
- [ ] All 4 effects work
- [ ] Keyboard shortcuts work (Spacebar)
- [ ] Browser console shows no errors

### Common Issues:

**1. Markdown files show as plain text:**

- Some hosts display `.md` as raw text
- **Solution A:** Use a Markdown viewer extension in browser
- **Solution B:** Convert to HTML:
  ```bash
  npm install -g marked
  marked users-guide.md -o users-guide.html
  marked technical-guide.md -o technical-guide.html
  ```
- **Solution C:** Host on GitHub (auto-renders Markdown)

**2. Audio doesn't play:**

- Check browser console for errors
- Verify browser supports Web Audio API
- Ensure HTTPS is enabled (some browsers require it)
- Check that Csound WASM CDN is accessible

**3. CORS errors:**

- Should not occur (no external resources except CDN)
- If using local file:// protocol, use a local server instead

**4. Blank page on load:**

- Check browser console for JavaScript errors
- Verify all files uploaded correctly
- Check file permissions (should be world-readable)

---

## Security Considerations

### Safe Practices:

✅ **No sensitive data** - Everything runs client-side  
✅ **No user authentication** - No passwords or logins  
✅ **No backend** - No server to hack  
✅ **No database** - localStorage is sandboxed per-origin  
✅ **CDN resources** - Csound WASM loaded from trusted CDN

### Recommended Headers:

If hosting on your own server, add these security headers:

```
Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline' https://cdn.jsdelivr.net; style-src 'self' 'unsafe-inline'; worker-src 'self' blob:;
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Referrer-Policy: strict-origin-when-cross-origin
```

**Note:** `unsafe-inline` is required because app-v2.1.html has inline JavaScript. To remove it, you'd need to extract all `<script>` content to external `.js` files.

---

## Performance Optimization

### 1. Enable Compression (gzip/brotli)

**Apache:**

```apache
<IfModule mod_deflate.c>
  AddOutputFilterByType DEFLATE text/html text/css application/javascript text/markdown
</IfModule>
```

**Nginx:**

```nginx
gzip on;
gzip_types text/html text/css application/javascript text/markdown;
```

**Result:** Reduces transfer size by ~70% (1.2 MB → ~350 KB)

### 2. Set Cache Headers

**Apache:**

```apache
<filesMatch ".(html|css|js)$">
  Header set Cache-Control "max-age=3600, public"
</filesMatch>
```

**Nginx:**

```nginx
location ~* \.(html|css|js)$ {
    expires 1h;
}
```

### 3. CDN Optimization

The app already uses CDN for Csound WASM:

```html
<script src="https://cdn.jsdelivr.net/npm/@csound/browser@7.0.0-beta26/dist/csound.browser.js"></script>
```

This is cached globally by jsDelivr CDN - no action needed.

---

## Monitoring

### Analytics (Optional)

Add Google Analytics to track usage:

1. Get tracking ID from [analytics.google.com](https://analytics.google.com)

2. Add to `<head>` of `index.html` and `app-v2.1.html`:
   ```html
   <!-- Google Analytics -->
   <script
     async
     src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"
   ></script>
   <script>
     window.dataLayer = window.dataLayer || [];
     function gtag() {
       dataLayer.push(arguments);
     }
     gtag("js", new Date());
     gtag("config", "G-XXXXXXXXXX");
   </script>
   ```

### Error Tracking (Optional)

Use [Sentry](https://sentry.io) for client-side error tracking:

```html
<script src="https://browser.sentry-cdn.com/7.x.x/bundle.min.js"></script>
<script>
  Sentry.init({ dsn: "YOUR-DSN-HERE" });
</script>
```

---

## Maintenance

### Updating the Application

1. **Make changes locally** to HTML files
2. **Test thoroughly** in browser
3. **Deploy updated files:**

   **GitHub Pages:**

   ```bash
   git add .
   git commit -m "Update: description of changes"
   git push
   ```

   **Netlify:**

   ```bash
   netlify deploy --prod
   ```

   **Vercel:**

   ```bash
   vercel --prod
   ```

   **Direct file upload:**
   - Re-upload changed files via FTP/SFTP/dashboard

### Version Control

Consider adding version number to `app-v2.1.html`:

```html
<!-- Dr.C Mandelbrot Explorer v2.1.0 -->
```

Track changes in git commits with semantic versioning:

- `v2.1.1` - Bug fixes
- `v2.2.0` - New features
- `v3.0.0` - Breaking changes

---

## Backup Strategy

The application has no backend, so backups are simple:

1. **Source files:** Keep in git repository (already done)
2. **User presets:** Stored in browser localStorage (can't back up server-side)
3. **Configuration:** All settings in HTML files (versioned in git)

**Recommendation:** Advise users to export presets periodically (could add export/import feature in future).

---

## Scaling Considerations

The application scales infinitely because:

✅ **Static files only** - No database queries  
✅ **Client-side processing** - CPU load on user's machine  
✅ **CDN for dependencies** - Csound WASM served globally  
✅ **No API calls** - Everything runs in browser

**Expected bandwidth per user:**

- First visit: ~1.5 MB (HTML + Csound WASM from CDN)
- Cached visits: ~50 KB (HTML only)

A free hosting tier handles thousands of users with no performance degradation.

---

## Cost Estimates

| Service                 | Free Tier                                 | Cost After Free Tier |
| ----------------------- | ----------------------------------------- | -------------------- |
| **GitHub Pages**        | 100 GB bandwidth/month                    | Free (soft limit)    |
| **Netlify**             | 100 GB bandwidth/month                    | $19/month for 400 GB |
| **Vercel**              | 100 GB bandwidth/month                    | $20/month for 1 TB   |
| **Cloudflare Pages**    | Unlimited bandwidth                       | Free forever         |
| **AWS S3 + CloudFront** | 5 GB storage, 50 GB transfer (first year) | ~$1-5/month after    |

**Recommendation:** Start with **Cloudflare Pages** (unlimited free bandwidth) or **GitHub Pages** (easiest setup).

---

## Custom Domain Setup

### 1. Purchase Domain

- [Namecheap](https://www.namecheap.com) (~$10-15/year)
- [Google Domains](https://domains.google) (~$12/year)
- [Cloudflare Registrar](https://www.cloudflare.com/products/registrar/) (at-cost, ~$8-10/year)

### 2. Configure DNS

**For GitHub Pages:**

```
Type: CNAME
Name: www
Value: YOUR-USERNAME.github.io
```

**For Netlify/Vercel/Cloudflare:**

- Platform provides DNS instructions
- Usually a CNAME pointing to their domain

### 3. Enable HTTPS

- All recommended platforms auto-provision SSL certificates
- HTTPS is mandatory for Web Audio API in some browsers

---

## Testing Before Deployment

Run these tests locally:

```bash
# Start a local server (Python 3)
cd /Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer
python3 -m http.server 8000

# Or use Node.js
npx serve .

# Open browser
open http://localhost:8000
```

**Test checklist:**

- [ ] Landing page links work
- [ ] App loads without console errors
- [ ] Audio plays
- [ ] Presets load
- [ ] All controls respond
- [ ] Spacebar toggles audio
- [ ] Fractal renders smoothly

---

## Support & Documentation Links

After deployment, users can access:

- **Landing Page:** `https://yourdomain.com/`
- **Application:** `https://yourdomain.com/app-v2.1.html`
- **Quick Start:** `https://yourdomain.com/quickstart.html`
- **User's Guide:** `https://yourdomain.com/users-guide.md`
- **Technical Guide:** `https://yourdomain.com/technical-guide.md`

---

## Recommended Deployment: Step-by-Step

**For quickest deployment (5 minutes):**

1. **Sign up for Netlify** (free): [netlify.com](https://netlify.com)

2. **Drag and drop:**
   - Click "Add new site" → "Deploy manually"
   - Drag entire `Dr.C-Mandelbrot-Explorer` folder
   - Wait 30 seconds

3. **Done!** Site is live at `https://random-name.netlify.app`

4. **Optional:** Customize subdomain or add custom domain in Site Settings

---

## Questions?

If you encounter issues:

1. Check browser console for errors (F12)
2. Verify all files uploaded correctly
3. Test in different browser (Chrome, Firefox, Safari)
4. Check hosting platform documentation
5. Ensure HTTPS is enabled

**The application is bulletproof** - if it works locally, it works deployed. No moving parts, no backend, no database. It's just HTML + JavaScript + WebAssembly.

---

## License Considerations

Before deploying publicly, clarify:

- **Code license:** What license for your HTML/JS code?
- **Csound license:** LGPL 2.1 (already complied with via CDN)
- **Documentation:** What license for guides?

**Recommendation:** Add a LICENSE file (MIT, GPL, or Creative Commons).

---

**Happy deploying! 🚀**

The Dr.C - Mandelbrot Explorer is ready to share with the world.
