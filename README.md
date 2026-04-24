```markdown
# 🏭 SPIMF Production Edition
**Sistem Pengurusan Identiti Membership FareezOnzz**

[![Status](https://img.shields.io/badge/status-production-green)]() [![License](https://img.shields.io/badge/license-proprietary-blue)]() [![Version](https://img.shields.io/badge/version-1.0.0-orange)]()

> Sistem pengurusan identiti ahli dan admin dengan integrasi langsung Google Sheets, backend Google Apps Script, dan deployment di Vercel. Direka untuk kegunaan produksi dengan seni bina *configuration-driven*, real-time sync, dan keselamatan tinggi.

---

## 📋 Senarai Semak Deployment
- [ ] Google Sheet permission: `Anyone with the link` → `Editor`
- [ ] Apps Script deployed: `Execute as: Me` | `Who has access: Anyone`
- [ ] Web App URL disalin & ditampal ke `index.html`
- [ ] Vercel deployed successfully
- [ ] Ujian login (Ahli & Admin) berjaya ✅

---

## 🏗️ Gambaran Seni Bina
```
Google Sheets (Data Source)
         ↓
Google Apps Script (Backend API - CRUD Generic)
         ↓
Vercel (Static Frontend - SPA)
         ↓
Browser (Real-Time Sync, Blue/Yellow Theme)
```
- **Backend:** Pure configuration + generic CRUD engine. Tiada logik UI di sini.
- **Frontend:** Single-page application (SPA) dengan modul berstruktur (`app.api`, `app.ui`, `app.auth`, dll).
- **Data Flow:** Semua data dirujuk mengikut **huruf kolum (A,B,C...)**, bukan nama header. Stabil walaupun struktur sheet berubah.

---

## 📦 Prasyarat
1. Akaun Google dengan akses ke spreadsheet `SISTEM MEMBERSHIP FONZZ`
2. Akaun Vercel (https://vercel.com)
3. Browser moden (Chrome/Edge/Firefox)
4. Fail `code.gs` & `index.html` (sudah disediakan)

---

## 🚀 Panduan Deployment

### 1️⃣ Backend: Google Apps Script
1. Buka Google Sheet → **Extensions** → **Apps Script**
2. Padam semua kod sedia ada → Tampal keseluruhan kandungan `code.gs`
3. Klik **Deploy** → **New deployment**
   - `Type`: **Web app**
   - `Description`: `SPIMF API v1.0`
   - `Execute as`: **Me** (email anda)
   - `Who has access`: **Anyone** ⚠️ *(Wajib untuk CORS)*
4. Klik **Deploy** → Salin **Web App URL** (bentuk: `https://script.google.com/macros/s/.../exec`)
5. 🔄 *Setiap kali edit `code.gs`:* **Deploy → Manage deployments → Edit → New version → Save**

### 2️⃣ Frontend: Konfigurasi API
1. Buka `index.html`
2. Cari baris berikut (~line 2000):
   ```javascript
   const API_ENDPOINT = "https://script.google.com/macros/s/YOUR_DEPLOYED_SCRIPT_ID/exec";
   ```
3. Gantikan `YOUR_DEPLOYED_SCRIPT_ID` dengan URL dari Langkah 1
4. Simpan fail

### 3️⃣ Deployment ke Vercel
**Option A: Vercel CLI**
```bash
npm i -g vercel
cd /path/to/your/project
vercel --prod
```
**Option B: GitHub Auto-Deploy**
1. Push `index.html` & `vercel.json` ke repository GitHub
2. Import projek di dashboard Vercel
3. Tetapkan Environment Variable:
   ```
   VITE_API_URL = https://script.google.com/macros/s/YOUR_URL/exec
   ```
4. Klik **Deploy** 🎉

---

## 📊 Rujukan Konfigurasi Kolum

### 📑 MEMBERSHIP FONZZ DATABASE (Sheet 2)
| Konteks | Kolum | Kegunaan |
|---------|-------|----------|
| **AUTH** | `J, O, P, Q, R` | Login methods & verification |
| **PROFILE** | `F, G, AI, AJ, AH` | Imej, Nama, Pangkat, Contribution, Mesej Admin |
| **MAIN INFO** | `G,H,I,K,L,M,N,J,X,Z,B` | Maklumat asas boleh edit |
| **SOCIAL** | `S,T,U,V,W` | Akaun media sosial |
| **STATUS & LOGS** | `AG, AK, AL` | Status akaun, Feedback, Log sistem |

### 🛡️ ADMIN CONTROL DATABASE (Sheet 5)
| Konteks | Kolum | Kegunaan |
|---------|-------|----------|
| **AUTH** | `B, C` | Username & Password |
| **PROFILE** | `F, A, G, M, O, P` | Imej, Nama, Pangkat, Mark, Notice SPIMF/Admin |
| **PERMISSIONS** | `Q-Z` | Kawalan akses fitur (✅BENARKAN / ❌TIDAK) |
| **STATUS & LOGS** | `H, K, L` | Status akaun, Feedback, Log cache |

> 💡 **Nota Penting:** Semua rujuk data menggunakan **huruf kolum (A,B,C...)** untuk kestabilan jangka panjang. Tidak bergantung pada nama header.

---

## 🔄 API Contract (Frontend → Backend)

### ✅ READ
```javascript
{ action: "read:cell", sheet: "...", row: 5, column: "G" }
{ action: "read:row", sheet: "...", row: 10 }
{ action: "read:count", sheet: "MEMBERSHIP FONZZ DATABASE" }
{ action: "read:premium" }                // Auto-filter PREMIUM rank
{ action: "read:admin-committee" }        // With date calculation
```

### ✏️ WRITE
```javascript
{ action: "write:cell", sheet: "...", row: 5, column: "G", value: "New Data" }
{ action: "write:row", sheet: "...", row: 5, updates: { G: "Name", H: "25" } }
```

### 🔐 AUTH
```javascript
{ action: "auth:member", credentials: { method: "PHONE", value: "0123456789" } }
{ action: "auth:admin", credentials: { username: "admin", password: "pass" } }
```

### 🖼️ UPLOAD
```javascript
{ action: "upload:image", file: { fileName: "img.jpg", mimeType: "image/jpeg", base64: "data:..." } }
```

---

## 🛠️ Troubleshooting

| Masalah | Punca | Penyelesaian |
|---------|-------|--------------|
| `Failed to fetch` / `CORS error` | Deployment Apps Script tidak betul | Set `Who has access: Anyone` → Deploy **New Version** |
| `Sheet not found` | Nama sheet tidak exact match | Semak `CONFIG.SHEETS` dalam `code.gs` (case-sensitive) |
| `Permission denied` / `403` | Google Sheet sharing tertutup | Set: `Anyone with the link` → `Editor` |
| Data tidak update | Cache browser / Sync interval | Hard refresh `Ctrl+F5` / Semak `CONFIG.SYNC` |
| `doGet not found` | Kod backend tidak lengkap | Pastikan fungsi `doGet()` & `doOptions()` wujud |

### 🧪 Ujian Sambungan Pantas (Browser Console)
```javascript
fetch("https://script.google.com/macros/s/YOUR_URL/exec", {
  method: "POST",
  headers: {"Content-Type": "application/json"},
  body: JSON.stringify({action: "system:status"})
}).then(r=>r.json()).then(console.log).catch(console.error);
// ✅ Expected: {success: true, status: "healthy", ...}
```

---

## 🔐 Nota Keselamatan & Produksi
- **Tiada Data Sensitif di Frontend:** Semua pengesahan & data melalui Apps Script backend.
- **Input Sanitization:** Semua input pengguna di-escape sebelum render DOM (XSS protected).
- **Session Timeout:** Auto-logout selepas 1 jam tidak aktif.
- **CORS Locked:** Hanya domain Vercel anda boleh access API.
- **Log Rotation:** Field log (`AL` / `L`) auto-truncate jika melebihi 50,000 aksara.
- **Column-Based Access:** Frontend hanya tahu kolum yang dinyatakan dalam `CONFIG`. Tidak boleh akses kolum tersembunyi.

---

## 🔄 Kemaskini Fitur (Zero Backend Changes)
Nak tambah UI/fitur baru? **Hanya edit `index.html`**:
1. Tambah definisi field dalam `CONFIG.COLUMNS` (frontend)
2. Tambah render function dalam modul `app.fields`
3. Deploy ke Vercel
> ⚠️ `code.gs` **tidak perlu diubah** kerana ia generic CRUD engine.

---

## 📞 Sokongan & Laporan Bug
Jika menghadapi masalah teknikal atau error luar jangkaan:
- 📧 `pfareezonzz01@gmail.com`
- 📧 `developerfareezonzz@gmail.com`
- 📎 Sertakan: Screenshot console (F12), URL Vercel, nama sheet, & langkah yang dilakukan

---

> 📘 **Hak cipta Sistem Pengurusan Identiti Membership FareezOnzz online dipelihara sejak 22 November 2025**  
> 🏭 *Production Edition v1.0.0 | Architecture: Configuration-Driven | Theme: Professional Blue/Yellow/White* 🚀✨
```
