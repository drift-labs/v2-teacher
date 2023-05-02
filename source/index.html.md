---
title: protocol-v2 API

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - typescript
  - python
  - shell

toc_footers:
  - <a href='https://github.com/drift-labs/protocol-v2/releases/'> release history </a>
  - <a href='https://docs.drift.trade/'> Read more about Drift Protocol in Archbee</a>
includes:
  - apiendpoints
  - historicaldata
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for drift protocol-v2
---


# Terms of Use
By using any API provided by Drift Labs, you agree to the [Terms of Use](https://docs.drift.trade/terms-of-use). If you do not agree to the foregoing, then do not use any such API.

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
  It is strictly against the [Terms of Use](https://docs.drift.trade/terms-of-use) to use these interfaces from a Restricted Territory.
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

auto-generated documentation here: https://drift-labs.github.io/protocol-v2/sdk/

## Python
Install driftpy from PyPI using pip:

`pip install driftpy`

auto-generated documentation here: https://drift-labs.github.io/driftpy/

## Connection

  ```typescript
import {Connection} from "@solana/web3.js";

const connection = new Connection('https://api.mainnet-beta.solana.com');
````

The connection object is used to send transactions to the Solana blockchain. It is used by the DriftClient to send transactions to the blockchain.

## Wallet

  ```typescript
import {Wallet, loadKeypair} from "@drift-labs/sdk";

const keyPairFile = '~/.config/solana/my-keypair.json';
const wallet = new Wallet(loadKeypair(privateKeyFile));
````

The wallet used to sign solana transactions. The wallet can be created from a private key or from a keypair file.

## Client Initialization

```typescript
import {DriftClient} from "@drift-labs/sdk";

const driftClient = new DriftClient({
  connection,
  wallet,
  env: 'mainnet-beta',
});
```
```python
  import driftpy;
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

```typescript
const [txSig, userPublickKey] = await driftClient.initializeUser(
  0,
  "toly"
);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| subAccountId | The sub account id for the new user account.  | Yes | 0 |
| name   | Display name for the user account    | Yes | Main Account |
| referrerInfo   | The address of the referrer and referrer stats accounts | Yes | |

Sub account ids are monotonic. The first user account created will have sub account id 0, the second will have sub account id 1, etc.
The next sub account id can be found by calling `driftClient.getNextSubAccountId()`.

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

## Depositing

```typescript
const marketIndex = 0;
const amount = driftClient.convertToSpotPrecision(marketIndex, 100);
const associatedTokenAccount = await driftClient.getAssociatedTokenAccount(marketIndex);

driftClient.deposit(
  amount,
  marketIndex,
  associatedTokenAccount,
);
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

## Getting Deposit/Borrow Amounts

```typescript
const marketIndex = 0;

const tokenAmount = driftClient.getTokenAmount(
  amount,
);

const isDeposit = tokenAmount.gte(new BN(0));
const isBorrow = tokenAmount.lt(new BN(0));
```

If token amount is greater than 0, it is a deposit. If less than zero, it is a borrow.

## Placing Perp Order

```typescript

const orderParams = {
  orderType: OrderType.LIMIT, 
  marketIndex: 0,
  direction: PositionDirection.LONG,
  baseAssetAmount: driftClient.convertToPerpPrecision(100),
  price: driftClient.convertToPricePrecision(100),
}

await driftClient.placePerpOrder(orderParams);
```

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| orderType | The type of order e.g. market, limit  | No | |
| marketIndex | The market to place order in  | No | |
| direction | The direction of order e.g. long (bid) or short (ask)  | No | |
| baseAssetAmount | The amount of base asset to buy or sell  | No | |
| marketType | The type of market order is for e.g. perp or spot  | Yes | perp |
| price | The limit price for order | Yes | 0 |
| reduceOnly | If the order can only reduce positions| Yes | false |
| postOnly | If the order can only be a maker | Yes | false |
| triggerPrice | at what price order is triggered. only applicable for triggerMarket and triggerLimit orders | Yes | |
| triggerCondition | whether order is triggered above or below triggerPrice. only applicable for triggerMarket and triggerLimit orders | Yes | |
| oraclePriceOffset | priceOffset for oracle derived limit price. only applicable for limit and oracle orders  | Yes | |
| auctionDuration | how many slots the auction lasts. only applicable for market and oracle orders | Yes | |
| auctionStartPrice | the price the auction starts at | Yes | |
| auctionEndPrice | the price the auction ends at | Yes | |
| maxTs | the max timestampe before the order expires | Yes | |

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
| marketType | The type of market order is for e.g. perp or spot  | Yes | spot |
| price | The limit price for order | Yes | 0 |
| reduceOnly | If the order can only reduce positions| Yes | false |
| postOnly | If the order can only be a maker | Yes | false |
| triggerPrice | at what price order is triggered. only applicable for triggerMarket and triggerLimit orders | Yes | |
| triggerCondition | whether order is triggered above or below triggerPrice. only applicable for triggerMarket and triggerLimit orders | Yes | |
| oraclePriceOffset | priceOffset for oracle derived limit price. only applicable for limit and oracle orders  | Yes | |
| auctionDuration | how many slots the auction lasts. only applicable for market and oracle orders | Yes | |
| auctionStartPrice | the price the auction starts at | Yes | |
| auctionEndPrice | the price the auction ends at | Yes | |
| maxTs | the max timestampe before the order expires | Yes | |

