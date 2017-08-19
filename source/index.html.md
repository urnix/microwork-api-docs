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

You can view code examples in the dark area to the right.


# Authentication
                
> To authorize, use this code:

```shell
# With curl, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -u MICROWORK_API_KEY:
```

> You must replace MICROWORK_API_KEY with your personal API key.

Microwork uses API keys to allow access to the API.

Microwork expects for the API key to be included in all API requests to the server. Provide your API key like this:

`-H "authorization: Bearer MICROWORK_API_KEY"`

<aside class="notice">
You must replace <code>MICROWORK_API_KEY</code> with your personal API key.
</aside>


# Tag image

```shell
curl -X POST "https://api.microwork.io/v1/task/images/tagging" \
     -u MICROWORK_API_KEY: \
     -d callbackUrl="http://www.example.com/callback" \
     -d objectsToTag="brands" \
     -d imageUrl="http://img11.hostingpics.net/pics/487001pepsiminimaillot.png"
```

> The above command returns JSON structured like this:

```json
{
    "taskId": "BuDEe3LABheGAbuL9",
    "createdAt": "2017-06-20T14:26:40.409Z",
    "callbackUrl": "http://www.example.com/callback",
    "type": "tagging",
    "objectsToTag": "brands",
    "state": "pending",
    "imageUrl": "http://img11.hostingpics.net/pics/487001pepsiminimaillot.png"
}
```

This endpoint creates a tagging task. In this task, one of our workers view the given image and draw bounding boxes around the brand logos, returning the coordinates of these boxes.

The required parameters for this task are `callbackUrl`, `imageUrl`, and `objectsToTag`. The `callbackUrl` is the URL which will be POSTed on task completion, and is described in more detail in the [Callbacks section ](#callbacks). The `imageUrl` is a URL to an image you’d like to be tagged.

`objectsToTag` is an string describing the different types of objects you’d like tagged (We currently only support `brands` value).

If successful, Microwork will immediately return the generated task object, of which you should at least store the `taskId`.

<aside class="warning">The tagging might take some time</aside>

### HTTP Request

`POST https://api.microwork.io/v1/task/images/tagging`

### URL Parameters

Parameter         |	Type        | Description   
----------------- | ----------- | -------------- 
`callbackUrl` | string | The full url (including the scheme `http://` or `https://`) of the callback when the task is completed. See the [Callbacks section ](#callbacks) for more details about callbacks.
`objectsToTag` (default `brands`) | string | An string describing which objects you’d like bounding boxes to be drawn around.
`imageUrl` | string	| A URL to the image you’d like to be tagged with bounding boxes.

## Callback Format

> Example callback body sent on completion

```json
{
    "taskId":"BuDEe3LABheGAbuL9",
    "createdAt":"2017-06-20T14:26:40.409Z",
    "callbackUrl":"http://www.example.com/callback",
    "type":"tagging",
    "objectsToTag":"brands",
    "state":"finished",
    "imageUrl":"http://img11.hostingpics.net/pics/487001pepsiminimaillot.png",
    "completedAt":"2017-06-20T14:34:41.815Z",
    "response":{
        "tags":[
            {
                "id":"wi2GEMfXxwmoZdYYb",
                "left":71,
                "top":83,
                "width":116,
                "height":50,
                "brand":"Pepsi",
                "brand_id":"dWYBjnknLhfZbEC6P",
                "template_url":"https://s3-us-west-2.amazonaws.com/microwork-worker-dev/brands/Pepsi/templates/2ef9f4981d1cdb966e9a157017b37cc6378fd269.png",
                "template_id":"CXRTbj5CebPeqmd6R"
            },
            {
                "id":"yM8dkq79AYBSQomGG",
                "left":31,
                "top":22,
                "width":64,
                "height":50,
                "brand":"Pepsi",
                "brand_id":"dWYBjnknLhfZbEC6P",
                "template_url":"https://s3-us-west-2.amazonaws.com/microwork-worker-dev/brands/Pepsi/templates/2ef9f4981d1cdb966e9a157017b37cc6378fd269.png",
                "template_id":"CXRTbj5CebPeqmd6R"
            }
        ]
    }
}
```

The `response` field, which is part of the callback POST request and permanently stored as part of the task object, will contain an `tags` field.

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


# Callbacks

> The callbackUrl will be POSTed with application/json data of the following object form:

```json
{
    "taskId":"BuDEe3LABheGAbuL9",
    "createdAt":"2017-06-20T14:26:40.409Z",
    "callbackUrl":"http://www.example.com/callback",
    "type":"tagging",
    "objectsToTag":"brands",
    "state":"finished",
    "imageUrl":"http://img11.hostingpics.net/pics/487001pepsiminimaillot.png",
    "completedAt":"2017-06-20T14:34:41.815Z",
    "response":{
        "tags":[
            {
                "id":"wi2GEMfXxwmoZdYYb",
                "left":71,
                "top":83,
                "width":116,
                "height":50,
                "brand":"Pepsi",
                "brand_id":"dWYBjnknLhfZbEC6P",
                "template_url":"https://s3-us-west-2.amazonaws.com/microwork-worker-dev/brands/Pepsi/templates/2ef9f4981d1cdb966e9a157017b37cc6378fd269.png",
                "template_id":"CXRTbj5CebPeqmd6R"
            },
            {
                "id":"yM8dkq79AYBSQomGG",
                "left":31,
                "top":22,
                "width":64,
                "height":50,
                "brand":"Pepsi",
                "brand_id":"dWYBjnknLhfZbEC6P",
                "template_url":"https://s3-us-west-2.amazonaws.com/microwork-worker-dev/brands/Pepsi/templates/2ef9f4981d1cdb966e9a157017b37cc6378fd269.png",
                "template_id":"CXRTbj5CebPeqmd6R"
            }
        ]
    }
}

```

On your tasks, you will be required to supply a `callbackUrl`, a fully qualified URL that we will POST with the results of the task when completed. The data will be served as a JSON body (`application/json`).

### POST data

* `taskId`: The `taskId` is the unique identifier for the task.
* `createdAt`: Timestamp of task creation.
* `callbackUrl`: the URL which will be POSTed on task completion.
* `type`: The type of task (We currently only support `tagging` value)
* `objectsToTag`: string describing the different types of objects you’d like tagged (We currently only support `brands` value).
* `state`: State of the task.
* `imageUrl`: URL to an image for tagging
* `completedAt`: Timestamp of task completion.
* `response`: The response object of the completed request. For `tagging`, it will contain a `tags` attribute.

# Task Endpoints

## Retrieve a Task

```shell
curl "https://api.microwork.io/v1/task/{taskId}" \
  -u MICROWORK_API_KEY:
```

> The above command returns JSON structured like this:

```json
{
    "taskId": "BuDEe3LABheGAbuL9",
    "createdAt": "2017-06-20T14:26:40.409Z",
    "callbackUrl": "http://www.example.com/callback",
    "type": "tagging",
    "objectsToTag": "brands",
    "state": "finished",
    "imageUrl": "http://img11.hostingpics.net/pics/487001pepsiminimaillot.png",
    "completedAt": "2017-06-20T14:34:41.815Z",
    "response": {
        "tags": [
            {
                "id": "wi2GEMfXxwmoZdYYb",
                "left": 71,
                "top": 83,
                "width": 116,
                "height": 50,
                "brand": "Pepsi",
                "brand_id": "dWYBjnknLhfZbEC6P",
                "template_url": "https://s3-us-west-2.amazonaws.com/microwork-worker-dev/brands/Pepsi/templates/2ef9f4981d1cdb966e9a157017b37cc6378fd269.png",
                "template_id": "CXRTbj5CebPeqmd6R"
            },
            {
                "id": "yM8dkq79AYBSQomGG",
                "left": 31,
                "top": 22,
                "width": 64,
                "height": 50,
                "brand": "Pepsi",
                "brand_id": "dWYBjnknLhfZbEC6P",
                "template_url": "https://s3-us-west-2.amazonaws.com/microwork-worker-dev/brands/Pepsi/templates/2ef9f4981d1cdb966e9a157017b37cc6378fd269.png",
                "template_id": "CXRTbj5CebPeqmd6R"
            }
        ]
    }
}
```

This endpoint retrieves a specific task.

### HTTP Request

`GET https://api.microwork.io/v1/task/{taskId}`

### URL Parameters

Parameter | Description
----------------- | ----------- 
`taskId`	| The taskId of the task to retrieve

### Returns

Returns a task if a valid identifier was provided, and returns a 404 error otherwise.


## Cancel a task

```shell
curl -X GET "https://api.microwork.io/v1/task/{taskId}/cancel}" \
  -u MICROWORK_API_KEY:
```

> The above command returns JSON structured like this:

```json
{
    "taskId": "X3mfYmGzMsn4iu5oP",
    "createdAt": "2017-06-20T14:26:33.868Z",
    "callbackUrl": "http://www.example.com/callback",
    "type": "tagging",
    "objectsToTag": "brands",
    "state": "canceled",
    "imageUrl": "http://img11.hostingpics.net/pics/487001pepsiminimaillot.png"
}
```

This endpoint cancels a task so that it will not be completed.

You may only cancel pending tasks, and the endpoint will return a 500 error code if you attempt to cancel a completed task.

### HTTP Request

`POST https://api.microwork.io/v1/task/{taskId}/cancel`

### URL Parameters

Parameter | Description
----------------- | ----------- 
`taskId`	| 	The taskId of the task to cancel

### Returns

Returns the canceled task if a valid identifier for a pending task was provided, and returns a 404 error or 500 error otherwise.
    

## List All Tasks

```shell
curl "https://api.microwork.io/v1/tasks" \
   -u MICROWORK_API_KEY:
```

> The above command returns JSON structured like this:

```json
[
    {
        "taskId": "xaWLEnDq3m6azqCKC",
        "createdAt": "2017-06-20T14:26:23.928Z",
        "callbackUrl": "http://www.example.com/callback",
        "type": "tagging",
        "objectsToTag": "brands",
        "state": "pending",
        "imageUrl": "http://img11.hostingpics.net/pics/487001pepsiminimaillot.png"
    },
    {
        "taskId": "X3mfYmGzMsn4iu5oP",
        "createdAt": "2017-06-20T14:26:33.868Z",
        "callbackUrl": "http://www.example.com/callback",
        "type": "tagging",
        "objectsToTag": "brands",
        "state": "canceled",
        "imageUrl": "http://img11.hostingpics.net/pics/487001pepsiminimaillot.png"
    },
    {
        "taskId": "BuDEe3LABheGAbuL9",
        "createdAt": "2017-06-20T14:26:40.409Z",
        "callbackUrl": "http://www.example.com/callback",
        "type": "tagging",
        "objectsToTag": "brands",
        "state": "finished",
        "imageUrl": "http://img11.hostingpics.net/pics/487001pepsiminimaillot.png",
        "completedAt": "2017-06-20T14:34:41.815Z",
        "response": {
            "tags": [
                {
                    "id": "wi2GEMfXxwmoZdYYb",
                    "left": 71,
                    "top": 83,
                    "width": 116,
                    "height": 50,
                    "brand": "Pepsi",
                    "brand_id": "dWYBjnknLhfZbEC6P",
                    "template_url": "https://s3-us-west-2.amazonaws.com/microwork-worker-dev/brands/Pepsi/templates/2ef9f4981d1cdb966e9a157017b37cc6378fd269.png",
                    "template_id": "CXRTbj5CebPeqmd6R"
                },
                {
                    "id": "yM8dkq79AYBSQomGG",
                    "left": 31,
                    "top": 22,
                    "width": 64,
                    "height": 50,
                    "brand": "Pepsi",
                    "brand_id": "dWYBjnknLhfZbEC6P",
                    "template_url": "https://s3-us-west-2.amazonaws.com/microwork-worker-dev/brands/Pepsi/templates/2ef9f4981d1cdb966e9a157017b37cc6378fd269.png",
                    "template_id": "CXRTbj5CebPeqmd6R"
                }
            ]
        }
    }
]
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


