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

