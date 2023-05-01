---
title: protocol-v2 API

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - typescript
  - python
  - https

toc_footers:
  - <a href='https://github.com/drift-labs/protocol-v2/releases/'> release history </a>
  - <a href='https://docs.drift.trade/'> Read more about Drift Protocol in Archbee</a>
includes:
  - historicaldata
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for drift protocol-v2
---

# Introduction

Drift Protocol is an open-sourced, decentralised exchange built on the Solana blockchain, enabling transparent and non-custodial trading on cryptocurrencies.

There are language bindings in [Typescript](https://github.com/drift-labs/protocol-v2/tree/master/sdk) and [Python](https://github.com/drift-labs/driftpy)! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This API documentation page is open sourced and available [here](https://github.com/drift-labs/v2-teacher) was created with [Slate](https://github.com/slatedocs/slate). Feel free to submit questions/comments in [Issues](https://github.com/drift-labs/v2-teacher/issues) or suggest changes as a [PR](https://github.com/drift-labs/v2-teacher/pulls).


## Program Addresses

<aside class="notice">
  All documented descriptions are for the following deployed programs IDs of the protocol.
</aside>

Enviroment | Program ID 
--------- | ------- |
mainnet-beta | [dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH](https://solscan.io/account/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH)
devnet | [dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH](https://solscan.io/account/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH?cluster=devnet) 


# Authentication
To access and interact with a blockchain, such as Solana, you need a keypair, which consists of a public key and a private key. The private key should be kept secure and not shared with anyone else.

To generate a new keypair, you can use the [Solana Tool Suite](https://docs.solana.com/cli/install-solana-cli-tools) by running the following command on the command line:

```typescript
solana-keygen new --outfile ~/.config/solana/my-keypair.json
```

This command will create a new keypair and store it in a JSON file located at ~/.config/solana/my-keypair.json.

To allow SDK code to use this keypair for authentication, you need to set the ANCHOR_WALLET environment variable to the path of the JSON file that contains the keypair. You can do this by running the following command:

```typescript
export ANCHOR_WALLET=~/.config/solana/my-keypair.json
```

This command sets the ANCHOR_WALLET environment variable to the path of the JSON file that contains your keypair, so that SDK code can access it when needed.

<aside class="notice">
  You must replace <code>~/.config/solana/my-keypair.json</code> with your personal key.
</aside>

# Client

## Typescript
Install @drift-labs/sdk from [npm](https://www.npmjs.com/package/@drift-labs/sdk) using yarn:

`yarn add @drift-labs/sdk`

auto-generated documentation here: https://drift-labs.github.io/protocol-v2/sdk/

## Python
Install driftpy from PyPI using pip:

`pip install driftpy`

auto-generated documentation here: https://drift-labs.github.io/driftpy/

## Client Initialization

```typescript
  import {DriftClient} from "@drift-labs/sdk";
```
```python
  import driftpy;
```

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

```https
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