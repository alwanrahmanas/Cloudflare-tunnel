# ⚙️ Instalasi n8n (Docker + WSL2 + Cloudflare Tunnel – Web UI)

Dokumen ini merangkum **instalasi n8n lengkap** berdasarkan **pengalaman setup nyata** di **PC lokal (Windows)** menggunakan **Docker + WSL2** dan dipublikasikan ke Internet melalui **Cloudflare Tunnel FULL via Web UI (Token)**.

Fokus utama:

* **Stabil** untuk jangka panjang
* **Mudah dipindah saat ganti PC**
* **Tanpa VPS**
* **Minim drama konfigurasi**

---

## 🎯 Tujuan

* Menjalankan **n8n** di PC lokal (Docker)
* Mengalokasikan **resource penuh PC** ke n8n
* Mengakses n8n via:

  * `http://localhost:5678`
  * Domain publik melalui **Cloudflare Tunnel (Web UI)**

---

## 🧱 Arsitektur

```
Browser
  ↓
Cloudflare DNS
  ↓
Cloudflare Tunnel (Web UI + Token)
  ↓
cloudflared (Windows Service)
  ↓
Docker (WSL2)
  ↓
n8n (localhost:5678)
```

---

## 1️⃣ Prasyarat

* Windows 10 / 11
* Docker Desktop (WSL2 backend)
* Domain aktif di Cloudflare
* Cloudflare Tunnel **ACTIVE** (via Web UI)
* PC **tidak sleep / hibernate**

---

## 2️⃣ Optimasi WSL2 (WAJIB)

Agar n8n bisa memakai **full resource PC**, buat file berikut:

**Lokasi (WAJIB tepat):**

```
C:\Users\<USERNAME>\.wslconfig
```

Isi file:

```ini
[wsl2]
memory=0
processors=0
swap=0
localhostForwarding=true
```

Restart:

```powershell
wsl --shutdown
```

Restart **Docker Desktop**.

---

## 3️⃣ Jalankan n8n dengan Docker

### 3.1 Buat Docker Volume (Persisten)

```powershell
docker volume create n8n_data
```

### 3.2 Jalankan Container n8n

```powershell
docker run -d --name n8n \
  -p 5678:5678 \
  -e TZ="Asia/Jakarta" \
  -e GENERIC_TIMEZONE="Asia/Jakarta" \
  -e N8N_RUNNERS_ENABLED=true \
  -e EXECUTIONS_MODE=main \
  -e N8N_CONCURRENCY_PRODUCTION_LIMIT=0 \
  -v n8n_data:/home/node/.n8n \
  docker.n8n.io/n8nio/n8n
```

Cek status:

```powershell
docker ps
```

---

## 4️⃣ Verifikasi n8n (Localhost)

Buka browser:

```
http://localhost:5678
```

Jika halaman setup/login muncul → **n8n berjalan dengan benar**.

---

## 5️⃣ Publikasikan n8n ke Internet (Cloudflare Tunnel – Web UI)

> Menggunakan **Cloudflare Tunnel FULL dari Web UI** (tanpa `config.yml`).

### 5.1 Tambahkan Public Hostname

Di **Cloudflare Dashboard → Zero Trust → Tunnels → Tunnel kamu**:

| Hostname             | Type | Service                 |
| -------------------- | ---- | ----------------------- |
| `n8n.domainkamu.com` | HTTP | `http://localhost:5678` |

Cloudflare akan:

* membuat DNS otomatis
* menghubungkan tunnel ke localhost

---

## 6️⃣ Test Akses Publik

Buka:

```
https://n8n.domainkamu.com
```

Jika n8n muncul → **setup publik sukses**.

---

## 7️⃣ Keamanan Dasar (PENTING)

### 🔐 Opsi 1 — Basic Auth n8n

Tambahkan env berikut (opsional):

```env
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=passwordkuat
```

### 🔐 Opsi 2 — Cloudflare Access (REKOMENDASI)

* Lindungi domain dengan email / OTP
* n8n tidak terbuka ke publik

---

## 8️⃣ Best Practice Produksi (Pengalaman Nyata)

* Gunakan **subdomain khusus** (`n8n.`)
* Jangan expose n8n tanpa auth
* Matikan sleep & hibernate Windows
* Power plan: **High Performance**
* Backup workflow & credentials berkala
* Gunakan volume Docker (`n8n_data`)

---

## 🧪 Troubleshooting Umum

### ❌ Domain tidak bisa diakses

* Pastikan tunnel status **ACTIVE**
* Pastikan service `cloudflared` **Running**
* Pastikan port `5678` benar

### ❌ n8n lambat

* Pastikan `.wslconfig` aktif
* Pastikan Docker tidak membatasi resource

---

## 🔥 Kesimpulan

* n8n **sangat layak dijalankan di PC lokal**
* Cloudflare Tunnel menggantikan VPS mahal
* Setup ini **mudah dipindahkan ke PC baru**
* Cocok untuk automation skala besar tanpa biaya server

---

✍️ README ini ditulis berdasarkan pengalaman setup nyata hingga n8n stabil & dapat diakses publik.
