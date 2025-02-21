# Fastlane

Drift runs a Fastlane endpoint as a service. For a detailed description of Fastlane, please see the whitepaper here:

Endpoints:

mainnet-beta: [https://fastlane.drift.trade/](https://fastlane.drift.trade/)
devnet: [https://master.fastlane.drift.trade/](https://master.fastlane.drift.trade/)

## Takers: Signing Order Messages

Fastlane relies on takers signing messages to send orders. This happens under the hood in the UI, but for programmatic traders, the best way to do this is via the DriftClient. In order to render useful messages to wallet providers, the process of encoding OrderParmas is quite involved -- so we recommend using the DriftClient utility functions. See the typescript example on how to do this.

```typescript
const slot = await driftClient.connection.getSlot();
const direction =
  Math.random() > 0.5 ? PositionDirection.LONG : PositionDirection.SHORT;

const marketIndex =
  marketIndexes[Math.floor(Math.random() * marketIndexes.length)];

const oracleInfo = driftClient.getOracleDataForPerpMarket(marketIndex);
const highPrice = oracleInfo.price.muln(101).divn(100);
const lowPrice = oracleInfo.price;

const orderMessage = {
  signedMsgOrderParams: getMarketOrderParams({
    marketIndex,
    marketType: MarketType.PERP,
    direction,
    baseAssetAmount: driftClient
      .getPerpMarketAccount(marketIndex)!
      .amm.minOrderSize.muln(2),
    auctionStartPrice: isVariant(direction, "long") ? lowPrice : highPrice,
    auctionEndPrice: isVariant(direction, "long") ? highPrice : lowPrice,
    auctionDuration: 50,
  }),
  subAccountId: driftClient.activeSubAccountId,
  slot: new BN(slot),
  uuid: generateSignedMsgUuid(),
  stopLossOrderParams: null,
  takeProfitOrderParams: null,
};
const { orderParams: message, signature } =
  driftClient.signSignedMsgOrderParamsMessage(orderMessage);
```

### Sending orders (/orders)

To submit an order to Fastlane, submit a POST request to the /orders path.

| Parameter         | Description                                                                                                                                                                               | Optional | Default      |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------------ |
| marketIndex       | The market index for the order                                                                                                                                                            | No       |              |
| marketType        | The market type (must be set to "perp" for now)                                                                                                                                           | No       |              |
| message           | Base64 string of Signed Message Hex Buffer (see above section)                                                                                                                            | No       |              |
| signature         | Base64 string of order signature (see above section)                                                                                                                                      | No       |              |
| taker_pubkey      | The authority wallet of the taker                                                                                                                                                         | No       |              |
| signing_authority | The authority wallet that signed the order message (usually the same as taker_pubkey, different if signer is a delegate). If not provided, will assume taker_pubkey is the message signer | Yes      | taker_pubkey |

```typescript
const response = await axios.default.post(
  "https://fastlane.drift.trade/orders",
  {
    market_index: 0,
    market_type: "perp",
    message: message.toString(),
    signature: signature.toString("base64"),
    taker_pubkey: driftClient.wallet.publicKey.toBase58(),
  },
  {
    headers: {
      "Content-Type": "application/json",
    },
  }
);
```

## Makers: Subscribing to Orders Messages

Drift sdks offer a FastlaneOrderSubscriber as a wrapper around the Fastlane websocket to easily subscribe to fastlane orders. Traders can also subscribe directly to the websocket feed and gain access to trades that way.

To start receiving messages from the fastlane server, you must authenticate your connection with your wallet by signing a nonce sent by the server. This all happens under the hood in the FastlaneOrderSubscriber, but examples for how to do this for direct connections are given below

### Subscribing to Websocket Server Directly

WS Server URLs:

mainnet-beta: [wss://fastlane.drift.trade/ws](wss://fastlane.drift.trade/ws)
devnet: [wss://master.fastlane.drift.trade/ws](wss://master.fastlane.drift.trade/ws)

#### Authenticating

Once a connection has been established, the websocket server will send a message with a nonce. The message will take the shape of:

{
channel: 'auth',
nonce: 'XXXX'
}

See the typescript example for how to decode the nonce message and sign and send back to the server.

```typescript
import nacl from 'tweetnacl';

ws.on('message', async (data: WebSocket.Data) => {
    const message = JSON.parse(data.toString());

    if (message['channel'] === 'auth' && message['nonce'] != null) {
      const messageBytes = decodeUTF8(message['nonce']);
      const signature = nacl.sign.detached(messageBytes, keypair.secretKey);
      const signatureBase64 = Buffer.from(signature).toString('base64');
      ws.send(
        JSON.stringify({
          pubkey: keypair.publicKey.toBase58(),
          signature: signatureBase64,
        })
      );
    }
}
```

Upon successful authentication, you will receive back a message of the following form:

{
channel: 'auth',
message: 'Authenticated'
}

#### Subscribing/Unsubscribing to feeds

To subscribe or unsubscribe to a market, send a message to the server with the following params:

| Parameter   | Description                                     | Example   |
| ----------- | ----------------------------------------------- | --------- |
| action      | string value of 'subscribe' or 'unsubscribe     | subscribe |
| market_type | The market type (must be set to "perp" for now) | perp      |
| market_name | Name of the perp market                         | SOL-PERP  |

```typescript
ws.send(
  JSON.stringify({
    action: "subscribe",
    market_type: "perp",
    market_name: "ETH-PERP",
  })
);
```

#### Decoding WS server messages & making against fastlane orders

Messages from the websocket server have the following parameters:

| Parameter         | Description                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------- |
| market_type       | market type (perp/spot), will be perp for now                                               |
| market_index      | market index of the order                                                                   |
| order_message     | hex buffer of the order message signed by the taker (must be submitted to program to trade) |
| order_signature   | order signature base64 buffer (must be submitted to program to trade)                       |
| taker_authority   | taker wallet authority                                                                      |
| signing_authority | signing authority                                                                           |
| uuid              | taker uuid                                                                                  |
| ts                | timestamp when the fastlane server received the message                                     |

Most useful information about the order is within the order_message field. Similar to how encoding the message is involved, so is decoding the message. We recommend decoding the message using the drift client to use the program's decoder. See the example for more details.

Within the order_message are the following params:

| Parameter             | Description                                                                        |
| --------------------- | ---------------------------------------------------------------------------------- |
| signedMsgOrderParams  | OrderParams                                                                        |
| subAccountId          | subaccount of the taker -- use with taker_authority to derive taker's user account |
| uuid                  | taker order id                                                                     |
| slot                  | the start slot of the order's auction                                              |
| takeProfitOrderParams | optional take profit order params taker is sending to pair with order              |
| stopLossOrderParams   | optional stop loss order params taker is sending to pair with order                |

All of the above is used to derive important order information and then supply the required arugments to the drift client instruction in order to place and make against the fastlane order. See the example code on how to do this.

As with other orders on drift, in order to pass all of the necessary accounts to the program for the taker's margin check, it's necessary to pass the taker's user account to the drift client function that generates the place and make instruction. This can be fetched via an RPC request, or we recommend using a subscribed UserMap to have the account ready to go.

```typescript
ws.on("message", async (data: WebSocket.Data) => {
  const message = JSON.parse(data.toString());
  const order = message["order"];
  const signedMsgOrderParamsBufHex = Buffer.from(order["order_message"]);
  const signedMsgOrderParamsBuf = Buffer.from(order["order_message"], "hex");
  const {
    signedMsgOrderParams,
    subAccountId: takerSubaccountId,
  }: SignedMsgOrderParamsMessage =
    driftClient.decodeSignedMsgOrderParamsMessage(signedMsgOrderParamsBuf);

  const signingAuthority = new PublicKey(order["signing_authority"]);
  const takerAuthority = new PublicKey(order["taker_authority"]);
  const takerUserPubkey = await getUserAccountPublicKey(
    driftClient.program.programId,
    takerAuthority,
    takerSubaccountId
  );
  const takerUserAccount = (
    await userMap.mustGet(takerUserPubkey.toString())
  ).getUserAccount();

  const isOrderLong = isVariant(signedMsgOrderParams.direction, "long");
  const ixs = await driftClient.getPlaceAndMakeSignedMsgPerpOrderIxs(
    {
      orderParams: signedMsgOrderParamsBufHex,
      signature: Buffer.from(order["order_signature"], "base64"),
    },
    decodeUTF8(order["uuid"]),
    {
      taker: takerUserPubkey,
      takerUserAccount,
      takerStats: getUserStatsAccountPublicKey(
        driftClient.program.programId,
        takerUserAccount.authority
      ),
      signingAuthority,
    },
    getLimitOrderParams({
      marketType: MarketType.PERP,
      marketIndex: signedMsgOrderParams.marketIndex,
      direction: isOrderLong ? PositionDirection.SHORT : PositionDirection.LONG,
      baseAssetAmount: signedMsgOrderParams.baseAssetAmount.divn(2),
      price: isOrderLong
        ? signedMsgOrderParams.auctionStartPrice!.muln(99).divn(100)
        : signedMsgOrderParams.auctionEndPrice!.muln(101).divn(100),
      postOnly: PostOnlyParams.MUST_POST_ONLY,
      immediateOrCancel: true,
    })
  );
});
```

### Subscribing to Fastlane via FastlaneOrderSubscriber

Subscribing to markets using FastlaneOrderSubscriber abstracts away the authentication and subscription process by using the drift client passed to the subscriber. Pass the marketIndexes, driftEnv (mainnet-beta or devnet), UserMap, and DriftClient via the constructor to instantiate.

After instantiating, subscribe with a callback function that runs upon receiving an order.
