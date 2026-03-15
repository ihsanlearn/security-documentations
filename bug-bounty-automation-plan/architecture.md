# Architecture

***

### FASE 1: Input & Orchestration (The Brain)

```
Start from frontend dashboard
                                                    
                          Input Target
                                │
                     Send request to FastAPI
                                |
                        FastAPI Handling
                                |
                     ┌──────────┴──────────┐
      PostgreSQL (presistence)           Redis
                                |
                              Perfect
```

Flow

* Memasukan target domain
* Frontend kirim ke FastAPI
* FastAPI menerima request, memvalidasi formatnya lalu menyimpan ke PostgreSQL (dengan status queued sebagai presistence data) akan tetapi juga melaporkan ke Redis (sebagai antrian catatan task)
* Perfect sebagai manager yang memantau redis, juga mendeteksi new task dan memicu workflow. Karena ini juga, Perfect dapat mengatur agar Fase 2 selesai dulu sebelum Fase 3 dimulai

{% hint style="info" %}
Izinkan input multiple target.



To do:

* Status yang disimpan ke PostgreSQL
{% endhint %}

### FASE 2: Passive & Active Recon (The Muscle - Go Tools)

```
         Redis
           |
        Perfect
           |
     Perfect Worker
```

**Subdomain Enumeration (sequence 1)**

* Sistem menjalankan `subfinder`, `assetfinder`, dan `amass` secara paralel.
* Logic: Hasil digabung (unique) disimpan ke tabel `subdomains`.

**DNS Resolution (sequence 2)**

* `dnsx` digunakan untuk memilah subdomain yang sudah mati.

**HTTP Probing & Fingerprinting (sequence 3)**

* `httpx` memeriksa status code, judul halaman, dan teknologi (`Wappalyzer` logic).
* Output: Kamu sekarang tahu mana yang `200 OK` dan mana yang pakai `Laravel`, `React`, atau `Nginx`.

**Mencari pintu masuk tersembunyi (sequence 4)**

* Spidering: `katana` atau `gau` merayap di setiap subdomain yang hidup untuk mencari file `.js`, `.php`, atau endpoint API.
* Parameter Discovery: `paramspider` atau `arjun` mencari parameter tersembunyi (misal: `?debug=true` atau `?admin=1`).
* Data Ingestion: Semua URL dan parameter baru masuk ke tabel `endpoints`.

### FASE 3: Vulnerability Engine (The Hunter) (not started)

Mencari celah secara otomatis.

1. Template Scanning: `nuclei` dijalankan dengan kumpulan template (CVE terbaru, miskonfigurasi, dll.).
2. Fuzzing: Untuk endpoint yang menarik, `ffuf` melakukan directory brute-force.
3. Dedicated Scan: Jika ditemukan input form, `dalfox` (XSS) atau `sqlmap` (SQLi) dipicu untuk pengujian spesifik.

#### 5. The AI Intelligence Layer (Decision Making)

Ini bagian paling keren di arsitektur kamu. AI tidak memindai, tapi berpikir.

* AI Agent memantau tabel `Potential` di Postgres.
* Begitu ada temuan, AI mengambil konteks: "Oh, ini ada temuan dari Nuclei di `admin.google.com`, teknologinya pakai `Go`, dan ada error `SQL Syntax`."
* AI melakukan Refinement: Dia memerintahkan Worker 4 (Exploiter) untuk menjalankan `sqlmap` dengan payload khusus yang disarankan AI.
* AI memfilter: _"Ini False Positive, abaikan"_ atau _"Ini Critical, segera buat laporan!"_

#### 6. The Alerting (Output Phase)

* Jika AI menganggap temuan itu valid (High/Critical), sistem memicu Webhook.
* Kamu dapat notifikasi di Telegram/Discord lengkap dengan link ke Dashboard kamu untuk melihat detail PoC-nya.

***

### Visualisasi State Management

Dalam arsitektur pro, setiap target harus punya State (Status). Ini contoh flow statusnya di database kamu nanti:

| **Step** | **Status di DB**           | **Action**                        |
| -------- | -------------------------- | --------------------------------- |
| 1        | `PENDING`                  | Menunggu antrean recon.           |
| 2        | `RECON_IN_PROGRESS`        | Subfinder sedang jalan.           |
| 3        | `PROBING`                  | Httpx sedang cek mana yang hidup. |
| 4        | `SCANNING`                 | Nuclei/Ffuf sedang jalan.         |
| 5        | `AI_ANALYZING`             | AI sedang memvalidasi temuan.     |
| 6        | `COMPLETED` / `VULN_FOUND` | Selesai atau ditemukan bug.       |

***

### Kenapa Arsitektur ini "Professional Level"?

1. Asynchronous: Website kamu nggak akan "loading" lama saat nunggu scan selesai. Semuanya jalan di belakang layar.
2. Scalable: Kalau kamu mau scan 100 target sekaligus, kamu tinggal tambah jumlah Worker (misal: jalankan 10 container worker sekaligus).
3. Resilient: Kalau VPS kamu _crash_ saat scan, Redis masih menyimpan daftar antrean. Begitu VPS nyala, scan lanjut otomatis.
