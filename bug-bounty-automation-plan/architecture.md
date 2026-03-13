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
* FastAPI menerima request, memvalidasi formatnya lalu menyimpan ke PostgreSQL (dengan status queued) dan melaporkan ke Redis
* Perfect mendeteksi new task dan memicu workflow. Perfect juga mengatur agar Fase 2 selesai dulu sebelum Fase 3 dimulai

{% hint style="info" %}
Izinkan input multiple target.



To do:

* Status yang disimpan ke PostgreSQL
{% endhint %}

#### 2. The Orchestration (Task Distribution)

Di sinilah Celery atau Temporal bekerja.

* Worker 1 (Recon Specialist) melihat ada antrean baru. Dia mengambil tugas "Recon google.com".
* Dia menjalankan `subfinder`, `amass`, dan `assetfinder`.
* Hasilnya (ribuan subdomain) tidak disimpan di file `.txt`, tapi langsung di-_insert_ ke PostgreSQL.

#### 3. The Processing & Filtering (Data Refinement)

Setelah subdomain terkumpul, kita tidak langsung scan semuanya (buang-buang resource).

* Worker 2 (Prober) mengambil daftar subdomain dari Postgres.
* Dia menjalankan `httpx` untuk cek: "Mana yang hidup? Mana yang pakai HTTPS? Apa status code-nya?"
* Data di Postgres di-_update_. Sekarang kita punya daftar target yang valid dan hidup.

#### 4. The Vulnerability Engine (Scanning Phase)

Sekarang mesin utama bekerja.

* Worker 3 (Scanner) menjalankan `nuclei` hanya pada target yang statusnya `is_alive = true`.
* Jika ditemukan potensi celah (misal: _Exposed Dashboard_), hasilnya dikirim kembali ke Database dengan status `Potential`.

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
