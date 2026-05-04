# ══════════════════════════════════════════════════
# DEPLOYMENT CHECKLIST & CI/CD GUIDE
# Portfolio Website — Dr. Arjun Mehta
# ══════════════════════════════════════════════════

## PRE-DEPLOYMENT CHECKLIST

### Security
- [ ] Change default admin password (admin@portfolio.dev / Admin@123)
- [ ] JWT_SECRET is at least 32 characters and randomly generated
- [ ] CORS_ORIGIN set to exact frontend production URL
- [ ] Remove all console.log statements in production
- [ ] Verify helmet() is enabled in server.js
- [ ] Rate limiting is active (global + auth routes)
- [ ] MongoDB Atlas IP whitelist configured

### Environment Variables — Backend (Render)
- [ ] NODE_ENV=production
- [ ] PORT=10000
- [ ] MONGO_URI set
- [ ] JWT_SECRET set
- [ ] JWT_EXPIRES_IN=7d
- [ ] CORS_ORIGIN=https://your-portfolio.vercel.app

### Environment Variables — Frontend (Vercel)
- [ ] VITE_API_URL=https://your-api.onrender.com/api

### Database
- [ ] MongoDB Atlas cluster created (free M0 tier)
- [ ] Database user created with readWrite role
- [ ] Connection string tested locally
- [ ] Seed script run successfully

### Performance
- [ ] `npm run build` completes without errors
- [ ] Bundle size < 500KB (check with `vite build --report`)
- [ ] All images optimized (WebP format preferred)

---

## STEP-BY-STEP DEPLOYMENT

### 1. MongoDB Atlas
```
1. https://cloud.mongodb.com → Register / Login
2. Create Organization → Create Project → Build a Cluster (M0 Free)
3. Database Access → Add Database User
   - Username: portfolio_user
   - Password: (auto-generate strong)
   - Role: readWriteAnyDatabase
4. Network Access → Add IP Address → 0.0.0.0/0 (allow all, for Render)
5. Connect → Connect your application → Copy connection string
   Format: mongodb+srv://portfolio_user:<password>@cluster0.xxxxx.mongodb.net/portfolio
```

### 2. Backend on Render (Free Tier)
```
1. https://render.com → Login with GitHub
2. New → Web Service → Connect repo
3. Settings:
   - Name: portfolio-api
   - Root Directory: backend
   - Environment: Node
   - Build Command: npm install
   - Start Command: node server.js
4. Environment Variables (click "Add Environment Variable"):
   NODE_ENV        = production
   PORT            = 10000
   MONGO_URI       = mongodb+srv://...
   JWT_SECRET      = (generate: openssl rand -base64 32)
   JWT_EXPIRES_IN  = 7d
   CORS_ORIGIN     = https://your-frontend.vercel.app
5. Click "Create Web Service"
6. Wait ~3 minutes for first deploy
7. Visit: https://portfolio-api.onrender.com/health
   Expected: { "status": "healthy" }
8. Run seed: Open Render Shell tab → node scripts/seed.js
```

### 3. Frontend on Vercel
```
1. https://vercel.com → Login with GitHub
2. New Project → Import repository
3. Settings:
   - Root Directory: frontend
   - Framework Preset: Vite
   - Build Command: npm run build
   - Output Directory: dist
4. Environment Variables:
   VITE_API_URL = https://portfolio-api.onrender.com/api
5. Deploy
6. Visit your Vercel URL → portfolio should be live!
```

### 4. Custom Domain (Optional)
```
Vercel: Settings → Domains → Add domain → Follow DNS instructions
Render: Settings → Custom Domains (paid plan required)
```

---

## CI/CD with GitHub Actions

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy Portfolio

on:
  push:
    branches: [main]

jobs:
  test-backend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: backend/package-lock.json
      - run: cd backend && npm ci
      - run: cd backend && npm test
        env:
          NODE_ENV: test
          MONGO_URI: ${{ secrets.MONGO_URI_TEST }}
          JWT_SECRET: ${{ secrets.JWT_SECRET }}

  build-frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: frontend/package-lock.json
      - run: cd frontend && npm ci
      - run: cd frontend && npm run build
        env:
          VITE_API_URL: ${{ secrets.VITE_API_URL }}
      - uses: actions/upload-artifact@v4
        with:
          name: dist
          path: frontend/dist

  # Render auto-deploys from GitHub — no extra step needed
  # Vercel auto-deploys from GitHub — no extra step needed
```

---

## GENERATE SECURE JWT SECRET

Run this command to generate a strong JWT secret:
```bash
node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"
```

Or use openssl:
```bash
openssl rand -base64 48
```

---

## QUICK LOCAL RESET

If you need to reset and reseed:
```bash
cd backend
MONGO_URI=<your-uri> node scripts/seed.js
```

Admin credentials after seed:
  Email:    admin@portfolio.dev
  Password: Admin@123
