# Example: Bots

<aside class="notice">
This examples should be used as code reference only. Be cautious running any unreviewed code, especially if not on devnet.
</aside>

## Typescript

[Keeper-bots-v2](https://github.com/drift-labs/keeper-bots-v2)

### Perp Markets
- perp filler
- floating maker
- jit maker
- funding rate update
- pnl settler

### Spot Markets
- spot filler
- insurance fund revenue settler

## Python

[driftpy examples/](https://github.com/drift-labs/driftpy/tree/master/examples)

# Example: Atomic Place & Take

## Place And Take Orders

**Place And Take orders allow you to place an order with an atomic fill in the same transaction. There are a few benefits to using them:**

- They are the fastest way to get into a position.
- They allow the use of a "fill or kill" style flag to ensure that the transaction fails unless the order is filled.
- These are the easiest way to integrate Drift orders into other products - you don't need to wait to see if the order is filled.

The one caveat to these orders is that there is an extra upfront cost in that you need to know ahead of time which onchain maker order you want to be filled against. Drift has some off-chain infrastructure which provides an API to help you do this.

```typescript
/**
 * Making a Place And Take with atomic "fill-or-kill" flag to ensure the transaction only succeeds if the entire order is filled
 * 
 **/

import {
	decodeUser,
	PublicKey,
	MarketType,
	BASE_PRECISION,
	PlaceAndTakeOrderSuccessCondition,
	UserAccount,
	isVariant,
	PositionDirection,
	DriftClient,
	getUserStatsAccountPublicKey,
	BN,
	OrderType,
} from '@drift-labs/sdk';

// Method to fetch the current top makers taking advantage of Drift's off-chain infra which can provide these. You may eventually want to keep track of the state of on-chain makers yourself.
async function getTopMakersForPlaceAndTake({
	marketIndex,
	marketType,
	side,
}: {
	marketIndex: number;
	marketType: MarketType;
	side: 'bid' | 'ask';
}): Promise<
	{
		userAccountPubKey: PublicKey;
		userAccount: UserAccount;
	}[]
> {
	const dlobServerUrl = `https://dlob.drift.trade/`;
	const marketTypeStr = isVariant(marketType, 'perp') ? 'perp' : 'spot';
	const limit = 4; // NOTE: This parameter controls the number of top makers that will be returned. It is suggested not to use more than 4, in our current testing the size of the transaction will larger than the current limits if you pass more than 4 makers in.

	const queryParams = `marketIndex=${marketIndex}&marketType=${marketTypeStr}&side=${side}&limit=${limit}&includeAccounts=true`;

	const result = await new Promise<
		{
			userAccountPubKey: string;
			accountBase64: string;
		}[]
	>((res) => {
		fetch(`${dlobServerUrl}/topMakers?${queryParams}`)
			.then(async (response) => {
				if (!response.ok) {
					// Handle failure
					return;
				}
				res(await response.json());
			})
			.catch((err) => {
				// Handle failure
			});
	});

	return result.map(
		(value: { userAccountPubKey: string; accountBase64: string }) => {
			return {
				userAccountPubKey: new PublicKey(value.userAccountPubKey),
				userAccount: decodeUser(Buffer.from(value.accountBase64, 'base64')),
			};
		}
	);

	return [];
}

// Fetch and process the top makers result into params for a place and take order
async function getMakerInfoForPlaceAndTake(
	orderDirection: PositionDirection,
	orderMarketIndex: number,
	orderMarketType: MarketType,
	driftClient: DriftClient
) {
	const topMakers = await getTopMakersForPlaceAndTake({
		marketIndex: orderMarketIndex,
		marketType: orderMarketType,
		side: isVariant(orderDirection, 'long') ? 'ask' : 'bid', // If we're going LONG, we want the makers on the ASK side, and vice-versa
	});

	const makerAccountKeys = topMakers.map((maker) => maker.userAccountPubKey);
	const makerStatsAccountKeys = topMakers.map((makerAccount) =>
		getUserStatsAccountPublicKey(
			driftClient.program.programId,
			makerAccount.userAccount.authority
		)
	);
	const makerAccounts = topMakers.map((maker) => maker.userAccount);

	return makerAccountKeys.map((makerUserAccountKey, index) => {
		return {
			maker: makerUserAccountKey,
			makerUserAccount: makerAccounts[index],
			makerStats: makerStatsAccountKeys[index],
		};
	});
}

// Create a PlaceAndTake Instruction. DriftClient also has a `placeAndTakePerpOrder` If you want DriftClient to create+sign+send the transaction if you don't want to manually handle the instruction.
async function makePlaceAndTakePerpOrderIx(
	orderDirection: PositionDirection,
	orderMarketIndex: number,
	orderSizeBase: BN,
	successCondition: PlaceAndTakeOrderSuccessCondition,
	driftClient: DriftClient
) {
	const makerInfos = await getMakerInfoForPlaceAndTake(
		orderDirection,
		orderMarketIndex,
		MarketType.PERP,
		driftClient
	);

	const placeAndTakeIx = await driftClient.getPlaceAndTakePerpOrderIx(
		{
			direction: orderDirection,
			baseAssetAmount: orderSizeBase,
			marketIndex: orderMarketIndex,
			marketType: MarketType.PERP,
			orderType: OrderType.MARKET,
		},
		makerInfos,
		undefined,
		successCondition
	);

	return placeAndTakeIx;
}

// Example: Make a LONG order for 1 SOL-PERP which will "fill-or-kill" if the entire order isn't filled atomically. 
const driftClient = DRIFT_CLIENT; // Assume you have a Drift Client set up and ready to go, see https://drift-labs.github.io/v2-teacher/#client-initialization
const sizeInSol = 1;
await makePlaceAndTakePerpOrderIx(
	PositionDirection.LONG,
	0, // SOL-PERP's market index is 0
	new BN(sizeInSol).mul(BASE_PRECISION),
	PlaceAndTakeOrderSuccessCondition.FullFill, // This ensures the order is completely filled, or the tx fails.
  driftClient
);
```

# Example: Get Entry Price & Price Impact

Once you have the L2 Orderbook state you can estimate the entry price and slippage of a proposed order.

To get the L2 Orderbook, you can use Drift's DLOB Server (See [here](/#get-l2-l3)).

```typescript
import { deserializeL2Response } from "@drift/common"; // See https://github.com/drift-labs/drift-common/blob/master/common-ts/src/utils/orderbook/index.ts#L40
import { calculateEstimatedEntryPriceWithL2 } from "@drift-labs/sdk"; // See https://github.com/drift-labs/protocol-v2/blob/master/sdk/src/math/trade.ts#L882
import { MarketType, PositionDirection, AssetType } from "@drift-labs/sdk";

const fetchOrderbook = async ({
  marketName,
  depth,
  includeOracle,
  includeVamm,
}: {
  marketName: string;
  depth: number;
  includeOracle: boolean;
  includeVamm: boolean;
}) => {
  const response = await fetch(
    `https://dlob.drift.trade/l2?marketName=${marketName}&depth=${depth}&includeOracle=${includeOracle}&includeVamm=${includeVamm}`
  );
  const result = await r.json();

  const resultsArray = resp.l2s as any[];

  const deserializedL2 = deserializeL2Response(result.l2s[0]);

  return orderbook;
};

const getEntryPriceForOrder = async ({
  marketName,
  marketIndex,
  marketType,
  orderDirection,
  orderSize,
}: {
  marketName: string;
  marketIndex: number;
  marketType: MarketType;
  orderDirection: PositionDirection;
  orderSize: BN;
}) => {
  const l2State = await fetchOrderbook({
    marketName,
    depth: 10,
    includeOracle: true,
    includeVamm: true,
  });

  const {
    entryPrice,
    priceImpact,
    bestPrice,
    worstPrice,
    baseFilled,
    quoteFilled,
  } = calculateEstimatedEntryPriceWithL2(
    "base", // Can be 'quote' if the amount is in quote,
    amount,
    direction,
    basePrecision, // BASE_PRECISION if PERP, otherwise look up the precision for the asset in https://github.com/drift-labs/protocol-v2/blob/master/sdk/src/constants/spotMarkets.ts
    l2State
  );

  return {
    entryPrice,
    priceImpact,
    bestPrice,
    worstPrice,
    baseFilled,
    quoteFilled,
  };
};

// You can find the market names, precisions, etc. in the follow config files. Alternatively you could fetch this data on chain on the market accounts.
// SPOT MARKET CONFIGS :: https://github.com/drift-labs/protocol-v2/blob/master/sdk/src/constants/spotMarkets.ts
// PERP MARKET CONFIGS :: https://github.com/drift-labs/protocol-v2/blob/master/sdk/src/constants/perpMarkets.ts

// Get the entry price data for a SOL-PERP LONG order of size 1 SOL
getEntryPriceForOrder({
  marketName: "SOL-PERP",
  marketIndex: 0,
  marketType: MarketType.PERP,
  orderDirection: PositionDirection.LONG,
  orderSize: new BN(1).mul(BASE_PRECISION), // 1 SOL worth of SOL-PERP
});
```

# Example: Get Funding Rate

The SDK has an easy-to-use `calculateFormattedLiveFundingRate` method to simplify fetching the live funding rate for a market. It sacrifices from precision for readability though and for a more precise calculation you need to use `calculateLongShortFundingRateAndLiveTwaps`. Both methods can be found in the SDK [here](https://github.com/drift-labs/protocol-v2/blob/master/sdk/src/math/funding.ts). Looking at the implementation of `calculateFormattedLiveFundingRate` should give you a good idea how to use the results of `calculateLongShortFundingRateAndLiveTwaps` for your own purposes.

Note that funding is paid each hour. The `calculateFormattedLiveFundingRate` lets you choose which format to return the funding rate in, if you use 'hour' as an argument it will return the actual percentage being paid each hour. If you use 'year' as an argument it will return the annualized percentage.

Historical funding rate data is available in the Data Api, see [here](/#data-api).

```typescript
import { DriftClient, calculateFormattedLiveFundingRate } from "@drift-labs/sdk";

export function calculateFormattedLiveFundingRate(
	market: PerpMarketAccount,
	oraclePriceData: OraclePriceData,
	period: 'hour'|'year'
): Promise<{
	longRate: number;
	shortRate: number;
	fundingRateUnit: string;
	formattedFundingRateSummary: string;
}> {

const driftClient = DRIFT_CLIENT; // see /#client-initialization section for how to set up a DriftClient

const SOLPerpMarket = driftClient.getPerpMarketAccount(0); // 0 is the market index for SOL-PERP
const SOLPerpOraclePriceData = driftClient.getOracleDataForPerpMarket(SOLPerpMarket.oraclePriceAccount);

const {
	longRate,
	shortRate,
	fundingRateUnit,
	formattedFundingRateSummary,
} = calculateFormattedLiveFundingRate(SOLPerpMarket, SOLPerpOraclePriceData, 'hour');
```

# Example: Get Borrow & Lend Rates

To calculate the current borrow and lend rates for a market, you can use the `calculateDepositRate` and `calculateBorrowRate` methods. Historical data is not currently available.

```typescript
import {
  DriftClient,
  calculateDepositRate,
  calculateBorrowRate,
} from "@drift-labs/sdk";

const driftClient = DRIFT_CLIENT; // see /#client-initialization section for how to set up a DriftClient

const SOLSpotMarket = driftClient.getSpotMarketAccount(0); // 0 is the market index for SOL-SPOT

// You can the implementation for the borrow/deposit rate methods here : https://github.com/drift-labs/protocol-v2/blob/master/sdk/src/math/spotBalance.ts#L416
const SOLBorrowRate = calculateBorrowRate(SOLSpotMarket); // Rate is given in %, precision = PERCENTAGE_PRECISION_EXP
const SOLDepositRate = calculateDepositRate(SOLSpotMarket); // Rate is given in %, precision = PERCENTAGE_PRECISION_EXP
```