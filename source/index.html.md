---
title: Microwork API Reference

language_tabs:
  - shell: cURL
  - javascript: JavaScript

toc_footers:
  - <a href='http://microwork.io'>Documentation for Microwork API</a>

search: true
---

# Introduction

Welcome to the Microwork API! You will be able to use our API to access Microwork API endpoints, for recognize logos and other objects on the images.

**At the moment, our API is at the prototype stage.**

We plan to have Curl and Javascript interfaces! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Authentication

> Here will be example of authentication

```shell
# ...
```

```javascript
//...
```
    
Here will be information about authentication

# BrandImages

## Tag image

```shell
curl -X POST "https://api.microwork.io/brandimages/tag"
     -H "Authorization: Bearer <your_token>"
     -d '{"url": "pngimg.com/uploads/pepsi/pepsi_PNG8.png"}'

```

```javascript
const microwork = require('microwork');

let api = kittn.authorize('your_token');
let taggingInfo = api.brandimages.tag('http://pngimg.com/uploads/pepsi/pepsi_PNG8.png');
```

> The above command returns JSON structured like this:

```json
{ 
    "_id" : "223HkAndczrjvBXx9", 
    "brandsTagging" : [
        { 
            "_id" : "tB2h6mHKPAqKxMaQ9", 
            "rectangle" : {
                "left" : 1051, 
                "top" : 660, 
                "width" : 353, 
                "height" : 126, 
                "angle" : 0, 
                "brandLogoId" : "iaEvAzztinCD7wdjk" 
            }, 
            "brandName" : "Ford Snapchat", 
        },
        { 
            "_id" : "YKzuC8Sm8EjA2sNpw", 
            "rectangle" : {
                "left" : 1135, 
                "top" : 1128, 
                "width" : 163, 
                "height" : 59, 
                "angle" : 0, 
                "brandLogoId" : "ZAxYa9RPcuLXhZyqT"
            }
        },
        { 
            "_id" : "RXxnxHb2HC7X3A8QX", 
            "rectangle" : {
                "left" : 2437, 
                "top" : 447, 
                "width" : 251, 
                "height" : 80, 
                "angle" : 0, 
                "brandLogoId" : null
            }
        },
        { 
            "_id" : "zjyhHzNcpuQj6XT5L", 
            "rectangle" : {
                "left" : 2162, 
                "top" : 502, 
                "width" : 66, 
                "height" : 184, 
                "angle" : 0, 
                "brandLogoId" : null
            }
        }
    ]
}
```

This endpoint takes image for tagging.

<aside class="warning">The tagging might take some time</aside>

### HTTP Request

`POST https://api.microwork.io/brandimages/tag`

### URL Parameters

Parameter | Description
--------- | -----------
url | The url of image to process


## Annotate image

```shell
curl -X POST "https://api.microwork.io/brandimages/annotate"
     -H "Authorization: Bearer <your_token>"
     -d '{"url": "pngimg.com/uploads/pepsi/pepsi_PNG8.png"}'

```

```javascript
const microwork = require('microwork');

let api = kittn.authorize('your_token');
let annotationInfo = api.brandimages.annotate('http://pngimg.com/uploads/pepsi/pepsi_PNG8.png');
```

> The above command returns JSON structured like this:

```json
{ 
    "_id" : "223HkAndczrjvBXx9", 
    "brandsTagging" : [
        { 
            "_id" : "tB2h6mHKPAqKxMaQ9", 
            "rectangle" : {
            "isGoodQuality" : false, 
            "isSubject" : true,
            "rectangle" : {
//                ... 
            }, 
            "brandName" : "Ford Snapchat",
        }
        { 
            "_id" : "YKzuC8Sm8EjA2sNpw", 
            "isGoodQuality" : false, 
            "isSubject" : false,
            "rectangle" : {
//                ... 
            }
        }
        { 
            "_id" : "RXxnxHb2HC7X3A8QX", 
            "isGoodQuality" : false, 
            "isSubject" : false,
            "rectangle" : {
//                ... 
            }
        }
        { 
            "_id" : "zjyhHzNcpuQj6XT5L", 
            "isGoodQuality" : false, 
            "isSubject" : false,
            "rectangle" : {
//                ... 
            }
        }
    ]
}
```

This endpoint takes image for annotating.

<aside class="warning">The annotating might take some time</aside>

### HTTP Request

`POST https://api.microwork.io/brandimages/tag`

### URL Parameters

Parameter | Description
--------- | -----------
url | The url of image to process

