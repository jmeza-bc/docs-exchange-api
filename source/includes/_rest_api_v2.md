# REST API V2

## Overview

> Example response & request cycle

```python
import requests

base_url = "http://10.103.50.44:8000"

method = "POST"

path = "/public/exchange/v4/authenticated/newOrder"

response = requests.request(
    method,
    base_url + path,
    data={
        "symbol": "BTC-USD",
        "side": "BUY",
        "PRICE": 20000,
        "quantity": "0.001",
        "ordType": "LIMIT",
        "timeInForce": "GTC",
        "clOrdId": "",
    },
    headers={
        "X-BCX-APIKEY": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkFQSSJ9.eyJhdWQiOiJtZXJjdXJ5IiwidWlkIjoiODE4ZDkzMjEtMGM4ZC00MmQzLTgzM2QtZjA3YTcyMDk4M2QyIiwiaXNzIjoiYmxvY2tjaGFpbiIsInJkbyI6ZmFsc2UsImlhdCI6MTY4MzA0NDkzOSwianRpIjoiNDhiZDAyZjMtMzk2Ni00MjlhLWI3NmQtMzQwYTY3ZTZmMzkwIiwic2VxIjoxMDIxNDY2MCwid2RsIjp0cnVlfQ.H3weH51EfqRvnyR3Z/LsYtfsHIZQ14LORs8MSG+fEALPBcHD/A3Ie6b21fcURkEM8aidVCpYP/4HTdFMFPPcuZc="
    },
)
```

**Connection**

Currently available environments are:

| Environment | URI                          |
|-------------|------------------------------|
| dev         | `http://10.103.50.44:8000`   |

<aside class="notice">
Note that for <code>POST</code> requests, parameters are passed in a JSON body, while for <code>GET</code> and <code>DELETE</code> these are placed in the query string.
</aside>

**Authentication**

Having [generated your API credentials](#generating-api-keys), attach your secret key in a `X-BCX-APIKEY` header for any authenticated request.

## Authenticated endpoints

### Send order

Sends in a new order.

> Example request

```json
{
  "symbol": "BTC-USD",
  "side": "BUY",
  "PRICE": 20000,
  "quantity": "0.001",
  "ordType": "LIMIT",
  "timeInForce": "GTC",
  "clOrdId": "",
}
```

> Example response

```json
{
  "error": "",
  "orderId": "288654242695",
  "symbol": "BTC-USD",
  "quantity": "0.001000000",
  "minQty": "0.0",
  "executedQty": "0.0",
  "price": "20000.0",
  "side": "BUY",
  "ordType": "LIMIT",
  "timeInForce": "GTC",
  "clOrdId": "1691592745735733",
  "selfTradePreventionMode": "DECREMENT",
  "ordStatus": "NEW",
  "expireDate": "20231112",
  "transactTime": "1691592745736"
}
```

**HTTP Request**

`POST /public/exchange/v4/authenticated/newOrder`

**Parameters**

| Name                    | Type   | Required | Notes                                                              |
|-------------------------|--------|----------|--------------------------------------------------------------------|
| symbol                  | string | True     | e.g `BTC-USD`, `ETH-USD` or `BTC-EUR`                              |
| ordType                 | string | True     | `MARKET`, `LIMIT`, `STOP`, `STOPLIMIT`                              |
| side                    | string | False    | `BUY`, `SELL`                                                      |
| price                   | string | False    | Required if `ordType` is not `MARKET`                              |
| quantity                | string | True     |
| timeInForce             | string | True     | `GTC`, `IOC`, `FOK`, `GTD`                                         |
| selfTakerPreventionMode | string | False    | `EXPIRE_TAKER`, `EXPIRE_MAKER`, `EXPIRE_BOTH`, `DECREMENT`, `NONE` |
| clOrdId                 | string | False    | If not present, will be automatically generated

Possible values for `ordType` are explained below:

| Name        | Description                                                                                      |
|-------------|--------------------------------------------------------------------------------------------------|
| `LIMIT`     | Will match at a specific price.                                                                  |
| `MARKET`    | Will match at any price, starting from the best bid or offer up until the order is fully filled. |
| `STOP`      | Triggers a market order once the stop/trigger price is reached.                                  |
| `STOPLIMIT` | Triggers a limit order once the stop/trigger price is reached.                                   |


Possible values for `timeInForce` are explained below:

| Name  | Description                                                                                                                  |
|-------|------------------------------------------------------------------------------------------------------------------------------|
| `GTC` | Good Till Cancel. The order will rest on the order book until it is cancelled or filled                                      |
| `GTD` | Good Till Date. The order will rest on the order book until it is cancelled, filled, or expired                              |
| `FOK` | Fill or Kill. The order will be completely filled or cancelled. No partial fills are permitted.                              |
| `IOC` | Immediate or Cancel. The order will be completely filled, partially filled and the remaining quantity canceled, or canceled. |

Supported `timeInForce` values by `ordType` are as follows:

|       | `MARKET` | `LIMIT` | `STOP` | `STOPLIMIT` |
|-------|----------|---------|--------|-------------|
| `GTC` |          | X       | X      | X           |
| `GTD` |          | X       | X      | X           |
| `IOC` |          | X       |        |             |
| `FOK` |          | X       |        |             |

Where an "X" denotes support.

### Cancel order

> Example request

```json
{
  "symbol": "BTC-USD",
  "orderId": "288654242809",
  "origClientOrderId": "1691675705931441",
  "clOrdId": "1691675705931441",
}
```

> Example response

```json
{
  "error": "",
  "orderId": "288654242809",
  "symbol": "BTC-USD",
  "quantity": "0.001000000",
  "minQty": "0.0",
  "executedQty": "0.0",
  "price": "20000.0",
  "side": "BUY",
  "ordType": "LIMIT",
  "timeInForce": "GTC",
  "clOrdId": "1691675705931441",
  "selfTradePreventionMode": "DECREMENT",
  "ordStatus": "CANCELED",
  "expireDate": "20231113",
  "transactTime": "1691675736366"
}
```

Cancels an open order. It will return the cancelled order.

**HTTP Request**

`POST /public/exchange/v4/authenticated/cancel`

**Parameters**

| Name              | Type   | Required | Notes                                 |
|-------------------|--------|----------|---------------------------------------|
| symbol            | string | True     | e.g `BTC-USD`, `ETH-USD` or `BTC-EUR` |
| orderId           | string | True     |                                       |
| origClientOrderId | string | True     |                                       |
| clOrdId           | string | True     |                                       |

### Cancel all orders

> Example response

```json
{
  "error": "",
  "canceledOrders": [
    {
      "error": "",
      "orderId": "288654242811",
      "symbol": "",
      "quantity": "0.001000000",
      "minQty": "0.0",
      "executedQty": "0.0",
      "price": "20000.0",
      "side": "BUY",
      "ordType": "LIMIT",
      "timeInForce": "GTC",
      "clOrdId": "1691676195541120",
      "selfTradePreventionMode": "DECREMENT",
      "ordStatus": "CANCELED",
      "expireDate": "20231113",
      "transactTime": "1691676202490"
    }
  ]
}
```

Cancels all open orders. It will return an array of all orders cancelled.

**HTTP Request**

`POST /public/exchange/v4/authenticated/cancelAll`

**Parameters**

None.
