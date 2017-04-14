---
title: Microwork API Reference

language_tabs:
  - shell: cURL

toc_footers:
  - <a href='http://microwork.io'>Documentation for Microwork API</a>

includes:
  - errors

search: true
---


# Introduction

Welcome to the Microwork API! You will be able to use our API to access Microwork API endpoints, for recognize logos and other objects on the images.

**At the moment, our API is at the prototype stage.**

You can view code examples in the dark area to the right.


# Authentication
                
> To authorize, use this code:

```shell
# With curl, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -u "MICROWORK_API_KEY:"
```

> You must replace MICROWORK_API_KEY with your personal API key.

Microwork uses API keys to allow access to the API

Microwork expects for the API key to be included in all API requests to the server via [HTTP Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication). Provide your API key as the basic auth username value. You do not need to provide a password. You can do this using the -u flag:

`-u "MICROWORK_API_KEY:"`

<aside class="notice">
You must replace <code>MICROWORK_API_KEY</code> with your personal API key.
</aside>


# Tag image

```shell
curl -X POST "https://api.microwork.io/v1/task/images/tagging" \
     -u "MICROWORK_API_KEY:" \
     -d callback_url="http://www.example.com/callback" \
     -d objects_to_tag="brands" # Describes what should be tagged. We currently only support brands for the tagging endpoint.
     -d image_url="https://ibb.co/fmMFRQ"
```

> The above command returns JSON structured like this:

```json
{
    "task_id": "oPkwaTCPLzNd873rx",
    "created_at": "2017-04-14T12:00:00.000Z",
    "callback_url": "http://www.example.com/callback",
    "type": "tagging",
    "objects_to_tag": "brands",
    "status": "pending",
    "image_url": "https://ibb.co/fmMFRQ"
}
```

This endpoint creates a tagging task. In this task, one of our workers view the given image and draw bounding boxes around the brand logos, returning the coordinates of these boxes.

The required parameters for this task are `callback_url`, `image_url`, and `objects_to_tag`. The `callback_url` is the URL which will be POSTed on task completion, and is described in more detail in the Callbacks section. The `image_url` is a URL to an image you’d like to be tagged.

`objects_to_tag` is an string describing the different types of objects you’d like tagged (We currently only support `brands` value).

If successful, Microwork will immediately return the generated task object, of which you should at least store the `task_id`.

<aside class="warning">The tagging might take some time</aside>

### HTTP Request

`POST https://api.microwork.io/v1/task/images/tagging`

### URL Parameters

Parameter         |	Type        | Description   
----------------- | ----------- | -------------- 
`callback_url` | string | The full url (including the scheme `http://` or `https://`) of the callback when the task is completed. See the Callback section for more details about callbacks.
`objects_to_tag` (default `brands`) | string | An string describing which objects you’d like bounding boxes to be drawn around.
`image_url` | string	| A URL to the image you’d like to be tagged with bounding boxes.

## Callback Format

> Example callback body sent on completion

```json
{
    "response": {
        "tags": [
         {
          "id": "nk5DHouybZD2MvGqX",
           "left" : 1408,
           "top" : 0,
           "width" : 601,
           "height" : 121,
           "brand": "Pepsi",
           "brand_id": "ATXsnhDdq5NyQw4qQ",
           "template_url": "http://images.data.microwork.io/templates/384551c8730bdea36aa971c1eb816898d10c03a2.png",
           "template_id": "TeSRfZBYCdWQbQwvH"
         },
         {
            "id": "JwfffWsnqX6KBFqom",
           "left" : 975,
           "top" : 1396,
           "width" : 1262,
           "height" : 1465,
           "brand": "Pepsi",
           "brand_id": "ATXsnhDdq5NyQw4qQ",
           "template_url": null,
           "template_id": null
         },
         { ... },
         { ... }
        ]
        },
        "task_id": "oPkwaTCPLzNd873rx",
        "task": {
            ...
        }
    }
}
```

The response field, which is part of the callback POST request and permanently stored as part of the task object, will contain only an `tags` field.

The `tags` field will contain an array of tags. Each `tag` will have the following values:

* `id`: Id of tag
* `left`: The distance, in pixels, between the left border of the bounding box and the left border of the image.
* `top`: The distance, in pixels, between the top border of the bounding box and the top border of the image.
* `width`: The width, in pixels, of the bounding box.
* `height`: The height, in pixels, of the bounding box.
* `brand` (if detected): Name of detected brand.
* `brand_id` (if detected): Id of detected brand.
* `template_url` (if detected): Url for brand logo image.
* `template_id` (if detected): Id of brand logo.

<aside class="notice">See the <a href="#callbacks">Callback section</a> for more details about callbacks.</aside>


# Annotate tags on image

```shell
curl -X POST "https://api.microwork.io/v1/task/images/tags/annotating" \
     -u "MICROWORK_API_KEY:" \
     -d callback_url="http://www.example.com/callback" \
     -d image_url="https://ibb.co/fmMFRQ"
     -d instruction="Detect if tag is **good**, **bad**, or **great**." \
     -d annotation_option="good" \
     -d annotation_option="bad" \
     -d annotation_option="great" \
     -d tags[0][id]="nk5DHouybZD2MvGqX" \
     -d tags[0][left]=1408 \
     -d tags[0][top]=0 \
     -d tags[0][width]=601 \
     -d tags[0][height]=121 \
     -d tags[1][id]="JwfffWsnqX6KBFqom" \
     -d tags[1][left]=975 \
     -d tags[1][top]=1396 \
     -d tags[1][width]=1262 \
     -d tags[1][height]=1465
```

> The above command returns JSON structured like this:

```json
{
     "task_id": "K63nQmiYa5C57iXm5",
     "created_at": "2017-04-14T12:00:00.000Z",
     "callback_url": "http://www.example.com/callback",
     "type": "annotating",
     "status": "pending",
     "image_url": "https://ibb.co/fmMFRQ",
     "instruction": "Detect if tag is **good**, **bad**, or **great**.",
     "options": ["good", "bad", "great"],
     "tags": [
           {
                 "id": "nk5DHouybZD2MvGqX",
                 "left" : 1408,
                 "top" : 0,
                 "width" : 601,
                 "height" : 121
           },
           {
                 "id": "JwfffWsnqX6KBFqom",
                 "left" : 975,
                 "top" : 1396,
                 "width" : 1262,
                 "height" : 1465
           }
     ]
}
```

This endpoint creates a annotating task. In this task, one of our workers view the given image and answer to question (given in `instruction` parameter) for each given `tags` (using one of `annotation_option`).

The required parameters for this task are: `callback_url`, `image_url`, `instruction`, `annotation_option` and `tags`. 

The `callback_url` is the URL which will be POSTed on task completion, and is described in more detail in the Callbacks section. The `image_url` is a URL to an image you’d like to be tagged. 

`instruction` contains question and `annotation_option` available variants of answers.

`tags` is array each element of which describe one rectangle on image.

If successful, Microwork will immediately return the generated task object, of which you should at least store the `task_id`.

<aside class="warning">The annotating might take some time</aside>

### HTTP Request

`POST https://api.microwork.io/v1/task/images/tags/annotating`

### URL Parameters

Parameter |	Type | Description
--------- | ----------- | ----------- 
`callback_url` | string | The full url (including the scheme `http://` or `https://`) of the callback when the task is completed. See the Callback section for more details about callbacks.
`image_url` | string	| A URL to the image you’d like to be tagged with bounding boxes.
`instruction` | string	| Question will be applied for each tag
`annotation_option` | string | Variants of answers
`tags` | [Object] | Array of tags

## Callback Format

> Example callback body sent on completion

```json
{
     "response": {
           "tags": [
                 {
                       "id": "nk5DHouybZD2MvGqX",
                       "left" : 1408,
                       "top" : 0,
                       "width" : 601,
                       "height" : 121,
                       "label": "good"
                 },
                 {
                       "id": "JwfffWsnqX6KBFqom",
                       "left" : 975,
                       "top" : 1396,
                       "width" : 1262,
                       "height" : 1465,
                       "label": "bad"
                 },
                 { ... },
                 { ... }
           ]
     },
     "task_id": "oPkwaTCPLzNd873rx",
     "task": {
           ...
     }
}
```

The response field, which is part of the callback POST request and permanently stored as part of the task object, will contain only an `tags` field.

The `tags` field will contain an array of tags. Each `tag` will have the following values:

* `id`: Id of tag
* `left`: The distance, in pixels, between the left border of the bounding box and the left border of the image.
* `top`: The distance, in pixels, between the top border of the bounding box and the top border of the image.
* `width`: The width, in pixels, of the bounding box.
* `height`: The height, in pixels, of the bounding box.
* `label`: Result of annotating

<aside class="notice">See the <a href="#callbacks">Callback section</a> for more details about callbacks.</aside>

# Callbacks

> The callback_url will be POSTed with application/json data of the following object form:

```json
{
  "task": {
    "task_id": "576ba74eec471ff9b01557cc",
    "completed_at": "2016-06-23T09:10:02.798Z",
    "created_at": "2016-06-23T09:09:34.752Z",
    "callback_url": "http://www.example.com/callback",
    "type": "tagging",
    "status": "completed",
    "params": {
    "objects_to_tag": "brands",
    "image_url": "https://ibb.co/fmMFRQ"
    "response": {
      ...
    }
  },
  "response": {
    "tags": [
        {
        "id": "nk5DHouybZD2MvGqX",
         "left" : 1408,
         "top" : 0,
         "width" : 601,
         "height" : 121,
         "brand": "Pepsi",
         "brand_id": "ATXsnhDdq5NyQw4qQ",
         "template_url": "http://images.data.microwork.io/templates/384551c8730bdea36aa971c1eb816898d10c03a2.png",
         "template_id": "TeSRfZBYCdWQbQwvH"
        },
        {
          "id": "JwfffWsnqX6KBFqom",
         "left" : 975,
         "top" : 1396,
         "width" : 1262,
         "height" : 1465,
         "brand": "Pepsi",
         "brand_id": "ATXsnhDdq5NyQw4qQ",
         "template_url": null,
         "template_id": null
        },
        { ... },
        { ... }
    ]
  }
  "task_id": "576ba74eec471ff9b01557cc"
}
```

On your tasks, you will be required to supply a `callback_url`, a fully qualified URL that we will POST with the results of the task when completed. The data will be served as a JSON body (`application/json`).

You should respond to the POST request with a 2xx status code. If we do not receive a 2xx status code, we will continue to retry up to 20 times over the course of the next 24 hours.

If we receive a 2xx status code, the task will be populated with a `true` value for the `callback_succeeded` parameter. Otherwise, if we do not receive a 2xx status code on any retry, the task will be populated with a `false` value for the `callback_succeeded` parameter.

# Task Endpoints

## Retrieve a Task

```shell
curl "https://api.microwork.io/v1/task/{task_id}" \
  -u "MICROWORK_API_KEY:"
```

> The above command returns JSON structured like this:

```json
{
    "task_id": "576ba74eec471ff9b01557cc",
    "completed_at": "2016-06-23T09:10:02.798Z",
    "created_at": "2016-06-23T09:09:34.752Z",
    "callback_url": "http://www.example.com/callback",
    "type": "tagging",
    "status": "completed",
    "params": {
      "objects_to_tag": "brands",
      "image_url": "https://ibb.co/fmMFRQ"
    },
     "response": {
        "tags": [
         {
          "id": "nk5DHouybZD2MvGqX",
           "left" : 1408,
           "top" : 0,
           "width" : 601,
           "height" : 121,
           "brand": "Pepsi",
           "brand_id": "ATXsnhDdq5NyQw4qQ",
           "template_url": "http://images.data.microwork.io/templates/384551c8730bdea36aa971c1eb816898d10c03a2.png",
           "template_id": "TeSRfZBYCdWQbQwvH"
         },
         {
            "id": "JwfffWsnqX6KBFqom",
           "left" : 975,
           "top" : 1396,
           "width" : 1262,
           "height" : 1465,
           "brand": "Pepsi",
           "brand_id": "ATXsnhDdq5NyQw4qQ",
           "template_url": null,
           "template_id": null
         },
         { ... },
         { ... }
        ]
        },
        "task_id": "oPkwaTCPLzNd873rx",
        "task": {
            ...
        }
    }
}
```

This endpoint retrieves a specific task.

### HTTP Request

`GET https://api.microwork.io/v1/task/{TASK_ID}`

### URL Parameters

Parameter | Description
----------------- | ----------- 
`task_id`	| The task_id of the task to retrieve

### Returns

Returns a task if a valid identifier was provided, and returns a 404 error otherwise.


## Cancel a task

```shell
curl -X POST "https://api.microwork.io/v1/task/{task_id}/cancel}" \
  -u "MICROWORK_API_KEY:"
```

> The above command returns JSON structured like this:

```json
{
    "task_id": "576ba74eec471ff9b01557cc",
    "created_at": "2016-06-23T09:09:34.752Z",
    "callback_url": "http://www.example.com/callback",
    "type": "tagging",
    "status": "canceled",
    "params": {
      "objects_to_tag": "brands",
      "image_url": "https://ibb.co/fmMFRQ"
    }
}
```

This endpoint cancels a task so that it will not be completed.

You may only cancel pending tasks, and the endpoint will return a 500 error code if you attempt to cancel a completed task.

### HTTP Request

`POST https://api.microwork.io/v1/task/{TASK_ID}/cancel`

### URL Parameters

Parameter | Description
----------------- | ----------- 
`task_id`	| 	The task_id of the task to cancel

### Returns

Returns the canceled task if a valid identifier for a pending task was provided, and returns a 404 error or 500 error otherwise.
    

## List All Tasks

```shell
curl "https://api.microwork.io/v1/tasks" \
  -u "MICROWORK_API_KEY:"
```

> The above command returns JSON structured like this:

```json
{
  "docs": [
    {
      "task_id": "576b998b4628d1bfaed7d3a4",
      "created_at": "2016-06-23T08:10:51.032Z",
      "callback_url": "http://www.example.com/callback",
      "type": "tagging",
      "status": "completed",
      "params": {
        "objects_to_tag": "brands",
        "image_url": "https://ibb.co/fmMFRQ"
      },
      "completed_at": "2016-06-23T19:36:23.084Z",
      "response": {
        ...
      },
    },
    {
      "task_id": "576ba301eed30241b0e9bbf7",
      "created_at": "2016-06-23T08:51:13.903Z",
      "callback_url": "http://www.example.com/callback",
      "type": "annotating",
      "status": "completed",
      "params": {
        "image_url":"https://ibb.co/fmMFRQ",
        "instruction":"Detect if tag is **good**, **bad**, or **great**.",
        "annotation_option":"good",
        "annotation_option":"bad",
        "annotation_option":"great",
        "tags": [
          {
            id: "nk5DHouybZD2MvGqX",
            left: 1408,
            top: 0,
            width: 601,
            height: 121
          }, 
          {
            id: "JwfffWsnqX6KBFqom",
            left: 975,
            top: 1396,
            width: 1262,
            height: 1465
         }
        ]  
      },
      "completed_at": "2016-06-23T09:09:10.108Z",
      "response": {
       ...
      }
    }
  ],
  "total": 2,
  "limit": 100,
  "offset": 0,
  "has_more": false
}
```

This is a paged endpoint retrieves a list of your tasks. The tasks will be returned in descending order based on `created_at` time. The pagination is based on the `limit` and `offset` parameters, which determine the page size and how many results to skip.

### HTTP Request

`GET https://api.microwork.io/v1/tasks`

### URL Parameters

Parameter |	Type | Description | Required
----------------- | ----------- | ----------- | -------
`start_time` | ISO 8601 Date | The minimum value of `created_at` for tasks to be returned | optional
`end_time` | ISO 8601 Date | The maximum value of `created_at` for tasks to be returned | optional
`status` | string | The status of the task - can be: `completed`, `pending`, or `canceled` | optional
`type` | string | The type of the task - can be: `transcription`, `categorization`, `phonecall`, `comparison`, `annotation`, `datacollection`, or any other task type. | optional
`limit` | integer | A number between 1 and 100, the maximum number of results to display per page | optional, default 100
`offset` | integer | The number of results to skip, for showing the next page | optional, default 0

### Returns

Returns a list of your tasks.


