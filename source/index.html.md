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
  programID: new PublicKey('dRiftyHA39MWEi3m9aunc5MzRF1JYuBsbn6VPcn33UH'),
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
| programId   | Drift program id      | No | |
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
