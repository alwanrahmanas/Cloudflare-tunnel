# 📲 Instalasi WAHA (WhatsApp HTTP API)

Dokumen ini merangkum **langkah instalasi WAHA (WhatsApp HTTP API)** berdasarkan **pengalaman setup nyata** di **Windows + Docker + Cloudflare Tunnel (FULL Web UI)**. Fokus pada **cara yang paling stabil, minim error, dan cocok untuk PC lokal (tanpa VPS)**.

---

## 🎯 Tujuan

* Menjalankan **WAHA (WhatsApp HTTP API)** di **PC lokal** menggunakan Docker
* Mengakses WAHA via:

  * `http://localhost:3000`
  * **Domain publik** melalui **Cloudflare Tunnel (Web UI)**
* Setup **aman**, **persisten**, dan **mudah dipindah saat ganti PC**

---

## 🧱 Arsitektur

```
WhatsApp Mobile
  ↓
WAHA (Docker Container)
  ↓
http://localhost:3000
  ↓
Cloudflare Tunnel (Web UI + Token)
  ↓
https://waha.domainkamu.com
```

---

## 1️⃣ Prasyarat

* Windows 10 / 11
* Docker Desktop (WSL2 backend)
* Cloudflare Tunnel **sudah ACTIVE** (via Web UI)
* Domain aktif di Cloudflare
* Cloudflared **ter-install & running sebagai Windows Service**

---

## 2️⃣ Jalankan Container WAHA

Gunakan image resmi:

```powershell
docker pull devlikeapro/waha:latest
```

Jalankan container:

```powershell
docker run -d --name waha \
  -p 3000:3000 \
  -e WAHA_API_KEY=your-secret-api-key \
  -v waha_data:/app/storage \
  devlikeapro/waha:latest
```

Cek container:

```powershell
docker ps
```

---

## 3️⃣ Verifikasi WAHA (Localhost)

Buka browser:

```
http://localhost:3000/health
```

Hasil sukses:

```json
{"status":"ok"}
```

---

## 4️⃣ Ambil Kredensial Dashboard WAHA

Saat pertama kali start, cek log:

```powershell
docker logs waha
```

WAHA akan generate kredensial seperti:

```env
WAHA_API_KEY=xxxxxxxx
WAHA_DASHBOARD_USERNAME=admin
WAHA_DASHBOARD_PASSWORD=xxxxxxxx
```

📌 **Simpan nilai ini** (disarankan ke `.env`).

---

## 5️⃣ Akses Dashboard WAHA

Buka:

```
http://localhost:3000
```

Login menggunakan:

* Username: `admin`
* Password: sesuai log

---

## 6️⃣ Publikasikan WAHA ke Internet (Cloudflare Tunnel – Web UI)

> Menggunakan **Cloudflare Tunnel FULL dari Web UI** (tanpa `config.yml`).

### 6.1 Tambahkan Public Hostname

Di **Cloudflare Dashboard → Zero Trust → Tunnels → Tunnel kamu**:

| Hostname              | Type | Service                 |
| --------------------- | ---- | ----------------------- |
| `waha.domainkamu.com` | HTTP | `http://localhost:3000` |

Cloudflare akan:

* membuat DNS otomatis
* menghubungkan tunnel ke localhost

---

## 7️⃣ Test Akses Publik

Buka:

```
https://waha.domainkamu.com/health
```

Jika:

```json
{"status":"ok"}
```

➡️ **WAHA siap dipakai secara publik**.

---

## 8️⃣ Keamanan Dasar (WAJIB)

### 🔐 Gunakan API Key

Setiap request WAHA wajib menyertakan header:

```
X-API-KEY: your-secret-api-key
```

---

## 9️⃣ Best Practice Produksi (Pengalaman Nyata)

* Gunakan **subdomain terpisah** untuk WAHA
* Jangan expose port 3000 ke jaringan lokal publik
* Gunakan **Cloudflare Access** bila perlu (email / OTP)
* Simpan Docker volume (`waha_data`) untuk persist session
* Matikan sleep & hibernate di PC

---

## 🧪 Troubleshooting Umum

### ❌ WAHA tidak bisa diakses via domain

* Tunnel status harus **ACTIVE**
* Pastikan service `cloudflared` running
* Pastikan port `3000` benar

### ❌ QR Code tidak muncul

* Cek log container
* Pastikan browser bisa load WebSocket

---

## 🔥 Kesimpulan

* WAHA **stabil dijalankan di PC lokal**
* Cloudflare Tunnel (Web UI) **menggantikan VPS mahal**
* Setup ini **mudah dipindah ke PC baru**
* Cocok untuk **automation WhatsApp + n8n**

---

✍️ README ini ditulis berdasarkan pengalaman setup nyata hingga WAHA berjalan stabil via domain publik.
