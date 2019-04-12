---
title: Cybex ROME API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python
  - shell
  - javascript

toc_footers:
  - <a href='https://cybex.io'>By Cybex, Decentralized Exchange</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Cybex ROME(Realtime Order Matching Engine) API! 

Cybex is a decentralized exchange striving to provide 
better trading experience with transparency, efficiency and fairness. In order to reduce the order latency of chain confirmation,
we introduce the innovative system called ROME, Realtime Order Matching Engine.

ROME, is a next generation blockchain-based real time trading system which is desinged and developed by Cybex team. 
On the promise of safety and fairness of blockchain, ROME provides the realtime market data, 
the fastest order confirmation and matching notificaiton. 
All these highlights make Cybex DEX trading performance meets the highest standards of industry and shows good experince
 as top centralized exchanges such as Binance, Bitmex.

<img src="images/12.png" width="100%" />

ROME API endpoints are exposed so that Cybex exchange is more approachable, with realtime response. 
As Cybex is based on blockchain, all endpoints are **public**, but every transactional operation needs to be signed with your private key. 
The transactional operation is required to sign with user’s private key before sending to API server. 
User can use the response message from cyb-signer and send the exact content to API server. 
However, non-transactional operation can be sent to API server directly.

To facilitate the signing process, we developed the open source tool __cyb-signer__(java), featuring fast signing, please refer the [signer section](#signer-endpoints) of the document for details. The purpose of the service is to provide segregation between trading logic and signing logic so that the traders or the trading program don’t have to possess any key.

For python users, we have developed a connection library with built-in quick signing module. Our developers are currently working on a similar module on JavaScript. 


## Cybex Python Library

If you use python, you may the library package called *ROMEapi*, with fast signing module included, you don't need to install a signer separately.

Inspired by the trending crypto library CCXT, Cybex python library *ROMEapi* adopts similar functional calls interface for easier usage. 

### Installation

`pip install romeapi`

Once you have the library installed, you can access ROME API endpoints pretty easily.

### Usage

```python
 from romeapi import Cybex
 # init with accountName and password
 cybex = Cybex(accountName="sample_user", password="sample_password")
 
 # market data
 cybex.load_markets()
 # ticker
 cybex.fetch_ticker("ETH/USDT")
 # check account balance
 cybex.fetch_balance()
 # create a market buy order
 order_transaction_id, result = cybex.create_market_buy_order("ETH/USDT", 0.1)
 # query with transcation id
 orders = cybex.fetch_order(order_transaction_id)
 # cancel order
 cancel_order = cybex.cancel_order(order_transaction_id)
```

For source code and more usage details, please visit the official [github](https://github.com/CybexDex/RomeAPI). For more examples, please visit our example [github](https://github.com/CybexDex/Cybex-python-demo).

## Cybex JavaScript Library

Cybex JavaScript library is under development and will be released soon.

## Cybex CCXT integration and other language bindings

CCXT is a popular crypto trading library. Cybex CCXT integration is under development. For other languages bindings and further improvement, we welcome the generous support from the community.

# API Server Endpoints

Before get started, please note the list of order status notation of the Cybex system, which is essential in order query/filter.
As a decentralized system, there are more order statuses other than OPEN, FILLED and CANCELED.

ID |Status | Type* | Details 
----|---------|---- | ------- 
1|PENDING_NEW| open |New valid order, confirmed by ROME but not yet confirmed on chain  
2|OPEN| open | Open order, confirmed on chain 
3|PENDING_CXL| open |Cancel order confirmed by ROME, not yet confirmed on chain
4|CANCELED| closed |Order cancelled by user or expired, confirmed on chain 
5|FILLED | closed |Order fully or partially filled
6|REJECTED| closed |Order rejected by ROME if not valid. Order rejected by ROME will not go to chain.

Order type is the category for function call get_open_orders and get_closed_orders. 

## Query market info

```python
import requests
url = "https://api.cybex.io/v1/refData"
requests.get(url)
```

```shell
curl "https://api.cybex.io/v1/refData"
```

```javascript
fetch('https://api.cybex.io/v1/refData').then(res => res.json())
```

> The above command returns JSON structured like this:

```json
  {
    "chainId" : "90be01e82b981c8f201c9a78a3d31f655743b29ff3274727b1439b093d04aa23",
    "refBlockId" : "008a4a0cb09d391e15712efb101c49a2ed9dfe72",
    "availableAssets" : [{
      "assetName" : "ETH",
      "assetId" : "1.3.2",
      "precision" : 6
    }, {
      "assetName" : "USDT",
      "assetId" : "1.3.27",
      "precision" : 6
    },
     ......
    ],
    "availableAssetPairs" : [{
      "name" : "EOS/USDT",
      "minTickSize" : 0.0001,
      "minQuantity" : 0.1
    }, {
      "name" : "ETH/USDT",
      "minTickSize" : 0.01,
      "minQuantity" : 0.01
    },
    ...... 
    ],
    "fees" : {
      "feeAssetId" : "1.3.0",
      "newFee" : 55,
      "cancelFee" : 5,
      "cancelAllFee" : 50
    }
  }
```

It will return market information regarding exchange, e.g. chain id, reference block id, available assets, available asset pairs and fee info.

### HTTP Request

`GET  https://api.cybex.io/v1/refData`

## Execute transaction 

```python
import requests
url = "https://api.cybex.io/v1/transaction"
data = {"transactionType": "NewLimitOrder", "transactionId": "040c6466dc0cebb8de40520ebb7346fe0e446b35",  "refBlockNum": 18956,  "refBlockPreix": 507092400,  "txExpiration": 1546926850,  "fee": { "assetId": "1.3.0", "amount": 55  },  "seller": "1.2.40658",  "amountToSell": {"assetId": "1.3.27","amount": 277000},  "minToReceive": {    "assetId": "1.3.4",    "amount": 100000  },  "expiration": 1546991999,  "signature": "1b477931ebb89b39f7e1f1953c9ead66f1c93eee8183f4334729e8f19dceab2a5000af53728952dd73bc3636ab0663cc8f0293714d4c4ed8f3399c5a50b9da2816", "fill_or_kill": 0}
requests.post(url, json=data, headers={'Content-type': 'application/json'})
```

```shell
curl --data '{ "transactionType": "NewLimitOrder",  "transactionId": "040c6466dc0cebb8de40520ebb7346fe0e446b35",  "refBlockNum": 18956,  "refBlockPreix": 507092400,  "txExpiration": 1546926850,  "fee": {    "assetId": "1.3.0",    "amount": 55  },  "seller": "1.2.40658",  "amountToSell": {"assetId": "1.3.27","amount": 277000  },  "minToReceive": {    "assetId": "1.3.4",    "amount": 100000  },  "expiration": 1546991999,  "signature": "1b477931ebb89b39f7e1f1953c9ead66f1c93eee8183f4334729e8f19dceab2a5000af53728952dd73bc3636ab0663cc8f0293714d4c4ed8f3399c5a50b9da2816", "fill_or_kill": 0}' https://api.cybex.io/v1/transaction
```

```javascript
const data = {"transactionType": "NewLimitOrder", "transactionId": "040c6466dc0cebb8de40520ebb7346fe0e446b35",  "refBlockNum": 18956,  "refBlockPreix": 507092400,  "txExpiration": 1546926850,  "fee": { "assetId": "1.3.0", "amount": 55  },  "seller": "1.2.40658",  "amountToSell": {"assetId": "1.3.27","amount": 277000},  "minToReceive": {    "assetId": "1.3.4",    "amount": 100000  },  "expiration": 1546991999,  "signature": "1b477931ebb89b39f7e1f1953c9ead66f1c93eee8183f4334729e8f19dceab2a5000af53728952dd73bc3636ab0663cc8f0293714d4c4ed8f3399c5a50b9da2816", "fill_or_kill": 0}
fetch('https://api.cybex.io/v1/transaction', {
  method: 'POST', // or 'PUT'
  body: JSON.stringify(data), // data can be `string` or {object}!
  headers:{
    'Content-Type': 'application/json'
  }
}).then(res => res.json())
```

> The above command returns JSON if success:

```json
  {
    "Status": "Successful",
    "orderSequence": 529016,
    "signature": "1b477931ebb89b39f7e1f1953c9ead66f1c93eee8183f4334729e8f19dceab2a5000af53728952dd73bc3636ab0663cc8f0293714d4c4ed8f3399c5a50b9da2816",
    "time": "2019-01-08T05:53:19.049071Z"
  }
```

> The above command returns JSON if failed:

```json
  {
    "Status" : "Failed",
    "Code" : 1005,
    "Message" : "Duplicated transaction",
    "signature" : "1b477931ebb89b39f7e1f1953c9ead66f1c93eee8183f4334729e8f19dceab2a5000af53728952dd73bc3636ab0663cc8f0293714d4c4ed8f3399c5a50b9da2816"
  }
```

This API endpoint is for order execution. POST request with the formatted order message with valid signature(s). 
If order execution failed, please refer to the list of [execution errors](#order-execution-error-code) for details.

### HTTP Request

`POST https://api.cybex.io/v1/transaction`

### URL Parameters
POST with the json content,ie, the formatted order message with valid signature, from cyb-signer as the content body of this post request.

## Query order status

```python
import requests
url = "https://api.cybex.io/v1/order?accountName=XXXX&reverse=true&orderStatus=FILLED,CANCELED"
requests.get(url)
```

```shell
curl 'https://api.cybex.io/v1/order?accountName=XXXX&reverse=true&orderStatus=FILLED,CANCELED'
```

```javascript
fetch('https://api.cybex.io/v1/order?accountName=XXXX&reverse=true&orderStatus=FILLED,CANCELED').then(res => res.json())
```

> The above command returns JSON if success:

```json
  [ 
    {
      "accountName" : "XXXX",
      "orderSequence" : 529016,
      "orderStatus" : "FILLED",
      "assetPair" : "EOS/USDT",
      "side" : "buy",
      "price" : 2.77,
      "quantity" : 0.1,
      "filledQuantity" : 0.102437,
      "averagePrice" : 2.7041010572,
      "transactionId" : "040c6466dc0cebb8de40520ebb7346fe0e446b35",
      "createTime" : "2019-01-08T05:53:19.046342Z",
      "lastUpdateTime" : "2019-01-08T05:53:21.806097Z"
    }, {
      "accountName" : "XXXX",
      "orderSequence" : 169409,
      "orderStatus" : "FILLED",
      "assetPair" : "EOS/USDT",
      "side" : "buy",
      "price" : 2.77,
      "quantity" : 0.1,
      "filledQuantity" : 0.100264,
      "averagePrice" : 2.762706455,
      "transactionId" : "e447bca7d48412d8fc4aecc91224d008a4946145",
      "createTime" : "2019-01-03T01:33:39.501859Z",
      "lastUpdateTime" : "2019-01-03T01:33:42.722457Z"
    }
  ] 

```

This API endpoint is used to query order statuses.

### HTTP Request

`GET https://api.cybex.io/v1/order`

### URL Parameters
    
Parameter | Type | Mandatory | Example | Description
--------- | --------- | ----------- | ----------- |  ----------- 
accountName | STRING |  YES | 1.2.40658  | Your-account-name
reverse | BOOL |  NO |  | If it is true, the most recent record will be shown at the beginning
assetPair | STRING |  NO | ETH/USDT | Asset pair filter
orderStatus | STRING |  NO | FILLED | Use comma as delimiter to include more than one statuses. E.g. FILLED, OPEN
startTime | STRING |  NO | 2018-01-07T01:20:48.647910Z | Beginning time of your query, format must be YYYY-MM-DDTHH:mm:ss.ssssssZ
endTime | STRING |  NO | 2019-01-07T01:20:48.647910Z | End time of your query, format must be YYYY-MM-DDTHH:mm:ss.ssssssZ.
start | INT |  NO | 0 | Number where result will be shown from
limit | INT |  NO | 100 | Max number of results returned
transactionId | STRING | NO | 410sdl8ila | Query by transaction id
    
## Query trade

```python
import requests
url = "https://api.cybex.io/v1/trade?accountName=XXXXXX&reverse=true"
requests.get(url)
```

```shell
curl 'https://api.cybex.io/v1/trade?accountName=XXXXXX&reverse=true'
```

```javascript
fetch('https://api.cybex.io/v1/trade?accountName=XXXXXX&reverse=true').then(res => res.json())
```

> The above command returns JSON if success:

```json
  [ {
    "accountName" : "XXXX",
    "orderSequence" : 529016,
    "chainOrderId" : 395937330,
    "assetPair" : "EOS/USDT",
    "side" : "buy",
    "orderPrice" : 2.77,
    "orderQuantity" : 0.1,
    "tradePrice" : 2.7041010572,
    "tradeQuantity" : 0.102437,
    "blockTime" : "2019-01-08T05:53:21.000000Z"
  }, {
    "accountName" : "XXXX",
    "orderSequence" : 792863,
    "chainOrderId" : 382273197,
    "assetPair" : "ETH/USDT",
    "side" : "sell",
    "orderPrice" : 143.1,
    "orderQuantity" : 0.01,
    "tradePrice" : 143.1799,
    "tradeQuantity" : 0.01,
    "blockTime" : "2019-01-02T07:52:54.000000Z"
  } ]

```

This API endpoint is used to query trades. Trades are filled orders. accountName and assetPair cannot both be null. 

Please note that this endpoint don't support ID query.

### HTTP Request

`GET https://api.cybex.io/v1/trade`

### URL Parameters
    
    Parameter | Type | Mandatory | Example | Description
    --------- | --------- | ----------- | ----------- |  ----------- 
    accountName | STRING |  NO | 1.2.40658  | Your-account-name
    reverse | BOOL |  NO | 1 | If it is true, the most recent record will be shown at the beginning
    assetPair | STRING |  NO | ETH/USDT | Asset pair filter
    startTime | STRING |  NO | 2018-01-07T01:20:48.647910Z | Beginning time of your query, format must be YYYY-MM-DDTHH:mm:ss.ssssssZ
    endTime | STRING |  NO | 2019-01-07T01:20:48.647910Z | End time of your query, format must be YYYY-MM-DDTHH:mm:ss.ssssssZ.
    start | INT |  NO | 0 | Number where result will be shown from
    limit | INT |  NO | 100 | Max number of results returned

## Query account position

```python
import requests
url = "https://api.cybex.io/v1/position?accountName=1.2.40658"
requests.get(url)
```

```shell
curl 'https://api.cybex.io/v1/position?accountName=1.2.40658'
```

```javascript
fetch('https://api.cybex.io/v1/position?accountName=1.2.40658').then(res => res.json())
```

> The above command returns JSON if success:

```json
{  
   "accountName":"1.2.40658",
   "positions":[  
      {  
         "assetName":"ETH",
         "available":0.02,
         "frozen":0
      },
      {  
         "assetName":"CYB",
         "available":0,
         "frozen":0
      }
   ],
   "time":"2019-04-11T02:21:16.780992Z"
}
```

This API endpoint is used to query account current position.

### HTTP Request

`GET https://api.cybex.io/v1/position`

### URL Parameters
    
Parameter | Type | Mandatory | Example | Description
--------- | --------- | ----------- | ----------- |  ----------- 
accountName | STRING |  YES | 1.2.40658  | Your-account-name

## Query order book

```python
url = "https://api.cybex.io/v1/orderBook?assetPair=ETH/USDT&limit=3"
requests.get(url)
```

```shell
curl 'https://api.cybex.io/v1/orderBook?assetPair=ETH/USDT&limit=3'
```

```javascript
fetch('https://api.cybex.io/v1/orderBook?assetPair=ETH/USDT&limit=3').then(res => res.json())
```

> The above command returns JSON if success:

```json
  {
  	"assetPair": "ETH/USDT",
  	"bids": [
  		[
  			"146.31",
  			"5.0",
  			"731.55"
  		],
  		[
  			"146.3",
  			"3.192823",
  			"467.110114"
  		],
  		[
  			"146.28",
  			"3.0",
  			"438.84"
  		]
  	],
  	"asks": [
  		[
  			"146.36",
  			"3.902031",
  			"571.062364"
  		],
  		[
  			"146.39",
  			"3.19903",
  			"468.306001"
  		],
  		[
  			"146.41",
  			"3.742901",
  			"547.960743"
  		]
  	],
  	"time": "2019-01-08T06:22:37.47311Z"
  }

```

This API endpoint is used to query order book of given asset pair.

### HTTP Request

`GET https://api.cybex.io/v1/orderBook`

### URL Parameters
    
Parameter | Type | Mandatory | Example | Description |
--------- | --------- | ----------- | ----------- |  ------ 
assetPair | STRING |  YES | ETH/USDT  | Target asset pair
limit | INT | YES | 10 | Number of levels
    

## Query kline data

```python
url = "https://api.cybex.io/v1/klines?assetPair=EOS/USDT&interval=1m&limit=2"
requests.get(url)
```

```shell
curl 'https://api.cybex.io/v1/klines?assetPair=EOS/USDT&interval=1m&limit=2'
```

```javascript
fetch('https://api.cybex.io/v1/klines?assetPair=EOS/USDT&interval=1m&limit=2').then(res => res.json())
```

> The above command returns JSON if success:

```json
[
  [
    1546928820000,      // Open time
    "2.70150000",       // Open price
    "2.70240000",       // High
    "2.70150000",       // Low
    "2.70200000",       // Close
    "282.91000000",     // Volume
    1546928879999,      // Close time
    "764.40811500",     // Quote asset volume
    3,                  // Number of trades
    "64.45000000",      // Taker buy base asset volume
    "174.16968000",     // Taker buy quote asset volume
    "0"                 // Reserved
  ],
  [
    1546928880000,
    "2.70200000",
    "2.70200000",
    "2.70160000",
    "2.70160000",
    "163.48000000",
    1546928939999,
    "441.70876800",
    2,
    "163.48000000",
    "441.70876800",
    "0"
  ]
]
```

### HTTP Request

`GET https://api.cybex.io/v1/klines`

### URL Parameters


|Parameter|Type|Mandatory|Example value|
| --- | --- | --- | --- |
| assetPair | STRING | YES | E.g. ETH/USDT, EOS/USDT |
interval | ENUM | YES | 1m, 3m, , 5m, 15m, 30m, 1h, 2h, 4h, 6h, 8h, 12h, 1d, 3d, 1w, 1M
startTime | LONG | NO | Beginning time of the query, the format must be YYYY-MM-DDTHH:mm:ss.ssssssZ. E.g. 2018-01-07T01:20:48.647910Z
endTime | LONG | NO | End time of the query, the format must be YYYY-MM-DDTHH:mm:ss.ssssssZ. E.g. 2019-01-07T01:20:48.647910Z
limit | INT | NO | Level to be shown
useTradePrice | BOOL | NO | Default is "false", and this api returns market prices. If it is specified as "true" then this api returns our exchange's prices.  

   
# Cyb-signer Installation

## Download

Cyb-signer, designed for quick signing for the Cybex system, is open source on github. If you are using Cybex ROME API with languages other than Python and JavsScript, you may find it useful for signing. 

You can use git to clone or download the zip directly.

Type | Link 
--------- | ------- 
signer | https://github.com/CybexDex/cyb-signer.git
release | https://github.com/CybexDex/cyb-signer/releases

`git clone https://github.com/CybexDex/cyb-signer.git`

After cloning, please access release url stated, and put the jar file in “cyb-signer/target” folder.

<aside class="notice">
Cyb-singer is based on Java, in order to make things work, make sure you have <a href="https://adoptopenjdk.net/installation.html" target="_blank">java</a> installed. 
</aside>

<aside class="notice">
If you prefer to build the jar file from source code, you could navigate to cyb-signer/scripts and run build script.<code>./build.sh</code> This requires you have jdk and <a href="https://maven.apache.org/index.html" target="_blank">maven</a> installed.
</aside>

## Config

> Following are example for your reference:

```
SIGNER_SERVER_PORT=8090 #an available local port
PRIVATE_KEY=5JicqQ9tcwYoFGXPtFvdM3jAmwEz6Qi1zsuT7muNXCrRND2XXXX #your private key
ACCOUNT_ID=1.2.xxxxx #your cybex account id
API_SERVER_ADDRESS=api.cybex.io
```

All parameters present in the file __env.properties__ under “cyb-signer/scripts” folder.

<aside class="warning">
ACCOUNT ID is not the same as ACCOUNT NAME and its format is 1.2.XXXXX.
</aside>

<aside class="warning"> 
Private key is not your login password in Cybex website
</aside>

## Launch
You could use start script to launch/stop cyb-signer. Once cyb-signer starts, you don’t need to restart it unless you want to change parameters.

For Linux users, run

`./start.sh` `./stop.sh` 

For windows users, run

`./start.bat` `./stop.bat` 

For mac users, run

`startMac.sh`

Your signer will be available at http://localhost:8090(your port)
<aside class="warning">
To increase security of signing, cyb-signer and REST-client must be in the same computer, using <b>localhost</b> in the url. (For example, following request will fail: http://192.168.1.10:8090/signer/v1/newOrder)
</aside>

# Cyb-signer Endpoints

## Create New Order
```python
import requests

url = "http://127.0.0.1:8090/signer/v1/newOrder"
data = {'assetPair': symbol, 'price': price, 'quantity': quantity, 'side': side}
response = requests.post(url, json=data, headers={'Content-type': 'application/json'})
response.json()
```

```shell
curl "http://127.0.0.1:8090/signer/v1/newOrder"
```

```javascript
var data = {'assetPair': symbol, 'price': price, 'quantity': quantity, 'side': side}
fetch("http://localhost:8090/signer/v1/newOrder", {
    method: "POST", // *GET, POST, PUT, DELETE, etc.
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data), // body data type must match "Content-Type" header
}).then(res => res.json()); // parses response to JSON
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

Creates new limit order, with details of asset pair, price, amount and side. Please note that Cybex only supports **limit order** creation.

### HTTP Request

`POST http://localhost:8090/signer/v1/newOrder`

### Query Parameters

All fields are required.

Parameter | Type|  Example | Description
--------- | ----| ------- | -----------
assetPair | STRING | ETH/USDT | Targeted asset pair of trading intention 
price | FLOAT | 162.1 | Price of trading
quantity | FLOAT |0.1 | Amount of trading
side | STRING | buy/sell | Buy side or sell side

## Create Cancel Order

```python
import requests

url = "http://127.0.0.1:8090/signer/v1/cancelOrder"
data = {'originalTransactionId': trxid}
response = requests.post(url, json=data, headers={'Content-type': 'application/json'})
response.json()
```

```shell
curl --data '{"originalTransactionId": "88cecaa11b8584fb21243cd57ed2227e7c181452"}' http://localhost:8090/signer/v1/cancelOrder
```

```javascript
var data = {'originalTransactionId': trxid};
fetch("http://127.0.0.1:8090/signer/v1/cancelOrder", {
    method: "POST", // *GET, POST, PUT, DELETE, etc.
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data), // body data type must match "Content-Type" header
})
.then(res => res.json()); // parses response to JSON
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

This endpoint cancel an order with a given id.

### HTTP Request

`GET http://127.0.0.1:8090/signer/v1/cancelOrder`

### URL Parameters

All fields are required.

Parameter | Type |Example
--------- | ---| -----------
originalTransactionId| STRING | 88cecaa11b8584fb21243cd57ed2227e7c181452

## 	Create CancelAll

```python
import requests

url = "http://127.0.0.1:8090/signer/v1/cancelAll"
data = {'assetPair': symbol}
response = requests.post(url, json=data, headers={'Content-type': 'application/json'})
response.json()
```

```shell
curl --data '{"assetPair": "ETH/USDT"}' http://localhost:8090/signer/v1/cancelAl
```

```javascript
var data = {'assetPair': symbol};
fetch("http://127.0.0.1:8090/signer/v1/cancelAll", {
    method: "POST", // *GET, POST, PUT, DELETE, etc.
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data), // body data type must match "Content-Type" header
})
.then(res => res.json()); // parses response to JSON
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
  "signature" : "1c3435fd231625f61a516e44bcb193f6344441974cf76def071ea01e82a1a2d0043e44ee28993d523fa13bad5445a297cdeef6f0b6a86507e8cbba7841ec6a9010"
}

```

This endpoint cancel all orders on a given asset pair.

### HTTP Request

`POST http://localhost:8090/signer/v1/cancelAll`

### URL Parameters

All fields are required.

Parameter | Type |Example | Description
--------- | --- |----------- | -----------
assetPair | STRING |ETH/USDT | Use “CYB/CYB” to cancel all your open orders no matter what asset pairs they are in


# Cybex Web Socket Streams 
To improve market data efficiency, Cybex provides a websockets connection to subscribe market data, so that users can 
reduce latency and get streamed update.  

* The base endpoint is: **wss://mdp.cybex.io**
* Websocket connections are **subscription based**
* All topic and symbols are **uppercase**
* Steams send out update on every **500ms**
* Prefix like JADE cannot be omitted and must be joint with an underscore.

You may refer to the basic examples below in python and javascript(nodejs) for quick start. 

```python
# you will need  websocket client dependency 
# pip install websocket_client
from websocket import WebSocketApp

def on_message(ws, message):
    print(message)  

def on_open(ws):
    ws.send('{"type":"subscribe","topic":"TICKER.JADE_ETHJADE_USDT"}')
        
ws = WebSocketApp("wss://mdp.cybex.io", on_message = on_message)
ws.on_open = on_open
```

```javascript
// you will need websocket dependency 
// npm install websocket
const WebSocketClient = require('websocket').client;

var wsClient = new WebSocketClient();

wsClient.on('connect', function (connection) {
    
  connection.on('message', function (message) {
        console.log(message)
    });
    
    connection.sendUTF(JSON.stringify({"type":"subscribe","topic":"TICKER.JADE_ETHJADE_USDT"}));
});

wsClient.connect('wss://mdp.cybex.io');
```


## Orderbook
This subscription topic retrieves orderbook of a given asset pair, with parameter of precision and level. 

> {"type":"subscribe","topic":"ORDERBOOK.JADE_ETHJADE_USDT.5.1"}

````
{  
   "sym":"JADE.ETHJADE.USDT",
   "type":"depth",
   "bids":[  
      [  
         "137.28",
         "2.5522",
         "350.366016"
      ]
   ],
   "asks":[  
      [  
         "137.30001",
         "3.0",
         "411.9"
      ]
   ],
   "time":"2019-03-27T07:27:27.263305Z",
   "topic":"ORDERBOOK.JADE_ETHJADE_USDT.5.1"
}
````

**Topic Name:** ORDERBOOK{AssetPair}.{Precision}.{Level}

Parameter | Type | Mandatory | Example | Description |
--------- | --------- | ----------- | ----------- |  ------ 
assetPair | STRING |  YES | JADE_ETHJADE_USDT  | Target asset pair
Precision | INT | YES | 5 | Aggregated precision
Level | INT | YES | 3 | Number of levels

## Ticker
This subscription topic retrieves ticker information of a given asset pair. 

> {"type":"subscribe","topic":"TICKER.JADE_ETHJADE_USDT"}

````
{  
   "type":"ticker",
   "sym":"JADE.ETHJADE.USDT",
   "px":"135.22999375",
   "qty":"0.16000000",
   "cymQty":"103.31605400",
   "time":"2019-03-06T04:02:15.47252Z",
   "topic":"TICKER.JADE_ETHJADE_USDT"
}
````

**Topic Name:** LASTPRICE{AssetPair}

Parameter | Type | Mandatory | Example | Description |
--------- | --------- | ----------- | ----------- |  ------ 
assetPair | STRING |  YES | JADE_ETHJADE_USDT  | Target asset pair

## Lastprice
This subscription topic retrieves price change information of a given asset pair. 

> {"type":"subscribe","topic":"LASTPRICE.JADE_ETHJADE_USDT"}

```
{  
   "time":"2019-03-27T07:29:28.545943Z",
   "topic":"LASTPRICE.JADE_ETHJADE_USDT",
   "px":137.32994872
}
```

**Topic Name:** TICKER{AssetPair}

Parameter | Type | Mandatory | Example | Description |
--------- | --------- | ----------- | ----------- |  ------ 
assetPair | STRING |  YES | JADE_ETHJADE_USDT  | Target asset pair
