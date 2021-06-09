# Hotpot.ai API examples

This repository contains examples of how to use Hotpot's [Background Remover](https://hotpot.ai/remove-background) API.

## Curl

```bash
curl -H 'Authorization: API_KEY_HERE' \
     -F 'image=@/full/path/to/image.jpg' \
     -o '/full/path/to/image-nobg.jpg' \
     -X POST 'https://api-bin.hotpot.ai/remove-background'
```

## Node

```javascript
'use strict';

const fs = require('fs');
const https = require('https');

const data = JSON.stringify({
  // Absolute path to an image file whose background you want to remove
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
    // Absolute path where you want to save the resulting image
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

## C# (.NET)

```c#
private async Task<PhotoResponseModel> RemoveBackgroundWithHotpot(string base64Image) {
  var httpClient = new HttpClient();
  using var request = new HttpRequestMessage(new HttpMethod("POST"), "https://api.hotpot.ai/remove-background");
  request.Headers.TryAddWithoutValidation("X-API-KEY", "YOUR API KEY HERE");
  request.Content = new StringContent("{\"imageBase64\": \"" + base64Image + "\"}");
  request.Content.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

  var response = await httpClient.SendAsync(request);

  var imageResultString = await response.Content.ReadAsStringAsync();
  var deserializedContents = JsonSerializer.Deserialize<HotpotResponseModel>(imageResultString, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });

  if (deserializedContents != null && deserializedContents.StatusCode == 200) {
      return new PhotoResponseModel { NewImage = deserializedContents.ImageBase64 };
  }

  return null;
}


public class HotpotResponseModel {
  public int StatusCode { get; set; }
  public string StatusMessage { get; set; }
  public string ImageBase64 { get; set; }
}
```
