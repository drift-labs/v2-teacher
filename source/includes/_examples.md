# Examples: Bots

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

# Examples: Atomic Place&Take

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
	driftClient,
)
```