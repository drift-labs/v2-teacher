# Historical Data

mainnet-beta url prefix: 'https://drift-historical-data.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/'

devnet url prefix: 'https://drift-historical-data.s3.us-east-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/'

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



| variable | description | example |
| --- | --- | --- |
| accountKey | user sub account public key (not authority) | |
| marketSymbol | market name | SOL-PERP |
| year |  | 2023 |
| month |  | 4 |
| day | utc time | 25 | 
| candleResolution | | 1M |
