# Visual (aquatone)

```
cat live-hosts.txt | aquatone -ports large -out aquatone
```

#### **"Harta Karun" 💎 (Apa yang Harus Dicari di Galeri Screenshot)**

Saat kamu men-scroll galeri _screenshot_ itu, matamu harus "terlatih" untuk mencari anomali. Ini adalah target prioritas utamamu:

* Halaman Login 🔑 (Login Pages):
  * Ini adalah "pintu depan". Selalu jadi target utama. (Cari: _default credentials_, _SQL Injection_, _bypass_ otentikasi).
* Panel Admin 👑 (Admin Panels):
  * Sama seperti halaman login, tapi ini adalah "pintu ruang singgasana". Target bernilai sangat tinggi.
* Halaman Error ❌ (Error Pages):
  * Cari halaman error PHP, Java, atau Node.js yang panjang.
  * Mengapa? Mereka sering membocorkan _path_ (`/var/www/html/...`), nama _file_, versi _framework_, atau bahkan _query_ database.
* Aplikasi Internal "Aneh" 🛠️ (Grafana, Jenkins, GitLab, Kibana):
  * Jika kamu melihat _dashboard_ yang terlihat seperti _tool developer_ (Jenkins si "butler", _dashboard_ Grafana), ini adalah JACKPOT!
  * Mengapa? Aplikasi ini sering salah konfigurasi, terekspos ke publik, atau punya _password default_ (`admin:admin`).
* Halaman Server Default 📦 ("Welcome to Nginx", "It works!", "Apache Tomcat"):
  * Ini menandakan server yang "setengah jadi" di-_deploy_.
  * Mengapa? Mungkin ada miskonfigurasi server, atau _file_ sensitif terekspos di direktori lain.
* Halaman 403 Forbidden / 401 Unauthorized:
  * Jika kamu melihat banyak halaman "Forbidden".
  * Mengapa? Ini adalah target bagus untuk _bypass_ kontrol akses. Coba akses pakai _header_ `X-Forwarded-For: 127.0.0.1` atau ganti _path_ (`/admin` -> `/admin/`).
