# Rental & Dividends

The rental mechanism enables researchers to access datasets by paying hourly fees, which automatically flow to token holders as dividends. This creates sustainable revenue streams backing dataset tokens as securities.

Researchers purchase access credentials by sending USDC to the IDO contract's rental payment function. The contract automatically splits proceeds: 95% flows to the RentalPool for dividend distribution to token holders, while 5% goes to protocol fees. The RentalPool uses the Accumulated Rewards algorithm to track dividends with O(1) gas complexity, enabling efficient settlement regardless of token holder count. Token holders can claim accumulated dividends at any time.

On-chain payment verification enables off-chain systems (DeLong Lab) to grant dataset access. Credentials are time-limited based on purchased hours. Hourly rates are set by governance through Pricing Proposals, allowing communities to adjust pricing based on demand elasticity and competitive positioning.
