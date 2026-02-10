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

Selalu biasakan untuk crawl atau cari API endpoints dimanapun itu entah coba di url atau di berbagai fitur yang dapat expose API endpoint documentation.

***

## Identifying API Endpoints

Mengumpulkan berbagai informasi dan API endpoints yang bisa di dapat dengan berbagai cara (dapat di lihat di bagian Reconaissance API).

### Interacting with API endpoints

Coba untu berinteraksi dengan endpoint itu lalu amati bagaimana perilaku untuk menemukan permukaan serangan. Menginvestigasi perubahan response dengan mengubah HTTP Method atau media type atau input lainnya, jangan lupa juga untuk mengapati pesan error yang terkadang ini berguna untuk membuat HTTP Request yang valid.

#### Identifying supported HTTP methods

HTTP methods menentukan tindakan yang akan dilakukan pada sumber daya, seperti:

* GET - Mengambil data from a resource
* PATCH - Menerapkan perubahan sebagian pada sumber daya
* OPTIONS - Mengambil informasi types of request method yang bisa digunakan pada suatu sumber daya.

API endpoint memungkinkan mendukung HTTP method yang berbeda, maka dari itu sangat penting untuk mengetes semua potential method saat menginvestigasi.

#### Identifying supported Content-Types

Sering kali sebuah endpoint mengharapkan data dengan suatu format spesifik. Oleh karena itu, memungkinkan untuk berperilaku berbeda tergantung pada request content type. Mengubah content type mungkin akan:

* Triggers error yang mengungkap informasi berguna
* Bypass flawed defences
* Manfaatkan perbedaan dalam logika pemrosesan. Misalnya, sebuah API mungkin aman saat menangani data JSON tetapi rentan terhadap serangan injeksi saat menangani XML.

#### Using intruder to find hidden endpoints (can also use other tools)

Setelah menemukan beberapa API endpoint, gunakan alat untuk bruteforoce/fuzzing endpoint lain untuk menemukan lebih banyak attack surface dan melakukan pengetesan pada setiap endpoint.

***

## Finding Hidden Parameters

Gunakan berbagai tools untuk menemukan parameter yang mungkin tidak terdokumentasi, ini menjadi peluang baru untuk mencari tahu perilaku aplikasi.

### Mass Assignment Vulnerabilities

Secara sederhana, ini adalah kerentanan di mana aplikasi secara otomatis memetakan input dari user (biasanya dalam bentuk JSON di API) langsung ke database atau objek internal tanpa melakukan filter mana aja yang boleh diubah, seperti contoh kode dibawah ini:

```typescript
app.put('/api/profile/update', async (req, res) => {
    // SANGAT BERBAHAYA: req.body langsung dimasukkan ke fungsi update
    await User.update(req.body, {
        where: { id: req.user.id }
    });
    
    res.send({ status: "Success" });
});
```

sangat rentan dengan request seperti ini:

```json
// default
{
  "username": "ihsan_hacker",
  "bio": "Sedang belajar Bug Bounty"
}

// after modification
{
  "username": "ihsan_hacker",
  "bio": "Sedang belajar Bug Bounty",
  "is_admin": true
}
```

{% hint style="info" %}
Disini finding hidden parameter sangat diperlukan, karena ketika kita menemukan mass assignment vulnerability kita dapat menggunakan parameter untuk mempengaruhi aplikasi.
{% endhint %}

***

## Server-Side Parameter Pollution

