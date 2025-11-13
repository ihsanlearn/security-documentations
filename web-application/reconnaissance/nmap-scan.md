# Nmap Scan

***

#### Host Discovery

Menemukan host (IP/Server) yang online pada sebuah jaringan

#### Port Scanning

Menemukan port mana yang open, closed, atau filtered (ada firewall) pada host tersebut

#### Service & Version Detection

Mencari tahu software dan versi apa yang berjalan dibalik port yang terbuka (eg. "Apache 2.4.18" atau "OpenSSH 7.2".

#### OS Detection

Mencoba menebak sistem operasi apa yang digunakan host

#### Scripting (Nmap Scripting Engine)

Menjalankan script kecil untuk menguji kerentanan umum secara otomatis.

***

### Best Practice

```
nmap -sV -sC -T4 [target/ip/domain]
```

*   **`-sV` (Service & Version Detection)**

    Kenapa? Nmap tidak hanya bilang "Port 80 terbuka". Ia akan bilang, "Port 80 menjalankan `Apache/2.4.18 (Ubuntu)`". Anda bisa langsung Google "Apache 2.4.18 vulnerability" dan menemukan _software_ usang.
*   **`-sC` (Default Scripts)**

    Kenapa? Nmap akan menjalankan skrip _default_ yang aman. Jika ia menemukan port FTP (21), ia akan mencoba _login_ sebagai `anonymous:anonymous`. Jika menemukan port _database_, ia akan memeriksa kredensial _default_. Ini langsung menemukan miskonfigurasi "Kredensial Bawaan".
*   **`-T4` (Timing Template "Aggressive")**

    Kenapa? Nmap bisa sangat lambat. `-T3` adalah _default_ (normal). `-T4` mempercepat pemindaian tanpa terlalu "berisik" atau membebani _server_ target. Hindari `-T5` (Insane) pada target _live_ karena bisa memicu alarm _firewall_ atau bahkan membuat layanan _crash_.
*   **`-p-` (Scan All Ports)**

    Kenapa? Secara _default_, Nmap hanya memindai 1.000 port paling umum. _Developer_ yang "nakal" mungkin menyembunyikan panel admin atau _database_ di _port_ aneh seperti `33333`. Flag `-p-` akan memindai semua 65.535 port. Ini membutuhkan waktu lama, tapi di sinilah Anda menemukan "harta karun" A02 (Layanan yang Tidak Perlu Terekspos).

***

## Flags & opsi penting (dijelaskan per bagian)

* `-p <ports>` — port(s) to scan. Contoh: `-p 22,80,443` atau `-p-` (semua 1-65535). Gunakan `-p-` hati-hati: heavy.
* `-T0..-T5` — timing templates (0 slowest/stealthiest → 5 fastest/most aggressive). Untuk production gunakan `-T2` atau `-T3`. `-T4/-T5` di lab atau saat kamu mengontrol target.
* `--min-rate <n>` — minimum packets per second; berguna kalau ingin memastikan throughput minimal. Gunakan dengan hati-hati.
* `--max-retries <n>` — jumlah retry paket; kurangi retries untuk jaringan baik, tingkatkan untuk jaringan lossy.
* `--host-timeout <ms>` — abort host jika melebihi waktu tertentu; mencegah scan macet di satu host.
* `-sV --version-intensity <level>` — tingkat agresivitas fingerprint version detection (0–9). Turunkan untuk cepat.
* `--script <scripts>` — jalankan NSE script(s). Bisa pakai kategori, contoh: `--script vuln` atau list `http-vuln*`.
* `--script-args` — argumen untuk NSE (mis. cred lists untuk brute).
* `-oA <basename>` — output tiga format (normal, xml, grepable). Sangat berguna untuk penyimpanan.
* `-oX/-oN/-oG/-oJ` — output XML / normal / grepable / JSON (nmap 7.80+ mendukung `-oJ` untuk JSON).
* `--reason` — tampilkan alasan port ditandai (RST/timeout/etc.).
* `--open` — hanya tunjukkan port yang terbuka (hilangkan closed/filtered dari output).
* `-v/-vv` — verbose level; `-d` untuk debugging.
* `--top-ports <N>` — scan N port paling populer (shortcut efisien).
* `-sC` — jalankan script default (equivalent `--script=default`). Berguna tetapi tahu apa yang berjalan.

***

| **Contoh Output Nmap**                                                                                                                 | **Penilaian**                           | **Kenapa Ini Miskonfigurasi (A02)?**                                                                                                                                                               |
| -------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p><code>PORT STATE SERVICE</code></p><p><br></p><p><code>80/tcp open http</code></p><p><br></p><p><code>443/tcp open https</code></p> | Aman ✅                                  | (Best Practice). Hanya port web yang diperlukan yang terbuka. Semua port lain (`22`, `3306`, dll) idealnya `closed` atau `filtered`.                                                               |
| `PORT STATE SERVICE VERSION`\<a/br>`22/tcp open ssh OpenSSH 7.2p2`                                                                     | Berbahaya 🚩                            | (Layanan Terekspos). Port SSH/RDP tidak seharusnya terbuka untuk seluruh dunia (`0.0.0.0`). Ini adalah undangan _brute force_. Seharusnya di-filter (`filtered`) dan hanya mengizinkan IP _admin_. |
| <p><code>PORT STATE SERVICE VERSION</code></p><p><br></p><p><code>3306/tcp open mysql MySQL 5.7.17</code></p>                          | Kritis 😱                               | (Layanan Terekspos). _Database_ tidak boleh terekspos ke internet. Ini miskonfigurasi _firewall_/_security group_ yang fatal.                                                                      |
| <p><code>PORT STATE SERVICE VERSION</code></p><p><br></p><p><code>21/tcp open ftp vsftpd 3.0.3</code></p><p><br></p><p>`</p>           | ftp-anon: Anonymous FTP login allowed\` | Kritis 😱                                                                                                                                                                                          |
| <p><code>PORT STATE SERVICE VERSION</code></p><p><br></p><p><code>8080/tcp open http-proxy Apache Tomcat 9.0.1</code></p>              | Mencurigakan 🤔                         | (Software Usang & Layanan Terekspos). Port 8080 sering digunakan untuk panel admin (Jenkins, Tomcat). Versi 9.0.1 sangat usang dan punya banyak eksploit. Ini temuan ganda!                        |
| <p><code>PORT STATE SERVICE</code></p><p><br></p><p><code>22/tcp filtered ssh</code></p>                                               | Aman ✅                                  | (Dikonfigurasi dgn Benar). `filtered` berarti _firewall_ memblokir port tersebut. Ini bagus! _Attacker_ bahkan tidak bisa memastikan apakah port itu ada atau tidak.                               |
