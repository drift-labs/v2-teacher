---
title: protocol-v2 API

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - typescript
  - python
  - shell: http

toc_footers:
  - <a href='https://github.com/drift-labs/protocol-v2/releases/'> Release History </a>
  - <a href='https://docs.drift.trade/'> Documentation </a>
includes:
  - examples
  - orderbook_blockchain
  - orderbook_dlobserver
  - data_api
  - historicaldata
  - errors
  - market_constants
  - prediction_markets

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for drift protocol-v2
---


# Terms of Use
By using any API provided by Drift Labs, you agree to the <a href="https://docs.drift.trade/legal-and-regulations/terms-of-use">Terms of Use</a>. If you do not agree to the foregoing, then do not use any such API.

# Introduction

Drift Protocol is an [open-sourced](https://github.com/drift-labs/protocol-v2/), decentralised exchange built on the [Solana](https://solana.com) blockchain, enabling transparent and non-custodial trading on cryptocurrencies.

There are language bindings in [Typescript](https://github.com/drift-labs/protocol-v2/tree/master/sdk) and [Python](https://github.com/drift-labs/driftpy)! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This API documentation page is open sourced and available [here](https://github.com/drift-labs/v2-teacher) was created with [Slate](https://github.com/slatedocs/slate). Feel free to submit questions/comments in [Issues](https://github.com/drift-labs/v2-teacher/issues) or suggest changes as a [PR](https://github.com/drift-labs/v2-teacher/pulls).


## Program Addresses

<aside class="notice">
  All documented descriptions are for the following deployed programs IDs of the protocol.
</aside>

Enviroment | Program ID | User Interface |
---------- | ----------- | -------- |
mainnet-beta | [dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH](https://solscan.io/account/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH) | [app](https://app.drift.trade) |
devnet | [dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH](https://solscan.io/account/dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH?cluster=devnet) | [app](https://beta.drift.trade) |

<aside class="warning">
  It is strictly against the <a href="https://docs.drift.trade/legal-and-regulations/terms-of-use">Terms of Use</a> to use these interfaces from a Restricted Territory.
</aside>



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

Auto-generated documentation <a href="https://drift-labs.github.io/protocol-v2/sdk/">here</a>

<aside class="notice">
  Latest TS dependencies can be found on GitHub <a href="https://github.com/drift-labs/protocol-v2/blob/master/sdk/package.json">here</a>
</aside>


## Python
Install driftpy from PyPI using pip:

`pip install driftpy`

Auto-generated documentation <a href="https://drift-labs.github.io/driftpy/">here</a>

<aside class="notice">
  Latest Python dependencies can be found on GitHub <a href="https://github.com/drift-labs/driftpy/blob/master/pyproject.toml">here</a>

</aside>

## HTTP
Use the self-hosted HTTP API [gateway](https://github.com/drift-labs/gateway)

## Connection

  ```typescript
import {Connection} from "@solana/web3.js";

// the default RPC for devnet is `https://api.devnet.solana.com`
const connection = new Connection('https://api.mainnet-beta.solana.com', 'confirmed');
```

```python
from solana.rpc.async_api import AsyncClient

url = 'https://api.mainnet-beta.solana.com' # replace w/ any rpc
connection = AsyncClient(url)
```

```shell
drift-gateway https://api.mainnet-beta.solana.com --port 8080
```

The connection object is used to send transactions to the Solana blockchain. It is used by the DriftClient to send transactions to the blockchain.


## Wallet

  ```typescript
import {Wallet, loadKeypair} from "@drift-labs/sdk";

const keyPairFile = `${process.env.HOME}/.config/solana/my-keypair.json`;
const wallet = new Wallet(loadKeypair(keyPairFile));
```
```python
import os
from anchorpy import Wallet
from driftpy.keypair import load_keypair

keypair_file = os.path.expanduser('~/.config/solana/my-keypair.json')
keypair = load_keypair(keypair_file)
wallet = Wallet(keypair)
```
```shell
# use `DRIFT_GATEWAY_KEY` environment variable to configure the gateway wallet
# either path to .json keypair or base58 seed string
export DRIFT_GATEWAY_KEY="</path/to/my-keypair.json|<KEYPAIR_BASE58_SEED>"
```

The wallet used to sign solana transactions. The wallet can be created from a private key or from a keypair file.

Make sure this wallet has some SOL first. SOL is used to pay for transactions and is required as rent for account intializations.

## Client Initialization

```typescript
import {Connection} from "@solana/web3.js";
import {Wallet, loadKeypair, DriftClient} from "@drift-labs/sdk";

const connection = new Connection('https://api.mainnet-beta.solana.com', 'confirmed');

const keyPairFile = `${process.env.HOME}/.config/solana/my-keypair.json`;
const wallet = new Wallet(loadKeypair(keyPairFile))

const driftClient = new DriftClient({
  connection,
  wallet,
  env: 'mainnet-beta',
});

await driftClient.subscribe();
```
```python
  from anchorpy import Wallet
  from driftpy.drift_client import DriftClient
  from solana.rpc.async_api import AsyncClient

  # set connection and wallet
  # ...
  drift_client = DriftClient(connection, wallet, "mainnet")
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| connection      | Connection object specifying solana rpc url       | No | |
| wallet   | The wallet used to sign transactions sent to solana blockchain       | No | |
| programId   | Drift program id      | Yes | dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH |
| env   | `devnet` or `mainnet-beta`. Used to automatically derive market accounts to subscribe to if they're not explicitly set | Yes | |
| perpMarketIndexes   | Which perp markets accounts to subscribe to. | Yes | Derived based on env|
| spotMarketIndexes   | Which spot markets accounts to subscribe to. | Yes | Derived based on env|
| oracleInfos   | Which oracles accounts to subscribe to. | Yes | Derived based on env|
| accountSubscription   | Whether to use websocket or polling to subscribe to on-chain accounts e.g. markets, users, oracle.| Yes | Websockets |
| opts   | Transaction confirmation status options | Yes | {preflightCommitment: "processed", commitment: "processed"} |
| activeSubAccountId   | Which sub account to use initially | Yes | 0 |
| subAccountIds   | All the sub account ids to subscribe to. If this and authoritySubAccountMap are empty, subscribes to all sub account ids. | Yes | [] |
| authority   | Which user account authority you're signing for. Only set if you're signing for delegated account. | Yes | wallet.publicKey |
| authoritySubAccountMap   | Map of authority to sub account ids to subscribe to. Only necessary if using multiple delegate accounts. If this and subAccountIds are empty, subscribes to all sub account ids. | Yes | {} |
| includeDelegates   | Whether or not to subscribe to delegates when subAccountIds and authoritySubAccountMap are empty | Yes | false |
| userStats   | Whether or not to listen subscribe to user stats account. | Yes | false |


### Python
| Parameter             | Description                                                                                     | Optional | Default                         |
| --------------------- | ----------------------------------------------------------------------------------------------- | -------- | ------------------------------- |
| connection            | AsyncClient connection to the Solana cluster                                                    | No       |                                 |
| wallet                | Wallet object, can be Keypair or Wallet type                                                    | No       |                                 |
| env                   | Drift environment, 'devnet' or 'mainnet'                                                        | Yes      | "mainnet"                       |
| program_id            | Drift program identifier                                                                        | Yes      | DRIFT_PROGRAM_ID                |
| opts                  | Options for transaction confirmation status                                                     | Yes      | DEFAULT_TX_OPTIONS              |
| authority             | Public key of the user account authority                                                        | Yes      | None                            |
| account_subscription  | Configuration for account subscriptions (websockets/polling)                                    | Yes      | AccountSubscriptionConfig.default() |
| perp_market_indexes   | List of perpetual market indexes to interact with                                               | Yes      | None                            |
| spot_market_indexes   | List of spot market indexes to interact with                                                    | Yes      | None                            |
| oracle_infos          | List of OracleInfo objects for market data                                                      | Yes      | None                            |
| tx_params             | Additional parameters for transactions                                                          | Yes      | None                            |
| tx_version            | Version of the transaction                                                                      | Yes      | None                            |
| tx_sender             | Object handling the sending of transactions                                                     | Yes      | None                            |
| active_sub_account_id | ID of the initially active sub-account                                                          | Yes      | None                            |
| sub_account_ids       | List of sub-account IDs to subscribe to                                                         | Yes      | None                            |
| market_lookup_table   | Public key for the market lookup table                                                          | Yes      | None                            |



  When subscribing to the client for delegate accounts, you have to be explicit about the following parameters:
```
subAccountIds: [SUBACCOUNT_ID],
activeSubAccountId: SUBACCOUNT_ID,
authority: TARGET_AUTHORITY,
```


## User Initialization

<aside class="notice">
  initializing a new drift user account requires some rent (~.035 SOL). this can be reclaimed upon deletion.
</aside>
<aside class="notice">
  Alternatively you can export the private key of an existing user that has been initialized via the UI. This will allow you to use the same user account programatically.
</aside>

```typescript
const [txSig, userPublickKey] = await driftClient.initializeUserAccount(
  0,
  "toly"
);
```

```python
tx_sig = await drift_client.initialize_user(sub_account_id=0, name="toly")
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| subAccountId | The sub account id for the new user account.  | Yes | 0 |
| name   | Display name for the user account    | Yes | Main Account |
| referrerInfo   | The address of the referrer and referrer stats accounts | Yes | |

### Python

| Parameter     | Description                              | Optional | Default |
| ------------- | ---------------------------------------- | -------- | ------- |
| sub_account_id| The sub account id for the new user account.         | Yes      | 0       |
| name          | Display name for the user account            | Yes      | None    |
| referrer_info | The address of the referrer and referrer stats accounts  | Yes      | None    |


Sub account ids are monotonic. The first user account created will have sub account id 0, the second will have sub account id 1, etc.
The next sub account id can be found by calling `driftClient.getNextSubAccountId()` in TypeScript.

## Updating User
Users accounts can update names, set custom max intial margin ratio, enable margin trading, and add a delegate account.

```typescript
const subaccountId = 0;

// set max 1x intiial leverage
await driftClient.updateUserCustomMarginRatio([
    {
        'marginRatio': MARGIN_PRECISION,
        'subAccountId': subaccountId
    }
]);

// enable spot margin trading
await driftClient.updateUserMarginTradingEnabled(
  true,
  subaccountId
);

// add a delegate this user account
await driftClient.updateUserDelegate(
  new PublicKey('satoshi'),
  subaccountId
);
```

## Switching Sub Accounts
```typescript
driftClient.switchActiveUser(
  1,
);
```

```python
drift_client.switch_active_user(sub_account_id=1)
```

```shell
curl http://localhost:8080/v2/<RESOURCE>?subAccountId=1
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| subAccountId | The sub account to switch to  | No | 0 |
| authority   | The authority of the sub account you're signing for. Only needed for delegate accounts | Yes | Current authority |

### Python
| Parameter     | Description                                   | Optional | Default |
| ------------- | --------------------------------------------- | -------- | ------- |
| sub_account_id| Identifier for the sub-account to be activated| No       |         |


## Deleting User Account
If an account contains no assets or liabilites, a user account can be deleted to reclaim rent.

```typescript
driftClient.deleteUser(
  1,
);
```

## Depositing

```typescript
const marketIndex = 0; // USDC
const amount = driftClient.convertToSpotPrecision(marketIndex, 100); // $100
const associatedTokenAccount = await driftClient.getAssociatedTokenAccount(marketIndex);

await driftClient.deposit(
  amount,
  marketIndex,
  associatedTokenAccount,
);
```

```python

spot_market_index = 0 # USDC
amount = drift_client.convert_to_spot_precision(100, spot_market_index) # $100

tx_sig = await drift_client.deposit(amount, spot_market_index)
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| amount | The amount to deposit in spot market's token mint precision  | No | |
| marketIndex   | The spot market index you're depositing into | No | |
| associatedTokenAccount   | The public key of the token account you're depositing from. For sol, it can be the wallet's public key | No | |
| subAccountId | The sub account you're depositing to  | Yes | active sub account |
| reduceOnly | Whether the deposit should only reduce borrow  | Yes | false |


### Python
| Parameter           | Description                                                                 | Optional | Default               |
| ------------------- | --------------------------------------------------------------------------- | -------- | --------------------- |
| amount              | The amount to deposit in the spot market's token mint precision             | No       |                       |
| spot_market_index   | The index of the spot market where the deposit is made                      | No       |                       |
| user_token_account  | The public key of the token account from which you are depositing           | Yes      | None (will derive ATA)|
| sub_account_id      | The sub account to which the deposit is being made                          | Yes      | Active sub-account    |
| reduce_only         | Whether the deposit should only reduce borrow                               | Yes      | false                 |
| user_initialized    | Indicates if the user is already initialized (used internally, typically)   | Yes      | true                  |


## Withdrawing

```typescript
const marketIndex = 0;
const amount = driftClient.convertToSpotPrecision(marketIndex, 100);
const associatedTokenAccount = await driftClient.getAssociatedTokenAccount(marketIndex);

await driftClient.withdraw(
  amount,
  marketIndex,
  associatedTokenAccount,
);
```

```python

market_index = 0 # USDC
amount = drift_client.convert_to_spot_precision(100, market_index) # $100

# derive user ata to withdraw to
# ...

tx_sig = await drift_client.withdraw(amount, spot_market_index, ata_to_withdraw_to)
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| amount | The amount to withdraw in spot market's token mint precision  | No | |
| marketIndex   | The spot market index you're withdrawing from | No | |
| associatedTokenAccount   | The public key of the token account you're withdrawing to. For sol, it can be the wallet's public key | No | |
| reduceOnly | Whether the withdraw should only decrease a deposit and block a new borrow | Yes | false |

### Python
| Parameter           | Description                                                                 | Optional | Default |
| ------------------- | --------------------------------------------------------------------------- | -------- | ------- |
| amount              | The amount to withdraw in the spot market's token mint precision             | No       |         |
| market_index        | The index of the spot market from which the withdrawal is made               | No       |         |
| user_token_account  | The public key of the token account to which you are withdrawing             | No       |         |
| reduce_only         | Whether the withdrawal should only decrease a deposit, blocking new borrows | Yes      | false   |
| sub_account_id      | The sub account from which the withdrawal is made                            | Yes      | None    |


Withdrawing can lead to a borrow if the user has no deposits in the market and the user has enough margin to cover it.

## Transferring Deposits

```typescript
const marketIndex = 0;
const amount = driftClient.convertToSpotPrecision(marketIndex, 100);
const fromSubAccountId = 0;
const toSubAccountId = 1;

await driftClient.transferDeposit(
  amount,
  marketIndex,
  fromSubAccountId,
  toSubAccountId,
);
```

```python
market_index = 0
<<<<<<< HEAD
amount = drift_client.convert_to_spot_precision(100, market_index)
=======
amount = drift_client.convert_to_spot_precision(market_index, 100)
>>>>>>> upstream/main
from_sub_account_id = 0
to_sub_account_id = 0

await drift_client.transfer_deposit(
  amount,
  market_index,
  from_sub_account_id,
  to_sub_account_id,
)
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| amount | The amount to transfer in spot market's token mint precision  | No | |
| marketIndex   | The spot market index you're transferring deposits in | No | |
| fromSubAccountId | The sub account you're withdrawing from  | No | |
| toSubAccountId | The sub account you're depositing too  | No | |

### Python
| Parameter           | Description                                           | Optional | Default |
| ------------------- | ----------------------------------------------------- | -------- | ------- |
| amount              | The amount to transfer in the spot market's token mint precision | No | |
| market_index        | The spot market index you're transferring deposits in | No | |
| from_sub_account_id | The sub account from which the funds are withdrawn    | No | |
| to_sub_account_id   | The sub account to which the funds are deposited      | No | |

## Order Types

### TypeScript
MARKET, LIMIT, ORACLE orders all support auction parameters.

| Type	| Description |
| ----- | ----------- |
| MARKET |	Market order. |
| LIMIT |	Limit order. |
| TRIGGER_MARKET |	Stop / Take-profit market order. |
| TRIGGER_LIMIT |	 Stop / Take-profit limit order. |
| ORACLE	| Market order using oracle offset for auction parameters. |

### Python
Market(), Limit(), Oracle() orders all support auction parameters.

| Type	| Description |
| ----- | ----------- |
| Market() |	Market order. |
| Limit() |	Limit order. |
| TriggerMarket() |	Stop / Take-profit market order. |
| TriggerLimit() |	 Stop / Take-profit limit order. |
| Oracle()	| Market order using oracle offset for auction parameters. |

## Order Params

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderType | The type of order e.g. market, limit  | No | |
| marketIndex | The market to place order in  | No | |
| direction | The direction of order e.g. long (bid) or short (ask)  | No | |
| baseAssetAmount | The amount of base asset to buy or sell  | No | |
| marketType | The type of market order is for e.g. PERP or SPOT  | Yes | Depends on method |
| price | The limit price for order | Yes | 0 |
| userOrderId | Unique order id specified by user| Yes | 0 |
| reduceOnly | If the order can only reduce positions| Yes | false |
| postOnly | If the order can only be a maker | PostOnlyParam | None |
| triggerPrice | at what price order is triggered. only applicable for triggerMarket and triggerLimit orders | Yes | |
| triggerCondition | whether order is triggered above or below triggerPrice. only applicable for triggerMarket and triggerLimit orders | Yes | |
| oraclePriceOffset | priceOffset for oracle derived limit price. only applicable for limit and oracle orders  | Yes | |
| auctionDuration | how many slots the auction lasts. only applicable for market and oracle orders | Yes | |
| auctionStartPrice | the price the auction starts at | Yes | |
| auctionEndPrice | the price the auction ends at | Yes | |
| maxTs | the max timestamp (on-chain unix timestamp) before the order expires | Yes | |

### Python
| Parameter           | Description                                           | Optional | Default                         |
| ------------------- | ----------------------------------------------------- | -------- | ------------------------------- |
| order_type          | Type of order                   | No       |                                 |
| market_index        | Index of the market to place order in                 | No       |                                 |
| direction           | Direction of the order         | No       |                                 |
| base_asset_amount   | Amount of base asset to buy or sell                   | No       |                                 |
| market_type         | Type of market                    | Yes      | Depends on method               |
| price               | Limit price for the order                             | Yes      | 0                               |
| user_order_id       | Unique order ID specified by user                     | Yes      | 0                               |
| reduce_only         | Whether the order is only to reduce positions         | Yes      | false                           |
| post_only           | If the order should only be a maker                   | Yes      | PostOnlyParams.NONE()             |
| immediate_or_cancel | Whether the order is immediate or cancel              | Yes      | false                           |
| max_ts              | Max timestamp for the order expiry                    | Yes      | None                            |
| trigger_price       | Trigger price for trigger orders | Yes  | None                            |
| trigger_condition   | Condition for triggering the order                    | Yes      | OrderTriggerCondition.Above()     |
| oracle_price_offset | Offset for oracle-derived limit price                 | Yes      | None                            |
| auction_duration    | Duration of the auction in slots                      | Yes      | None                            |
| auction_start_price | Starting price of the auction                         | Yes      | None                            |
| auction_end_price   | Ending price of the auction                           | Yes      | None                            |


## Post Only Params

Drift orderbook is not a strict clob that enforces price-time priority. This is to maximize the parallelization of placing orders to
take advantage of the solana runtime. To force an order to always be a maker, users most set the post only params. If a user order is set to post only,
drift will check that an order does not cross the vamm spread, similar to how a traditional clob would check that an order doesn't cross the book's best bid/ask.
If the post only is not used, a limit order can end up being a taker or maker.


| Parameter   | Description |
| ----------- | ----------- |
| None | Does not enforce being maker |
| MustPostOnly | Tx fails if order crosses the vamm |
| TryPostOnly | Order is skipped (not placed) and tx succeeds if order crosses the vamm |
| Slide | Order price is modified to be one tick below/above the vamm ask/bid |

## Placing Perp Order

```typescript

// market buy for 100 SOL-PERP @ $21.20->$21.30 over 60 slots (~30 seconds)
// after 60 slots, market buy 100 SOL-PERP @ $21.35 until maxTs
const orderParams = {
  orderType: OrderType.MARKET,
  marketIndex: 0,
  direction: PositionDirection.LONG,
  baseAssetAmount: driftClient.convertToPerpPrecision(100),
  auctionStartPrice: driftClient.convertToPricePrecision(21.20),
  auctionEndPrice: driftClient.convertToPricePrecision(21.30),
  price: driftClient.convertToPricePrecision(21.35),
  auctionDuration: 60,
  maxTs: now + 100,
}
await driftClient.placePerpOrder(orderParams);

// bid for 100 SOL-PERP @ $21.23
const orderParams = {
  orderType: OrderType.LIMIT,
  marketIndex: 0,
  direction: PositionDirection.LONG,
  baseAssetAmount: driftClient.convertToPerpPrecision(100),
  price: driftClient.convertToPricePrecision(21.23),
}
await driftClient.placePerpOrder(orderParams);

// ask for 100 SOL-PERP @ ${OraclePrice} + .05
const orderParams = {
  orderType: OrderType.LIMIT,
  marketIndex: 0,
  direction: PositionDirection.SHORT,
  baseAssetAmount: driftClient.convertToPerpPrecision(100),
  oraclePriceOffset: driftClient.convertToPricePrecision(.05).toNumber(),
}
await driftClient.placePerpOrder(orderParams);
```

```python
from driftpy.types import *
from driftpy.constants.numeric_constants import BASE_PRECISION, PRICE_PRECISION

market_index = 0

# place order to long 1 SOL-PERP @ $21.88 (post only)
order_params = OrderParams(
            order_type=OrderType.Limit(),
            base_asset_amount=drift_client.convert_to_perp_precision(1),
            market_index=market_index,
            direction=PositionDirection.Long(),
            price=drift_client.convert_to_price_precision(21.88),
            post_only=PostOnlyParams.TryPostOnly(),
        )
await drift_client.place_perp_order(order_params)
```

```shell
curl -X POST -H 'content-type: application/json' \
  -d '{
    "orders": [{
        "marketIndex": 0,
        "marketType": "perp",
        "amount": 1.23,
        "price": 80.0,
        "postOnly": true,
        "orderType": "limit",
        "immediateOrCancel": false,
        "reduceOnly": false
    }]
   }' \
localhost:8080/v2/orders
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderParams | The order params  | No | |


### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| order_params | The order params  | No | |

The order type is set to PERP / Perp() by default.

## Placing Spot Order

```typescript

const orderParams = {
  orderType: OrderType.LIMIT,
  marketIndex: 1,
  direction: PositionDirection.LONG,
  baseAssetAmount: driftClient.convertToSpotPrecision(1, 100),
  price: driftClient.convertToPricePrecision(100),
}

await driftClient.placeSpotOrder(orderParams);
```

```python

market_index = 1

order_params = OrderParams(
            order_type=OrderType.Limit(),
            base_asset_amount=drift_client.convert_to_spot_precision(100, market_index),
            market_index=market_index,
            direction=PositionDirection.Long(),
            price=drift_client.convert_to_price_precision(100),
        )

await driftClient.place_spot_order(order_params);
```

```shell
curl -X POST -H 'content-type: application/json' \
  -d '{
    "orders": [{
        "marketIndex": 0,
        "marketType": "spot",
        "amount": -1.23,
        "price": 80.0,
        "postOnly": true,
        "orderType": "limit",
        "immediateOrCancel": false,
        "reduceOnly": false
    }]
   }' \
localhost:8080/v2/orders
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderParams | The order params  | No | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| order_params | The order params  | No | |

The order type is set to SPOT / Spot() by default.

## Placing Multiple Orders

```typescript

const placeOrderParams = [
	{
    orderType: OrderType.LIMIT,
    marketType: MarketType.PERP,
    marketIndex: 0,
    direction: PositionDirection.LONG,
    baseAssetAmount: driftClient.convertToPerpPrecision(100),
    price: driftClient.convertToPricePrecision(21.23),
  },
  {
    orderType: OrderType.LIMIT,
    marketType: MarketType.PERP,
    marketIndex: 0,
    direction: PositionDirection.SHORT,
    baseAssetAmount: driftClient.convertToPerpPrecision(100),
    oraclePriceOffset: driftClient.convertToPricePrecision(.05).toNumber(),
  }
];

await driftClient.placeOrders(placeOrderParams);
```

```python

place_order_params = [
	OrderParams(
    order_type=OrderType.Limit(),
    base_asset_amount=drift_client.convert_to_perp_precision(100),
    market_index=0,
    direction=PositionDirection.Long(),
    market_type=MarketType.Perp(),
    price=drift_client.convert_to_price_precision(21.23),
  ),
   OrderParams(
    order_type=OrderType.Limit(),
    base_asset_amount=drift_client.convert_to_perp_precision(100),
    market_index=0,
    direction=PositionDirection.Short(),
    market_type=MarketType.Perp(),
    oracle_price_offset=drift_client.convert_to_price_precision(.05),
  )
]

await drift_client.place_orders(place_order_params);
```

```shell
curl -X POST -H 'content-type: application/json' \
  -d '{
      "orders": [{
          "marketIndex": 0,
          "marketType": "spot",
          "amount": 1.23,
          "price": 80.0,
          "postOnly": true,
          "orderType": "limit",
          "immediateOrCancel": false,
          "reduceOnly": false
      },
      {
          "marketIndex": 2,
          "marketType": "perp",
          "amount": 0.1,
          "price": 10000.0,
          "orderType": "market",
      }]
    }' \
localhost:8080/v2/orders
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| placeOrderParams | Parameters for place order instructions | | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| place_order_params | Parameters for place order instructions | | |

Placing multiple orders in one tx can be cheaper than placing them in separate tx.

## Placing Oracle Market Orders

```typescript
const oraclePrice = driftClient.getOracleDataForPerpMarket(18).price;
const auctionStartPrice = oraclePrice.neg().divn(1000); // start auction 10bps below oracle
const auctionEndPrice = oraclePrice.divn(1000); // end auction 10bps above oracle
const oraclePriceOffset = oraclePrice.divn(500); // limit price after auction 20bps above oracle
const auctionDuration = 30; // 30 slots
const orderParams = {
  orderType: OrderType.ORACLE,
  baseAssetAmount: driftClient.convertToPerpPrecision(10),
  direction: PositionDirection.LONG,
  marketIndex: 18,
  auctionStartPrice: auctionStartPrice,
  auctionEndPrice: auctionEndPrice,
  oraclePriceOffset: oraclePriceOffset,
  auctionDuration: auctionDuration,
};
await driftClient.placePerpOrder(orderParams)
```

```python
oracle_price = drift_client.get_oracle_price_data_for_perp_market(18).price
auction_start_price = -oracle_price // 1000 # start auction 10bps below oracle
auction_end_price = oracle_price // 1000 # end auction 10bps above oracle
oracle_price_offset = oracle_price // 500 # limit price after auction 20bps above oracle
auction_duration = 30 # 30 slots
order_params = OrderParams(
  order_type=OrderType.Oracle(),
  base_asset_amount=drift_client.convert_to_perp_precision(10),
  market_index=18,
  direction=PositionDirection.Long(),
  auction_start_price=auction_start_price,
  auction_end_price=auction_end_price,
  oracle_price_offset=oracle_price_offset,
  auction_duration=auction_duration
)
await drift_client.place_perp_order(order_params)
```

Oracle market orders enable a user to define their auction params as an offset (or relative to) the oracle price.

## Canceling Order

```typescript

const orderId = 1;
await driftClient.cancelOrder(orderId);
```

```python

order_dd = 1;
await drift_client.cancel_order(order_id);
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderId | The order being canceled  | No | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| order_id | The order being canceled  | No | |
| sub_account_id | Sub account to cancel order under  | Yes | None |

```shell
curl -X DELETE -H 'content-type: application/json' \
  -d '{ "ids": [1] }' \
  localhost:8080/v2/orders
```

## Canceling Order By User Order Id

```typescript

const userOrderId = 1;
await driftClient.cancelOrderByUserOrderId(userOrderId);
```

```python

const user_order_id = 1;
await drift_client.cancel_order_by_user_order_id(user_order_id);
```

```shell
curl -X DELETE -H 'content-type: application/json' \
  -d '{ "userIds": [1] }' \
  localhost:8080/v2/orders
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| userOrderId | Unique order id specified by user when order was placed  | No | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| user_order_id | Unique order id specified by user when order was placed  | No | |
| sub_account_id | Sub account to cancel orders under  | Yes | None |

## Cancel Orders

```typescript

const marketType = MarketType.PERP;
const marketIndex = 0;
const direction = PositionDirection.LONG;
await driftClient.cancelOrders(marketType, marketIndex, direction);
```

``` python
market_type = MarketType.Perp()
market_index = 0
direction = PositionDirection.Long()
await drift_client.cancel_orders(market_type, market_index, direction) # cancel bids in perp market 0

await drift_client.cancel_orders() # cancels all orders
```

```shell
curl -X DELETE -H 'content-type: application/json' \
  -d '{ "marketIndex": 1, "marketType": "spot" }' \
  localhost:8080/v2/orders
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketType | The market type of orders to cancel. Must be set if marketIndex set | Yes | |
| marketIndex | The market index of orders to cancel. Must be set if marketType set | Yes | |
| direction | The direction of orders to cancel. | Yes | |

### Python
| Parameter     | Description                                             | Optional | Default |
| ------------- | ------------------------------------------------------- | -------- | ------- |
| market_type   | The market type of orders to cancel      | Yes      | None    |
| market_index  | The market index of orders to cancel                    | Yes      | None    |
| direction     | The direction  of orders to cancel       | Yes      | None    |
| sub_account_id| The sub account from which to cancel orders          | Yes      | None    |


To cancel all orders, do not set any parameters.

## Cancel and Place Orders

```typescript

const cancelOrderParams = {
   marketType: MarketType.PERP,
   marketIndex: 0,
};

const placeOrderParams = [
	{
    orderType: OrderType.LIMIT,
    marketIndex: 0,
    direction: PositionDirection.LONG,
    baseAssetAmount: driftClient.convertToPerpPrecision(100),
    price: driftClient.convertToPricePrecision(21.23),
  },
  {
    orderType: OrderType.LIMIT,
    marketIndex: 0,
    direction: PositionDirection.SHORT,
    baseAssetAmount: driftClient.convertToPerpPrecision(100),
    oraclePriceOffset: driftClient.convertToPricePrecision(.05).toNumber(),
  }
];

await driftClient.cancelAndPlaceOrders(cancelOrderParams, placeOrderParams);
```

```python

canel_order_params = (MarketType.Perp(), 0, None) # cancel all orders in perp market 0
place_order_params = [
	OrderParams(
    order_type=OrderType.Limit(),
    base_asset_amount=drift_client.convert_to_perp_precision(100),
    market_index=0,
    direction=PositionDirection.Long(),
    market_type=MarketType.Perp(),
    price=drift_client.convert_to_price_precision(21.23),
  ),
   OrderParams(
    order_type=OrderType.Limit(),
    base_asset_amount=drift_client.convert_to_perp_precision(100),
    market_index=0,
    direction=PositionDirection.Short(),
    market_type=MarketType.Perp(),
    oracle_price_offset=drift_client.convert_to_price_precision(.05),
  )
]

await drift_client.cancel_and_place_orders(canel_order_params, place_order_params);
```

```shell
curl -X POST -H 'content-type: application/json' \
-d '{
      "cancel": {
        "marketIndex": 0,
        "marketType": "perp"
      },
      "place": {
        "orders": [{
            "marketIndex": 0,
            "marketType": "perp",
            "amount": -1.23,
            "price": 80.0,
            "postOnly": true,
            "orderType": "limit",
            "immediateOrCancel": false,
            "reduceOnly": false
        }]
      }
   }' \
localhost:8080/v2/orders/cancelAndPlace
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| cancelOrderParams | Parameters for cancel orders instruction | | |
| placeOrderParams | Parameters for place order instructions | | |

### Python
| Parameter          | Description                                                | Optional | Default |
| ------------------ | ---------------------------------------------------------- | -------- | ------- |
| cancel_params      | Tuple with optional MarketType, market index, and PositionDirection for canceling orders | Yes | None |
| place_order_params | List of OrderParams for placing new orders                 | No       |         |
| sub_account_id     | The sub account to use          | Yes      | None    |


To cancel all orders, do not set any parameters.

## Modify Order Params

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderId | The order id of order to modify  | No | |
| baseAssetAmount | The amount of base asset to buy or sell  | Yes | |
| direction | The direction of order e.g. long (bid) or short (ask)  | Yes | |
| limitPrice | The limit price for order | Yes | |
| reduceOnly | If the order can only reduce positions| Yes | |
| postOnly | If the order can only be a maker | Yes | |
| triggerPrice | at what price order is triggered. only applicable for triggerMarket and triggerLimit orders | Yes | |
| triggerCondition | whether order is triggered above or below triggerPrice. only applicable for triggerMarket and triggerLimit orders | Yes | |
| oraclePriceOffset | priceOffset for oracle derived limit price. only applicable for limit and oracle orders  | Yes | |
| auctionDuration | how many slots the auction lasts. only applicable for market and oracle orders | Yes | |
| auctionStartPrice | the price the auction starts at | Yes | |
| auctionEndPrice | the price the auction ends at | Yes | |
| maxTs | the max timestamp before the order expires | Yes | |

### Python
| Parameter          | Description                                                                 | Optional | Default |
| ------------------ | --------------------------------------------------------------------------- | -------- | ------- |
| direction          | The direction of the order                          | Yes      | None    |
| base_asset_amount  | Amount of base asset to buy or sell                                         | Yes      | None    |
| price              | Limit price for the order                                                   | Yes      | None    |
| reduce_only        | If the order should only reduce positions                                  | Yes      | None    |
| post_only          | If the order should only be a maker order                                   | Yes      | None    |
| immediate_or_cancel| Whether the order is immediate or cancel                                    | Yes      | None    |
| max_ts             | Max timestamp for order expiry                                              | Yes      | None    |
| trigger_price      | Trigger price for trigger orders                     | Yes      | None    |
| trigger_condition  | Condition for triggering the order                                          | Yes      | None    |
| oracle_price_offset| Offset for oracle-derived limit price                                       | Yes      | None    |
| auction_duration   | Duration of the auction in slots                                            | Yes      | None    |
| auction_start_price| Starting price of the auction                                               | Yes      | None    |
| auction_end_price  | Ending price of the auction                                                 | Yes      | None    |
| policy             | Policy for modifying the order                                              | Yes      | None    |


## Modifying Order

```typescript

const updateParams = {
  orderId: 1,
  newBaseAssetAmount: driftClient.convertToPerpPrecision(200),
}

await driftClient.modifyOrder(orderParams);
```

```python

order_id = 1

modify_order_params = ModifyOrderParams(
  base_asset_amount=drift_client.convert_to_perp_precision(1),
  price=drift_client.convert_to_price_precision(20),
)

await drift_client.modify_order(order_id, modify_order_params);
```

```shell
curl -X PATCH -H 'content-type: application/json' \
  -d '{
    "orders": [{
        "orderId": 32,
        "amount": 1.05,
        "price": 61.0
    }]
   }' \
localhost:8080/v2/orders
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderId | The order id of order to modify  | No | |
| modifyOrderParams | The modify order params  | Yes | |

### Python
| Parameter           | Description                              | Optional | Default |
| ------------------- | ---------------------------------------- | -------- | ------- |
| order_id            | The order ID of the order to modify      | No       |         |
| modify_order_params | The parameters for modifying the order   | No       |         |
| sub_account_id      | The sub account to use       | Yes      | None    |


Modify cancels and places a new order

For typescript, the orderId and modifyOrderParams are merged into a single object and some properties are prefixed with `new` e.g. `newBaseAssetAmount`

## Modifying Order By User Order Id

```typescript

const updateParams = {
  userOrderId: 1,
  newBaseAssetAmount: driftClient.convertToPerpPrecision(200),
}

await driftClient.modifyOrderByUserOrderId(orderParams);
```

```python

user_order_id = 1

modify_order_params = ModifyOrderParams(
  base_asset_amount=drift_client.convert_to_perp_precision(1),
  price=drift_client.convert_to_price_precision(20),
)

await drift_client.modify_order_by_user_id(user_order_id, modify_order_params);
```

```shell
curl -X PATCH -H 'content-type: application/json' \
  -d '{
    "orders": [{
        "userOrderId": 69,
        "amount": 1.05,
        "price": 61.0
    }]
   }' \
localhost:8080/v2/orders
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| userOrderId | The user order id of order to modify  | No | |
| modifyOrderParams | The modify order params  | Yes | |

### Python
| Parameter            | Description                                        | Optional | Default |
| -------------------- | -------------------------------------------------- | -------- | ------- |
| user_order_id        | The user order ID of the order to modify           | No       |         |
| modify_order_params  | The parameters for modifying the order             | No       |         |
| sub_account_id       | The sub-account ID associated with the order       | Yes      | None    |

Modify cancels and places a new order

For typescript, the userOrderId and modifyOrderParams are merged into a single object and some properties are prefixed with `new` e.g. `newBaseAssetAmount`

## Settle Perp PNL

```typescript
const marketIndex = 0;
const user =  driftClient.getUser();
await driftClient.settlePNL(
   user.userAccountPublicKey,
   user.getUserAccount(),
   marketIndex
);
```

```python    
market_index = 0
user =  drift_client.get_user()
await drift_client.settle_pnl(
   user.user_public_key,
   user.get_user_account(),
   market_index
)
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| settleeUserAccountPublicKey | User address you're settling pnl for | No | |
| settleeUserAccount | User account data you're settling pnl for | No | |
| marketIndex | Market index for the perp market  | No | |

### Python
| Parameter                          | Description                                     | Optional | Default |
| ---------------------------------- | ----------------------------------------------- | -------- | ------- |
| settlee_user_account_public_key    | Public key of the user account to settle PNL for| No       |         |
| settlee_user_account               | User account data for PNL settlement            | No       |         |
| market_index                       | Index of the perpetual market for PNL settlement| No       |         |

## Get Spot Market Account

```typescript
const marketIndex = 1;
const spotMarketAccount = driftClient.getSpotMarketAccount(marketIndex);
```

```python
market_index = 0;

spot_market_account = drift_client.get_spot_market_account(market_index);
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | The market index for the spot market | No | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| market_index | The market index for the spot market | No | |


## Get Perp Market Account

```typescript
const marketIndex = 0;
const perpMarketAccount = driftClient.getPerpMarketAccount(marketIndex);
```

```python
market_index = 0;

perp_market_account = drift_client.get_perp_market_account(market_index);
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | The market index for the perp market | No | |


### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| market_index | The market index for the perp market | No | |

# Swift
Swift is an extension to Drift which enables users to place orders without needing to submit a transaction to the Solana network. 
Instead of having users submit transactions to the Solana network, users sign a message containing their order parameters and submit it to keepers and market makers offchain. Keepers and market makers then bundle the message with their transaction to fill user orders.

Orders have to be submitted to the Swift API: [https://swift.drift.trade]

## Order setup
Similar to normal orders, order parameters need to be defined. Order parameters <a href="https://drift-labs.github.io/v2-teacher/#order-params">overview</a>

To pass an order to Swift, the following steps are required:
1. Define order parameters
2. Generate and Sign order
3. Submit to Swift API

## Order example(market taker)
```typescript
const MarketIndex = 0; // 0 = SOL-PERP market
const direction = PositionDirection.LONG;

const oracleInfo = driftClient.getOracleDataForPerpMarket(MarketIndex);

const marketOrderParams = {
    marketIndex: MarketIndex,
    marketType: MarketType.PERP,
    direction,
    baseAssetAmount: driftClient.getPerpMarketAccount(MarketIndex)!.amm.minOrderSize.muln(2),
    auctionStartPrice: oracleInfo.price.muln(99).divn(100), // 1% lower than oracle price
    auctionEndPrice: oracleInfo.price.muln(101).divn(100), // 1% higher than oracle price
    auctionDuration: 50, // Duration of auction in slots (~5s)
};
```

## Order example(limit taker)
```typescript
const MarketIndex = 0; // 0 = SOL-PERP market
const direction = PositionDirection.LONG;

const limitOrderParams = {
    orderType: OrderType.LIMIT,
    marketIndex: perpMarketIndex,
    marketType: MarketType.PERP,
    direction,
    baseAssetAmount: driftClient.convertToPerpPrecision(1), // Size 1 Sol
    price: driftClient.convertToPricePrecision(100), // At price 100
};
```

## Order example(limit maker)
```typescript
WIP
```

## Sign order
```typescript
const slot = await driftClient.connection.getSlot();

const orderMessage = {
    signedMsgOrderParams: marketOrderParams,
    subAccountId: driftClient.activeSubAccountId,
    slot: new BN(slot),
    uuid: generateSignedMsgUuid(),
    stopLossOrderParams: null,
    takeProfitOrderParams: null,
};

// Sign the message
const signedOrder = driftClient.signSignedMsgOrderParamsMessage(orderMessage);
const message = Buffer.from(signedOrder.orderParams).toString('hex');
const signature = Buffer.from(signedOrder.signature).toString('base64');
```

## Submit order to Swift API
```typescript
const swiftUrl = 'https://swift.drift.trade/orders';

const response = await axios.default.post(swiftUrl, {
    market_index: perpMarketIndex,
    market_type: 'perp',
    message: message,
    signature: signature,
    taker_pubkey: driftClient.wallet.publicKey.toBase58(),
}, {
    headers: { 'Content-Type': 'application/json' }
});

console.log("Order response:", response.data);
```

# User

## Get User

```typescript
   const user = driftClient.getUser();
```

```python
  user = drift_client.get_user();
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| subAccountId | The sub account id of user to get  | Yes | active sub account |
| authority | The authority of user to get. Only necessary if using multiple delegate accounts | Yes | current authority |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| sub_account_id | The sub account id of user to get  | Yes | active sub account |

## Getting Deposit/Borrow Amounts

```typescript
const marketIndex = 0;

const tokenAmount = user.getTokenAmount(
  marketIndex,
);

const isDeposit = tokenAmount.gte(new BN(0));
const isBorrow = tokenAmount.lt(new BN(0));
```

```python
market_index = 0

token_amount = user.get_token_amount(marketIndex)

is_deposit = token_amount > 0
is_borrow = token_amount < 0
```

```shell
 curl -X GET \
  -H 'content-type: application/json' \
  -d '{"marketIndex":0,"marketType":"spot"}' \
localhost:8080/v2/positions
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | Market index for the spot market  | No | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| market_index | Market index for the spot market  | No | |

If token amount is greater than 0, it is a deposit. If less than zero, it is a borrow.

## Get Perp Position
```typescript
const marketIndex = 0;

const baseAssetAmount = user.getPerpPosition(
  marketIndex,
)?.baseAssetAmount;

const isLong = baseAssetAmount.gte(new BN(0));
const isShort = baseAssetAmount.lt(new BN(0));
```

```python
market_index = 0

perp_position = user.get_perp_position(market_index)

base_asset_amount = perp_position.base_asset_amount if perp_position is not None else 0

is_long = base_asset_amount > 0
is_short = base_asset_amount < 0
```

```shell
curl -X GET \
  -H 'content-type: application/json' \
  -d '{"marketIndex":0,"marketType":"perp"}' \
localhost:8080/v2/positions
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | Market index for the perp market  | No | |


### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| market_index | Market index for the perp market  | No | |

If base amount is greater than 0, it is a long. If less than zero, it is a short.

## Get Order
```typescript
const orderId = 1;

const order = user.getOrder(
  orderId,
);
```

```python
order_id = 1

order = user.get_order(order_id)
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderId | Order id for the order you're getting | No | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| order_id | Order id for the order you're getting | No | |

## Get Order By User Order Id
```typescript
const userOrderId = 1;

const order = user.getOrderByUserOrderId(
  userOrderId,
);
```

```python
user_order_id = 1

order = user.get_order_by_user_order_id(user_order_id)
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| userOrderId | User order id for the order you're getting | No | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| user_order_id | User order id for the order you're getting | No | |

## Get Open Orders
```typescript
const orders = user.getOpenOrders();
```

```python
orders = user.get_open_orders()
```

```shell
curl localhost:8080/v2/orders
```

## Get Unrealized Perp Pnl

  ```typescript
  const pnl = user.getUnrealizedPNL();
  ```

  ```python
  pnl = user.get_unrealized_pnl()
  ```

### TypeScript
| Parameter               | Description                                          | Optional | Default |
| ----------------------- | ---------------------------------------------------- | -------- | ------- |
| withFunding             | Whether to include unsettled funding payments       | Yes      | false   |
| marketIndex             | Index of a specific market for PNL calculation       | Yes      |         |
| withWeightMarginCategory| To include margin category weighting in PNL calculation | Yes   |         |
| strict                  | Whether the calculation should be strict             | Yes      | false   |


### Python
| Parameter               | Description                                          | Optional | Default |
| ----------------------- | ---------------------------------------------------- | -------- | ------- |
| with_funding             | Whether to include unsettled funding payments       | Yes      | false   |
| market_index             | Index of a specific market for PNL calculation       | Yes      |         |
| with_weight_margin_category| To include margin category weighting in PNL calculation | Yes   |         |
| strict                  | Whether the calculation should be strict             | Yes      | false   |


## Get Unrealized Funding Pnl

  ```typescript
  const pnl = user.getUnrealizedFundingPNL();
  ```

  ```python
  pnl = user.get_unrealized_funding_pnl()
  ```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | Whether to only return pnl for specific market  | Yes | |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| market_index | Whether to only return pnl for specific market  | Yes | |

## Get Total Collateral

  ```typescript
  const totalCollateral = user.getTotalCollateral();
  ```

  ```python
  total_collateral = user.get_total_collateral()
  ```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marginCategory | Initial or Maintenance  | Yes | Initial |
| strict                  | Whether the calculation should be strict             | Yes      | false   |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| margin_category | Initial or Maintenance  | Yes | Initial |
| strict                  | Whether the calculation should be strict             | Yes      | false   |

Asset weights vary based on whether you're checking the initial or maintenance margin requirement. Initial is used for initial leverage extension, maintenance for determining liquidations.

## Get Margin Requirement

  ```typescript
  const marginRequirement = user.getMarginRequirement();
  ```

  ```python
  margin_requirement = user.get_margin_requirement()
  ```

### TypeScript
| Parameter        | Description                            | Optional | Default |
| ---------------- | -------------------------------------- | -------- | ------- |
| marginCategory   | The type of margin (Initial or Maintenance) | No   |         |
| liquidationBuffer| Buffer value for liquidation calculation | Yes     |         |
| strict           | Whether the calculation should be strict | Yes     | false   |

### Python
| Parameter        | Description                                         | Optional | Default                 |
| ---------------- | --------------------------------------------------- | -------- | ----------------------- |
| margin_category  | Type of margin, either Initial or Maintenance       | Yes      | MarginCategory.INITIAL  |
| liquidation_buffer| Additional buffer value for liquidation calculation| Yes      | 0                       |
| strict           | Whether the calculation should be strict | Yes     | False   |


Liability weights (for borrows) and margin ratios (for perp positions) vary based on whether you're checking the initial or maintenance margin requirement. Initial is used for initial leverage extension, maintenance for determining liquidations.

## Get Free Collateral

  ```typescript
  const freeCollateral = user.getFreeCollateral();
  ```

  ```python
  free_collateral = user.get_free_collateral()
  ```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marginCategory | Initial or Maintenance  | Yes | Initial |

### Python
| Parameter       | Description                          | Optional | Default                 |
| --------------- | ------------------------------------ | -------- | ----------------------- |
| marginCategory  | Type of margin, either Initial or Maintenance | Yes | MarginCategory.INITIAL  |


Free collateral is the difference between your total collateral and your margin requirement.

## Get Leverage

  ```typescript
  const leverage = user.getLeverage();
  ```

  ```python
  leverage = user.get_leverage()
  ```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| includeOpenOrders | Whether to factor in open orders in position size  | Yes | true |

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| include_open_orders | Whether to factor in open orders in position size  | Yes | True |

Leverage is the total liability value (borrows plus total perp position) divided by net asset value (total assets plus total liabilities)


# Events

## Event Subscription

Subscribing to Drift Events is a core component necessary to react to events happening in Drift. The Drift SDK has an [Event Subscriber](https://github.com/drift-labs/protocol-v2/blob/master/sdk/src/events/eventSubscriber.ts) class to help you do this.

You should also note that you can Serialize and Deserialize Drift Events using the [Serializer](https://github.com/drift-labs/drift-common/blob/master/common-ts/src/serializableTypes.ts#L2131) class exported from the [Drift Common library](https://github.com/drift-labs/drift-common) (`@drift/common` on npm). There are "UI" versions of the deserialized records available where the `BN` numbers have been cast into our own `BigNum` class which makes it easier to work with the numbers inside the records.

```typescript
import {EventSubscriber} from "@drift-labs/sdk";

const options = {
  eventTypes: [
    'DepositRecord',
    'FundingPaymentRecord',
    'LiquidationRecord',
    'OrderRecord',
    'OrderActionRecord',
    'FundingRateRecord',
    'NewUserRecord',
    'SettlePnlRecord',
    'LPRecord',
    'InsuranceFundRecord',
    'SpotInterestRecord',
    'InsuranceFundStakeRecord',
    'CurveRecord',
  ],
  maxTx: 4096,
  maxEventsPerType: 4096,
  orderBy: 'blockchain',
  orderDir: 'asc',
  commitment: 'confirmed',
  logProviderConfig: {
    type: 'websocket',
  },
}

const eventSubscriber = new EventSubscriber(connection, driftClient.program, options);
await eventSubscriber.subscribe();

eventSubscriber.eventEmitter.on('newEvent', (event) => {
  console.log(event);
});
```

```python
from driftpy.events.event_subscriber import EventSubscriber
from driftpy.events.types import WrappedEvent, EventSubscriptionOptions, WebsocketLogProviderConfig

options = EventSubscriptionOptions(
    event_types = (
        'DepositRecord',
        'FundingPaymentRecord',
        'LiquidationRecord',
        'OrderRecord',
        'OrderActionRecord',
        'FundingRateRecord',
        'NewUserRecord',
        'SettlePnlRecord',
        'LPRecord',
        'InsuranceFundRecord',
        'SpotInterestRecord',
        'InsuranceFundStakeRecord',
        'CurveRecord',
    ),
    max_tx= 4096,
    max_events_per_type=4096,
    order_by="blockchain",
    order_dir="asc",
    commitment="processed",
    log_provider_config=WebsocketLogProviderConfig()
)

event_subscriber = EventSubscriber(connection, drift_client.program, options)
event_subscriber.subscribe()

event_subscriber.event_emitter.new_event += lambda event: print(event)
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| connection | Connection object specifying solana rpc url   | No | |
| program | Anchor program object used to deserialize events from transaction logs | No | |
| options.eventTypes | Which events types to trigger event callbacks for | Yes | All events |
| options.maxTx | Max number of transactions to keep in memory | Yes | 4096 |
| options.maxEventsPerType | Max number of events per event type to keep in memory | Yes | 4096 |
| options.orderBy | Whether to sort the tx in memory by the order they occurred on chain ('blockchain') or received by client ('client') | Yes | 'blockchain' |
| options.orderDir | Whether to sort the tx in memory to be most recent ('desc') or oldest ('asc') | Yes | 'asc' |
| options.commitment | What transaction commitment to wait for | Yes | 'confirmed' |
| options.logProviderConfig | How to get events: RPC Polling, RPC Websocket, or the Drift Events Server                                            | Yes      | {type: polling/websocket/events-server, url?: {events_server_url}}<br><br>see [Environment Constants](https://github.com/drift-labs/drift-common/blob/master/common-ts/src/EnvironmentConstants.ts) for the Events Server URL to use |
| options.address | Which address to listen to events for. Defaults to drift program. | Yes | dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH |

### Python
| Parameter               | Description                                                                               | Optional | Default                                            |
| ----------------------- | ----------------------------------------------------------------------------------------- | -------- | -------------------------------------------------- |
| connection              | AsyncClient object specifying Solana RPC URL                                               | No       |                                                    |
| program                 | Anchor program object used to deserialize events from transaction logs                   | No       |                                                    |
| options                 | Configuration options for event subscription                                              | Yes      | EventSubscriptionOptions.default()                 |
| - address               | Address to listen to events for, defaults to Drift program                                | Yes      | DRIFT_PROGRAM_ID                                   |
| - event_types           | Types of events to trigger event callbacks for                                            | Yes      | DEFAULT_EVENT_TYPES                                |
| - max_events_per_type   | Maximum number of events per event type to keep in memory                                 | Yes      | 4096                                               |
| - order_by              | Sort order of transactions, by 'blockchain' order or 'client' received order              | Yes      | "blockchain"                                       |
| - order_dir             | Sorting direction, either most recent ('desc') or oldest ('asc')                           | Yes      | "asc"                                              |
| - commitment            | Transaction commitment level to wait for                                                  | Yes      | "confirmed"                                        |
| - max_tx                | Maximum number of transactions to keep in memory                                          | Yes      | 4096                                               |
| - log_provider_config   | Configuration for log provider, either websocket or polling                               | Yes      | WebsocketLogProviderConfig()                       |
| - until_tx              | Signature to listen until, used for log provider                                          | Yes      | None                                               |


Protocol events are recorded in transactions logs. To listen for events, one must subscribe to the drift program's transaction logs.

## Event Types

| Event Type   | Description |
| ----------- | ----------- |
| DepositRecord | A record of a user depositing or withdrawing funds from protocol |
| FundingPaymentRecord | A record of a user paying/receiving funding payments |
| LiquidationRecord | A record of a user being liquidated |
| OrderRecord | A record of a user placing an order, including all of its parameters |
| OrderActionRecord | A record of a user action on an order, including place, cancel and fill |
| FundingRateRecord | A record of the funding rate changing |
| NewUserRecord | A record of a new user |
| SettlePnlRecord | A record of a user settling their pnl |
| LPRecord | A record of a user adding or removing passive perp liquidity |
| InsuranceFundRecord | A record of the insurance fund changing |
| SpotInterestRecord | A record of the spot interest changing |
| InsuranceFundStakeRecord | A record of a user staking or unstaking from the insurance fund |
| CurveRecord | A record of the amm curve updating |

## Listening to Perp Market Fills

```typescript

const marketIndex = 0;
const isPerpMarketFill = (event) => {
  if (event.eventType !== 'OrderActionRecord') {
    return false;
  }

  if (event.marketIndex !== marketIndex) {
    return false;
  }

  if (!isVariant(event.marketType, 'perp')) {
    return false;
  }

  if (!isVariant(event.action, 'fill')) {
    return false;
  }

  return true;
};

const fillCallback = (event) => {
  console.log(event);
}

eventSubscriber.eventEmitter.on('newEvent', (event) => {
  if (isPerpMarketFill(event)) {
    fillCallback(event);
  }
});

```

```python
market_index = 0

def fill_callback(event: WrappedEvent):
    if event.event_type != "OrderActionRecord":
        return

    if event.data.market_index != market_index:
        return

    if not is_variant(event.data.market_type, "Perp"):
        return

    if not is_variant(event.data.action, "Fill"):
        return

    print(event)


event_subscriber.event_emitter.new_event += fill_callback
```

## Listening to User Fills

```python
options = EventSubscriptionOptions(
    address=drift_client.get_user_account_public_key(),
)

event_subscriber = EventSubscriber(connection, drift_client.program, options)
event_subscriber.subscribe()

def fill_callback(event: WrappedEvent):
    if event.event_type != "OrderActionRecord":
        return

    if not is_variant(event.data.action, "Fill"):
        return

    is_taker = event.data.taker == drift_client.get_user_account_public_key()
    is_maker = event.data.taker == drift_client.get_user_account_public_key()
    if not is_taker and not is_maker:
        return

    print(event)


event_subscriber.event_emitter.new_event += fill_callback
```

## Getting Events Received By Type

```typescript

const eventType = 'OrderActionRecord';
const events = eventSubscriber.getEventsReceived(eventType);
```

```python

event_type = 'OrderActionRecord'
events = event_subscriber.get_events_array(event_type)
```

This returns all the events that the event subscriber currently has stored in memory.

## Getting Events By Transaction

```typescript
const txSig = '3dq5PtQ3VnNTkQRrHhQ1nRACWZaFVvSBKs1RLXM8WvCqLHTzTuVGc7XER5awoLFLTdJ4kqZiNmo7e8b3pXaEGaoo';
const events = eventSubscriber.getEventsByTx(txSig);
```

```python

tx_sig = '3dq5PtQ3VnNTkQRrHhQ1nRACWZaFVvSBKs1RLXM8WvCqLHTzTuVGc7XER5awoLFLTdJ4kqZiNmo7e8b3pXaEGaoo'
events = event_subscriber.get_events_by_tx(tx_sig)
```

This returns the events that the event subscriber currently has stored in memory for a given transaction.

# Margin System

Drift offers a cross-collateral margining system, allowing users to utilize multiple assets as trading collateral.

The margining system tracks each user's total collateral, the weighted sum of the user's deposits and perp pnl, as well
as their margin requirement, the weighted value out the user's outstanding borrow and perp positions.

Total collateral is calculated as:

\\[\sum_{i=1}^n d_i \cdot p_i \cdot w_i^a\ +\ \sum_{j=1}^n pnl_j \cdot qp_j \cdot w_j^{pnl} \\]

Where

* \\(d_i\\) is the deposit amount for spot market \\(i\\)
* \\(p_i\\) is the price for spot market \\(i\\)
* \\(w_i^a\\) is the asset weight for spot market \\(i\\)
* \\(pnl_j\\) is the pnl for perp market \\(j\\)
* \\(qp_j\\) is the quote asset price for perp market \\(j\\)
* \\(w_j^{pnl}\\) is the pnl weight for perp market \\(j\\)

Margin requirement is calculated as:

\\[ \sum_{i=1}^n b_i \cdot p_i \cdot w_i^l\ +\ \sum_{j=1}^n ba_j \cdot o_j \cdot qp_j \cdot m_j \\]

Where

* \\(b_i\\) is the borrow amount for spot market \\(i\\)
* \\(p_i\\) is the price for spot market \\(i\\)
* \\(w_i^l\\) is the liability weight for spot market \\(i\\)
* \\(ba_j\\) is the base amount for perp market \\(j\\)
* \\(o_j\\) is the price for perp/prediction market \\(j\\)
* \\(qp_j\\) is the quote asset price for perp market \\(j\\)
* \\(m_j\\) is the margin ratio for perp market \\(j\\)

The weights and margin ratios depend on whether you're calculating the initial or maintenance values.

The initial maintenance check governs leverage extension. To open a new perp position or borrow, a user's initial total collateral must be greater than their initial margin requirement.

The maintenance check governs when a user's position must be liquidated. If a user's maintenance total collateral drops below their maintenance margin requirement, a user's position can be liquidated
to reduce their risk.

The prices used for deposits, borrows and perp quote assets differ between the initial and maintenance checks. The maintenance check uses
the current oracle price. The initial check uses the `min(oracle_price, oracle_twap)` for deposits and positive perp pnl and `max(oracle_price, oracle_twap)`
for borrows, negative perp pnl and perp base amount.

For prediction markets, the price used in the margin system depends on the direction of the position. For short positions (betting no), the margin system uses
1 - oracle price. For long position (betting yes), it uses the oracle price. This is the account for the user's worst case loss. E.g. if a user shorts at $.01,
their worst case loss is $.99.

# Numerical Precisions

To maintain numerical precision, the on-chain program stores all values as integers.

## Getting a Current Perp Position

```typescript

const perpMarketIndex = 0; // SOL-PERP
const perpPosition = driftClient.getPerpPosition(perpMarketIndex);
console.log(convertToNumber(perpPosition.baseAssetAmount, BASE_PRECISION));
```

```python
perp_market_index = 0 # SOL-PERP
perp_position = drift_client.get_perp_position(market_index)
print(convert_to_number(perp_position.base_asset_amount, BASE_PRECISION))
```

This prints the size of the current perp position in perp market index 0 (SOL-PERP)

## Getting a Current Spot Position

```typescript

const spotMarketIndex = 0; // USDC
const spotConfig = SpotMarkets['mainnet-beta'][spotMarketIndex];
const spotMarket = driftClient.getSpotMarketAccount(spotMarketIndex);
const spotPosition = driftClient.getSpotPosition(spotMarketIndex);
const tokenAmount = getTokenAmount(spotPosition.scaledBalance, spotMarket, spotPosition.balanceType);
console.log(convertToNumber(tokenAmount, spotConfig.precision));
```

```python
spot_market_index = 0 # USDC
spot_market = drift_client.get_spot_market_account(spot_market_index)
spot_position = drift_client.get_spot_position(spot_market_index)
token_amount = get_token_amount(spot_psoition.scaled_balance, spot_market, spot_position.balance_type)
print(convert_to_number(token_amount, (10 ** spot_market.decimals)))
```

This prints the current spot position in spot market index 0 (USDC). This value is the same as the value shown on the UI,
it includes any accumulated interest.


## Common precision values

| Value   | Precision | Constant |
| ----------- | ----------- | -------- |
| perp base asset amount | 1e9 | `BASE_PRECISION` |
| perp quote asset amount | 1e6 | `QUOTE_PRECISION` |
| price | 1e6 | `PRICE_PRECISION` |
| funding rate | 1e9 | `FUNDING_RATE_PRECISION` |
| spot token amount | derived from token mint's decimals (USDC is 1e6, SOL is 1e9) | `SpotMarketConfig.precision` |
| spot token balance | 1e9 | `SPOT_MARKET_BALANCE_PRECISION` |
| margin ratio | 1e4 | `MARGIN_PRECISION` |
| asset/liability weight | 1e4 | `SPOT_WEIGHT_PRECISION` |
| imf weight precision | 1e6 | `SPOT_IMF_PRECISION` |
