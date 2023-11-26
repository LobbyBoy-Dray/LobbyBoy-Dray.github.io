---
title: >
  [Python] POST Request Body: Content Type
author: Jingwei Gao
pubDatetime: 2021-04-25T08:21:53+08
postSlug: python-post-request-body
featured: false
draft: false
tags:
  - programming
description: >
  How to specify a format (json/url-encoded string) for POST request body in Python?
---

> How to specify a format (json/url-encoded string) for POST request body in Python?

To send data using POST method in an HTTP request, you need to include the data in the request body. In most cases, data are formatted as `application/json` or `application/x-www-form-urlencoded`.

**application/json**

The data will be formatted as JSON:

```python
# POST request body: json format
{"accountName": "Crane", "password": 88888888}
```

In python, if you choose this format for your POST request body, you need to call `json.dumps` to convert the data dictionary to a JSON string first. Then you pass the JSON as the `data` value in `post` method:

```python
import requests
import json

data = {
	"accountName":"Crane",
	"password":88888888
}

r = requests.post(url, headers=headers, data=json.dumps(data))
```

Or you can pass the data dictionary as the `json` value in `post` method directly:

```python
r = requests.post(url, headers=headers, json=data)
```

**application/x-www-form-urlencoded**

This format consists of key-value pairs separated by `&` symbols, where each key-value pair is separated by an `=` symbol. It is similar to the parameters in GET request.

```python
# POST request body: url-encoded format
accountName=Crane&password=88888888
```

In python, you just pass the data dictionary as the `data` value in `post` method. It will be formatted into url-encoded data automatically:

```r
import requests

data = {
	"accountName":"Crane",
	"password":88888888
}

r = requests.post(url, headers=headers, data=data)
```

**[Warning]** If your data dictionary is nested, you should not pass it as `data` value directly. Otherwise, you would find your nested dictionary is not packaged into the POST request body as you expect. There are two approaches to deal with the nested dictionary:

```r
import requests
import json

data = {
    "id":12345,
    "accountInfo":{
        "name":"Crane",
        "password":88888888
    }
}

# Approach 1: Format it as JSON
r = requests.post(url, headers=headers, data=json.dumps(data))
r = requests.post(url, headers=headers, json=data)

# Approach 2: Format it as url-encoded data
data = {key:json.dumps(data[key]) for key in data}
r = requests.post(url, headers=headers, data=data)
```

**[Summary]** If a dictionary is passed as `data`, requests library will by default do url-encoded string conversion for you. If a string is passed as `data`, requests library will do nothing and send the string as the POST request body.

**[Reference]** [](https://stackoverflow.com/questions/68288779/python-post-request-with-nested-dictionary-values-args)[https://stackoverflow.com/questions/68288779/python-post-request-with-nested-dictionary-values-args](https://stackoverflow.com/questions/68288779/python-post-request-with-nested-dictionary-values-args)
