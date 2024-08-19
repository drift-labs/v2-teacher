# Prediction Markets

Prediction Markets are Perp Markets with the `contract_type` set as `Prediction`.

Prediction Markets function the same as Perp Markets with the following additional constraints:

- Prices must be between 0 and 1
- Funding is paused
- Margin ratios are set to 1 so that the position is fully collateralized
- The margin system uses `1 - oracle price` to value short positions
- They use prelaunch oracles (the oracle price is the mark twap)
