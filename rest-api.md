# Rest API

## Endpoints
https://api.cryptocore.ai/

## Authorization
To use resources that require authorized access, you will need to provide an API key to us when making HTTP requests.
You can authorize by providing additional custom header named `X-CryptoCore-ApiKey` and API key as its value.
Assuming that your API key is 9173A9CF-040C-427F-99FF-256877BAA9F6, then the authorization header you should send to us will look like:
```
X-CryptoCore-ApiKey: 9173A9CF-040C-427F-99FF-256877BAA9F6
```

## HTTP Requests
Each HTTP request must contain the header Accept: `application/json` as all our responses are in JSON format.

### HTTP Success
Successful HTTP responses have the status code 200 and the body in a format according to documentation of the requested resource. You should always check that your HTTP response status code is equal to 200, otherwise the requested was not successful.

### HTTP Errors
All HTTP requests with response status codes different to 200 must be considered as failed and you should expect additional JSON inside the body of the response with the error message encapsulated inside it as shown in the example.

Error message is returned in JSON structured like this:
```json
{
   "message": "Invalid API key"
}
```

## Metadata
### List all exchanges
Get a detailed list of exchanges provided by the system

HTTP Request
`GET /v1/data/exchanges`

Output

| Variable | Description |
| :--- | :--: |
| exchange_id | Our exchange identifier |

Output Example:
```json
[
  {
    "exchange_id": "BITSTAMP"
  },
  {
    "exchange_id": "BITFINEX"
  }
]
```
### List all symbols
Get detailed list of symbols.

HTTP Request
`GET /v1/data/symbols?exchange_id={exchange_id}`

Parameters:

| Parameter | Type | Description |
| :-- | :--: | :--: |
| exchange_id | string | Exchange identifier used to filter response. (optional) |

Output:

| Variable | Description |
| :-- | :--: |
| symbol_id | Our symbol identifier, format: `{base_coin}-{quoted-coin}-{exchange_id}` |

Output example:
```json
[
  {
    "symbol_id": "ADA-BTC-BINANCE"
  },
  {
    "symbol_id": "CS-ETH-BITTREX"
  }
]
```

## OHLCV
API calls described in this section are related to downloading OHLCV (Open, High, Low, Close, Volume) timeseries data. Each data point of this timeseries represents several indicators calculated from transactions activity inside a time range (period).

### List of Periods

| Time unit | Period identificators |
| :-- | :--: |
| Minute | 1MIN, 5MIN, 15MIN, 30MIN |
| Hour | 1HR |
| Day | 1DAY |

### Latest data
Get OHLCV latest timeseries data for requested symbol and period, returned in time descending order.

HTTP Request
`GET /v1/data/ohlcv/{symbol_id}/latest?period_id={period_id}&limit={limit}`

Parameters

| Parameter | Type | Description |
| :-- | :--: | :-- |
| symbol_id | string | Symbol identifier of requested timeseries |
| period_id | string | Identifier of requested timeseries period (required, e.g. 1MIN or 1HRS) |
| limit | int | Amount of items to return (optional, mininum is 1, maximum is 1000, default value is 100) |

Output

| Variable | Description |
| :-- | :-- |
| timestamp | Period starting time |
| open | First trade price inside period range |
| high | Highest traded price inside period range |
| low | Lowest traded price inside period range |
| close | Last trade price inside period range |
| volume | Cumulative base amount traded inside period range |

Output example
```json
[
  {
    "timestamp": "2017-08-09T14:31:00.0000000Z",
    "open": 3255.590000000,
    "high": 3255.590000000,
    "low": 3244.740000000,
    "close": 3244.740000000,
    "volume": 16.903274550
  },
  {
    "timestamp": "2017-08-09T14:32:00.0000000Z",
    "open": 3255.590000000,
    "high": 3255.590000000,
    "low": 3244.740000000,
    "close": 3244.740000000,
    "volume": 16.903274550
  }
]
```

### Historical Data
Get OHLCV timeseries data for requested symbol and period, returned in time ascending order.

HTTP Request
`GET /v1/data/ohlcv/{symbol_id}/history?period_id={period_id}&time_start={time_start}&time_end={time_end}`

Parameters

| Parameter | Type | Description |
| :-- | :--: | :-- |
| symbol_id | string | Symbol identifier of requested timeseries |
| period_id | string | Identifier of requested timeseries period (required, e.g. 1MIN or 1HRS) |
| time_start | timestring | Timeseries starting time (required) |
| time_end | timestring | Timeseries ending time (optional, if ncot supplied 100 elements will returned) |

Output

| Variable | Description |
| :-- | :-- |
| timestamp | Period starting time |
| open | First trade price inside period range |
| high | Highest traded price inside period range |
| low | Lowest traded price inside period range |
| close | Last trade price inside period range |
| volume | Cumulative base amount traded inside period range |

Output example
```json
[
  {
    "timestamp": "2017-08-09T14:31:00.0000000Z",
    "open": 3255.590000000,
    "high": 3255.590000000,
    "low": 3244.740000000,
    "close": 3244.740000000,
    "volume": 16.903274550
  },
  {
    "timestamp": "2017-08-09T14:32:00.0000000Z",
    "open": 3255.590000000,
    "high": 3255.590000000,
    "low": 3244.740000000,
    "close": 3244.740000000,
    "volume": 16.903274550
  }
]
```

## Trades
This section describes calls related to executed transactions data, also known as matched orders data or active data.

### Latest trades

HTTP Request
`GET /v1/data/trades/{symbol_id}/latest?limit={limit}`

Parameters

| Parameter | Type | Description |
| :-- | :--: | :-- |
| symbol_id | string | Symbol identifier |
| limit | int | Amount of items to return (optional, mininum is 1, maximum is 1000, default value is 100 |

Output

| Variable | Description |
| :-- | :-- |
| timestamp | Time of trade reported by exchange |
| price | Price of the transaction |
| size | Base asset amount traded in the transaction |

Output example
```json
[
  {
    "timestamp": "2013-09-28T22:40:50.0000000Z",
    "price": 770.000000000,
    "size": 0.050000000
  }
]
```

## Quotes
This section describes calls related to quotes data, also known as quotes or passive level 1 data.

### Latest quotes

HTTP Request
`GET /v1/data/quotes/{symbol_id}/latest`

Parameters

| Parameter | Type | Description |
| :-- | :--: | :-- |
| symbol_id | string | Symbol identifier | 

Output

| Variable | Description |
| :-- | :-- |
| timestamp | Exchange time of order book |
| ask | Best asking price |
| ask_size | Volume resting on best ask |
| bid | Best bidding price |
| bid_size | Volume resting on best bid |
| last | Last executed transaction |
| last_size | Last executed transaction size |

Output example
```json
{
   "timestamp":"2013-09-28T22:40:50.0000000Z",
   "ask":770.000000000,
   "ask_size":3252,
   "bid":760,
   "bid_size":124,
   "last":3252,
   "last_size":12
}
```

## Order book
This section describes calls related to order book data, also known as books or passive level 2 data.

### Latest book

HTTP Request
`GET /v1/data/orderbooks/{symbol_id}/latest`

Parameters

| Parameter | Type | Description |
| :-- | :--: | :-- |
| symbol_id | string | Symbol identifier |

Output

| Variable | Description |
| :-- | :-- |
| timestamp | Exchange time of order book |
| asks | Best 20 ask levels in order from best to worst |
| bids | Best 20 bid levels in order from best to worst |
| price | Price of bid/ask |
| size | Volume resting on bid/ask level in base amount |

Output example
```json
{
   "timestamp":"2013-09-28T22:40:50.0000000Z",
   "asks":[
      {
         "price":456.35,
         "size":123
      },
      {
         "price":456.36,
         "size":23
      }
   ],
   "bids":[
      {
         "price":456.10,
         "size":42
      },
      {
         "price":456.09,
         "size":5
      }
   ]
}
```

## Balances

### Latest balances

HTTP Request
`GET /v1/account/balances/{exchange_id}/latest`

Parameters

| Parameter | Type | Description |
| :-- | :--: | :-- |
| exchange_id | string | Exchange identifier |

Output

| Variable | Description |
| :-- | :-- |
| currency | |
| balance | |
| available | |

```json
[{
    "currency" : "DOGE",
    "balance" : 0.00000000,
    "available" : 0.00000000
}]
```

## Trading

### Place order

HTTP Request:
`POST /v1/orders/{exchange_id}/{symbol_id}`

Parameters:

| Parameter | Type | Description |
| :-- | :--: | :-- |
| exchange_id | string | Exchange indentifier |
| symbol_id | string | Symbol identifier of the order to place. |
| price | double | Requested order price. |
| quantity | double | Requested order quantity. |
| type | string | Order type ("LIMIT") |
| side | string | Order side ("BUY" or "SELL"). |

Output:

| Variable | Type | Description |
| :-- | :--: | :-- |
| order_id | string | Identifier of the order. |
| symbol_id | string | Symbol identifier of the order. |
| creation_time | timestring | The time the order was placed. |
| price | double | Requested order price. |
| quantity | double | Requested order quantity. |
| executed_quantity | double | Executed quantity. |
| side | string | Order side ("BUY" or "SELL"). |
| type | string | Order type ("LIMIT"; "MARKET" is currently unsupported). |
| status | string | Current status of the order ("NEW", "FILLED", "PARTIALLY_FILLED", "REJECTED", "CANCELED", "PENDING_CANCEL"). |

Output example:
```json
{
    "order_id": "e35f2d59278f4a81a957469df5f3a899",
    "symbol_id": "ADA-BTC-BINANCE",
    "creation_time": "2018-09-18T12:00:00.0000000Z",
    "price": 770.000000000,
    "quantity": 0.10000000,
    "executed_quantity": 0.02000000,
    "side": "BUY",
    "type": "LIMIT",
    "status": "NEW"
}
```

### Cancel order

HTTP Request:
`DELETE /v1/orders/{exchange_id}/{symbol_id}/active/{order_id}`

Parameters:

| Parameter | Type | Description |
| :-- | :--: | :-- |
| exchange_id | string | Exchange identifier |
| symbol_id | string | Symbol identifier |
| order_id | string | Identifier of the order to cancel. |

Output:

| Variable | Type | Description |
| :-- | :--: | :-- |
| order_id | string | Identifier of the order. |
| symbol_id | string | Symbol identifier of the order. |
| creation_time | timestring | The time the order was placed. |
| price | double | Requested order price. |
| quantity | double | Requested order quantity. |
| executed_quantity | double | Executed quantity. |
| side | string | Order side ("BUY" or "SELL"). |
| type | string | Order type ("LIMIT"; "MARKET" is currently unsupported). |
| status | string | Current status of the order ("NEW", "FILLED", "PARTIALLY_FILLED", "REJECTED", "CANCELED", "PENDING_CANCEL"). |

Output example:
```json
{
    "order_id": "e35f2d59278f4a81a957469df5f3a899",
    "symbol_id": "ADA-BTC-BINANCE",
    "creation_time": "2018-09-18T12:00:00.0000000Z",
    "cancellation_time": "2018-09-18T12:10:00.0000000Z",
    "price": 770.000000000,
    "quantity": 0.10000000,
    "executed_quantity": 0.02000000,
    "side": "BUY",
    "type": "LIMIT",
    "status": "PENDING_CANCEL"
}
```

### Get Order

HTTP Request:
`GET /v1/orders/{exchange_id}/{order_id}`

Parameters:

| Parameter | Type | Description |
| :-- | :--: | :-- |
| exchange_id | string | Exchange identifier |
| order_id | string | Identifier of the order to get. |

Output:

| Variable | Type | Description |
| :-- | :--: | :-- |
| order_id | string | Identifier of the order. |
| symbol_id | string | Symbol identifier of the order. |
| creation_time | timestring | The time the order was placed. |
| price | double | Requested order price. |
| quantity | double | Requested order quantity. |
| executed_quantity | double | Executed quantity. |
| side | string | Order side ("BUY" or "SELL"). |
| type | string | Order type ("LIMIT"; "MARKET" is currently unsupported). |
| status | string | Current status of the order ("NEW", "FILLED", "PARTIALLY_FILLED", "REJECTED", "CANCELED", "PENDING_CANCEL"). |

Output example:
```json
{
    "order_id": "e35f2d59278f4a81a957469df5f3a899",
    "symbol_id": "ADA-BTC-BINANCE",
    "creation_time": "2018-09-18T12:00:00.0000000Z",
    "price": 770.000000000,
    "quantity": 0.10000000,
    "executed_quantity": 0.10000000,
    "side": "BUY",
    "type": "LIMIT",
    "status": "FILLED"
}
```

### Get Active Orders

HTTP Request:
`GET /v1/orders/{exchange_id}/active`

Parameters:

| Parameter | Type | Description |
| :-- | :--: | :-- |
| exchange_id | string | Exchange identifier |

Output:

| Variable | Type | Description |
| :-- | :--: | :-- |
| order_id | string | Identifier of the order. |
| symbol_id | string | Symbol identifier of the order. |
| creation_time | timestring | The time the order was placed. |
| price | double | Requested order price. |
| quantity | double | Requested order quantity. |
| executed_quantity | double | Executed quantity. |
| side | string | Order side ("BUY" or "SELL"). |
| type | string | Order type ("LIMIT"; "MARKET" is currently unsupported). |
| status | string | Current status of the order ("NEW", "FILLED", "PARTIALLY_FILLED", "REJECTED", "CANCELED", "PENDING_CANCEL"). |

Output example:
```json
[{
    "order_id": "e35f2d59278f4a81a957469df5f3a899",
    "symbol_id": "ADA-BTC-BINANCE",
    "creation_time": "2018-09-18T12:00:00.0000000Z",
    "price": 770.000000000,
    "quantity": 0.10000000,
    "executed_quantity": 0.02000000,
    "side": "BUY",
    "type": "LIMIT",
    "status": "PARTIALLY_FILLED"
}]
```

### Get Orders History

HTTP Request:
`GET /v1/orders/{exchange_id}/history?time_start={time_start}&time_end={time_end}&limit={limit}`

Parameters:

| Parameter | Type | Description |
| :-- | :--: | :-- |
| exchange_id | string | Exchange identifier |
| time_start | timestring | Order history starting time. |
| time_end | timestring | Order history ending time (optional, if not supplied then the data is returned to the end or when count of result elements reaches the limit). |
| limit | int | Amount of items to return (optional, minimum is 1, maximum is 1000, default value is 100). |

Output:

| Variable | Type | Description |
| :-- | :--: | :-- |
| order_id | string | Identifier of the order. |
| symbol_id | string | Symbol identifier of the order. |
| creation_time | timestring | The time the order was placed. |
| price | double | Requested order price. |
| quantity | double | Requested order quantity. |
| executed_quantity | double | Executed quantity. |
| side | string | Order side ("BUY" or "SELL"). |
| type | string | Order type ("LIMIT"; "MARKET" is currently unsupported). |
| status | string | Current status of the order ("NEW", "FILLED", "PARTIALLY_FILLED", "REJECTED", "CANCELED", "PENDING_CANCEL"). |

Output example:
```json
[{
    "order_id": "e35f2d59278f4a81a957469df5f3a899",
    "symbol_id": "ADA-BTC-BINANCE",
    "creation_time": "2018-09-18T12:00:00.0000000Z",
    "price": 770.000000000,
    "quantity": 0.10000000,
    "executed_quantity": 0.10000000,
    "side": "BUY",
    "type": "LIMIT",
    "status": "FILLED"
}]
```
