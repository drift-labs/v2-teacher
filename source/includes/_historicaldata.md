# Historical Data

## NOTE: S3 flat files are deprecated and have stopped updating in January 2025. Please use the [Data API](https://drift-labs.github.io/v2-teacher/?typescript#data-api) going forward.

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
| swaps | `user/${accountKey}/swapRecords/${year}/${year}${month}${day}` |
| funding-rates | `market/${marketSymbol}/fundingRateRecords/${year}/${year}${month}${day}` |
| funding-payments | `user/${accountKey}/fundingPaymentRecords/${year}/${year}${month}${day}` |
| deposits | `user/${accountKey}/depositRecords/${year}/${year}${month}${day}` |
| liquidations | `user/${accountKey}/liquidationRecords/${year}/${year}${month}${day}` |
| settle-pnl | `user/${accountKey}/settlePnlRecords/${year}/${year}${month}${day}` |
| lp | `user/${accountKey}/lpRecord/${year}/${year}${month}${day}` |
| insurance-fund | `market/${marketSymbol}/insuranceFundRecords/${year}/${year}${month}${day}` |
| insurance-fund-stake | `authority/${authorityAccountKey}/insuranceFundStakeRecords/${year}/${year}${month}${day}` |
| candle-history | `candle-history/{year}/{marketKey}/{candleResolution}.csv` | 



### Variables
| variable | description | example |
| --- | --- | --- |
| accountKey | user sub account public key (not authority) | |
| authority | authority public key | |
| marketSymbol | market name. E.g. `SOL-PERP` for Solana PERP or `SOL` for Solana SPOT | SOL-PERP |
| year |  | 2023 |
| month |  | 4 |
| day | utc time | 25 |
| marketKey | The key for the market. Format: {marketType}_{marketIndex} | perp_0 |
| candleResolution | Candle Resolution. See "Available Candle Resolutions" below | M |

### Available Candle Resolutions:

| resolution | description |
| --- | --- |
| 1 | 1 minute |
| 15 | 15 minute |
| 60 | 1 hr |
| 240 | 4 hr |
| D | 1 day |
| W | 1 week |

## Example: Trades for market
```python
import requests


URL_PREFIX = 'https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH'

market_symbol = 'SOL-PERP'
year = '2024'
month = '01'
day = '01'

# Method 1: using pandas (this is the easiest and fastest way)
import pandas as pd
df = pd.read_csv(f'{URL_PREFIX}/market/{market_symbol}/tradeRecords/{year}/{year}{month}{day}')
print(df)

# Method 2: using csv reader
import csv
from io import StringIO

response = requests.get(f'{URL_PREFIX}/market/{market_symbol}/tradeRecords/{year}/{year}{month}{day}')
response.raise_for_status()

csv_data = StringIO(response.text)
reader = csv.reader(csv_data)
for row in reader:
    print(row)

```

```typescript
import { parse } from 'csv-parse/sync';

const URL_PREFIX = 'https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH';

async function fetchAndParseCSV(marketSymbol: string, year: string, month: string, day: string): Promise<void> {
  const url = `${URL_PREFIX}/market/${marketSymbol}/tradeRecords/${year}/${year}${month}${day}`;

  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const csvText = await response.text();
    const records = parse(csvText, {
      skip_empty_lines: true
    });

    records.forEach((row: string[]) => {
      console.log(row);
    });
  } catch (error) {
    console.error('Error fetching or parsing CSV:', error);
  }
}

async function main() {
  const marketSymbol = 'SOL-PERP';
  const year = '2024';
  const month = '01';
  const day = '01';

  await fetchAndParseCSV(marketSymbol, year, month, day);
}

main().catch(error => console.error('An error occurred:', error));
```

Get historical trades on `SOL-PERP` for a given date (ex. 2024-01-01):
```
https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/market/SOL-PERP/tradeRecords/2024/20240101
```


## Example: Trades for date range
```python
import requests
import csv
from io import StringIO
from datetime import date, timedelta
import pandas as pd

URL_PREFIX = 'https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH'

# Method 1: using pandas
def get_trades_for_range_pandas(account_key, start_date, end_date):
    all_trades = []
    current_date = start_date
    while current_date <= end_date:
        year = current_date.year
        month = current_date.month
        day = current_date.day
        url = f"{URL_PREFIX}/user/{account_key}/tradeRecords/{year}/{year}{month:02}{day:02}"
        
        try:
            df = pd.read_csv(url)
            all_trades.append(df)
        except requests.exceptions.RequestException as e:
            print(f"Error fetching data for {current_date}: {e}")
        except pd.errors.EmptyDataError:
            print(f"No data available for {current_date}")
        
        current_date += timedelta(days=1)
    
    if all_trades:
        return pd.concat(all_trades, ignore_index=True)
    else:
        return pd.DataFrame()

# Method 2: using csv reader
def get_trades_for_range_csv(account_key, start_date, end_date):
    all_trades = []
    current_date = start_date
    while current_date <= end_date:
        year = current_date.year
        month = current_date.month
        day = current_date.day
        url = f"{URL_PREFIX}/user/{account_key}/tradeRecords/{year}/{year}{month:02}{day:02}"
        response = requests.get(url)
        response.raise_for_status()

        csv_data = StringIO(response.text)
        reader = csv.reader(csv_data)
        for row in reader:
            all_trades.append(row)

        current_date += timedelta(days=1)

    return all_trades


# Example usage
account_key = "<Some Account Key>"
start_date = date(2024, 1, 24)
end_date = date(2024, 1, 26)

trades = get_trades_for_range(account_key, start_date, end_date)
```

```typescript
import { parse } from 'csv-parse/sync';

const URL_PREFIX = 'https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH';

interface Trade {
  // Define the structure of a trade record here
  // For example:
  // date: string;
  // amount: number;
  // price: number;
  [key: string]: any;
}

async function getTradesForRange(accountKey: string, startDate: Date, endDate: Date): Promise<Trade[]> {
  const allTrades: Trade[] = [];
  let currentDate = new Date(startDate);

  while (currentDate <= endDate) {
    const year = currentDate.getFullYear();
    const month = (currentDate.getMonth() + 1).toString().padStart(2, '0');
    const day = currentDate.getDate().toString().padStart(2, '0');
    
    const url = `${URL_PREFIX}/user/${accountKey}/tradeRecords/${year}/${year}${month}${day}`;

    try {
      const response = await fetch(url);
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      
      const csvText = await response.text();
      const records = parse(csvText, {
        skip_empty_lines: true,
        columns: true
      });

      allTrades.push(...records);
    } catch (error) {
      console.error(`Error fetching or parsing CSV for ${year}-${month}-${day}:`, error);
    }

    currentDate.setDate(currentDate.getDate() + 1);
  }

  return allTrades;
}

async function main() {
  const accountKey = "<Some Account Key>";
  const startDate = new Date(2024, 0, 24); // Note: month is 0-indexed in JavaScript
  const endDate = new Date(2024, 0, 26);

  try {
    const trades = await getTradesForRange(accountKey, startDate, endDate);
    console.log(`Total trades fetched: ${trades.length}`);
    console.log('First few trades:', trades.slice(0, 5));
  } catch (error) {
    console.error('An error occurred:', error);
  }
}

main();
```

> Note: To speed this up, you could download the data in parallel (Promise.all or asyncio).

We can write a script to download all the data, one by one, for a given account in a given date range.

## Records Columns

Below are definitions of the columns in each record type.

### trades

| variable | description | example |
| --- | --- | --- |
| accountKey | user sub account public key (not authority) | |

### funding-rates

note: 'rate' is in quote per base, to allow for async settlement

| variable | description | example |
| --- | --- | --- |
| fundingRate | the quote asset amount (precision=1e6) per base asset amount (precision=1e9) | |

to convert to the rates seen on the ui, use the following formula: `(funding_rate / BASE_PRECISION) / (oracle_twap / QUOTE_PRECISION)  * 100`

<!-- ### market-trades

### funding-payments

### deposits

### liquidations

### candles

### settle-pnl-records -->


```python
import requests

outcsv = requests.get('https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/user/FrEFAwxdrzHxgc7S4cuFfsfLmcg8pfbxnkCQW83euyCS/tradeRecords/2023/20230201')'
```

```shell
curl https://drift-historical-data-v2.s3.eu-west-1.amazonaws.com/program/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH/user/FrEFAwxdrzHxgc7S4cuFfsfLmcg8pfbxnkCQW83euyCS/tradeRecords/2023/20230201 --output out.csv.gz
```
