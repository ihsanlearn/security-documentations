# GraphQL Hunt

***

## Finding GraphQL endpoints

Sebelum melakukan pengetesan pada GraphQL API, maka butuh untuk menemukan endpointnya terlebih dahulu.

{% hint style="info" %}
Burp Scanner can automatically test for GraphQL endpoints as part of its scans. It raises a "GraphQL endpoint found" issue if any such endpoints are discovered.
{% endhint %}

### Universal queries

Jika mengirimkan `query { __typename }` ke GraphQL endpoint manapun, ini akan menyertakan string `{"data": {"__typename": "query"}}` pada responsnya.

Ini berguna untuk meyakinkan bahwa itu adalah endpoint GraphQL, karena tinggal POST request seperti dibawah ini untuk meyakinkan:

```http
// request
POST /target
Content-Type: application/json

{
  "query": "query{__typename}"
}

// response
{"data":{"__typename":"query"}}
```

### Common endpoint names

If these common endpoints don't return a GraphQL response, you could also try appending `/v1` to the path.

```
/graphql
/api
/api/graphql
/graphql/api
/graphql/graphql
```

{% hint style="info" %}
GraphQL services will often respond to any non-GraphQL request (incomplete request) with a "query not present" or similar error.
{% endhint %}

### Request methods

Pada dasarnya, best practice implementasi graphql selau menggunakan method POST dan juga content type `application/json`. Namun, coba gunakan request method yang berbeda dan juga mungkin untuk content type yang brbeda seperti `application/www-form-urlencoded`.

### Initial testing

Setelah menemukan dan memastikan suatu graphql endpoint, lakukan pengetesan yang menghasilkan informasi bagaimana endpoint itu bekerja. Jangan lupa untuk berperilaku seperti user biasa terlebih dahulu dan amati apa yang dilakukan aplikasi terhadap suatu endpoint, amati query, mutations, dll.

***

## Exploiting unsanitized arguments

Pada fase ini, mulai untuk melihat sebuah kerentanan. Testing query arguments is a good place to start. Jika API menggunakan argumen untuk mengakses objek secara langsung, maka API tersebut mungkin rentan terhadap access control vulnerabilities.

```graphql
query {
  user(id: 1) {
    posts {
      id
      content
    }
  }
}

## coba untuk ubah id / argumen apapun
## apabila user aman, mungkin nested post tidak ada filtering
```

***

## Discovering Schema information

### Using introspection & Running a full introspection query&#x20;

Baca pada dokumentasi utama GraphQL, [disini](./#introspections).

### Probing for introspection

Coba gunakan query ini untuk mengecek apakah introspection enabled, Jika introspection enabled, maka responnya adalah nama dari query yang tersedia.

```json
{
    "query": "{__schema{queryType{name}}}"
}
```

### Visualizing introspection results

Response introspection query dapat penuh dengan informasi, tapi sering kali sangat panjang dan susah untuk diproses/dilihat. Maka dari itu, gunakan [GraphQL visualizer](http://nathanrandal.com/graphql-visualizer/) untuk melihat bagaimana hubungan antar entitas pada schema.

### Suggestions

Meskipun introspection disable sepenuhnya, terkadang dengan suggestions dapat memungiut informasi pada struktur API. Suggestion adalah fitur dari Apollo GraphQL platform di mana server dapat menyarankan perubahan kueri dalam pesan kesalahan.

Ini umumnya digunakan ketika kueri sedikit tidak akurat tetapi masih dapat dikenali, for example "There is no entry for 'productInfo'. Did you mean 'productInformation' instead?"

Gunakan tools [Clairvoyance](https://github.com/nikitastupin/clairvoyance)
