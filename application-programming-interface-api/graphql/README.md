# GraphQL

***

## What is GraphQL?

GraphQL adalah query language yang dirancang untuk memfasilitasi komunikasi yang efisien antara client dan server. Ini memungkinkan pengguna untuk mengakses data secara spesifik yang diinginkan pada response.&#x20;

GraphQL mendefinisikan kontrak dimana client memungkinkan berkomunikasi dengan server. Client tidak perlu tahu dimana letak data. Melainkan, client mengirim query ke GraphQL server, lalu ia fetch data.

***

## How GraphQL works?

GraphQL schema define the structure of the service's data, listing the available objects (known as types), fields, and relationships.

The data described by a GraphQL schema can be manipulated using three types of operations:

* Queries fetch data
* Mutations add, change or remove data
* Subscriptions are similar to queries, but set up a permanent connection by which a server can proactively push data to a client in the specified format.

Hampir semua operasi GraphQL menggunakan endpoint yang sama dan pada umumnya menggunakan POST request. Dengan GraphQL, type dan name of operation menentukan bagaimana query ditangani.

```graphql
/graphql
/api
/api/graphql
/graphql/api
/graphql/graphql
/graphql/v1
```

***

## What is GraphQL schema?

Pada GraphQL, schema ini merepresentasikan kontrak antara frontend dan backend. Sistem ini mendefinisikan data yang tersedia sebagai serangkaian tipe. Jenis-jenis ini kemudian dapat diimplementasikan oleh suatu layanan.

Dibawah ini merupakan contoh simple schema definition untuk Product type.

```graphql
type Product {
    id: ID!
    name: String!
    description: String!
    price: Int
}

# ! menandakan value nullable, wajib
```

Schema wajib memiliki setidaknya satu query yang tersedia. Biasanya juga mengandung detail dari mutasi yang tersedia.

***

## What are GraphQL queries?

GraphQL queries mengambil data dari database/data store. Ini mirip seperti GET request pada REST API.

Queries biasanya memiliki beberapa komponen sebagai berikut:

* A query operation type. Technicaly optional but encourage, explicitly tell the server that the incoming request is a query
* A query name. Can be anything you want
* A data structure. the data that the query should return
* Optionally, one or more arguments.&#x20;

Contoh query dengan nama `myGetProductQuery` yang meminta field nama dan deskripsi product dengan id 123:

```graphql
query myGetProductQuery {
    getProduct(id: 123) {
        name
        description
    }
}
```

***

## What are GraphQL mutations?

Mutations change data in some way, entah edit, hapus, atau menambahkan. Ini mirip seperti POST, PUT, DELETE pada REST API.

Sama hal nya seperti queries, mutations juga memiliki operation type, name, and structure for the returned data. Namun, mutations selalu membutuhkan inputof some type.&#x20;

Dibawah ini merupakan contoh mutations untuk membuat product baru dan response yang terkait dengannya. Pada kasus ini, layanan terkonfigurasi untuk otomatis memberikan id pada produk baru, seperti:

```graphql
# contoh mutations request 
mutation {
    createProduct(name: "Flamin' Cocktail Glasses", listed: "yes") {
        id
        name
        listed
    }
}
```

```json
// contoh mutation response
{
    "data": {
        "createProduct": {
            "id": 123,
            "name": "Flamin' Cocktail Glasses",
            "listed": "yes"
        }
    }
}
```

***

## Components of queries and mutations

### Fields

`id`, `name.firstname`, dan `name.lastname` disini adalah fields yang diminta.

```graphql
query myGetEmployeeQuery {
    getEmployees {
        id        
        name {    
            firstname
            lastname
        }
    }
}

```

### Arguments

id:1 disini merupakan argument, lalu server hanya merespon dengan id yang sesuai.

```graphql
query myGetEmployeeQuery {
    getEmployees(id:1) {
        name {
            firstname
            lastname
        }
    }
}

```

{% hint style="info" %}
If user-supplied arguments are used to access objects directly then a GraphQL API can be vulnerable to access control vulnerabilities such as insecure direct object references (IDOR).
{% endhint %}

### Variables

```graphql
query getEmployeeWithVariable($id: ID!) {
    getEmployees(id:$id) {
        name {
            firstname
            lastname
        }
     }
}

Variables:
{
    "id": 1
}
```

### Aliases

```graphql
# invalid query
query getProductDetails {
    getProduct(id: 1) {
        id
        name
    }
    getProduct(id: 2) {
        id
        name
    }
}

# valid query
query getProductDetails {
    product1: getProduct(id: "1") {
        id
        name
    }
    product2: getProduct(id: "2") {
        id
        name
    }
}

```

```json
// response
{
    "data": {
        "product1": {
            "id": 1,
            "name": "Juice Extractor"
         },
        "product2": {
            "id": 2,
            "name": "Fruit Overlays"
        }
    }
}
```

{% hint style="info" %}
Using aliases with mutations effectively enables you to send multiple GraphQL messages in one HTTP request, but it can be vulnerable like bypass some rate limit.
{% endhint %}

### Fragments

Fragments adalah bagian yang dapat digunakan kembali pada queries atau mutations. Perilaku fragments mirip dengan function.

```graphql
fragment productInfo on Product {
    id
    name
    listed
}

query {
    getProduct(id: 1) {
        ...productInfo
        stock
    }
}

```

```json
// response
{
    "data": {
        "getProduct": {
            "id": 1,
            "name": "Juice Extractor",
            "listed": "no",
            "stock": 5
        }
    }
}

```

```graphql
# example
query GetComparison($userA: ID!, $userB: ID!, $showDetail: Boolean!) {
   pembeli: user(id: $userA) {
      ...UserFields 
    } 
   penjual: user(id: $userB) {
      ...UserFields 
    email @include(if: $showDetail) 
   } 
 } 
 
 fragment UserFields on User { 
    id 
    username 
    bio 
    reputation(format: \"PERCENTAGE\") 
 }
```

***

## Subscription

Subscription memungkinkan client untuk tetap terkoneksi dengan server dalam waktu yang lama, berbeda dengan query yang setelah request response koneksi terputus. Subscription biasanya diimplementasikan menggunakan WebSocket.

```graphql
subscription OnNewMessage { newMessage { sender text timestamp } }
```

***

## Introspections

Introspections adalah fungsi built-in GraphQL yang memungkinkan untuk mendapatkan informasi tentang schema melalui query

```graphql
query IntrospectionQuery {
  __schema {
    queryType { name }
    mutationType { name }
    subscriptionType { name }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
      onOperation  #Often needs to be deleted to run query
      onFragment   #Often needs to be deleted to run query
      onField      #Often needs to be deleted to run query
    }
  }
}

fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}

fragment InputValue on __InputValue {
  name
  description
  type { ...TypeRef }
  defaultValue
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```

{% hint style="info" %}
If introspection is enabled but the above query doesn't run, try removing the `onOperation`, `onFragment`, and `onField` directives from the query structure. Many endpoints do not accept these directives as part of an introspection query, and you can often have more success with introspection by removing them.
{% endhint %}
