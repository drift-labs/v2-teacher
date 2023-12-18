# Websocket API

Drift currently offers websocket streaming for two data streams: orderbooks and trades. The data streams are constructed from blockhain data. More data sources and user specific feeds will be added as this functionality is expanded.

mainnet-beta: `wss://dlob.drift.trade/ws`

devnet: `wss://master.dlob.drift.trade/ws`

## Subscribing

Clients must send subscribe messages over the websocket connection in order to start receiving messages.

### Orderbook

Each market currently requires its own subscribe message. The two examples below show subscribe messages for a perp and spot market:

#### Perp markets
`
{
  type: 'subscribe',
  marketType: 'perp',
  channel: 'orderbook',
  market: 'SOL-PERP'
}
`

#### Spot markets
`
{
  type: 'subscribe',
  marketType: 'spot',
  channel: 'orderbook',
  market: 'SOL'
}
`

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
  type: 'subscribe',
  marketType: 'perp',
  channel: 'trades',
  market: 'SOL-PERP'
}
`

#### Spot markets
`
{
  type: 'subscribe',
  marketType: 'spot',
  channel: 'trades',
  market: 'SOL'
}
`

## Unsubscribing

To unsubscribe to a channel, send a subscribe-like message, with the message type set to `unsubscribe`. Unsubscribe requests to channels not previously subscribed to will have no impact.

## Liveness measure

To alleviate backpressure on websocket servers, drift websockets stop sending messages to clients that have more than 50 messages unproccessed in their buffer, until the buffer is cleared and the messages are processed. We recommend listening for heartbeat messages from the server to determine if your client is still receiving messages. 

Heartbeat messages are sent every 5 seconds and take the following form:
`
  {channel: 'heartbeat'}
`

### Ping/pong

No ping messages are sent from the websocket server. Unsolicited ping messages are allowed and will receive pongs back.

## Feedback

For any feedback on websockets, please reach out on discord under the channel research-and-dev-chat
