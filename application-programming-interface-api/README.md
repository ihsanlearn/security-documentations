# Application Programming Interface (API)

***

## API Reconnaissance

Untuk memulai pengetesan sebuah API, sebagai attacker harus menemukan berbagai informasi tentang API untuk menemukan permukaan serangannnya.

1. Identifikasi API Endpoint
2. Melakukan berbagai interaksi dengan API Endpoint yang ditentukan, seperti:
   1. Mengidentifikasi input data yang diproses oleh API, termasuk parameter wajib dan opsionalnya,
   2. Jenis permintaan yang diterima API, termasuk HTTP Method yang didukung dan media formatnya,
   3. Rate Limit dan mekanisme autentikasi.

***

## API Documentation

API biasanya terdokumentasi sehingga developer tau bagaimana penggunaanya dan integrasinya. Terdapat dua jenis dokumentasi API yaitu Human-Readable dan Machine-Readable. Human-Readable dirancang untuk developer agar memahami bagaimana penggunaannya, sedangkan Machine-Readable dirancang untuk diproses oleh perangkat lunak guna mengotomatiskan tugas-tugas seperti integrasi dan validasi API. Ini dituliskan dengan format terstruktur seperti JSON atau XML.

Dokumentasi API sering kali tersedia untuk public, maka dari itu selalu mulai untuk review dokumentasi API.

### Discovering API documentation

Gunakan Burp Scanner atau tool lain untuk crawl API Endpoints, seperti:

```
/api
/swagger/index.html
/openapi.json
```

Jika menemukan sebuah endpoint, pastikan untuk selalu menginvestigasi base path-nya, seperti jika menemukan `/api/swagger/v1/users/123` pastikan juga untuk menginvestigasi:

```
/api/swagger/v1
/api/swagger
/api
```

Selalu biasakan untuk crawl atau cari API endpoints dimanapun itu entah coba di url atau di berbagai fitur yang dapat expose API endpoint.

***

