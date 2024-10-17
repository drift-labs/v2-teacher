# Orderbook (Blockchain)

The drift orderbook is a collection of all open orders on the Drift protocol. There is no single source of truth for the orderbook. The most up to date view of the orderbook is one where you track user orders and maintain the orderbook structure yourself. This section shows how to do that with the various SDKs.

## Dlob Source

This is the main source of orders for maintaing the orderbook.

## Dlob Source - `UserMap`

`UserMap` stores a complete map of all user accounts (idle users are commonly filtered ou).

```typescript
import {Connection} from "@solana/web3.js";
import {DriftClient, UserMap, Wallet, loadKeypair} from "@drift-labs/sdk";

const connection = new Connection("https://api.mainnet-beta.solana.com", 'confirmed');

const keyPairFile = `${process.env.HOME}/.config/solana/my-keypair.json`;
const wallet = new Wallet(loadKeypair(keyPairFile))

const driftClient = new DriftClient({
  connection,
  wallet,
  env: 'mainnet-beta',
});
await driftClient.subscribe();

// polling keep users updated with periodic calls to getProgramAccounts
// websocket keep users updated via programSubscribe
const subscriptionConfig:
  | {
    type: 'polling';
    frequency: number;
    commitment?: Commitment;
  } | {
    type: 'websocket';
    resubTimeoutMs?: number;
    commitment?: Commitment;
  } = {
    type: 'websocket',
    resubTimeoutMs: 30_000,
    commitment: stateCommitment,
  };

const userMap = new UserMap({
  driftClient,
  connection,
  subscriptionConfig,
  skipInitialLoad: false, // skips initial load of user accounts
  includeIdle: false, // filters out idle users
});

await userMap.subscribe();
```


```python
from solana.rpc.async_api import AsyncClient
from solders.keypair import Keypair
from anchorpy import Wallet
from driftpy.drift_client import DriftClient
from driftpy.keypair import load_keypair
from driftpy.user_map.user_map import UserMap
from driftpy.user_map.user_map_config import UserMapConfig, WebsocketConfig

connection = AsyncClient("https://api.mainnet-beta.solana.com")  # switch for your own rpc

keypair_file = ""  # path to keypair file

wallet = Wallet(load_keypair(keypair_file))

drift_client = DriftClient(
  connection,
  wallet,
  "mainnet"
)

await drift_client.subscribe()

user_map_config = UserMapConfig(drift_client, WebsocketConfig())
user_map = UserMap(user_map_config)

await user_map.subscribe()
```

## Dlob Source - `OrderSubscriber`

`OrderSubscriber` is a more efficient version of `UserMap`, only tracking user accounts that have orders.

```typescript
import {Connection} from "@solana/web3.js";
import {DriftClient, OrderSubscriber, Wallet, loadKeypair} from "@drift-labs/sdk";

const connection = new Connection("https://api.mainnet-beta.solana.com", 'confirmed');

const keyPairFile = `${process.env.HOME}/.config/solana/my-keypair.json`;
const wallet = new Wallet(loadKeypair(keyPairFile))

const driftClient = new DriftClient({
  connection,
  wallet,
  env: 'mainnet-beta',
});
await driftClient.subscribe();

const subscriptionConfig:
  | {
    type: 'polling',
    frequency: ORDERBOOK_UPDATE_INTERVAL,
    commitment: stateCommitment,
  }
  | {
    type: 'websocket',
    commitment: stateCommitment,
    resyncIntervalMs: WS_FALLBACK_FETCH_INTERVAL,
  } = {
    type: 'websocket',
    commitment: stateCommitment,
    resyncIntervalMs: WS_FALLBACK_FETCH_INTERVAL, // periodically resyncs the orders in case of missed websocket messages
  };

const orderSubscriber = new OrderSubscriber({
  driftClient,
  subscriptionConfig,
});

await orderSubscriber.subscribe();
```

## Orderbook Subscription

With a `DlobSource` you can then subscribe to the orderbook.

```typescript
import {DLOBSubscriber} from "@drift-labs/sdk";

 const dlobSubscriber = new DLOBSubscriber({
    driftClient,
    dlobSource: orderSubscriber, // or UserMap
    slotSource: orderSubscriber, // or UserMap
    updateFrequency: 1000,
 });

await dlobSubscriber.subscribe();
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| driftClient | DriftClient object | No | |
| dlobSource | Where to build the orderbook from. Can subscribe to user accounts on-chain using UserMap or request DLOB from api using DLOBApiClient | No | |
| slotSource | Where to get slot from | No | |
| updateFrequency | How often to rebuild the orderbook from the dlobSource in milliseconds | No | |


```python
from driftpy.dlob.dlob_subscriber import DLOBSubscriber
from driftpy.dlob.client_types import DLOBClientConfig
from driftpy.slot.slot_subscriber import SlotSubscriber

slot_subscriber = SlotSubscriber(drift_client)

config = DLOBClientConfig(drift_client, user_map, slot_subscriber, 1)
dlob_subscriber = DLOBSubscriber(config = config)

await dlob_subscriber.subscribe()
```

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| drift_client | DriftClient object | No | |
| dlob_source | Where to build the orderbook from. Can subscribe to user accounts on-chain using UserMap. | No | |
| slot_source | Where to get slot from | No | |
| update_frequency | How often to rebuild the orderbook from the dlob_source in milliseconds | No | |


## Get L2 Orderbook

```typescript
const l2 = dlobSubscriber.getL2({
  marketName: 'SOL-PERP',
  depth: 50,
});
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketName | The market name of the orderbook to get. If not set, marketIndex and marketType must be set | Yes | |
| marketIndex | The market index of the orderbook to get. If not set, marketName must be set | Yes | |
| marketType | The market type of the orderbook to get. If not set, marketName must be set | Yes | |
| depth | The depth of the orderbook to get | Yes | 10 |
| includeVamm | Whether to include vAMM | Yes | false |
| fallbackL2Generators | L2OrderbookGenerators for fallback liquidity e.g. vAmm, openbook, phoenix. Unnecessary if includeVamm is true | Yes | |

```python
l2 = dlob_subscriber.get_l2_orderbook_sync("SOL-PERP")
```

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| market_name | The market name of the orderbook to get. If not set, market_index and market_type must be set | Yes | |
| market_index | The market index of the orderbook to get. If not set, market_name must be set | Yes | |
| market_type | The market type of the orderbook to get. If not set, market_name must be set | Yes | |
| depth | The depth of the orderbook to get | Yes | 10 |
| include_vamm | Whether to include vAMM | Yes | false |
| num_vamm_orders | Number of orders to include from the vAMM.  If not provided, depth is used. | Yes | |
| fallback_l2_generators | L2OrderbookGenerators for fallback liquidity e.g. vAmm, openbook, phoenix. Unnecessary if includeVamm is true | Yes | |

The L2 orderbook is an aggregate of drift dlob orders and, optionally, fallback liquidity.

## Get L3 Orderbook

```typescript
const l3 = dlobSubscriber.getL3({
  marketName: 'SOL-PERP',
});
```

### TypeScript
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketName | The market name of the orderbook to get. If not set, marketIndex and marketType must be set | Yes | |
| marketIndex | The market index of the orderbook to get. If not set, marketName must be set | Yes | |
| marketType | The market type of the orderbook to get. If not set, marketName must be set | Yes | |

```python
l3 = dlob_subscriber.get_l3_orderbook_sync("SOL-PERP")
```

### Python
| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| market_name | The market name of the orderbook to get. If not set, market_index and market_type must be set | Yes | |
| market_index | The market index of the orderbook to get. If not set, market_name must be set | Yes | |
| market_type | The market type of the orderbook to get. If not set, market_name must be set | Yes | |

The L3 orderbook contains every maker order on drift dlob, including the address for the user that placed the order.
