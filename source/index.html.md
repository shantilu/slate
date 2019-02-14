---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript
  - java

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Cybex API! You can use our API to access Cybex API endpoints, which can get information on various cats, kittens, and breeds in our database.

We have language bindings in Shell, Ruby, Python, and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

The transactional operation is required to sign with user’s private key before sending to API server. User can use the response message from cyb-signer and send the exact content to API server. 

# Authentication

> To authorize, use this code:

```ruby
require 'Cybex'

api = Cybex::APIClient.authorize!('meowmeowmeow')
```

```python
import Cybex

api = Cybex.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const Cybex = require('Cybex');

let api = Cybex.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Cybex uses API keys to allow access to the API. You can register a new Cybex API key at our [developer portal](http://example.com/developers).

Cybex expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
ACCOUNT ID is not the same as ACCOUNT NAME and its format is 1.2.XXXXX. 
Private key is not your login password in cybex website
</aside>

# Transactional API

## Create New Order

```ruby
require 'Cybex'

api = Cybex::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import Cybex

api = Cybex.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const Cybex = require('Cybex');

let api = Cybex.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
  {
    "transactionType" : "NewLimitOrder",
    "transactionId" : "40c275d77233c5761472673bd0532150dde723a0",
    "refBlockNum" : 18956,
    "refBlockPrefix" : 507092400,
    "txExpiration" : 1546919505,
    "fee" : {
      "assetId" : "1.3.0",
      "amount" : 55
    },
    "seller" : "1.2.40658",
    "amountToSell" : {
      "assetId" : "1.3.27",
      "amount" : 1466699999
    },
    "minToReceive" : {
      "assetId" : "1.3.2",
      "amount" : 10000000
    },
    "expiration" : 1546991999,
    "signature" : "1c7a00b61039b6bc1ace93916446207a28a69e60a97fbd6b267518754a13e7adca463239452b0e00aa90b71b30a57ea4d65fa4cee44a30ab2c7841fb5cd0145913",
    "fill_or_kill" : 0
  }

```

This endpoint retrieves all kittens.

### HTTP Request

`POST http://localhost:8090/signer/v1/newOrder`

### Query Parameters

Parameter | Example | Description
--------- | ------- | -----------
assetPair | ETH/USDT | If set to true, the result will also include cats.
price | true | If set to false, the result will include kittens that have already been adopted.
quantity | 10 | If set to false, the result will include kittens that have already been adopted.
side | buy/sell | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
-	ACCOUNT ID is not the same as ACCOUNT NAME
</aside>

## 

```ruby
require 'Cybex'

api.kittens.get(2)
```

```python
import Cybex

api.kittens.get(2)
```

```shell
curl --data '{"originalTransactionId": "88cecaa11b8584fb21243cd57ed2227e7c181452"}' http://localhost:8090/signer/v1/cancelOrder
```

```javascript
const Cybex = require('Cybex');

let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "transactionType" : "Cancel",
  "transactionId" : "e3a8b5c5afd2daa038707aa2e9e2d60701d726c0",
  "originalTransactionId" : "88cecaa11b8584fb21243cd57ed2227e7c181452",
  "refBlockNum" : 18956,
  "refBlockPrefix" : 507092400,
  "txExpiration" : 1546950595,
  "orderId" : "0",
  "fee" : {
    "assetId" : "1.3.0",
    "amount" : 5
  },
  "feePayingAccount" : "1.2.40658",
  "signature" : "1c313f1f34c7975e3999d483ddf1d2d2322751593b82f3f087f987e95a03b1639d696e2348105f2a9070c11c974b2c94ed616d95559108d7714f516fa5ee55d559"
}
```
## Create Cancel Order

This endpoint cancel an order with a given id.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Example
--------- | -----------
originalTransactionId | 88cecaa11b8584fb21243cd57ed2227e7c181452

## 	Create CancelAll

```ruby
require 'Cybex'

api.kittens.delete(2)
```

```python
import Cybex

api.kittens.delete(2)
```

```shell
curl --data '{"assetPair": "ETH/USDT"}' http://localhost:8090/signer/v1/cancelAl
```

```javascript
const Cybex = require('Cybex');

let api = Cybex.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "transactionType" : "CancelAll",
  "transactionId" : "9616b03391ccf0107c58326f2674c4b51a7bf284",
  "refBlockNum" : 18956,
  "refBlockPrefix" : 507092400,
  "txExpiration" : 1547007167,
  "fee" : {
    "assetId" : "1.3.0",
    "amount" : 50
  },
  "seller" : "1.2.40658",
  "sellAssetId" : "1.3.2",
  "recvAssetId" : "1.3.27",
  "signature" : "1c3435fd231625f61a516e44bcb193f6344441974cf76def07
1ea01e82a1a2d0043e44ee28993d523fa13bad5445a297cdeef6f0b6a8650
7e8cbba7841ec6a9010"
}

```

This endpoint deletes all orders on a given asset pair.

### HTTP Request

`POST http://localhost:8090/signer/v1/cancelAll`

### URL Parameters

Parameter | Example | Description
--------- | ----------- | -----------
assetPair | ETH/USDT | Use “CYB/CYB” to cancel all your open orders no matter what asset pairs they are in

