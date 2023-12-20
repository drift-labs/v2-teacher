# Historical Data

Snapshots are collected by parsing on-chain transaction logs. For convience the below are parsed logs collected, stored as a CSV, and stored off-chain (~99% of records).

Please share any transaction signatures or time ranges you believe might be missing in Drift Protocol Discord.

## URL Prefix
mainnet-beta:  `https://drift-historical-data.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/`

devnet: `https://drift-historical-data.s3.us-east-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/`

## URL Suffix

### Schema
| recordType | url suffix |
| --- | --- |
| trades | `user/${accountKey}/trades/${year}/${month}` |
| market-trades | `market/${marketSymbol}/trades/${year}/${month}/${day}` |
| funding-rates | `market/${marketSymbol}/funding-rates/${year}/${month}` |
| funding-payments | `user/${accountKey}/funding-payments/${year}/${month}` |
| deposits | `user/${accountKey}/deposits/${year}/${month}` |
| liquidations | `user/${accountKey}/liquidations/${year}/${month}` |
| candles | `market/${marketSymbol}/candles/${year}/${month}/resolution/${candleResolution}` |
| settle-pnl-records | `user/${accountKey}/settlePnls/${year}/${month}` |

### Variables
| variable | description | example |
| --- | --- | --- |
| accountKey | user sub account public key (not authority) | |
| marketSymbol | market name | SOL-PERP |
| year |  | 2023 |
| month |  | 4 |
| day | utc time | 25 |
| candleResolution | | 1M |


## Examples

Get historical trades on `SOL-PERP` for August 5, 2023:
```
https://drift-historical-data.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/market/SOL-PERP/trades/2023/8/5
```

## Records Columns

Below are definitions of the columns in each record type.

### trades

| variable | description | example |
| --- | --- | --- |
| accountKey | user sub account public key (not authority) | |

### market-trades

### funding-rates

### funding-payments

### deposits

### liquidations

### candles

### settle-pnl-records


```python
import requests

outcsv = requets.get('https://drift-historical-data.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/user/2dy78vpWpquDgAoLB8w8Ewfns9WXYzQx4CGt3HSgZLEe/trades/2023/4')'
```

```shell
curl https://drift-historical-data.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/user/2dy78vpWpquDgAoLB8w8Ewfns9WXYzQx4CGt3HSgZLEe/trades/2023/4 --output out.csv.gz
```