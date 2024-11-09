# Data API 

The Drift Data API provides public access to various APIs that Drift uses, offering information about markets, contracts, and tokenomics. This API allows developers and users to retrieve data related to the Drift protocol.

mainnet-beta: [https://data.api.drift.trade/](https://data.api.drift.trade/)
devnet: [https://master-data.drift.trade/](https://master-data.api.drift.trade/)

## Rate Limiting
To ensure fair usage and maintain system stability, the Drift Data API implements rate limiting. Users are restricted to a certain number of requests per minute. The exact limit may vary depending on the endpoint and overall system load. If you exceed the rate limit, you'll receive a 429 (Too Many Requests) response. It's recommended to implement exponential backoff in your applications to handle rate limiting gracefully.

## Caching
The Drift Data API utilizes caching mechanisms to improve performance and reduce server load. Responses may be cached for a short period, typically ranging from a few seconds to a few minutes, depending on the endpoint and the nature of the data. While this ensures quick response times, it also means that there might be a slight delay in reflecting the most recent updates. Time-sensitive operations should account for this potential delay.

## `GET /contracts`

Returns the contract information for each market. Contract information contains funding rate and open interest (oi).

Example: [https://data.api.drift.trade/contracts](https://data.api.drift.trade/contracts)

## `GET /fundingRates`

Returns the last 30 days of funding rates by marketName or marketIndex.

```python
import requests

def get_funding_rates(market_symbol='SOL-PERP'):
    url = f'https://data.api.drift.trade/fundingRates'
    params = {'marketName': market_symbol}

    response = requests.get(url, params=params)
    return response.json()['fundingRates']

# Example usage, print the funding rates for SOL-PERP
market_symbol = 'SOL-PERP'
rates = get_funding_rates(market_symbol)

print(f"Funding Rates for {market_symbol}:")
for rate in rates:
    funding_rate = float(rate['fundingRate']) / 1e9
    # ... any logic here, for example...
    print(f"Slot: {rate['slot']}, Funding Rate: {funding_rate:.9f}")

```
```typescript
interface FundingRate {
  slot: number;
  fundingRate: string;
  // Other fields... (view the response section for the full list)
}

async function getFundingRates(marketSymbol: string = 'SOL-PERP'): Promise<FundingRate[]> {
  const url = `https://data.api.drift.trade/fundingRates?marketName=${marketSymbol}`;

  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    return data.fundingRates;
  } catch (error) {
    console.error('Error fetching funding rates:', error);
    return [];
  }
}

async function main() {
  const marketSymbol = 'SOL-PERP';
  const rates = await getFundingRates(marketSymbol);

  console.log(`Funding Rates for ${marketSymbol}:`);
  rates.forEach(rate => {
    const fundingRate = parseFloat(rate.fundingRate) / 1e9;
    // ... any logic here, for example...
    console.log(`Slot: ${rate.slot}, Funding Rate: ${fundingRate.toFixed(9)}`);
  });
}

main().catch(error => console.error('An error occurred:', error));
```

<aside class="notice">
The funding rate is returned as a string and needs to be divided by 1e9 to get the actual rate.
</aside>


| Parameter        | Description                                      | Optional | Values                                              |
| ---------------- | ------------------------------------------------ | -------- | --------------------------------------------------- |
| marketName or marketIndex               | The market name or index for the perp market	         | NO       |  |

Example: [https://data.api.drift.trade/fundingRates?marketName=SOL-PERP](https://data.api.drift.trade/fundingRates?marketName=SOL-PERP)

### Response
The response is a json object with a `fundingRates` array. Each funding rate entry contains the following fields:

| Field | Type | Description |
|-------|------|-------------|
| `txSig` | string | Transaction signature |
| `slot` | integer | Slot number |
| `ts` | string | Timestamp |
| `recordId` | string | Record identifier |
| `marketIndex` | integer | Market index |
| `fundingRate` | string | Funding rate (divide by 1e9 for actual rate) |
| `cumulativeFundingRateLong` | string | Cumulative funding rate for long positions |
| `cumulativeFundingRateShort` | string | Cumulative funding rate for short positions |
| `oraclePriceTwap` | string | Oracle price time-weighted average price |
| `markPriceTwap` | string | Mark price time-weighted average price |
| `fundingRateLong` | string | Funding rate for long positions |
| `fundingRateShort` | string | Funding rate for short positions |
| `periodRevenue` | string | Revenue for the period |
| `baseAssetAmountWithAmm` | string | Base asset amount with AMM |
| `baseAssetAmountWithUnsettledLp` | string | Base asset amount with unsettled LP |



## `GET /DRIFT/`

Returns the tokenomics information about the Drift token.

| Parameter        | Description                                      | Optional | Values                                              |
| ---------------- | ------------------------------------------------ | -------- | --------------------------------------------------- |
| q                | Metrics related to the drift tokenomics          | NO       | `circulating-supply`,`locked-supply`,`total-supply` |

Example: [https://data.api.drift.trade/DRIFT?q=circulating-supply](https://data.api.drift.trade/DRIFT?q=circulating-supply)

