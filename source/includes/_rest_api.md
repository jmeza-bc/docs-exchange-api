# REST API

## Overview

> Example request & response cycle

```python
import requests

base_url = "https://api.blockchain.com/v3/exchange"

symbols = requests.request(
  "GET",
  base_url + "/symbols",
)

print(symbols.text)

headers = {
  "X-API-Token": "", # TODO: Add in your secret API key
}

place_order = requests.request(
  "POST",
  base_url + "/orders",
  headers=headers,
  json={
    "clOrdId": "123456",
    "ordType": "LIMIT",
    "symbol": "ETH-USD",
    "side": "BUY",
    "orderQty": "0.1",
    "price": "100",
  }, # POST request params are passed as a JSON body
)

print(place_order.text)

get_all_orders = requests.request(
  "GET",
  base_url + "/orders",
  headers=headers,
  params={
    "symbol": "ETH-USD",
    'status': 'OPEN'
  }, # Params for GET requests go in the query string
)

print(get_all_orders.text)

cancel_all_orders = requests.request(
  "DELETE",
  base_url + "/orders",
  headers=headers,
  params={
    'symbol': "ETH-USD"
  } # DELETE request params go in the query string too
)

print(cancel_all_orders.text)
```

**Connection**

Currently available environments are:

| Name        | Base URL                                 |
| ----------- | ---------------------------------------- |
| production  | `https://api.blockchain.com/v3/exchange` |

<aside class="notice">
Note that for <code>POST</code> requests, parameters are passed in a JSON body, while for <code>GET</code> and <code>DELETE</code> these are placed in the query string.
</aside>

**Authentication**

Having [generated your API credentials](#generating-api-keys), attach your secret key in a `X-API-Token` header for any authenticated request.

## Anonymous Endpoints

### Get Symbols

> Example request

```shell
curl https://api.blockchain.com/v3/exchange/symbols
```

> Example response

```json
{
  "BTC-USD": {
    "base_currency": "BTC",
    "base_currency_scale": 8,
    "counter_currency": "USD",
    "counter_currency_scale": 2,
    "min_price_increment": 1,
    "min_price_increment_scale": 2,
    "min_order_size": 50000,
    "min_order_size_scale": 8,
    "max_order_size": 0,
    "max_order_size_scale": 8,
    "lot_size": 1,
    "lot_size_scale": 8,
    "status": "open",
    "id": 1,
    "auction_price": 29500.1,
    "auction_size": 0.0,
    "auction_time": "0",
    "imbalance": 0.2
  },
  "BTC-EUR": {
    "base_currency": "BTC",
    "base_currency_scale": 8,
    "counter_currency": "EUR",
    "counter_currency_scale": 2,
    "min_price_increment": 1,
    "min_price_increment_scale": 2,
    "min_order_size": 50000,
    "min_order_size_scale": 8,
    "max_order_size": 0,
    "max_order_size_scale": 8,
    "lot_size": 1,
    "lot_size_scale": 8,
    "status": "open",
    "id": 4,
    "auction_price": 27800.0,
    "auction_size": 0.0,
    "auction_time": "0",
    "imbalance": 0.004
  },
  "BTC-GBP": {
    "base_currency": "BTC",
    "base_currency_scale": 8,
    "counter_currency": "GBP",
    "counter_currency_scale": 2,
    "min_price_increment": 1,
    "min_price_increment_scale": 2,
    "min_order_size": 70000,
    "min_order_size_scale": 8,
    "max_order_size": 0,
    "max_order_size_scale": 8,
    "lot_size": 1,
    "lot_size_scale": 8,
    "status": "open",
    "id": 35,
    "auction_price": 23157.15,
    "auction_size": 0.0,
    "auction_time": "0",
    "imbalance": 0.05621622
  }
}
```

`GET /symbols`

Retrieves all available symbols supported by Blockchain.com Exchange.

**Parameters**

None.

### Get Symbol

> Example request

```shell
curl https://api.blockchain.com/v3/exchange/symbols/BTC-USD
```

> Example response

```json
{
  "base_currency": "BTC",
  "base_currency_scale": 8,
  "counter_currency": "USD",
  "counter_currency_scale": 2,
  "min_price_increment": 1,
  "min_price_increment_scale": 2,
  "min_order_size": 50000,
  "min_order_size_scale": 8,
  "max_order_size": 0,
  "max_order_size_scale": 8,
  "lot_size": 1,
  "lot_size_scale": 8,
  "status": "open",
  "id": 1,
  "auction_price": 29500.1,
  "auction_size": 0.0,
  "auction_time": "0",
  "imbalance": 0.2
}
```

`GET /symbols/{symbol}`

Retrieves an individual symbol supported by Blockchain.com Exchange.

**Parameters**

| Name    | Description                         |
| ------- | ------------------------------------|
| symbol  | e.g `BTC-USD`, `BTC-EUR`, `ETH-USD` |

### Get L2 Orderbook

> Example request

```shell
curl https://api.blockchain.com/v3/exchange/l2/BTC-USD
```

> Example response

```json
{
  "symbol": "BTC-USD",
  "bids": [
    {
      "px": 29117.37,
      "qty": 0.0346872,
      "num": 1
    },
    {
      "px": 29116.28,
      "qty": 0.31219648,
      "num": 2
    },
    {
      "px": 29114.82,
      "qty": 0.52035355,
      "num": 1
    }
  ],
  "asks": [
    {
      "px": 29120.86,
      "qty": 0.03468304,
      "num": 2
    },
    {
      "px": 29121.95,
      "qty": 0.31213569,
      "num": 1
    },
    {
      "px": 29123.4,
      "qty": 0.52020025,
      "num": 1
    }
  ]
}
```

`GET /l2/{symbol}`

Returns an orderbook snapshot for a specific symbol. Each entry in `bids` and `asks` is an aggregated price level.

**Parameters**

| Name    | Description                         |
| ------- | ------------------------------------|
| symbol  | e.g `BTC-USD`, `BTC-EUR`, `ETH-USD` |

### Get L3 Orderbook

> Example request

```shell
curl https://api.blockchain.com/v3/exchange/l3/BTC-USD
```

> Example response

```json
{
  "symbol": "BTC-USD",
  "bids": [
    {
      "px": 29109.59,
      "qty": 0.03469647,
      "num": 844439726977141
    },
    {
      "px": 29107.43,
      "qty": 0.3122914,
      "num": 844439726977327
    },
    {
      "px": 29107.05,
      "qty": 0.52049246,
      "num": 844439726977145
    }
  ],
  "asks": [
    {
      "px": 29121.82,
      "qty": 0.0346819,
      "num": 844439726977142
    },
    {
      "px": 29121.84,
      "qty": 0.31213687,
      "num": 844439726977328
    },
    {
      "px": 29123.74,
      "qty": 0.52019418,
      "num": 844439726977146
    }
  ]
}
```

`GET /l3/{symbol}`

Returns an orderbook snapshot for a specific symbol. Each entry in `bids` and `asks` represents an order. In contrast with the L2 orderbook, the L3 orderbook contains all individual orders without aggregation.

**Parameters**


| Name    | Description                         |
| ------- | ------------------------------------|
| symbol  | e.g `BTC-USD`, `BTC-EUR`, `ETH-USD` |


### Get Tickers

> Example request

```shell
curl https://api.blockchain.com/v3/exchange/tickers
```

> Example response

```json
[
  {
    "symbol": "BTC-USD",
    "price_24h": 29387.44,
    "volume_24h": 28.89521219,
    "last_trade_price": 29081.4
  },
  {
    "symbol": "BTC-EUR",
    "price_24h": 26898.43,
    "volume_24h": 4.48302417,
    "last_trade_price": 26621.63
  },
  {
    "symbol": "ETH-USD",
    "price_24h": 1841.61,
    "volume_24h": 62.83513382,
    "last_trade_price": 1819.32
  }
]
```

`GET /tickers`

Retrieves tickers for all symbols supported by Blockchain.com exchange.

**Parameters**

None.

### Get Ticker

> Example request

```shell
curl https://api.blockchain.com/v3/exchange/tickers/BTC-USD
```

> Example response

```json
{
  "symbol": "BTC-USD",
  "price_24h": 29387.44,
  "volume_24h": 28.89521219,
  "last_trade_price": 29081.4
}
```

`GET /tickers/{symbol}`

Retrieves the ticker for a specific symbol.

**Parameters**

None.

## Authenticated Endpoints

### Place order

> Example request

```json
{
  "clOrdId": "123456",
  "ordType": "LIMIT",
  "symbol": "BTC-USD",
  "side": "BUY",
  "orderQty": "0.1",
  "price": "100"
}
```

> Example response

```json
{
  "exOrdId": 11111111,
  "clOrdId": "ABC",
  "ordType": "MARKET",
  "ordStatus": "FILLED",
  "side": "BUY",
  "price": 0.12345,
  "text": "string",
  "symbol": "BTC-USD",
  "lastShares": 0.5678,
  "lastPx": 3500.12,
  "leavesQty": 10,
  "cumQty": 0.123345,
  "avgPx": 345.33,
  "timestamp": 1592830770594
}
```

Creates an order.

**HTTP Request**

`POST /orders`

**Parameters**

| Name        | Type   | Required | Description                         |
| ----------- | ------ | -------- | ------------------------------------|
| symbol      | string | True     | e.g `BTC-USD`, `BTC-EUR`, `ETH-USD` |
| ordType     | string | True     | `LIMIT`, `MARKET`, `STOP` or `STOPLIMIT` |
| side        | string | True     | `buy` or `sell` |
| orderQty    | string | True     | Order size in terms of base currency. |
| price       | string | False    | Price at which to fill the order. Required if `ordType` is not `MARKET`. |
| clOrdId     | string | True     | Reference ID provided by client. Cannot exceed 20 alphanumeric characters. |
| timeInForce | string | False    | `GTC`, `IOC`, `FOK` or `GTD` |
| expireDate  | string | False    | Expiry date in the format `YYYYMMDD` |
| minQty      | string | False    | Minimum quantity required for `IOC` orders |
| stopPx      | string | False    | Price at which to trigger a `STOPLIMIT` order |

### Cancel order

Cancels an open order.

**HTTP Request**

`DELETE /orders/{orderId}`

**Parameters**

| Name        | Required  |  Description |
| ----------- | --------- |  ----------- |
| orderId     |  True     |  Exchange-assigned order ID. |

### Cancel all orders

Cancels all open orders.

**HTTP Request**

`DELETE /orders`

**Parameters**

| Name        | Required   | Description |
| ----------- | ---------- | ----------- |
| symbol      |  False     | If specified, will cancel all orders only for that symbol |

### Get order

> Example response

```json
{
  "orderId": 21745988181,
  "gwOrderId": 2789221636,
  "clOrdId": "d7402f75314a4cf1webd",
  "symbol": "ETH-BTC",
  "ordType": "limit",
  "timeInForce": "GTC",
  "side": "buy",
  "orderQty": 1.0,
  "minQty": 0.0,
  "cumQty": 0.0,
  "price": 0.015797,
  "stopPx": 0.0,
  "ordStatus": "cancelled",
  "expireDate": 20200414,
  "execID": "537374697",
  "avgPx": 0.0
}
```

Gets a specific order by its ID.

**HTTP Request**

`GET /orders/{orderId}`

**Parameters**

| Name    | Description                         |
| ------- | ------------------------------------|
| orderId  | Exchange-assigned order ID. |

### Get all orders

> Example response

```json
[
  {
    "clOrdId": "42393cc5882b47f5webd",
    "ordType": "LIMIT",
    "ordStatus": "OPEN",
    "side": "SELL",
    "symbol": "BTC-USD",
    "exOrdId": 528318716822,
    "price": 28700.0,
    "text": "",
    "lastShares": 0.1713819,
    "lastPx": 28700.0,
    "leavesQty": 0.1713819,
    "cumQty": 0.0,
    "avgPx": 0.0,
    "timestamp": 1689094384009
  }
]
```

Retrieves a list of orders.

**HTTP Request**

`GET /internal/orders`

**Parameters**

| Name   | Description                         |
| ------ | ------------------------------------|
| symbol | e.g `BTC-USD`, `BTC-EUR`, `ETH-USD` |
| from   | Epoch timestamp in milliseconds. |
| to     | Epoch timestamp in milliseconds. |
| status     | `OPEN`, `REJECTED`, `CANCELED`, `FILLED`, `PART_FILLED`, `EXPIRED`, `PENDING` |
| limit  | Maximum result count to return in a single call. Defaults to `100`. |

### Get all orders (from the matching engine)

> Example response

```json
{
  "nextOrderId": 11111111,
  "orders": [
    {
      "exOrdId": 11111111,
      "clOrdId": "ABC",
      "ordType": "MARKET",
      "ordStatus": "FILLED",
      "side": "BUY",
      "price": 0.12345,
      "text": "string",
      "symbol": "BTC-USD",
      "lastShares": 0.5678,
      "lastPx": 3500.12,
      "leavesQty": 10,
      "cumQty": 0.123345,
      "avgPx": 345.33,
      "timestamp": 1592830770594
    }
  ]
}
```

Retrieves all orders directly from the matching engine.

**HTTP Request**

`GET /internal/orders`

**Parameters**

| Name   | Description                         |
| ------ | ------------------------------------|
| symbol | e.g `BTC-USD`, `BTC-EUR`, `ETH-USD` |
| from   | Epoch timestamp in milliseconds. |
| to     | Epoch timestamp in milliseconds. |
| limit  | Maximum result count to return in a single call. Defaults to `100`. |


### Get trades

> Example response

```json
[
  {
    "exOrdId": 11111111,
    "clOrdId": "ABC",
    "ordType": "MARKET",
    "ordStatus": "FILLED",
    "side": "BUY",
    "price": 0.12345,
    "text": "string",
    "symbol": "BTC-USD",
    "lastShares": 0.5678,
    "lastPx": 3500.12,
    "leavesQty": 10,
    "cumQty": 0.123345,
    "avgPx": 345.33,
    "timestamp": 1592830770594
  }
]
```

Returns filled orders, including partial fills. Returns at most 100 results - use the returned timestamp to paginate for further results.

**HTTP Request**

`GET /trades`

**Parameters**

| Name   | Description                         |
| ------ | ------------------------------------|
| symbol | e.g `BTC-USD`, `BTC-EUR`, `ETH-USD` |
| from   | Epoch timestamp in milliseconds. |
| to     | Epoch timestamp in milliseconds. |
| limit  | Maximum result count to return in a single call. Defaults to `100`. |

### Get fills

> Example response

```json
[
  {
    "exOrdId": 11111111,
    "tradeId": 11111111,
    "execId": 11111111,
    "side": "BUY",
    "symbol": "BTC-USD",
    "price": 0.12345,
    "qty": 10.23,
    "fee": 10.23,
    "timestamp": 1592830770594
  }
]
```

Retrieves individual fills. Returns at most 1000 results - use the returned timestamp and execId to navigate.

**HTTP Request**

`GET /fills`

**Parameters**

| Name   | Description                         |
| ------ | ------------------------------------|
| symbol | e.g `BTC-USD`, `BTC-EUR`, `ETH-USD` |
| from   | Epoch timestamp in milliseconds. |
| fromExecId   | List from ExecId onwards. |
| to     | Epoch timestamp in milliseconds. |
| toExecId   | List from ExecId backwards. |
| limit  | Maximum result count to return in a single call. Defaults to `1000`. |


### Get fee level

> Example response

```json
{
  "makerRate": 0.0014,
  "takerRate": 0.0024,
  "volumeInUSD": 1500
}
```

Gets the current fee level for the account.

**HTTP Request**

`GET /fees`

**Parameters**

None.
