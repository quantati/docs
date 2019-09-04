# Quantati’s API documentation

This repository contains the documentation for [Quantati](https://quantati.com)’s API.

#### Contents

- [Overview](#1-overview)
- [Authentication](#2-authentication)
- [Resources](#3-resources)
  - [Product](#31-product)
  - [Category](#32-category)
  - [Article](#33-article)
- [SDKs](SDK.md)

## 1. Overview

Quantati’s API is a JSON-based OAuth2 API. All requests are made to endpoints beginning:
`https://api.quantati.com/v1`


## 2. Authentication

In order to use any Quantati's API account, you will need a developer token. A developer token is generated upon sign-up.

## 3. Resources

The API is RESTful and arranged around resources. All requests must be made with an developer token.


### 3.1. Product

#### Overview
The Product Page API automatically extracts clean product data from a product page, including pricing info, product IDs, name, brand, .etc.

To use the Product API, perform a HTTP GET request on the following endpoint:

```
GET https://api.quantati.com/v1/product
```

Example request:

```
GET /v1/product?token=Ab6bWWFHbCcyIgZ66Vpf&url=https%3A%2F%2Fwww.ecommerce-site.com%2Fproduct%2F0691170819 HTTP/1.1
Host: api.quantati.com
Content-Type: application/json
Accept: application/json
Accept-Charset: utf-8
```

With the following fields:

| Parameter       | Type         | Required?  | Description                                     |
| -------------   |--------------|------------|-------------------------------------------------|
| token           | string       | required   | Developer token |
| url             | string       | required   | Web page URL of the product to process (URL encoded) |                                   |
| timeout         | int64        | optional   | Number of milliseconds to wait for the data retrieval from the requested URL. The default timeout is 30 seconds (30000). |

The Product API returns data in JSON format. Example response:

```
HTTP/1.1 201 OK
Content-Type: application/json; charset=utf-8

{
  humanLanguage: 'en',
  resolvedPageUrl: 'https://ecommerce-site/product/final-redirect-url',
  type: 'product',
  primaryImage: 'https://i5.cdn-stores.com/asr/92f2df9a-8ffc-4fd1-9592106d.jpeg',
  title: 'Instant Pot LUX60 6 Qt 6-in-1 Multi-Use Programmable Pressure Cooker',
  offerPrice: 69,
  regularPrice: 99,
  productId: '55505580',
  availability: true,
  url: 'https://ecommerce-site/product'
}
```

Where response data contains the following fields:

| Field         | Type         | Description                                     |
| --------------|--------------|-------------------------------------------------|
| type          | string       | Type of page (always product)               |
| url           | string       | URL of the page the API was called with  |
| resolvedPageUrl | string     | Final url in case of redirect from pageURL                 |
| title         | string       | Title of the product.                                 |
| brand         | string       | Product brand name (beta not present on all pages) |
| offerPrice    | float        | Offer or actual/final price of the product.                 |
| regularPrice  | float        | Regular or original price of the product, if available. |
| productId     | string       | Unique product ID determined by Quantati. Can be between upc, sku, mpn, or extracted from product URL |
| primaryImage  | object       | Primary image details: including url, title, width, height  |
| humanLanguage | string       | Language two-letter code ISO 639-1 of the submitted page  |
| availability  | bool         | (Beta) Item's availability, either true or false.  |

Possible errors:

| Error code           | Description                                                                                                          |
| ---------------------|----------------------------------------------------------------------------------------------------------------------|
| 400 Bad Request      | Required fields were invalid, not specified.                                                                         |
| 401 Unauthorized     | The access token is invalid or has been revoked.                                                                     |

### 3.2. Category

#### Overview
The Product Category Page API retrieves all products details from a category page. In production we cache extraction metadata therefore subsequent requests are significantly faster.

To use the Product Category API, perform a HTTP GET request on the following endpoint:

```
GET https://api.quantati.com/v1/category
```

Example request:

```
GET /v1/category?token=Ab6bWWFHbCcyIgZ66Vpf&url=https%3A%2F%2Fwww.ecommerce-site.com%2Fproduct%2F0691170819 HTTP/1.1
Host: api.quantati.com
Content-Type: application/json
Accept: application/json
Accept-Charset: utf-8
```

With the following fields:

| Parameter       | Type         | Required?  | Description                                     |
| -------------   |--------------|------------|-------------------------------------------------|
| token           | string       | required   | Developer token |
| url             | string       | required   | Web page URL of the product to process (URL encoded) |                                   |
| timeout         | int64        | optional   | Number of milliseconds to wait for the data retrieval from the requested URL. The default timeout is 30 seconds (30000). |

The Product API returns data in JSON format. Example response:

```
HTTP/1.1 201 OK
Content-Type: application/json; charset=utf-8

[
  {
    primaryImage: 'https://assets.store.com/images/w_280,h_280,f_auto,q6/ultraboost-t-s-l-shoes.jpg',
    title: 'Adidas Ultraboost',
    offerPrice: 150,
    regularPrice: 180,
    availability: true,
    productURL: 'https://store.com/us/ultraboost-s-l-shoes/EF0726.html'
  },
... more items ...

  {
    primaryImage: 'https://assets.store.com/images/w_280,h_280,f_auto,q6/superstart-s-l-shoes.jpg',
    title: 'Superstar Shoes',
    offerPrice: 80,
    regularPrice: 95,
    availability: true,
    productURL: 'https://store.com/us/superstar-s-l-shoes/FG723.html'
  },

]
```

Where response data contains an array of products the following fields:

| Field         | Type         | Description                                     |
| --------------|--------------|-------------------------------------------------|
| title         | string       | Title of the product.                                 |
| offerPrice    | float        | Offer or actual/final price of the product.                 |
| regularPrice  | float        | Regular or original price of the product, if available. |
| productId     | string       | Unique product ID determined by Quantati. Can be between upc, sku, mpn, or extracted from product URL |
| primaryImage  | object       | Primary image details: including url, title, width, height  |
| availability  | bool         | (Beta) Item's availability, either true or false.  |

Possible errors:

| Error code           | Description                                                                                                          |
| ---------------------|----------------------------------------------------------------------------------------------------------------------|
| 400 Bad Request      | Required fields were invalid, not specified.                                                                         |
| 401 Unauthorized     | The access token is invalid or has been revoked.                                                                     |

### 3.3. Article

#### Overview
The Article Page API extracts clean article content, authors, published dates, excerpts, lead images.

To use the Article API, perform a HTTP GET request on the following endpoint:

```
GET https://api.quantati.com/v1/article
```

Example request:

```
GET /v1/article?token=Ab6bWWFHbCcyIgZ66Vpf&url=https%3A%2F%2Fblog.com%2Farticle%2F0691170819 HTTP/1.1
Host: api.quantati.com
Content-Type: application/json
Accept: application/json
Accept-Charset: utf-8
```

With the following fields:

| Parameter       | Type         | Required?  | Description                                     |
| -------------   |--------------|------------|-------------------------------------------------|
| token           | string       | required   | Developer token |
| url             | string       | required   | Web page URL of the article to process (URL encoded) |                                   |

The Article API returns data in JSON format. Example response:

```
HTTP/1.1 201 OK
Content-Type: application/json; charset=utf-8

{
  "title": "Kabbage acquires Radius Intelligence, the marketing tech firm with a database of 20M small businesses – TechCrunch",
  "author": null,
  "published": "2019-09-02T21:00:00.000Z",
  "primaryImage": "https://techcrunch.com/wp-content/uploads/2019/07/GettyImages-506181336.jpg?w=714",
  "html": "https://techcrunch.com/2019/09/03/kabbage-acquires-radius-intelligence/",
  "summary": "Data is the new oil, as the saying goes, and today Kabbage — a fintech startup backed by SoftBank that has built a business around lending up to $250,000 to small and medium enterprises, using AI&hellip;",
  "totalWords": 767
}
```

Where response data contains an array of products the following fields:

| Field         | Type         | Description                                     |
| --------------|--------------|-------------------------------------------------|
| title         | string       | Title of the product.                                 |
| author        | string       | Offer or actual/final price of the product.                 |
| published     | string       | Published date. |
| primaryImage  | string       | Primary image details url, title, width, height  |
| html          | string       | HTML content.  |
| summary       | string       | Article excerpt.  |
| totalWords    | integer      | Total number of words.  |

Possible errors:

| Error code           | Description                                                                                                          |
| ---------------------|----------------------------------------------------------------------------------------------------------------------|
| 400 Bad Request      | Required fields were invalid, not specified.                                                                         |
| 401 Unauthorized     | The access token is invalid or has been revoked.                                                                     |


## 5. SDKs

Comming Soon
