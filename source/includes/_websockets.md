# Websockets

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