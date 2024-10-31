# Errors

<aside class="notice">
This is a table of program errors as described in the programs IDL.
</aside>

Drift Protocol uses has following error codes:

|   code | name                                       | msg                                                         |
|-------:|:-------------------------------------------|:------------------------------------------------------------|
|   6000 | InvalidSpotMarketAuthority                 | Invalid Spot Market Authority                               |
|   6001 | InvalidInsuranceFundAuthority              | Clearing house not insurance fund authority                 |
|   6002 | InsufficientDeposit                        | Insufficient deposit                                        |
|   6003 | InsufficientCollateral                     | Insufficient collateral                                     |
|   6004 | SufficientCollateral                       | Sufficient collateral                                       |
|   6005 | MaxNumberOfPositions                       | Max number of positions taken                               |
|   6006 | AdminControlsPricesDisabled                | Admin Controls Prices Disabled                              |
|   6007 | MarketDelisted                             | Market Delisted                                             |
|   6008 | MarketIndexAlreadyInitialized              | Market Index Already Initialized                            |
|   6009 | UserAccountAndUserPositionsAccountMismatch | User Account And User Positions Account Mismatch            |
|   6010 | UserHasNoPositionInMarket                  | User Has No Position In Market                              |
|   6011 | InvalidInitialPeg                          | Invalid Initial Peg                                         |
|   6012 | InvalidRepegRedundant                      | AMM repeg already configured with amt given                 |
|   6013 | InvalidRepegDirection                      | AMM repeg incorrect repeg direction                         |
|   6014 | InvalidRepegProfitability                  | AMM repeg out of bounds pnl                                 |
|   6015 | SlippageOutsideLimit                       | Slippage Outside Limit Price                                |
|   6016 | OrderSizeTooSmall                          | Order Size Too Small                                        |
|   6017 | InvalidUpdateK                             | Price change too large when updating K                      |
|   6018 | AdminWithdrawTooLarge                      | Admin tried to withdraw amount larger than fees collected   |
|   6019 | MathError                                  | Math Error                                                  |
|   6020 | BnConversionError                          | Conversion to u128/u64 failed with an overflow or underflow |
|   6021 | ClockUnavailable                           | Clock unavailable                                           |
|   6022 | UnableToLoadOracle                         | Unable To Load Oracles                                      |
|   6023 | PriceBandsBreached                         | Price Bands Breached                                        |
|   6024 | ExchangePaused                             | Exchange is paused                                          |
|   6025 | InvalidWhitelistToken                      | Invalid whitelist token                                     |
|   6026 | WhitelistTokenNotFound                     | Whitelist token not found                                   |
|   6027 | InvalidDiscountToken                       | Invalid discount token                                      |
|   6028 | DiscountTokenNotFound                      | Discount token not found                                    |
|   6029 | ReferrerNotFound                           | Referrer not found                                          |
|   6030 | ReferrerStatsNotFound                      | ReferrerNotFound                                            |
|   6031 | ReferrerMustBeWritable                     | ReferrerMustBeWritable                                      |
|   6032 | ReferrerStatsMustBeWritable                | ReferrerMustBeWritable                                      |
|   6033 | ReferrerAndReferrerStatsAuthorityUnequal   | ReferrerAndReferrerStatsAuthorityUnequal                    |
|   6034 | InvalidReferrer                            | InvalidReferrer                                             |
|   6035 | InvalidOracle                              | InvalidOracle                                               |
|   6036 | OracleNotFound                             | OracleNotFound                                              |
|   6037 | LiquidationsBlockedByOracle                | Liquidations Blocked By Oracle                              |
|   6038 | MaxDeposit                                 | Can not deposit more than max deposit                       |
|   6039 | CantDeleteUserWithCollateral               | Can not delete user that still has collateral               |
|   6040 | InvalidFundingProfitability                | AMM funding out of bounds pnl                               |
|   6041 | CastingFailure                             | Casting Failure                                             |
|   6042 | InvalidOrder                               | InvalidOrder                                                |
|   6043 | InvalidOrderMaxTs                          | InvalidOrderMaxTs                                           |
|   6044 | InvalidOrderMarketType                     | InvalidOrderMarketType                                      |
|   6045 | InvalidOrderForInitialMarginReq            | InvalidOrderForInitialMarginReq                             |
|   6046 | InvalidOrderNotRiskReducing                | InvalidOrderNotRiskReducing                                 |
|   6047 | InvalidOrderSizeTooSmall                   | InvalidOrderSizeTooSmall                                    |
|   6048 | InvalidOrderNotStepSizeMultiple            | InvalidOrderNotStepSizeMultiple                             |
|   6049 | InvalidOrderBaseQuoteAsset                 | InvalidOrderBaseQuoteAsset                                  |
|   6050 | InvalidOrderIOC                            | InvalidOrderIOC                                             |
|   6051 | InvalidOrderPostOnly                       | InvalidOrderPostOnly                                        |
|   6052 | InvalidOrderIOCPostOnly                    | InvalidOrderIOCPostOnly                                     |
|   6053 | InvalidOrderTrigger                        | InvalidOrderTrigger                                         |
|   6054 | InvalidOrderAuction                        | InvalidOrderAuction                                         |
|   6055 | InvalidOrderOracleOffset                   | InvalidOrderOracleOffset                                    |
|   6056 | InvalidOrderMinOrderSize                   | InvalidOrderMinOrderSize                                    |
|   6057 | PlacePostOnlyLimitFailure                  | Failed to Place Post-Only Limit Order                       |
|   6058 | UserHasNoOrder                             | User has no order                                           |
|   6059 | OrderAmountTooSmall                        | Order Amount Too Small                                      |
|   6060 | MaxNumberOfOrders                          | Max number of orders taken                                  |
|   6061 | OrderDoesNotExist                          | Order does not exist                                        |
|   6062 | OrderNotOpen                               | Order not open                                              |
|   6063 | FillOrderDidNotUpdateState                 | FillOrderDidNotUpdateState                                  |
|   6064 | ReduceOnlyOrderIncreasedRisk               | Reduce only order increased risk                            |
|   6065 | UnableToLoadAccountLoader                  | Unable to load AccountLoader                                |
|   6066 | TradeSizeTooLarge                          | Trade Size Too Large                                        |
|   6067 | UserCantReferThemselves                    | User cant refer themselves                                  |
|   6068 | DidNotReceiveExpectedReferrer              | Did not receive expected referrer                           |
|   6069 | CouldNotDeserializeReferrer                | Could not deserialize referrer                              |
|   6070 | CouldNotDeserializeReferrerStats           | Could not deserialize referrer stats                        |
|   6071 | UserOrderIdAlreadyInUse                    | User Order Id Already In Use                                |
|   6072 | NoPositionsLiquidatable                    | No positions liquidatable                                   |
|   6073 | InvalidMarginRatio                         | Invalid Margin Ratio                                        |
|   6074 | CantCancelPostOnlyOrder                    | Cant Cancel Post Only Order                                 |
|   6075 | InvalidOracleOffset                        | InvalidOracleOffset                                         |
|   6076 | CantExpireOrders                           | CantExpireOrders                                            |
|   6077 | CouldNotLoadMarketData                     | CouldNotLoadMarketData                                      |
|   6078 | PerpMarketNotFound                         | PerpMarketNotFound                                          |
|   6079 | InvalidMarketAccount                       | InvalidMarketAccount                                        |
|   6080 | UnableToLoadPerpMarketAccount              | UnableToLoadMarketAccount                                   |
|   6081 | MarketWrongMutability                      | MarketWrongMutability                                       |
|   6082 | UnableToCastUnixTime                       | UnableToCastUnixTime                                        |
|   6083 | CouldNotFindSpotPosition                   | CouldNotFindSpotPosition                                    |
|   6084 | NoSpotPositionAvailable                    | NoSpotPositionAvailable                                     |
|   6085 | InvalidSpotMarketInitialization            | InvalidSpotMarketInitialization                             |
|   6086 | CouldNotLoadSpotMarketData                 | CouldNotLoadSpotMarketData                                  |
|   6087 | SpotMarketNotFound                         | SpotMarketNotFound                                          |
|   6088 | InvalidSpotMarketAccount                   | InvalidSpotMarketAccount                                    |
|   6089 | UnableToLoadSpotMarketAccount              | UnableToLoadSpotMarketAccount                               |
|   6090 | SpotMarketWrongMutability                  | SpotMarketWrongMutability                                   |
|   6091 | SpotMarketInterestNotUpToDate              | SpotInterestNotUpToDate                                     |
|   6092 | SpotMarketInsufficientDeposits             | SpotMarketInsufficientDeposits                              |
|   6093 | UserMustSettleTheirOwnPositiveUnsettledPNL | UserMustSettleTheirOwnPositiveUnsettledPNL                  |
|   6094 | CantUpdatePoolBalanceType                  | CantUpdatePoolBalanceType                                   |
|   6095 | InsufficientCollateralForSettlingPNL       | InsufficientCollateralForSettlingPNL                        |
|   6096 | AMMNotUpdatedInSameSlot                    | AMMNotUpdatedInSameSlot                                     |
|   6097 | AuctionNotComplete                         | AuctionNotComplete                                          |
|   6098 | MakerNotFound                              | MakerNotFound                                               |
|   6099 | MakerStatsNotFound                         | MakerNotFound                                               |
|   6100 | MakerMustBeWritable                        | MakerMustBeWritable                                         |
|   6101 | MakerStatsMustBeWritable                   | MakerMustBeWritable                                         |
|   6102 | MakerOrderNotFound                         | MakerOrderNotFound                                          |
|   6103 | CouldNotDeserializeMaker                   | CouldNotDeserializeMaker                                    |
|   6104 | CouldNotDeserializeMakerStats              | CouldNotDeserializeMaker                                    |
|   6105 | AuctionPriceDoesNotSatisfyMaker            | AuctionPriceDoesNotSatisfyMaker                             |
|   6106 | MakerCantFulfillOwnOrder                   | MakerCantFulfillOwnOrder                                    |
|   6107 | MakerOrderMustBePostOnly                   | MakerOrderMustBePostOnly                                    |
|   6108 | CantMatchTwoPostOnlys                      | CantMatchTwoPostOnlys                                       |
|   6109 | OrderBreachesOraclePriceLimits             | OrderBreachesOraclePriceLimits                              |
|   6110 | OrderMustBeTriggeredFirst                  | OrderMustBeTriggeredFirst                                   |
|   6111 | OrderNotTriggerable                        | OrderNotTriggerable                                         |
|   6112 | OrderDidNotSatisfyTriggerCondition         | OrderDidNotSatisfyTriggerCondition                          |
|   6113 | PositionAlreadyBeingLiquidated             | PositionAlreadyBeingLiquidated                              |
|   6114 | PositionDoesntHaveOpenPositionOrOrders     | PositionDoesntHaveOpenPositionOrOrders                      |
|   6115 | AllOrdersAreAlreadyLiquidations            | AllOrdersAreAlreadyLiquidations                             |
|   6116 | CantCancelLiquidationOrder                 | CantCancelLiquidationOrder                                  |
|   6117 | UserIsBeingLiquidated                      | UserIsBeingLiquidated                                       |
|   6118 | LiquidationsOngoing                        | LiquidationsOngoing                                         |
|   6119 | WrongSpotBalanceType                       | WrongSpotBalanceType                                        |
|   6120 | UserCantLiquidateThemself                  | UserCantLiquidateThemself                                   |
|   6121 | InvalidPerpPositionToLiquidate             | InvalidPerpPositionToLiquidate                              |
|   6122 | InvalidBaseAssetAmountForLiquidatePerp     | InvalidBaseAssetAmountForLiquidatePerp                      |
|   6123 | InvalidPositionLastFundingRate             | InvalidPositionLastFundingRate                              |
|   6124 | InvalidPositionDelta                       | InvalidPositionDelta                                        |
|   6125 | UserBankrupt                               | UserBankrupt                                                |
|   6126 | UserNotBankrupt                            | UserNotBankrupt                                             |
|   6127 | UserHasInvalidBorrow                       | UserHasInvalidBorrow                                        |
|   6128 | DailyWithdrawLimit                         | DailyWithdrawLimit                                          |
|   6129 | DefaultError                               | DefaultError                                                |
|   6130 | InsufficientLPTokens                       | Insufficient LP tokens                                      |
|   6131 | CantLPWithPerpPosition                     | Cant LP with a market position                              |
|   6132 | UnableToBurnLPTokens                       | Unable to burn LP tokens                                    |
|   6133 | TryingToRemoveLiquidityTooFast             | Trying to remove liqudity too fast after adding it          |
|   6134 | InvalidSpotMarketVault                     | Invalid Spot Market Vault                                   |
|   6135 | InvalidSpotMarketState                     | Invalid Spot Market State                                   |
|   6136 | InvalidSerumProgram                        | InvalidSerumProgram                                         |
|   6137 | InvalidSerumMarket                         | InvalidSerumMarket                                          |
|   6138 | InvalidSerumBids                           | InvalidSerumBids                                            |
|   6139 | InvalidSerumAsks                           | InvalidSerumAsks                                            |
|   6140 | InvalidSerumOpenOrders                     | InvalidSerumOpenOrders                                      |
|   6141 | FailedSerumCPI                             | FailedSerumCPI                                              |
|   6142 | FailedToFillOnExternalMarket               | FailedToFillOnExternalMarket                                |
|   6143 | InvalidFulfillmentConfig                   | InvalidFulfillmentConfig                                    |
|   6144 | InvalidFeeStructure                        | InvalidFeeStructure                                         |
|   6145 | InsufficientIFShares                       | Insufficient IF shares                                      |
|   6146 | MarketActionPaused                         | the Market has paused this action                           |
|   6147 | MarketPlaceOrderPaused                     | the Market status doesnt allow placing orders               |
|   6148 | MarketFillOrderPaused                      | the Market status doesnt allow filling orders               |
|   6149 | MarketWithdrawPaused                       | the Market status doesnt allow withdraws                    |
|   6150 | ProtectedAssetTierViolation                | Action violates the Protected Asset Tier rules              |
|   6151 | IsolatedAssetTierViolation                 | Action violates the Isolated Asset Tier rules               |
|   6152 | UserCantBeDeleted                          | User Cant Be Deleted                                        |
|   6153 | ReduceOnlyWithdrawIncreasedRisk            | Reduce Only Withdraw Increased Risk                         |
|   6154 | MaxOpenInterest                            | Max Open Interest                                           |
|   6155 | CantResolvePerpBankruptcy                  | Cant Resolve Perp Bankruptcy                                |
|   6156 | LiquidationDoesntSatisfyLimitPrice         | Liquidation Doesnt Satisfy Limit Price                      |
|   6157 | MarginTradingDisabled                      | Margin Trading Disabled                                     |
|   6158 | InvalidMarketStatusToSettlePnl             | Invalid Market Status to Settle Perp Pnl                    |
|   6159 | PerpMarketNotInSettlement                  | PerpMarketNotInSettlement                                   |
|   6160 | PerpMarketNotInReduceOnly                  | PerpMarketNotInReduceOnly                                   |
|   6161 | PerpMarketSettlementBufferNotReached       | PerpMarketSettlementBufferNotReached                        |
|   6162 | PerpMarketSettlementUserHasOpenOrders      | PerpMarketSettlementUserHasOpenOrders                       |
|   6163 | PerpMarketSettlementUserHasActiveLP        | PerpMarketSettlementUserHasActiveLP                         |
|   6164 | UnableToSettleExpiredUserPosition          | UnableToSettleExpiredUserPosition                           |
|   6165 | UnequalMarketIndexForSpotTransfer          | UnequalMarketIndexForSpotTransfer                           |
|   6166 | InvalidPerpPositionDetected                | InvalidPerpPositionDetected                                 |
|   6167 | InvalidSpotPositionDetected                | InvalidSpotPositionDetected                                 |
|   6168 | InvalidAmmDetected                         | InvalidAmmDetected                                          |
|   6169 | InvalidAmmForFillDetected                  | InvalidAmmForFillDetected                                   |
|   6170 | InvalidAmmLimitPriceOverride               | InvalidAmmLimitPriceOverride                                |
|   6171 | InvalidOrderFillPrice                      | InvalidOrderFillPrice                                       |
|   6172 | SpotMarketBalanceInvariantViolated         | SpotMarketBalanceInvariantViolated                          |
|   6173 | SpotMarketVaultInvariantViolated           | SpotMarketVaultInvariantViolated                            |
|   6174 | InvalidPDA                                 | InvalidPDA                                                  |
|   6175 | InvalidPDASigner                           | InvalidPDASigner                                            |
|   6176 | RevenueSettingsCannotSettleToIF            | RevenueSettingsCannotSettleToIF                             |
|   6177 | NoRevenueToSettleToIF                      | NoRevenueToSettleToIF                                       |
|   6178 | NoAmmPerpPnlDeficit                        | NoAmmPerpPnlDeficit                                         |
|   6179 | SufficientPerpPnlPool                      | SufficientPerpPnlPool                                       |
|   6180 | InsufficientPerpPnlPool                    | InsufficientPerpPnlPool                                     |
|   6181 | PerpPnlDeficitBelowThreshold               | PerpPnlDeficitBelowThreshold                                |
|   6182 | MaxRevenueWithdrawPerPeriodReached         | MaxRevenueWithdrawPerPeriodReached                          |
|   6183 | MaxIFWithdrawReached                       | InvalidSpotPositionDetected                                 |
|   6184 | NoIFWithdrawAvailable                      | NoIFWithdrawAvailable                                       |
|   6185 | InvalidIFUnstake                           | InvalidIFUnstake                                            |
|   6186 | InvalidIFUnstakeSize                       | InvalidIFUnstakeSize                                        |
|   6187 | InvalidIFUnstakeCancel                     | InvalidIFUnstakeCancel                                      |
|   6188 | InvalidIFForNewStakes                      | InvalidIFForNewStakes                                       |
|   6189 | InvalidIFRebase                            | InvalidIFRebase                                             |
|   6190 | InvalidInsuranceUnstakeSize                | InvalidInsuranceUnstakeSize                                 |
|   6191 | InvalidOrderLimitPrice                     | InvalidOrderLimitPrice                                      |
|   6192 | InvalidIFDetected                          | InvalidIFDetected                                           |
|   6193 | InvalidAmmMaxSpreadDetected                | InvalidAmmMaxSpreadDetected                                 |
|   6194 | InvalidConcentrationCoef                   | InvalidConcentrationCoef                                    |
|   6195 | InvalidSrmVault                            | InvalidSrmVault                                             |
|   6196 | InvalidVaultOwner                          | InvalidVaultOwner                                           |
|   6197 | InvalidMarketStatusForFills                | InvalidMarketStatusForFills                                 |
|   6198 | IFWithdrawRequestInProgress                | IFWithdrawRequestInProgress                                 |
|   6199 | NoIFWithdrawRequestInProgress              | NoIFWithdrawRequestInProgress                               |
|   6200 | IFWithdrawRequestTooSmall                  | IFWithdrawRequestTooSmall                                   |
|   6201 | IncorrectSpotMarketAccountPassed           | IncorrectSpotMarketAccountPassed                            |
|   6202 | BlockchainClockInconsistency               | BlockchainClockInconsistency                                |
|   6203 | InvalidIFSharesDetected                    | InvalidIFSharesDetected                                     |
|   6204 | NewLPSizeTooSmall                          | NewLPSizeTooSmall                                           |
|   6205 | MarketStatusInvalidForNewLP                | MarketStatusInvalidForNewLP                                 |
|   6206 | InvalidMarkTwapUpdateDetected              | InvalidMarkTwapUpdateDetected                               |
|   6207 | MarketSettlementAttemptOnActiveMarket      | MarketSettlementAttemptOnActiveMarket                       |
|   6208 | MarketSettlementRequiresSettledLP          | MarketSettlementRequiresSettledLP                           |
|   6209 | MarketSettlementAttemptTooEarly            | MarketSettlementAttemptTooEarly                             |
|   6210 | MarketSettlementTargetPriceInvalid         | MarketSettlementTargetPriceInvalid                          |
|   6211 | UnsupportedSpotMarket                      | UnsupportedSpotMarket                                       |
|   6212 | SpotOrdersDisabled                         | SpotOrdersDisabled                                          |
|   6213 | MarketBeingInitialized                     | Market Being Initialized                                    |
|   6214 | InvalidUserSubAccountId                    | Invalid Sub Account Id                                      |
|   6215 | InvalidTriggerOrderCondition               | Invalid Trigger Order Condition                             |
|   6216 | InvalidSpotPosition                        | Invalid Spot Position                                       |
|   6217 | CantTransferBetweenSameUserAccount         | Cant transfer between same user account                     |
|   6218 | InvalidPerpPosition                        | Invalid Perp Position                                       |
|   6219 | UnableToGetLimitPrice                      | Unable To Get Limit Price                                   |
|   6220 | InvalidLiquidation                         | Invalid Liquidation                                         |
|   6221 | SpotFulfillmentConfigDisabled              | Spot Fulfullment Config Disabled                            |
|   6222 | InvalidMaker                               | Invalid Maker                                               |
|   6223 | FailedUnwrap                               | Failed Unwrap                                               |
|   6224 | MaxNumberOfUsers                           | Max Number Of Users                                         |
|   6225 | InvalidOracleForSettlePnl                  | InvalidOracleForSettlePnl                                   |
|   6226 | MarginOrdersOpen                           | MarginOrdersOpen                                            |
|   6227 | TierViolationLiquidatingPerpPnl            | TierViolationLiquidatingPerpPnl                             |
|   6228 | CouldNotLoadUserData                       | CouldNotLoadUserData                                        |
|   6229 | UserWrongMutability                        | UserWrongMutability                                         |
|   6230 | InvalidUserAccount                         | InvalidUserAccount                                          |
|   6231 | CouldNotLoadUserStatsData                  | CouldNotLoadUserData                                        |
|   6232 | UserStatsWrongMutability                   | UserWrongMutability                                         |
|   6233 | InvalidUserStatsAccount                    | InvalidUserAccount                                          |
|   6234 | UserNotFound                               | UserNotFound                                                |
|   6235 | UnableToLoadUserAccount                    | UnableToLoadUserAccount                                     |
|   6236 | UserStatsNotFound                          | UserStatsNotFound                                           |
|   6237 | UnableToLoadUserStatsAccount               | UnableToLoadUserStatsAccount                                |
|   6238 | UserNotInactive                            | User Not Inactive                                           |
|   6239 | RevertFill                                 | RevertFill                                                  |
|   6240 | InvalidMarketAccountforDeletion            | Invalid MarketAccount for Deletion                          |
|   6241 | InvalidSpotFulfillmentParams               | Invalid Spot Fulfillment Params                             |
|   6242 | FailedToGetMint                            | Failed to Get Mint                                          |
|   6243 | FailedPhoenixCPI                           | FailedPhoenixCPI                                            |
|   6244 | FailedToDeserializePhoenixMarket           | FailedToDeserializePhoenixMarket                            |
|   6245 | InvalidPricePrecision                      | InvalidPricePrecision                                       |
|   6246 | InvalidPhoenixProgram                      | InvalidPhoenixProgram                                       |
|   6247 | InvalidPhoenixMarket                       | InvalidPhoenixMarket                                        |
|   6248 | InvalidSwap                                | InvalidSwap                                                 |
|   6249 | SwapLimitPriceBreached                     | SwapLimitPriceBreached                                      |
|   6250 | SpotMarketReduceOnly                       | SpotMarketReduceOnly                                        |
|   6251 | FundingWasNotUpdated                       | FundingWasNotUpdated                                        |
|   6252 | ImpossibleFill                             | ImpossibleFill                                              |
|   6253 | CantUpdatePerpBidAskTwap                   | CantUpdatePerpBidAskTwap                                    |
|   6254 | UserReduceOnly                             | UserReduceOnly                                              |
|   6255 | InvalidMarginCalculation                   | InvalidMarginCalculation                                    |
|   6256 | CantPayUserInitFee                         | CantPayUserInitFee                                          |
|   6257 | CantReclaimRent                            | CantReclaimRent                                             |
|   6258 | InsuranceFundOperationPaused               | InsuranceFundOperationPaused                                |
|   6259 | NoUnsettledPnl                             | NoUnsettledPnl                                              |
|   6260 | PnlPoolCantSettleUser                      | PnlPoolCantSettleUser                                       |
|   6261 | OracleNonPositive                          | OracleInvalid                                               |
|   6262 | OracleTooVolatile                          | OracleTooVolatile                                           |
|   6263 | OracleTooUncertain                         | OracleTooUncertain                                          |
|   6264 | OracleStaleForMargin                       | OracleStaleForMargin                                        |
|   6265 | OracleInsufficientDataPoints               | OracleInsufficientDataPoints                                |
|   6266 | OracleStaleForAMM                          | OracleStaleForAMM                                           |
|   6267 | UnableToParsePullOracleMessage             | Unable to parse pull oracle message                         |
|   6268 | MaxBorrows                                 | Can not borow more than max borrows                         |
|   6269 | OracleUpdatesNotMonotonic                  | Updates must be monotonically increasing                    |
|   6270 | OraclePriceFeedMessageMismatch             | Trying to update price feed with the wrong feed id          |
|   6271 | OracleUnsupportedMessageType               | The message in the update must be a PriceFeedMessage        |
|   6272 | OracleDeserializeMessageFailed             | Could not deserialize the message in the update             |
|   6273 | OracleWrongGuardianSetOwner                | Wrong guardian set owner in update price atomic             |
|   6274 | OracleWrongWriteAuthority                  | Oracle post update atomic price feed account must be drift program  |
|   6275 | OracleWrongVaaOwner                        | Oracle vaa owner must be wormhole program                   |
|   6276 | OracleTooManyPriceAccountUpdates           | Multi updates must have 2 or fewer accounts passed in remaining accounts    |
|   6277 | OracleMismatchedVaaAndPriceUpdates         | Don't have the same remaining accounts number and merkle price updates left |
|   6278 | OracleBadRemainingAccountPublicKey         | Remaining account passed is not a valid pda                 |
|   6279 | FailedOpenbookV2CPI                        | FailedOpenbookV2CPI                                         |
|   6280 | InvalidOpenbookV2Program                   | InvalidOpenbookV2Program                                    |
|   6281 | InvalidOpenbookV2Market                    | InvalidOpenbookV2Market                                     |
|   6282 | NonZeroTransferFee                         | Non zero transfer fee                                       |
|   6283 | LiquidationOrderFailedToFill               | Liquidation order failed to fill                            |
|   6284 | InvalidPredictionMarketOrder               | Invalid prediction market order                             |
|   6285 | InvalidVerificationIxIndex                 | Ed25519 Ix must be before place and make swift order ix     |
|   6286 | SigVerificationFailed                      | Swift message verificaiton failed                           |
|   6287 | MismatchedSwiftOrderParamsMarketIndex      | Market index mismatched b/w taker and maker swift order params  |
|   6288 | InvalidSwiftOrderParam                     | Swift only available for market/oracle perp orders          |
|   6289 | PlaceAndTakeOrderSuccessConditionFailed    | Place and take order success condition failed               |
|   6290 | InvalidHighLeverageModeConfig              | Invalid High Leverage Mode Config                           |
|   6291 | InvalidRFQUserAccount                      | Invalid RFQ User Account                                    |
|   6292 | RFQUserAccountWrongMutability              | RFQUserAccount should be mutable                            |
|   6293 | RFQUserAccountFull                         | RFQUserAccount has too many active RFQs                     |
|   6294 | RFQOrderNotFilled                          | RFQ order not filled as expected                            |
|   6295 | InvalidRFQOrder                            | RFQ orders must be jit makers                               |
|   6296 | InvalidRFQMatch                            | RFQ matches must be valid                          
