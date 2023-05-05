---
title: protocol-v2 API

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - typescript
  - python
  - shell

toc_footers:
  - <a href='https://github.com/drift-labs/protocol-v2/releases/'> Release History </a>
  - <a href='https://docs.drift.trade/'> Documentation </a>
includes:
  - examples
  - historicaldata
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for drift protocol-v2
---


# Terms of Use
By using any API provided by Drift Labs, you agree to the <a href="https://docs.drift.trade/terms-of-use">Terms of Use</a>. If you do not agree to the foregoing, then do not use any such API.

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
  It is strictly against the <a href="https://docs.drift.trade/terms-of-use">Terms of Use</a> to use these interfaces from a Restricted Territory.
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

auto-generated documentation here: [https://drift-labs.github.io/protocol-v2/sdk/]

## Python
Install driftpy from PyPI using pip:

`pip install driftpy`

auto-generated documentation here: [https://drift-labs.github.io/driftpy/]

## Connection

  ```typescript
import {Connection} from "@solana/web3.js";

// the default RPC for devnet is `https://api.devnet.solana.com`
const connection = new Connection('https://api.mainnet-beta.solana.com');
```

```python
from solana.rpc.async_api import AsyncClient

url = 'https://api.mainnet-beta.solana.com' # replace w/ any rpc
connection = AsyncClient(url)
```

The connection object is used to send transactions to the Solana blockchain. It is used by the DriftClient to send transactions to the blockchain.

## Wallet

  ```typescript
import {Wallet, loadKeypair} from "@drift-labs/sdk";

const keyPairFile = '~/.config/solana/my-keypair.json';
const wallet = new Wallet(loadKeypair(privateKeyFile));
```
```python
from solana.keypair import Keypair
from anchorpy import Wallet
import os
import json

key_pair_file = '~/.config/solana/my-keypair.json'
with open(os.path.expanduser(key_pair_file), 'r') as f: secret = json.load(f) 
kp = Keypair.from_secret_key(bytes(secret))
wallet = Wallet(kp)
```

The wallet used to sign solana transactions. The wallet can be created from a private key or from a keypair file.

Make sure this wallet has some SOL first. SOL is used to pay for transactions and is required as rent for account intializations.

## Client Initialization

```typescript
import {Connection} from "@solana/web3.js";
import {Wallet, loadKeypair, DriftClient} from "@drift-labs/sdk";

const connection = new Connection('https://api.mainnet-beta.solana.com');

const keyPairFile = '~/.config/solana/my-keypair.json';
const wallet = new Wallet(loadKeypair(privateKeyFile))

const driftClient = new DriftClient({
  connection,
  wallet,
  env: 'mainnet-beta',
});

driftClient.subscribe();
```
```python
  import driftpy
  from anchorpy import Wallet, Provider
  from driftpy.constants.config import configs
  from driftpy.clearing_house import ClearingHouse

  # set connection and wallet
  # ...
  provider = Provider(connection, wallet)
  config = configs['mainnet'] # or devnet
  drift_client = ClearingHouse.from_config(config, provider)
```

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

## User Initialization

<aside class="notice">
  initializing a new drift user account requires some rent (~.035 SOL). this can be reclaimed upon deletion.
</aside>

```typescript
const [txSig, userPublickKey] = await driftClient.initializeUser(
  0,
  "toly"
);
```

```python
# todo: cannot init with name
tx_sig = await drift_client.intialize_user(0)  
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| subAccountId | The sub account id for the new user account.  | Yes | 0 |
| name   | Display name for the user account    | Yes | Main Account |
| referrerInfo   | The address of the referrer and referrer stats accounts | Yes | |

Sub account ids are monotonic. The first user account created will have sub account id 0, the second will have sub account id 1, etc.
The next sub account id can be found by calling `driftClient.getNextSubAccountId()`.

## Updating User
Users accounts can update names, set custom max intial margin ratio, enable margin trading, and add a delegate account.

```typescript
const subaccountId = 0;

// set max 1x intiial leverage
await this.driftClient.updateUserCustomMarginRatio(
  MARGIN_PRECISION, 
  subaccountId
);

// enable spot margin trading
await this.driftClient.updateUserMarginTradingEnabled(
  true,
  subaccountId
);

// add a delegate this user account
await this.driftClient.updateUserDelegate(
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

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| subAccountId | The sub account to switch to  | No | 0 |
| authority   | The authority of the sub account you're signing for. Only needed for delegate accounts | Yes | Current authority |

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

driftClient.deposit(
  amount,
  marketIndex,
  associatedTokenAccount,
);
```

```python
from driftpy.accounts import get_spot_market_account
from spl.token.instructions import get_associated_token_address

spot_market_index = 0 # USDC
spot_market = await get_spot_market_account(drift_client.program, spot_market_index)
user_token_account = get_associated_token_address(drift_client.authority, spot_market.mint)
amount = 100 * QUOTE_PRECISION # $100

tx_sig = await drift_client.deposit(amount, spot_market_index, user_token_account)
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| amount | The amount to deposit in spot market's token mint precision  | No | |
| marketIndex   | The spot market index you're depositing into | No | |
| associatedTokenAccount   | The public key of the token account you're depositing from. For sol, it can be the wallet's public key | No | |
| subAccountId | The sub account you're depositing too  | Yes | active sub account |
| reduceOnly | Whether the deposit should only reduce borrow  | Yes | false |

## Withdrawing

```typescript
const marketIndex = 0;
const amount = driftClient.convertToSpotPrecision(marketIndex, 100);
const associatedTokenAccount = await driftClient.getAssociatedTokenAccount(marketIndex);

driftClient.withdraw(
  amount,
  marketIndex,
  associatedTokenAccount,
);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| amount | The amount to withdraw in spot market's token mint precision  | No | |
| marketIndex   | The spot market index you're withdrawing from | No | |
| associatedTokenAccount   | The public key of the token account you're withdrawing to. For sol, it can be the wallet's public key | No | |
| reduceOnly | Whether the withdraw should only decrease a deposit and block a new borrow | Yes | false |

Withdrawing can lead to a borrow if the user has no deposits in the market and the user has enough margin to cover it.

## Transferring Deposits

```typescript
const marketIndex = 0;
const amount = driftClient.convertToSpotPrecision(marketIndex, 100);
const fromSubAccountId = 0;
const toSubAccountId = 1;

driftClient.transferDeposit(
  amount,
  marketIndex,
  fromSubAccountId,
  toSubAccountId,
);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| amount | The amount to transfer in spot market's token mint precision  | No | |
| marketIndex   | The spot market index you're transferring deposits in | No | |
| fromSubAccountId | The sub account you're withdrawing from  | No | |
| toSubAccountId | The sub account you're depositing too  | No | |

## Order Types

MARKET, LIMIT, ORACLE orders all support auction parameters.

| Type	| Description |
| ----- | ----------- | 
| MARKET |	Market order. |
| LIMIT |	Limit order. |
| TRIGGER_MARKET |	Stop / Take-profit market order. |
| TRIGGER_LIMIT |	 Stop / Take-profit limit order. |
| ORACLE	| Market order using oracle offset for auction parameters. |

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

subaccount_id = 0
market_index = 0

# place order to long 1 SOL-PERP @ $21.88 (post only)
bid_params = OrderParams(
            order_type=OrderType.LIMIT(),
            market_type=MarketType.PERP(),
            direction=PositionDirection.LONG(),
            user_order_id=0,
            base_asset_amount=int(1 * BASE_PRECISION),
            price=21.88 * PRICE_PRECISION,
            market_index=market_index,
            reduce_only=False,
            post_only=PostOnlyParams.TRY_POST_ONLY(),
            immediate_or_cancel=False,
            trigger_price=0,
            trigger_condition=OrderTriggerCondition.ABOVE(),
            oracle_price_offset=0,
            auction_duration=None,
            max_ts=None,
            auction_start_price=None,
            auction_end_price=None,
        )
await drift_client.get_place_perp_order(bid_order_params, subaccount_id)
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderType | The type of order e.g. market, limit  | No | |
| marketIndex | The market to place order in  | No | |
| direction | The direction of order e.g. long (bid) or short (ask)  | No | |
| baseAssetAmount | The amount of base asset to buy or sell  | No | |
| marketType | The type of market order is for e.g. PERP or SPOT  | Yes | PERP |
| price | The limit price for order | Yes | 0 |
| userOrderId | Unique order id specified by user| Yes | 0 |
| reduceOnly | If the order can only reduce positions| Yes | false |
| postOnly | If the order can only be a maker | Yes | false |
| triggerPrice | at what price order is triggered. only applicable for triggerMarket and triggerLimit orders | Yes | |
| triggerCondition | whether order is triggered above or below triggerPrice. only applicable for triggerMarket and triggerLimit orders | Yes | |
| oraclePriceOffset | priceOffset for oracle derived limit price. only applicable for limit and oracle orders  | Yes | |
| auctionDuration | how many slots the auction lasts. only applicable for market and oracle orders | Yes | |
| auctionStartPrice | the price the auction starts at | Yes | |
| auctionEndPrice | the price the auction ends at | Yes | |
| maxTs | the max timestamp (on-chain unix timestamp) before the order expires | Yes | |

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

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderType | The type of order e.g. market, limit  | No | |
| marketIndex | The market to place order in  | No | |
| direction | The direction of order e.g. long (bid) or short (ask)  | No | |
| baseAssetAmount | The amount of base asset to buy or sell  | No | |
| marketType | The type of market order is for e.g. PERP or SPOT  | Yes | SPOT |
| price | The limit price for order | Yes | 0 |
| userOrderId | Unique order id specified by user| Yes | 0 |
| reduceOnly | If the order can only reduce positions| Yes | false |
| postOnly | If the order can only be a maker | Yes | false |
| triggerPrice | at what price order is triggered. only applicable for triggerMarket and triggerLimit orders | Yes | |
| triggerCondition | whether order is triggered above or below triggerPrice. only applicable for triggerMarket and triggerLimit orders | Yes | |
| oraclePriceOffset | priceOffset for oracle derived limit price. only applicable for limit and oracle orders  | Yes | |
| auctionDuration | how many slots the auction lasts. only applicable for market and oracle orders | Yes | |
| auctionStartPrice | the price the auction starts at | Yes | |
| auctionEndPrice | the price the auction ends at | Yes | |
| maxTs | the max timestamp before the order expires | Yes | |

## Canceling Order

```typescript

const orderId = 1;
await driftClient.cancelOrder(orderId);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderId | The order being canceled  | No | |

## Canceling Order By User Order Id

```typescript

const userOrderId = 1;
await driftClient.cancelOrderByUserOrderId(userOrderId);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| userOrderId | Unique order id specified by user when order was placed  | No | |

## Cancel Orders

```typescript

const marketType = MarketType.PERP;
const marketIndex = 0;
const direction = PositionDirection.LONG;
await driftClient.cancelOrders(marketType, marketIndex, direction);
```

``` python
subaccount_id = 0
await drift_client.cancel_orders(subaccount_id) # cancels all orders
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketType | The market type of orders to cancel. Must be set if marketIndex set | Yes | |
| marketIndex | The market index of orders to cancel. Must be set if marketType set | Yes | |
| direction | The direction of orders to cancel. | Yes | |

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

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| cancelOrderParams | Parameters for cancel orders instruction | | |
| placeOrderParams | Parameters for place order instructions | | |

To cancel all orders, do not set any parameters.

## Modifying Order

```typescript

const updateParams = {
  orderId: 1,
  newBaseAssetAmount: driftClient.convertToPerpPrecision(200),
}

await driftClient.modifyOrder(orderParams);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderId | The order id of order to modify  | No | |
| newBaseAssetAmount | The amount of base asset to buy or sell  | Yes | |
| newDirection | The direction of order e.g. long (bid) or short (ask)  | Yes | |
| newLimitPrice | The limit price for order | Yes | |
| reduceOnly | If the order can only reduce positions| Yes | |
| postOnly | If the order can only be a maker | Yes | |
| newTriggerPrice | at what price order is triggered. only applicable for triggerMarket and triggerLimit orders | Yes | |
| newTriggerCondition | whether order is triggered above or below triggerPrice. only applicable for triggerMarket and triggerLimit orders | Yes | |
| newOraclePriceOffset | priceOffset for oracle derived limit price. only applicable for limit and oracle orders  | Yes | |
| auctionDuration | how many slots the auction lasts. only applicable for market and oracle orders | Yes | |
| auctionStartPrice | the price the auction starts at | Yes | |
| auctionEndPrice | the price the auction ends at | Yes | |
| maxTs | the max timestampe before the order expires | Yes | |

Modify order cancels and places a new order.

## Modifying Order By User Order Id

```typescript

const updateParams = {
  userOrderId: 1,
  newBaseAssetAmount: driftClient.convertToPerpPrecision(200),
}

await driftClient.modifyOrder(orderParams);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| userOrderId | The user order id of order to modify  | No | |
| newBaseAssetAmount | The amount of base asset to buy or sell  | Yes | |
| newDirection | The direction of order e.g. long (bid) or short (ask)  | Yes | |
| newLimitPrice | The limit price for order | Yes | |
| reduceOnly | If the order can only reduce positions| Yes | |
| postOnly | If the order can only be a maker | Yes | |
| newTriggerPrice | at what price order is triggered. only applicable for triggerMarket and triggerLimit orders | Yes | |
| newTriggerCondition | whether order is triggered above or below triggerPrice. only applicable for triggerMarket and triggerLimit orders | Yes | |
| newOraclePriceOffset | priceOffset for oracle derived limit price. only applicable for limit and oracle orders  | Yes | |
| auctionDuration | how many slots the auction lasts. only applicable for market and oracle orders | Yes | |
| auctionStartPrice | the price the auction starts at | Yes | |
| auctionEndPrice | the price the auction ends at | Yes | |
| maxTs | the max timestampe before the order expires | Yes | |

Modify order cancels and places a new order.

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

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| settleeUserAccountPublicKey | User address you're settling pnl for | No | |
| settleeUserAccount | User account data you're settling pnl for | No | |
| marketIndex | Market index for the perp market  | No | |

## Get Spot Market Account

```typescript
const marketIndex = 1;
const spotMarketAccount = driftClient.getSpotMarketAccount(marketIndex);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | The market index for the spot market | No | |

## Get Perp Market Account

```typescript
const marketIndex = 0;
const perpMarketAccount = driftClient.getPerpMarketAccount(marketIndex);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | The market index for the perp market | No | |

# User

## Get User

```typescript
   const user = await driftClient.getUser();
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| subAccountId | The sub account id of user to get  | Yes | active sub account |
| authority | The authority of user to get. Only necessary if using multiple delegate accounts | Yes | current authority |

## Getting Deposit/Borrow Amounts

```typescript
const marketIndex = 0;

const tokenAmount = user.getTokenAmount(
  marketIndex,
);

const isDeposit = tokenAmount.gte(new BN(0));
const isBorrow = tokenAmount.lt(new BN(0));
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | Market index for the spot market  | No | |

If token amount is greater than 0, it is a deposit. If less than zero, it is a borrow.

## Get Perp Position
```typescript
const marketIndex = 0;

const baseAssetAmount = user.getPerpPosiiton(
  marketIndex,
)?.baseAssetAmount;

const isLong = baseAssetAmount.gte(new BN(0));
const isShort = baseAssetAmount.lt(new BN(0));
```


| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | Market index for the perp market  | No | |

If base amount is greater than 0, it is a long. If less than zero, it is a short.

## Get Order
```typescript
const orderId = 1;

const order = user.getOrder(
  orderId,
);
```


| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderId | Order id for the order you're getting | No | |

## Get Order By User Order Id
```typescript
const userOrderId = 1;

const order = user.getOrderByUserOrderId(
  userOrderId,
);
```


| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| userOrderId | User order id for the order you're getting | No | |

## Get Open Orders
```typescript
const order = user.getOpenOrders();
```

## Get Unrealized Perp Pnl

  ```typescript
  const pnl = await user.getUnrealizedPNL();
  ```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| withFunding | Whether to included unsettled funding payments  | Yes | false |
| marketIndex | Whether to only return pnl for specific market  | Yes | |


## Get Unrealized Funding Pnl

  ```typescript
  const pnl = await user.getUnrealizedFundingPNL();
  ```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketIndex | Whether to only return pnl for specific market  | Yes | |

## Get Total Collateral

  ```typescript
  const totalCollateral = await user.getTotalCollateral();
  ```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marginCategory | Initial or Maintenance  | Yes | Initial |

Asset weights vary based on whether you're checking the initial or maintenance margin requirement. Initial is used for initial leverage extension, maintenance for determining liquidations.

## Get Margin Requirement

  ```typescript
  const marginRequirement = await user.getMarginRequirement();
  ```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marginCategory | Initial or Maintenance  | Yes | Initial |

Liability weights (for borrows) and margin ratios (for perp positions) vary based on whether you're checking the initial or maintenance margin requirement. Initial is used for initial leverage extension, maintenance for determining liquidations.

## Get Free Collateral

  ```typescript
  const freeCollateral = await user.getFreeCollateral();
  ```

Free collateral is the difference between your total collateral and your initial margin requirement.

## Get Leverage

  ```typescript
  const leverage = await user.getLeverage();
  ```

Leverage is the total liability value (borrows plus total perp position) divided by net asset value (total assets plus total liabilities)



# Orderbook

## Slot Subscription

```typescript
   import {Connection} from "@solana/web3.js";
   import {SlotSubscriber} from "@drift-labs/sdk";

   const connection = new Connection("https://api.mainnet-beta.solana.com");
   const slotSubscriber = new SlotSubscriber(connection);

   await slotSubscriber.subscribe();
   const slot = slotSubscriber.getSlot();

   slotSubscriber.eventEmitter.on('newSlot', async (slot) => {
      console.log('new slot', slot);
   });
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| connection | Connection object specifying solana rpc url   | No | |

The slot subscriber subscribes to the latest slot and updates the slot value every time a new slot is received. The state of the orderbook is dependent on the slot value, so to build the orderbook you must keep track of the slot value.

## User Subscription

```typescript
   import {Connection} from "@solana/web3.js";
   import {DriftClient, UserMap, Wallet, loadKeypair} from "@drift-labs/sdk";

   const connection = new Connection("https://api.mainnet-beta.solana.com");

   const keyPairFile = '~/.config/solana/my-keypair.json';
   const wallet = new Wallet(loadKeypair(privateKeyFile))
   
   const driftClient = new DriftClient({
     connection,
     wallet,
     env: 'mainnet-beta',
   });

   await driftClient.subscribe();
   
   const includeIdleUsers = false;
   const userMap = new UserMap(driftClient, {type: 'websocket'}, false);
   await userMap.subscribe();
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| driftClient | DriftClient object | No | |
| accountSubscription | Whether to use websocket or polling to subscribe to users | No | |
| includeIdle | Whether to include idle users. An idle user has had no orders, perp position or borrow for 7 days  | Yes | |

Orders are stored on user accounts. To reconstruct the orderbook, you must keep track of the user accounts that have orders. 
The user map subscribes to user account updates.  

## Orderbook Subscription

```typescript
import {DLOBSubscriber} from "@drift-labs/sdk";

// on-chain subscription to users
const userMap = new UserMap(driftClient, {type: 'websocket'}, false);
await userMap.subscribe();

 const dlobSubscriber = new DLOBSubscriber({
    driftClient,
    dlobSource: userMap,
    slotSource: slotSubscriber,
    updateFrequency: 1000,
 });
 
await dlobSubscriber.subscribe();
```

```typescript
import {DLOBApiClient, DLOBSubscriber} from "@drift-labs/sdk";

 // Polling from api
 const dlobApiClient = new DLOBApiClient({
   url: 'https://dlob.drift.trade/orders/idlWithSlot',
 });

 const dlobSubscriber = new DLOBSubscriber({
    driftClient,
    dlobSource: dlobApiClient,
    slotSource: slotSubscriber, 
    updateFrequency: 1000,
 });

 await dlobSubscriber.subscribe();
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| driftClient | DriftClient object | No | |
| dlobSource | Where to build the orderbook from. Can subscribe to user accounts on-chain using UserMap or request DLOB from api using DLOBApiClient | No | |
| slotSource | Where to get slot from | No | |
| updateFrequency | How often to rebuild the orderbook from the dlobSource in milliseconds | No | |

## Get L2 Orderbook

```typescript
const l2 = dlobSubscriber.getL2({
  marketName: 'SOL-PERP',
  depth: 50,
});
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketName | The market name of the orderbook to get. If not set, marketIndex and marketType must be set | Yes | |
| marketIndex | The market index of the orderbook to get. If not set, marketName must be set | Yes | |
| marketType | The market type of the orderbook to get. If not set, marketName must be set | Yes | |
| depth | The depth of the orderbook to get | Yes | 10 |
| includeVamm | Whether to include vAMM | Yes | false |
| fallbackL2Generators | L2OrderbookGenerators for fallback liquidity e.g. vAmm, openbook, phoenix. Unnecessary if includeVamm is true | Yes | |

The L2 orderbook is an aggregate of drift dlob orders and, optionally, fallback liquidity.

## Get L3 Orderbook

```typescript
const l3 = dlobSubscriber.getL3({
  marketName: 'SOL-PERP',
});
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketName | The market name of the orderbook to get. If not set, marketIndex and marketType must be set | Yes | |
| marketIndex | The market index of the orderbook to get. If not set, marketName must be set | Yes | |
| marketType | The market type of the orderbook to get. If not set, marketName must be set | Yes | |

The L3 orderbook contains every maker order on drift dlob, including the address for the user that placed the order.

# Numerical Precisions

| Value   | Precision | Constant |
| ----------- | ----------- | -------- | ------- |
| perp base asset amount | 1e9 | BASE_PRECISION |
| perp quote asset amount | 1e6 | QUOTE_PRECISION |
| price | 1e6 | PRICE_PRECISION |
| funding rate | 1e9 |  |
| spot token amount | derived from token mint's decimals (USDC is 1e6, SOL is 1e9) | |
| margin ratio | 1e4 | MARGIN_PRECISION |
| asset/liability weight | 1e4 | SPOT_WEIGHT_PRECISION |
