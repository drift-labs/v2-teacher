
# API Endpoints

## Contracts
Contracts endpoint gives a high level overview of all the markets available in the protocol.

### HTTP Request
`GET https://mainnet-beta.api.drift.trade/contracts`


```python
import requests

contracts = requests.get('https://mainnet-beta.api.drift.trade/contracts')
contracts.json()
```

```shell
curl "https://mainnet-beta.api.drift.trade/contracts"
```

```typescript
import fetch from "node-fetch";
const resp = await fetch(
            "https://mainnet-beta.api.drift.trade/contracts",
            {
                method: "GET",
                headers: {
                    Accept: "application/json",
                },
            }
        );
const result = await resp.json();
```

> The above command returns JSON structured like this:

```json
{"contracts":
  [
     {
      "contract_index":0,
      "ticker_id":"SOL-PERP",
      "base_currency":"SOL",
      "quote_currency":"USDC",
      "last_price":"21.881487",
      "base_volume":"110593.000000000",
      "quote_volume":"1788085.901185",
      "high":"23.852900",
      "low":"21.759228",
      "product_type":"PERP",
      "open_interest":"119147.600000000",
      "index_price":"21.887634",
      "index_name":"SOL",
      "index_currency":"USDC",
      "start_timestamp":"1667560505000",
      "end_timestamp":"1682956473378",
      "funding_rate":"0.003867637",
      "next_funding_rate":"0.00124",
      "next_funding_rate_timestamp":"1682956805000"
      },
      ...
  ]
}
```

### Response Values

Value | Description
--------- | -----------
ticker_id | market name
last_price | last trade price
product_type | either PERP or SPOT
open_interest | outstanding contracts opened in market, denominated in base currency
index_price | referenced oracle price for market, used for trigger orders
next_funding_rate | next predicted funding rate (in %) (based on oracle and market twap difference)
next_funding_rate_timestamp | time of next funding rate


This endpoint retrieves all contracts available within the protocol.