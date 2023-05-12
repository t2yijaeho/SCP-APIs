# Samsung Cloud Platform (SCP) Application Programming Interfaces (APIs)

## Creating a SCP account

To use SCP APIs, you need to have a SCP account. This allows you to use SCP developer products, including SCP console, SCP CLI, Logging, and Monitoring

[***SCP Free Trials***](https://cloud.samsungsds.com/serviceportal/freetrials.html)

## Creating a SCP project

To use SCP APIs, you also need to have a SCP project. A project provides a managing boundary for your usage of SCP services and dashboards are grouped by projects, you can create one using the [***SCP console***](https://cloud.samsungsds.com/console/)

## Discovering APIs

Before using any Cloud APIs, you should use [***SCP API Guide***](https://cloud.samsungsds.com/openapiguide/) to browse available APIs

## Authenticating to APIs

SCP supports API keys to authenticat to an API requests. To use SCP APIs, you have to create an ***Access Key*** and ***Access Secret Key*** pair using console according to guide

### Authenticating Headers

The specific headers that used with API Call

- ***X-Cmp-AccessKey***: This is an Access Key provided by prior step

- ***X-Cmp-Signature***: The encrypted signature of the API request. First creating a message that is composed of the HTTP method, the URL, the timestamp, the access key, the project ID, and the client type. The message is then hashed using the HMAC-SHA256 algorithm with the secret key. The resulting hash is then encoded in Base64 format

- ***X-Cmp-Timestamp***: This header specifies the amount of time that has passed in milliseconds since January 1, 1970 00:00:00 Coordinated Universal Time (UTC)

- ***X-Cmp-ClientType***: This header identifies the type of client making the API call

- ***X-Cmp-ProjectId***: This header contains the unique identifier (Project ID) of the project for which you intend to execute the API

- ***X-Cmp-Language*** : Set the language to be used when executing API

### Securing API key

>*You have to ensure that Access Key and Access Secret Key are kept secure during both storage and transmission. Publicly exposing your keys can lead to unexpected charges on your account*

## Using an API with REST

You can pass each values into a REST API requests use the authenticating headers with the following format

For example, to use for a Access Keys API:

```Bash
curl -i -X GET
-H "X-Cmp-AccessKey:<Access Key>"
-H "X-Cmp-Signature:<Signature>"
-H "X-Cmp-Timestamp:<Time Stamp>"
-H "X-Cmp-ClientType:OpenApi"
-H "X-Cmp-ProjectId:<Project ID>"
-H "X-Cmp-Language:en-US"
'https://openapi.samsungsdscloud.com/iam/v2/access-keys'
```

## Create an API Application with Node.js on Ubuntu

### 1. Install Node.js

Refer to [Debian and Ubuntu based Linux distributions](https://github.com/nodesource/distributions#debinstall)

### 2. Create a Node.js application

- Setup working directory

```Bash
mkdir SCP-API && cd SCP-API
```

```Bash
ubuntu@SCP:~$ mkdir SCP-API && cd SCP-API
ubuntu@SCP:~/SCP-API$
```

- Create `scp-api.js` and add following code

>***Replace `<Access Key>, <Secret key>, <Project ID>`***

```Bash
cat <<EOF >scp-api.js
const CryptoJS = require('crypto-js');

async function main() {
  const fetch = (await import('node-fetch')).default;

  function createSignature(method, url, timestamp, accessKey, secretKey, projectId, clientType) {
    const encodedUrl = encodeURI(url);
    const message = method + encodedUrl + timestamp + accessKey + projectId + clientType;
    const hash = CryptoJS.HmacSHA256(message, secretKey);
    return CryptoJS.enc.Base64.stringify(hash);
  }

  const method = 'GET';
  const url = 'https://openapi.samsungsdscloud.com/iam/v2/access-keys';
  const timestamp = Date.now();
  const accessKey = '<Access Key>';
  const secretKey = '<Secret key>';
  const projectId = '<Project ID>';
  const clientType = 'OpenApi';
  const language = 'en-US';

  const hmacSignature = createSignature(method, url, timestamp, accessKey, secretKey, projectId, clientType);

  const options = {
    method,
    headers: {
      'X-Cmp-AccessKey': accessKey,
      'X-Cmp-Signature': hmacSignature,
      'X-Cmp-Timestamp': timestamp,
      'X-Cmp-ClientType': clientType,
      'X-Cmp-ProjectId': projectId,
      'X-Cmp-Language': language,
    },
  };

  try {
    const res = await fetch(url, options);
    const body = await res.json();
    console.log(JSON.stringify(body, null, 2));
  } catch (err) {
    console.error(err);
  }
}

main();
EOF
```


### 3. Install moduels and run application

- Install `Crypto JS` for creating signature and `Node Fetch` to request API

```Bash
npm install crypto-js node-fetch
```

```Bash
ubuntu@SCP:~/SCP-API$ npm install crypto-js node-fetch

added 7 packages in 547ms

3 packages are looking 
found 0 vulnerabilities
```

- Run Node.js application

```Bash
node scp-api.js
```

```Bash
ubuntu@SCP:~/SCP-API$ node scp-api.js
{
  "totalCount": 1,
  "contents": [
    {
      "accessKey": "accesskey",
      "accessKeyActivated": true,
      "accessKeyId": "accesskeyid",
      "createdBy": "xxxxx",
      "createdByEmail": "scp.support@samsung.com",
      "createdByName": "SCP Support",
      "createdDt": "2023-12-25T06:13:55.462Z",
      "expiredDt": null,
      "modifiedBy": "xxxxx",
      "modifiedByEmail": "scp.support@samsung.com",
      "modifiedByName": "SCP Support",
      "modifiedDt": "2023-12-25T06:13:55.462Z",
      "projectId": "",
      "projectName": null
    }
  ],
  "page": 0,
  "size": 20,
  "sort": null
}
```
