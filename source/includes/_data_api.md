# Data API 

The Drift Data API provides public access to various APIs that Drift uses, offering information about markets, contracts, and tokenomics. This API allows developers and users to retrieve data related to the Drift protocol.

mainnet-beta: [https://data.api.drift.trade/](https://data.api.drift.trade/)
devnet: [https://master-data.drift.trade/](https://master-data.api.drift.trade/)

## Rate Limiting
To ensure fair usage and maintain system stability, the Drift Data API implements rate limiting. Users are restricted to a certain number of requests per minute. The exact limit may vary depending on the endpoint and overall system load. If you exceed the rate limit, you'll receive a 429 (Too Many Requests) response. It's recommended to implement exponential backoff in your applications to handle rate limiting gracefully.

## Caching
The Drift Data API utilizes caching mechanisms to improve performance and reduce server load. Responses may be cached for a short period, typically ranging from a few seconds to a few minutes, depending on the endpoint and the nature of the data. While this ensures quick response times, it also means that there might be a slight delay in reflecting the most recent updates. Time-sensitive operations should account for this potential delay.

## `GET /contracts`

Returns the contract information for each market 

Example: [https://data.api.drift.trade/contracts](https://data.api.drift.trade/contracts)


## `GET /DRIFT/`

Returns the tokenomics information about the Drift token.

| Parameter        | Description                                      | Optional | Values    |
| ---------------- | ------------------------------------------------ | -------- | ------------------------------------------------ |
| q                | Metrics related to the drift tokenomics          | NO       | circulating-supply | locked-supply | total-supply   |

Example: [https://data.api.drift.trade/DRIFT?q=circulating-supply](https://data.api.drift.trade/DRIFT?q=circulating-supply)


