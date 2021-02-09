# Hotpot.ai API examples

This repository contains examples of how to use Hotpot's [Background Remover](https://hotpot.ai/remove-background) API.

## Overview

Please **do not include** Content-Type, Content-Encoding, or charset with your base64 strings:

Bad:

`data:image/png;base64,iVBORw0KGgoA...`

Good:

`iVBORw0KGgoA...`

## Curl

```bash
curl -H 'Content-Type: application/json' -H 'X-API-KEY: API_KEY_HERE' --data '{"imageBase64": "BASE_64_STRING_HERE"}' -X POST "https://api.hotpot.ai/remove-background"
```

## Node

```javascript
'use strict';

var fs = require('fs');
var https = require('https');

const data = JSON.stringify({
  // an absolute path to an image file whose background you want to remove
  imageBase64: fs.readFileSync('/absolute/file/path.jpg', 'base64'),
});

const options = {
  method: 'POST',
  hostname: 'api.hotpot.ai',
  port: 443,
  path: '/remove-background',
  headers: {
    'Content-Type': 'application/json',
    'X-API-KEY': config.external_api_keys.hotpot,
  },
};

const request = https.request(options, response => {
  response.setEncoding('utf-8');

  const body = [];

  response.on('data', chunk => {
    body.push(chunk);
  })

  response.on('end', () => {
    const jsonResponse = JSON.parse(body.join(''));
    // an absolute path where you want to save the resulting image
    fs.writeFileSync('/absolute/file/path-nobackground.jpg', Buffer.from(jsonResponse['imageBase64'], 'base64'));
  })
});

request.on('error', error => {
  console.error(error);
});

request.write(data);
request.end();
```

## Python

Make sure you have [requests](https://requests.readthedocs.io/en/master/) package installed:

```bash
pip3 install requests
```

```python
import base64
import requests

# change `file.jpg` to a file path that leads to the base image
image = open('file.jpg', 'rb').read()
base64_encoded_image = base64.b64encode(image).decode('utf-8')

response = requests.post(
    'https://api.hotpot.ai/remove-background',
    json={'imageBase64': base64_encoded_image},
    headers={'X-API-KEY': 'API KEY HERE'}
)

result = response.json().get('imageBase64')

# change `result.jpg` to a file path where you want to save the resulting image
with open('result.jpg', 'wb') as file:
    file.write(base64.b64decode(result))
```

## PHP

```php
<?php

// an absolute path to an image file whose background you want to remove
$path = '/full/file/path/to/image.jpg';
$base64 = base64_encode(file_get_contents($path));

$ch = curl_init();

curl_setopt($ch, CURLOPT_URL, 'https://api.hotpot.ai/remove-background');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, "{\"imageBase64\": \"{$base64}\"}");

$headers = array();
$headers[] = 'Content-Type: application/json';
// Replace YOUR_API_KEY_HERE with your actual API key
$headers[] = 'X-API-KEY: YOUR_API_KEY_HERE';
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);

$result = curl_exec($ch);
if (curl_errno($ch)) {
  echo 'Error:' . curl_error($ch);
}
curl_close($ch);

$json_response = json_decode($result, true);
$image = base64_decode($json_response['imageBase64']);

// an absolute path where you want to save the resulting image
file_put_contents('/full/file/path/to/image-nobg.jpg', $image);
```
