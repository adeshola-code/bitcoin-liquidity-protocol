# Bitcoin Liquidity Protocol

This project implements a **Bitcoin Liquidity Protocol** with advanced features such as **dynamic liquidity pools**, **governance**, **yield farming**, and **flash loans**. Built on **Stacks**, the protocol leverages smart contracts to facilitate decentralized exchanges (DEX), automated liquidity provision, and yield optimization.

## Key Features

1. **Liquidity Pools**: Automated liquidity provision using two token pairs. Dynamic pricing based on reserve balances.
2. **Governance**: Token holders can vote and stake to influence protocol changes.
3. **Yield Farming**: Reward liquidity providers for staking their tokens in specific pools.
4. **Flash Loans**: Borrow tokens from liquidity pools without upfront collateral as long as the loan is repaid within the same transaction.
5. **Multi-hop Swaps**: Perform multiple token swaps across several pools in one transaction.

## Smart Contract Overview

### Fungible Token Trait

Defines the standard functions for handling fungible tokens (FT):

- `transfer`: Transfer tokens between accounts.
- `get-balance`: Get the balance of an account.
- `get-total-supply`: Retrieve the total token supply.

```clarity
(define-trait ft-trait
    (
        (transfer (uint principal principal (optional (buff 34))) (response bool uint))
        (get-balance (principal) (response uint uint))
        (get-total-supply () (response uint uint))
    )
)
```

### Error Codes

Predefined error codes for handling common errors in the protocol:

```clarity
(define-constant ERR-NOT-AUTHORIZED (err u1000))
(define-constant ERR-INSUFFICIENT-BALANCE (err u1001))
...
```

### Protocol Parameters

The protocol includes adjustable parameters for fees, liquidity, and price impact management.

```clarity
(define-constant FEE-DENOMINATOR u10000) ;; Basis points for fees
(define-constant MAX-PRICE-IMPACT u200) ;; 2% max price impact
(define-constant FLASH-LOAN-FEE u10) ;; 0.1% flash loan fee
```

## Core Components

### Data Structures

- **Pools**: Contains token reserves, fee rates, and pricing information for each liquidity pool.
- **Liquidity Providers**: Stores information about liquidity stakers, their shares, and unclaimed fees.
- **Governance Stakes**: Manages staked governance tokens and delegation power.
- **Flash Loans**: Tracks the status and repayment of flash loans.
- **Yield Farms**: Contains information about yield farming, including reward distribution and staked amounts.

### Internal Functions

Key internal logic for the protocol:

- **Price Impact Check**: Ensures that large swaps do not exceed the max price impact.
- **Liquidity Shares Calculation**: Determines the number of shares to mint for liquidity providers based on the current pool reserves.
- **Farm Rewards Calculation**: Distributes rewards to stakers based on their proportional stake in the pool.

### Public Functions

#### Pool Creation

```clarity
(define-public (create-pool (token-x <ft-trait>) (token-y <ft-trait>) (initial-x uint) (initial-y uint))
```

Creates a new liquidity pool between two fungible tokens.

#### Add Liquidity

```clarity
(define-public (add-liquidity (pool-id uint) (token-x <ft-trait>) (token-y <ft-trait>) (amount-x uint) (amount-y uint) (min-shares uint))
```

Allows users to deposit both tokens to the pool and mint liquidity shares.

#### Token Swap

```clarity
(define-public (swap-exact-x-for-y (pool-id uint) (token-x <ft-trait>) (token-y <ft-trait>) (amount-x uint) (min-y uint))
```

Executes a swap of token `X` for token `Y`, ensuring minimum output is received.

#### Flash Loan

```clarity
(define-public (flash-swap (pool-id uint) (token-x <ft-trait>) (token-y <ft-trait>) (amount-x uint) (callback-contract principal))
```

Facilitates flash loans, allowing borrowers to take out a loan and execute a callback contract to handle the loan repayment.

### Governance Functions

- **Staking**: Users can stake governance tokens to earn voting power.
- **Setting Governance Token**: The protocol owner can set the token used for governance.

```clarity
(define-public (stake-governance (token <ft-trait>) (amount uint) (lock-blocks uint))
```

## Installation & Usage

1. Clone the repository:

   ```bash
   git clone https://github.com/adeshola-code/bitcoin-liquidity-protocol.git
   ```

2. Deploy the contract using the Stacks CLI:

   ```bash
   stacks-cli deploy ./contracts/bitquid-amm.clar
   ```

3. Interact with the contract using the Stacks API to create pools, add liquidity, and perform swaps.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

### Disclaimer

This protocol is in active development. Use at your own risk and perform adequate security reviews before using in production.
