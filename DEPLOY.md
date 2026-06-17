# 🎯 Deployment Guide - SkillShift

## Overview

Monorepo structure dengan:
- **Frontend** → Vercel
- **Backend** → Railway
- **Database** → SQLite (di Railway persistent storage)

---

## 📦 Step 1: Persiapan Repository

### Jika belum ada repository GitHub:
```bash
# Navigate ke folder project
cd "Project_Akhir_SkillShift - Copy/project pwd/SkillShift"

# Initialize git (jika belum)
git init

# Add semua file
git add .

# Commit
git commit -m "Initial commit"

# Buat repository baru di GitHub, lalu:
git remote add origin https://github.com/USERNAME/skillshift.git
git branch -M main
git push -u origin main
```

---

## 🚂 Step 2: Deploy Backend ke Railway

### 2.1 Buka [railway.app](https://railway.app)

### 2.2 Login dengan GitHub

### 2.3 Buat Project Baru
- Click **"New Project"**
- Pilih **"Deploy from GitHub repo"**
- Select repository kamu

### 2.4 Configure Railway
Railway akan auto-detect Express app. Pastikan setting:

| Setting | Value |
|---------|-------|
| Root Directory | `/` (root) |
| Build Command | Auto-detect |
| Start Command | `node server/index.js` |
| Health Check | `/api/health` |

### 2.5 Set Environment Variables (jika perlu)
Railway akan auto-set `PORT`. Untuk production, bisa tambah:
```
NODE_ENV=production
```

### 2.6 Tunggu Deploy
- Railway akan build dan deploy otomatis
- Copy URL backend, contoh: `https://skillshift-api.up.railway.app`

---

## 🌐 Step 3: Deploy Frontend ke Vercel

### 3.1 Buka [vercel.com](https://vercel.com)

### 3.2 Login dengan GitHub

### 3.3 Import Project
- Click **"Add New..."** → **"Project"**
- Select repository kamu
- Vercel akan auto-detect Vite/React project

### 3.4 Configure Build Settings

| Setting | Value |
|---------|-------|
| Framework | Vite |
| Root Directory | `./` |
| Build Command | `npm run build` |
| Output Directory | `dist` |

### 3.5 Set Environment Variables
Di Vercel dashboard → Settings → Environment Variables:

| Name | Value |
|------|-------|
| `VITE_API_URL` | `https://skillshift-api.up.railway.app/api` |

> ⚠️ Ganti dengan URL Railway kamu yang sebenarnya!

### 3.6 Deploy
- Click **"Deploy**
- Tunggu build selesai
- Copy URL frontend, contoh: `https://skillshift.vercel.app`

---

## ⚙️ Step 4: Update Frontend Code (Optional)

Jika ingin pakai API URL dari environment variable secara dinamis, sudah dikonfigurasi di [src/App.jsx](src/App.jsx:5):

```javascript
const API_URL = import.meta.env.VITE_API_URL || 'https://skillshift-api.up.railway.app/api';
```

---

## 🔄 Step 5: Update CORS (Otomatis)

Backend sudah dikonfigurasi untuk accept semua origin. Jika perlu restrict:

Di [server/index.js](server/index.js:15-19):
```javascript
app.use(cors({
  origin: ['https://skillshift.vercel.app', 'http://localhost:5173'],
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization']
}));
```

---

## 🔗 URLs Setelah Deploy

| Service | URL |
|---------|-----|
| Frontend (Vercel) | `https://skillshift.vercel.app` |
| Backend (Railway) | `https://skillshift-api.up.railway.app` |
| Backend Health | `https://skillshift-api.up.railway.app/api/health` |

---

## 🧪 Testing

1. Buka URL Vercel frontend
2. Login dengan akun demo:
   - **Admin**: `admin@skillshift.com` / `admin123`
   - **Mahasiswa**: `mahasiswa@skillshift.com` / `123456`

3. Test CRUD lowongan (admin)
4. Test save & accept jobs (mahasiswa)

---

## 🔄 Update Deployment

Setelah push kode ke GitHub:
- **Railway**: Auto-deploy on push
- **Vercel**: Auto-deploy on push (jika setting default)

---

## ❓ Troubleshooting

### Railway Build Failed
- Pastikan `package.json` ada di root
- Pastikan `engines.node` compatible
- Check Railway logs untuk error detail

### Frontend 404 on Refresh
- Vercel SPA routing sudah dikonfigurasi
- Pastikan `vercel.json` ada di root

### CORS Error
- Pastikan backend URL benar
- Pastikan CORS origin include Vercel URL

### Database Reset
- Railway SQLite files tidak persist永久 jika volume tidak dikonfigurasi
- Untuk production, consider upgrade ke PostgreSQL

---

## 📝 Catatan Penting

1. **SQLite di Railway** - Data akan persist selama project aktif, tapi hilang jika delete project
2. **Free Tier Limits** - Railway free tier: 500 hours/month, 1GB RAM
3. **Cold Start** - Railway mungkin lambat pertama kali (cold start ~30 detik)
4. **Vercel Edge** - Frontend serving sangat cepat dari edge network
