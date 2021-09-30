# Hotpot.ai API examples

This repository contains examples of how to use Hotpot's [Background Remover](https://hotpot.ai/remove-background) API.

## Curl

```bash
curl -H 'Authorization: API_KEY_HERE' \
     -F 'image=@/full/path/to/image.jpg' \
     -o '/full/path/to/image-nobg.jpg' \
     https://api-bin.hotpot.ai/remove-background
```

## Node

Install the [form-data](https://www.npmjs.com/package/form-data) library first:

```bash
yarn add form-data
```


```javascript
'use strict';

const fs = require('fs');
const https = require('https');
const FormData = require('form-data');

const form = new FormData();
// change to a full file path of the image you want to transform
form.append('image', fs.createReadStream('/full/path/to/image.jpg'));

const customHeaders = {
  'Authorization': 'API_KEY_HERE'
}

// setting a correct MIME type and (multipart/form-data) a boundary for the payload
const headers = {...form.getHeaders(), ...customHeaders}

const options = {
  method: 'POST',
  hostname: 'api-bin.hotpot.ai',
  port: 443,
  path: '/remove-background',
  headers: headers,
  encoding: null,
};

const request = https.request(options, response => {
  const body = [];

  response.on('data', chunk => {
    body.push(Buffer.from(chunk));
  })

  response.on('end', () => {
    // change to a full file path where you want to save the resulting image
    fs.writeFileSync('/full/path/to/image-nobg.jpg', Buffer.concat(body), 'binary');
    request.end();
  })
});

request.on('error', error => {
  console.error(error);
});

form.pipe(request);
```

## Python

Install the [requests](https://requests.readthedocs.io/en/master/) library first:

```bash
pip3 install requests
```

```python
import requests

headers = {
  'Authorization': 'API_KEY_HERE',
}

# change to a full file path of the image you want to transform
body = {
  'image': open('/full/path/to/image.jpg', 'rb'),
}

response = requests.post('https://api-bin.hotpot.ai/remove-background', headers=headers, files=body)

# change to a full file path where you want to save the resulting image
with open('/full/path/to/image-nobg.jpg', 'wb') as file:
  file.write(response.content)

```

## PHP

```php
<?php

$ch = curl_init();

// change to a full file path of the image you want to transform
$body = [
  'image' => new CurlFile('/full/path/to/image.jpg')
];

curl_setopt($ch, CURLOPT_URL, 'https://api-bin.hotpot.ai/remove-background');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, $body);

$headers = array('Authorization: API_KEY_HERE');
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);

$response = curl_exec($ch);

curl_close($ch);

// change to a full file path where you want to save the resulting image
file_put_contents('/full/path/to/image-nobg.jpg', $response);

```

## C# #1

Install the [Flurl.Http](https://flurl.dev/) library first:

```bash
dotnet add package Flurl.Http
```

```csharp
using var memoryStream = new MemoryStream(data);

var request = await "https://api-bin.hotpot.ai/remove-background"
    .WithHeader("Authorization", "API_KEY_HERE")
     // change to a full file path of the image you want to transform
    .PostMultipartAsync(builder => builder.AddFile("image", "/full/path/to/image.jpg"));

var response = await request.GetBytesAsync();

 // change to a full file path where you want to save the resulting image
await File.WriteAllBytesAsync("/full/path/to/image-nobg.jpg", response);
```

## C# #2

Note: the `Add` function requires three parameters. Otherwise the binary data will be incorrectly sent as a string.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Threading.Tasks;

class Program {
  public static async Task Main (string[] args) {
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Authorization", "API KEY HERE");

    var form = new MultipartFormDataContent();
    var image = new ByteArrayContent(File.ReadAllBytes("bg.jpg"));
    form.Add(image, "image", "bg.jpg");

    var response = await client.PostAsync("https://api-bin.hotpot.ai/remove-background", form);
    var result = await response.Content.ReadAsByteArrayAsync();

    System.IO.File.WriteAllBytes("nobg.jpg", result);
  }
}
```
