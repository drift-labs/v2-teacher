# Orderbook/Trades (DLOB Server)

Drift runs a [`dlob-server`](https://github.com/drift-labs/dlob-server) to reduce the RPC load on UI users and traders. You can access this server (or run your own!) instead of [maintaing an order book from the blockchain](#orderbook-blockchain).


mainnet-beta: [https://dlob.drift.trade/](https://dlob.drift.trade/)
devnet: [https://master.dlob.drift.trade/](https://master.dlob.drift.trade/)

All endpoints follow the same query parameter scheme to specify a market:

| Parameter   | Description | Optional | Default |
| ----------- | ----------- | -------- | ------- |
| marketName | The market name of the orderbook to get. If not set, marketIndex and marketType must be set | Yes | |
| marketIndex | The market index of the orderbook to get. If not set, marketName must be set | Yes | |
| marketType | The market type of the orderbook to get. If not set, marketName must be set | Yes | |

## `GET /l2`
## `GET /l3`


Returns an L2 (aggregate price levels) or L3 (individual orders) orderbook for the specificed market.

| Parameter     | Description                                      | Optional | Default    | L2 only |
| ------------- | ------------------------------------------------ | -------- | ---------- | ------- |
| depth         | Number of records to return per side             | Yes      | all orders | Yes     |
| includeVamm   | `true` to include vAMM liquidity in the response | Yes      | `false`    | Yes     |
| includeOracle | `true` to include oracle data with the response  | Yes      | `false`    | No      |

Example: [https://dlob.drift.trade/l2?marketName=JTO-PERP&depth=10&includeOracle=true&includeVamm=true](https://dlob.drift.trade/l2?marketName=JTO-PERP&depth=10&includeOracle=true&includeVamm=true)

Example: [https://dlob.drift.trade/l3?marketName=JTO-PERP&includeOracle=true](https://dlob.drift.trade/l3?marketName=JTO-PERP&includeOracle=true)

## `GET /topMakers`

Returns the top makers (currently returns an exhaustive list) for a given market (useful for `place_and_take` orders).

| Parameter        | Description                                      | Optional | Default    |
| ---------------- | ------------------------------------------------ | -------- | ---------- |
| side             | Side to return makers for (`bid` or `ask`)       | No       |            |
| limit            | Limit number of makers to return                 | Yes      | all        |
| includeUserStats | `true` to include full UserStats                 | Yes      | `false`    |

Example: [https://dlob.drift.trade/topMakers?marketName=JTO-PERP&side=bid&limit=5](https://dlob.drift.trade/topMakers?marketName=JTO-PERP&side=bid&limit=5)

## Websocket

The mainnet-beta websocket endpoint is: [wss://dlob.drift.trade/ws](wss://dlob.drift.trade/ws)

Drift currently offers websocket streaming for two data streams: orderbooks and trades. The data streams are constructed from blockhain data. More data sources and user specific feeds will be added as this functionality is expanded.

mainnet-beta: [wss://dlob.drift.trade/ws](wss://dlob.drift.trade/ws)

devnet: [wss://master.dlob.drift.trade/ws](wss://master.dlob.drift.trade/ws)

## Websocket - Subscribing

Clients must send subscribe messages over the websocket connection in order to start receiving messages.

### Orderbook

Each market currently requires its own subscribe message. The two examples below show subscribe messages for a perp and spot market:

#### Perp markets
`
{
  "type": "subscribe",
  "marketType": "perp",
  "channel": "orderbook",
  "market": "SOL-PERP"
}
`

#### Response
| Field       | Description                                                                                                                            |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------|
| channel     | Identifies the type of data being streamed. `orderbook_perp_0` indicates data for perp market 0 order book (SOL-PERP).                 |
| data        | Contains the actual order book data in JSON format. This field is a JSON string that needs to be parsed.                               |
| bids        | A list of bid orders in the order book, each representing an offer to buy.                                                             |
| price       | The price at which the bid is made, indicating the maximum price the buyer is willing to pay.                                          |
| size        | The size of the bid, indicating the quantity of the asset the buyer wishes to purchase.                                                |
| sources     | Indicates the origin or source of the bid, such as `vamm` (Virtual Automated Market Maker) or `dlob` (Decentralized Limit Order Book). |


#### Spot markets
`
{
  "type": "subscribe",
  "marketType": "spot",
  "channel": "orderbook",
  "market": "SOL"
}
`

#### Response
| Field       | Description                                                                                                                         |
|-------------|-------------------------------------------------------------------------------------------------------------------------------------|
| channel     | Identifies the type of data being streamed. i.e `orderbook_spot_1` indicates data for spot market 0 order book (SOL/USDC).          |
| data        | Contains the actual order book data in JSON format. This field is a JSON string that needs to be parsed.                            |
| bids        | A list of bid orders in the order book, each representing an offer to buy.                                                          |
| price       | The price at which the bid is made, indicating the maximum price the buyer is willing to pay.                                       |
| size        | The size of the bid, indicating the quantity of the asset the buyer wishes to purchase.                                             |
| sources     | Indicates the origin or source of the bid, such as `phoenix` or `serum`, representing different liquidity providers.                |


```typescript

import WebSocket from 'ws';
const ws = new WebSocket('wss://dlob.drift.trade/ws');

ws.on('open', async () => {
  console.log('Connected to the server');

  // Subscribe to orderbook data
  ws.send(JSON.stringify({ type: 'subscribe', marketType: 'perp', channel: 'orderbook', market: 'SOL-PERP' }));
  ws.send(JSON.stringify({ type: 'subscribe', marketType: 'spot', channel: 'orderbook', market: 'SOL' }));


  // Subscribe to trades data
  ws.send(JSON.stringify({ type: 'subscribe', marketType: 'perp', channel: 'trades', market: 'SOL-PERP' }));
});

ws.on('message', (data: WebSocket.Data) => {
  try {
    const message = JSON.parse(data.toString());
    console.log(`Received data from channel: ${JSON.stringify(message.channel)}`);
    // book and trades data is in message.data
  } catch (e) {
    console.error('Invalid message:', data);
  }
});

```

```python
import json
import websocket
import ssl

def on_message(ws, message):
    try:
        message = json.loads(message)
        print(f"Received data from channel: {json.dumps(message['channel'])}")
        # book and trades data is in message['data']
    except ValueError:
        print(f"Invalid message: {message}")

def on_error(ws, error):
    print(f"Error: {error}")

def on_close(ws, close_status_code, close_msg):
    print("### Closed ###")

def on_open(ws):
    print("Connected to the server")

    # Subscribe to orderbook data
    ws.send(json.dumps({'type': 'subscribe', 'marketType': 'perp', 'channel': 'orderbook', 'market': 'SOL-PERP'}))
    ws.send(json.dumps({'type': 'subscribe', 'marketType': 'spot', 'channel': 'orderbook', 'market': 'SOL'}))

    # Subscribe to trades data
    ws.send(json.dumps({'type': 'subscribe', 'marketType': 'perp', 'channel': 'trades', 'market': 'SOL-PERP'}))

if __name__ == "__main__":
    websocket.enableTrace(True)
    ws = websocket.WebSocketApp("wss://dlob.drift.trade/ws",
                                on_open=on_open,
                                on_message=on_message,
                                on_error=on_error,
                                on_close=on_close)

    ws.run_forever(sslopt={"cert_reqs": ssl.CERT_NONE})
```

#### Update frequency

Currently, orderbook websockets send messages every 1000ms. Connections that contain frequent updates are coming soon.

### Trades

Trades feed subscribe messages take a similar form to orderbook data, just change the channel.

#### Perp markets
`
{
  "type": "subscribe",
  "marketType": "perp",
  "channel": "trades",
  "market": "SOL-PERP"
}
`

#### Response
| Field                                      | Description                                                                                                         |
|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| channel                                    | Identifies the type of data being streamed. `trades_perp_0` indicates data for trades in perp market 0. (SOL-PERP)  |
| ts                                         | Timestamp of the trade.                                                                                             |
| marketIndex                                | Index of the market where the trade occurred.                                                                       |
| marketType                                 | Type of the market, here it's `perp` for perpetual.                                                                 |
| filler                                     | The address or identifier of the filler (taker) in the trade.                                                       |
| takerFee                                   | Fee paid by the taker in the trade.                                                                                 |
| makerFee                                   | Fee paid or received by the maker in the trade.                                                                     |
| quoteAssetAmountSurplus                    | Surplus amount in quote asset.                                                                                      |
| baseAssetAmountFilled                      | The amount of the base asset that was filled in this trade.                                                         |
| quoteAssetAmountFilled                     | The amount of the quote asset that was filled in this trade.                                                        |
| takerOrderId                               | Order ID of the taker's order, if available.                                                                        |
| takerOrderBaseAssetAmount                  | Base asset amount specified in the taker's order.                                                                   |
| takerOrderCumulativeBaseAssetAmountFilled  | Cumulative base asset amount filled in the taker's order.                                                           |
| takerOrderCumulativeQuoteAssetAmountFilled | Cumulative quote asset amount filled in the taker's order.                                                          |
| maker                                      | The address or identifier of the maker in the trade.                                                                |
| makerOrderId                               | Order ID of the maker's order.                                                                                      |
| makerOrderDirection                        | Direction of the maker's order (e.g., 'short' or 'long').                                                           |
| makerOrderBaseAssetAmount                  | Base asset amount specified in the maker's order.                                                                   |
| makerOrderCumulativeBaseAssetAmountFilled  | Cumulative base asset amount filled in the maker's order.                                                           |
| makerOrderCumulativeQuoteAssetAmountFilled | Cumulative quote asset amount filled in the maker's order.                                                          |
| oraclePrice                                | The oracle price at the time of the trade.                                                                          |
| txSig                                      | Transaction signature.                                                                                              |
| slot                                       | Slot number in which the trade occurred.                                                                            |
| action                                     | Type of action that occurred (e.g., 'fill').                                                                        |
| actionExplanation                          | Explanation of the action (e.g., 'orderFilledWithAmm' indicating order filled with Automated Market Maker).         |
| referrerReward                             | Reward amount for the referrer, if applicable.                                                                      |


#### Spot markets
`
{
  "type": "subscribe",
  "marketType": "spot",
  "channel": "trades",
  "market": "SOL"
}
`

#### Response
| Field                                      | Description                                                                                                              |
|--------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| channel                                    | Identifies the type of data being streamed. `trades_spot_1` indicates data for trades in spot market 0 (SOL/USDC).       |
| ts                                         | Timestamp of the trade.                                                                                                  |
| marketIndex                                | Index of the market where the trade occurred.                                                                            |
| marketType                                 | Type of the market, here it's `spot`.                                                                                    |
| filler                                     | The address or identifier of the filler (taker) in the trade.                                                            |
| takerFee                                   | Fee paid by the taker in the trade.                                                                                      |
| makerFee                                   | Fee paid or received by the maker in the trade.                                                                          |
| quoteAssetAmountSurplus                    | Surplus amount in quote asset.                                                                                           |
| baseAssetAmountFilled                      | The amount of the base asset that was filled in this trade.                                                              |
| quoteAssetAmountFilled                     | The amount of the quote asset that was filled in this trade.                                                             |
| taker                                      | The address or identifier of the taker in the trade.                                                                     |
| takerOrderId                               | Order ID of the taker's order, if available.                                                                             |
| takerOrderDirection                        | Direction of the taker's order (e.g., 'long').                                                                           |
| takerOrderBaseAssetAmount                  | Base asset amount specified in the taker's order.                                                                        |
| takerOrderCumulativeBaseAssetAmountFilled  | Cumulative base asset amount filled in the taker's order.                                                                |
| takerOrderCumulativeQuoteAssetAmountFilled | Cumulative quote asset amount filled in the taker's order.                                                               |
| maker                                      | The address or identifier of the maker in the trade.                                                                     |
| makerOrderId                               | Order ID of the maker's order.                                                                                           |
| makerOrderDirection                        | Direction of the maker's order (e.g., 'short').                                                                          |
| makerOrderBaseAssetAmount                  | Base asset amount specified in the maker's order.                                                                        |
| makerOrderCumulativeBaseAssetAmountFilled  | Cumulative base asset amount filled in the maker's order.                                                                |
| makerOrderCumulativeQuoteAssetAmountFilled | Cumulative quote asset amount filled in the maker's order.                                                               |
| oraclePrice                                | The oracle price at the time of the trade.                                                                               |
| txSig                                      | Transaction signature.                                                                                                   |
| slot                                       | Slot number in which the trade occurred.                                                                                 |
| action                                     | Type of action that occurred (e.g., 'fill').                                                                             |
| actionExplanation                          | Explanation of the action (e.g., 'orderFilledWithMatch' indicating order filled with a matching order).                  |
| referrerReward                             | Reward amount for the referrer, if applicable.                                                                           |


## Websocket - Unsubscribing

To unsubscribe to a channel, send a subscribe-like message, with the message type set to `unsubscribe`. Unsubscribe requests to channels not previously subscribed to will have no impact.
`
{
  "type": "unsubscribe",
  "marketType": "perp",
  "channel": "orderbook",
  "market": "SOL-PERP"
}
`

## Websocket - Liveness measure

To alleviate backpressure on websocket servers, drift websockets stop sending messages to clients that have more than 50 messages unproccessed in their buffer, until the buffer is cleared and the messages are processed. We recommend listening for heartbeat messages from the server to determine if your client is still receiving messages.

Heartbeat messages are sent every 5 seconds and take the following form:
`
  {"channel": "heartbeat"}
`

### Ping/pong

No ping messages are sent from the websocket server. Unsolicited ping messages are allowed and will receive pongs back.

