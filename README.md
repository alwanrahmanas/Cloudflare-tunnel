# 🌐 Cloudflare Tunnel — FULL via Web UI (No config.yml, No JSON)

Dokumen ini menjelaskan **setup Cloudflare Tunnel 100% dari Web UI**, tanpa `config.yml`, tanpa file `*.json`, dan **tanpa restore file lama**. Cocok untuk **ganti PC**, **PC rumahan**, dan **anti ribet**.

---

## 🎯 Target Setup

* Tunnel dibuat **hanya dari Cloudflare Dashboard**
* Agent `cloudflared` di PC **connect via TOKEN**
* Routing hostname → `localhost` dilakukan **di Web UI**
* Auto-start via **Windows Service**

---

## 🧱 Arsitektur

```
Browser
  ↓
Cloudflare DNS
  ↓
Cloudflare Tunnel (Web-managed)
  ↓
cloudflared (Windows Service)
  ↓
Local Services (Docker / App)
```

---

## 1️⃣ Prasyarat

* Domain sudah aktif di Cloudflare
* Windows 10 / 11
* Aplikasi lokal berjalan (contoh):

  * n8n → `http://localhost:5678`
  * WAHA → `http://localhost:3000`

---

## 2️⃣ Install cloudflared (Windows)

1. Download **cloudflared Windows 64-bit**
2. Rename menjadi:

   ```
   cloudflared.exe
   ```
3. Simpan di:

   ```
   C:\cloudflared\cloudflared.exe
   ```

Cek:

```powershell
C:\cloudflared\cloudflared.exe --version
```

---

## 3️⃣ Buat Tunnel dari Cloudflare Web UI

1. Login ke **Cloudflare Dashboard**
2. Masuk **Zero Trust**
3. Pilih **Networks → Tunnels**
4. Klik **Create a tunnel**
5. Pilih **Cloudflared**
6. Beri nama tunnel

> Tunnel berhasil dibuat **tanpa CLI sama sekali**

---

## 4️⃣ Konfigurasi Hostname Routing (Web UI)

Masuk ke tunnel → **Public Hostnames** → **Add a hostname**

Contoh konfigurasi:

| Hostname          | Type | Service                 |
| ----------------- | ---- | ----------------------- |
| `n8n.domain.com`  | HTTP | `http://localhost:5678` |
| `waha.domain.com` | HTTP | `http://localhost:3000` |

✔ DNS otomatis dibuat oleh Cloudflare
✔ Tidak perlu edit DNS manual

---

## 5️⃣ Ambil TOKEN (PENTING)

Di halaman tunnel, Cloudflare akan menampilkan command:

```powershell
cloudflared.exe tunnel run --token eyJhIjoi...PANJANG...
```

📌 **Yang dipakai adalah TOKEN PANJANG**, bukan Tunnel ID (UUID).

---

## 6️⃣ Test Koneksi (Manual)

Jalankan di PowerShell biasa:

```powershell
C:\cloudflared\cloudflared.exe tunnel run --token <TOKEN_PANJANG>
```

Jika sukses:

```
INF Connected to Cloudflare
```

Status tunnel di dashboard:

```
INACTIVE → ACTIVE
```

---

## 7️⃣ Install sebagai Windows Service (Auto Start)

⚠️ Jalankan **PowerShell → Run as Administrator**

```powershell
C:\cloudflared\cloudflared.exe service install --token <TOKEN_PANJANG>
```

Start service:

```powershell
sc.exe start cloudflared
```

Cek status:

```powershell
Get-Service cloudflared
```

Harus:

```
Status : Running
```

---

## 8️⃣ Test Akhir

Buka browser:

```
https://n8n.domain.com
```

Jika halaman muncul:

✅ Tunnel aktif
✅ Routing sukses
✅ Setup selesai

---

## 🧠 FAQ Singkat

### ❓ Tunnel ID vs Token?

* Tunnel ID → identitas
* Token → autentikasi (yang dipakai agent)

### ❓ Perlu `config.yml`?

❌ Tidak (kalau pakai Web UI)

### ❓ Ganti PC perlu restore file lama?

❌ Tidak

---

## 🛡️ Best Practice (Opsional)

* Gunakan subdomain terpisah per service
* Aktifkan **Cloudflare Access** untuk proteksi login
* Simpan token di password manager
* Gunakan Docker volume untuk persist data

---

## ✅ Kesimpulan

Cloudflare Tunnel via **FULL Web UI + Token** adalah:

* Paling simpel
* Paling stabil
* Paling cocok untuk PC lokal
* Anti drama saat ganti PC

---

✍️ Ditulis berdasarkan setup nyata yang berhasil setelah trial-error.
