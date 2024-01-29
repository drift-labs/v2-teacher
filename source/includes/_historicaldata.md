# Historical Data

Snapshots are collected by parsing on-chain transaction logs. For convience the below are parsed logs collected, stored as a CSV, and stored off-chain (~99% of records). Records are updated once per day.

Please share any transaction signatures or time ranges you believe might be missing in Drift Protocol Discord.

## URL Prefix
mainnet-beta:  `https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/`

<!-- devnet: `https://drift-historical-data.s3.us-east-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/` -->

## URL Suffix

### Schema
| recordType | url suffix |
| --- | --- |
| trades | `user/${accountKey}/tradeRecords/${year}/${year}${month}${day}` |
| market-trades | `market/${marketSymbol}/tradeRecords/${year}/${year}${month}${day}` |
| funding-rates | `market/${marketSymbol}/fundingRateRecords/${year}/${year}${month}${day}` |
| funding-payments | `user/${accountKey}/fundingPaymentRecords/${year}/${year}${month}${day}` |
| deposits | `user/${accountKey}/depositRecords/${year}/${year}${month}${day}` |
| liquidations | `user/${accountKey}/liquidationRecords/${year}/${year}${month}${day}` |
| settle-pnl | `user/${accountKey}/settlePnlRecords/${year}/${year}${month}${day}` |
| lp | `user/${accountKey}/lpRecord/${year}/${year}${month}${day}` |
| insurance-fund-stake | `authority/${authorityAccountKey}/insuranceFundStakeRecords/${year}/${year}${month}${day}` |


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
https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/market/SOL-PERP/tradeRecords/2024/20240101
```

<!-- ## Records Columns

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

### settle-pnl-records -->


```python
import requests

outcsv = requets.get('https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/user/FrEFAwxdrzHxgc7S4cuFfsfLmcg8pfbxnkCQW83euyCS/tradeRecords/2023/20230201')'
```

```shell
curl https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/user/FrEFAwxdrzHxgc7S4cuFfsfLmcg8pfbxnkCQW83euyCS/tradeRecords/2023/20230201 --output out.csv.gz
```