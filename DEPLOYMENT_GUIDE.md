# Complete Deployment Guide for Vercel

This guide walks you through deploying both the frontend and backend of your Feedback System to Vercel.

## 📋 Prerequisites

Before you start, ensure you have:
- A GitHub account (already have since the repo is here)
- A Vercel account (create at https://vercel.com)
- A PostgreSQL database (use Supabase, Railway, or PlanetScale - free tier available)
- Node.js 18+ locally installed

---

## 🔧 Step 1: Set Up Your Database

### Using Supabase (Recommended for Beginners)

1. Go to https://supabase.com
2. Click "Start Your Project"
3. Create a new project (free tier: 500MB database)
4. Go to Project Settings → Database
5. Copy the connection string under "URI"
6. Format: `postgresql://postgres:PASSWORD@HOST:5432/postgres`

### Using Railway

1. Go to https://railway.app
2. Create new project → Add Database → PostgreSQL
3. Go to the database, click "Connect"
4. Copy the PostgreSQL connection string

---

## 🚀 Step 2: Deploy Backend

### 2.1 Generate JWT Secret

```bash
openssl rand -base64 32
```
Save this value - you'll need it for environment variables.

### 2.2 Connect Backend to Vercel

1. Go to https://vercel.com/dashboard
2. Click "Add New" → "Project"
3. Select your GitHub repository: `ZiadNader1011/FEEDBACK-SYSTEM-OF-MODERN-COMPANY`
4. Fill in deployment settings:
   - **Framework Preset:** Node.js
   - **Root Directory:** `feedback-system-main/backend`
   - **Build Command:** `npm run build`
   - **Output Directory:** Leave empty

5. Click "Environment Variables" and add:
   - `DATABASE_URL` = Your PostgreSQL connection string
   - `JWT_SECRET` = The value you generated above
   - `NODE_ENV` = `production`
   - `FRONTEND_URL` = (Leave empty for now, you'll add it after frontend deploys)

6. Click "Deploy" ✅

### 2.3 Run Database Migrations

After deployment completes:

1. Go to your Vercel project
2. Click on the deployment
3. Check the logs - you'll see your backend URL (e.g., `https://backend-xxx.vercel.app`)
4. Run migrations in your local terminal:

```bash
cd feedback-system-main/backend
npx prisma migrate deploy
```

Or if that doesn't work, push the schema:

```bash
npx prisma db push
```

---

## 🎨 Step 3: Deploy Frontend

### 3.1 Connect Frontend to Vercel

1. Go to https://vercel.com/dashboard
2. Click "Add New" → "Project"
3. Select the same GitHub repository
4. Fill in deployment settings:
   - **Framework Preset:** Vite
   - **Root Directory:** `feedback-system-main/supply-insights-hub-main`
   - **Build Command:** `npm run build`
   - **Output Directory:** `dist`

5. Click "Environment Variables" and add:
   - `VITE_API_URL` = Your backend URL (e.g., `https://backend-xxx.vercel.app`)

6. Click "Deploy" ✅

---

## 🔄 Step 4: Update CORS on Backend

Now that you have your frontend URL:

1. Go back to your backend Vercel project
2. Go to Settings → Environment Variables
3. Update `FRONTEND_URL` = Your frontend URL (e.g., `https://frontend-xxx.vercel.app`)
4. Redeploy the backend (Settings → Deployments → Select latest → Click the menu → Redeploy)

---

## ✅ Verification Checklist

- [ ] Backend deployed and running at `https://backend-xxx.vercel.app`
- [ ] Visiting backend URL shows: `{ "message": "Welcome to Feedback System Backend API is running successfully!" }`
- [ ] Frontend deployed and running at `https://frontend-xxx.vercel.app`
- [ ] Frontend loads without CORS errors
- [ ] Can login/register and API calls work

---

## 🐛 Troubleshooting

### Frontend shows CORS errors
- Verify `VITE_API_URL` is set correctly in frontend environment variables
- Verify `FRONTEND_URL` is set correctly in backend environment variables
- Redeploy both services

### Database connection fails
- Verify `DATABASE_URL` format is correct
- Check database is not full (Supabase free tier: 500MB limit)
- Verify IP whitelist allows all IPs (Vercel uses dynamic IPs)

### Prisma migration fails
- Ensure database user has proper permissions
- Run locally first: `npm install && npx prisma migrate deploy`
- Check database logs for errors

### Build fails on Vercel
- Check deployment logs (Vercel dashboard → Deployments → View logs)
- Ensure all dependencies are in `package.json`
- Try building locally: `npm run build`

---

## 📝 Environment Variables Reference

### Backend (`feedback-system-main/backend`)
```
DATABASE_URL=postgresql://user:pass@host:5432/db
JWT_SECRET=your-secret-key-from-openssl
NODE_ENV=production
FRONTEND_URL=https://your-frontend.vercel.app
PORT=5000
```

### Frontend (`feedback-system-main/supply-insights-hub-main`)
```
VITE_API_URL=https://your-backend.vercel.app
```

---

## 🔐 Security Best Practices

1. **Never commit `.env` files** - they're already in `.gitignore`
2. **Use strong JWT secrets** - minimum 32 characters
3. **Enable CORS only for your domain** - not `*` in production
4. **Use environment-specific URLs** - different for dev/prod
5. **Rotate secrets regularly** - especially if exposed

---

## 📞 Need Help?

- Vercel Docs: https://vercel.com/docs
- Prisma Docs: https://www.prisma.io/docs
- Your Backend URL: Check Vercel dashboard for `*.vercel.app` URL
- Logs: View real-time logs in Vercel dashboard under "Deployments"

---

## 🎉 Congratulations!

Your full-stack application is now deployed on Vercel! 

- **Frontend:** https://your-frontend.vercel.app
- **Backend API:** https://your-backend.vercel.app

Both front and back are running and connected. 🚀
