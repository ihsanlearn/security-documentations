# GraphQL

***

## What is GraphQL?

GraphQL adalah query language yang dirancang untuk memfasilitasi komunikasi yang efisien antara client dan server. Ini memungkinkan pengguna untuk mengakses data secara spesifik yang diinginkan pada response.&#x20;

GraphQL mendefinisikan kontrak dimana client memungkinkan berkomunikasi dengan server. Client tidak perlu tahu dimana letak data. Melainkan, client mengirim query ke GraphQL server, lalu ia fetch data.

## How GraphQL works?

GraphQL schema define the structure of the service's data, listing the available objects (known as types), fields, and relationships.

The data described by a GraphQL schema can be manipulated using three types of operations:

* Queries fetch data
* Mutations add, change or remove data
* Subscriptions are similar to queries, but set up a permanent connection by which a server can proactively push data to a client in the specified format.

Hampir semua operasi GraphQL menggunakan endpoint yang sama dan pada umumnya menggunakan POST request. Dengan GraphQL, type dan name of operation menentukan bagaimana query ditangani.

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

// ! menandakan value nullable, wajib
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

id, name.firstname, dan name.lastname disini adalah fields yang diminta.

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

### Variables

```
// Some code
```
