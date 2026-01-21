# CLI: Staking Commands

> **⚠️ WARNING: Development Only**
>
> The `glam marinade`, `glam stake`, and `glam lst` commands are **unaudited** and require `NODE_ENV=development` flag to use. Do not use these in production without thorough testing and security review.
>
> ```bash
> # Enable development mode to use these commands
> NODE_ENV=development glam marinade stake <VAULT> --amount 100
> ```

SOL staking operations via multiple protocols.

## Available Staking Integrations

| Integration | Description | LST Received |
|-------------|-------------|--------------|
| `MarinadeStaking` | Marinade liquid staking | mSOL |
| `NativeStaking` | Native Solana staking | None (stake account) |
| `SplStakePool` | SPL stake pools | Pool token |
| `SanctumStaking` | Sanctum unified staking | Various LSTs |

## Marinade Staking

**Prerequisite:** Enable `MarinadeStaking` integration.

```bash
glam integration enable <VAULT_ADDRESS> MarinadeStaking
```

### Stake SOL to get mSOL

```bash
glam marinade stake <VAULT_ADDRESS> --amount <SOL_AMOUNT>
```

### Unstake mSOL

```bash
# Liquid unstake (instant, small fee)
glam marinade unstake <VAULT_ADDRESS> --amount <MSOL_AMOUNT>

# Delayed unstake (no fee, ~2 days)
glam marinade delayed-unstake <VAULT_ADDRESS> --amount <MSOL_AMOUNT>
```

### Claim delayed unstake

```bash
glam marinade claim <VAULT_ADDRESS> --ticket <TICKET_ADDRESS>
```

---

## Native Staking

**Prerequisite:** Enable `NativeStaking` integration.

```bash
glam integration enable <VAULT_ADDRESS> NativeStaking
```

### Stake to validator

```bash
glam stake stake <VAULT_ADDRESS> --validator <VOTE_ACCOUNT> --amount <SOL_AMOUNT>
```

### Deactivate stake

```bash
glam stake deactivate <VAULT_ADDRESS> --stake-account <STAKE_ACCOUNT>
```

### Withdraw stake (after deactivation completes)

```bash
glam stake withdraw <VAULT_ADDRESS> --stake-account <STAKE_ACCOUNT>
```

---

## SPL Stake Pools

**Prerequisite:** Enable `SplStakePool` integration.

```bash
glam integration enable <VAULT_ADDRESS> SplStakePool
```

### Deposit to stake pool

```bash
glam stake-pool deposit <VAULT_ADDRESS> --pool <POOL_ADDRESS> --amount <SOL_AMOUNT>
```

### Withdraw from stake pool

```bash
glam stake-pool withdraw <VAULT_ADDRESS> --pool <POOL_ADDRESS> --amount <TOKEN_AMOUNT>
```

---

## Sanctum Staking

**Prerequisite:** Enable `SanctumStaking` integration.

```bash
glam integration enable <VAULT_ADDRESS> SanctumStaking
```

### Stake via Sanctum

```bash
glam sanctum stake <VAULT_ADDRESS> --lst <LST_MINT> --amount <SOL_AMOUNT>
```

### Unstake via Sanctum

```bash
glam sanctum unstake <VAULT_ADDRESS> --lst <LST_MINT> --amount <LST_AMOUNT>
```

---

## Common LST Mints

| LST | Mint Address |
|-----|--------------|
| mSOL | `mSoLzYCxHdYgdzU16g5QSh3i5K3z3KZK7ytfqcJm7So` |
| jitoSOL | `J1toso1uCk3RLmjorhTtrVwY9HJ7X8V9yYac6Y7kGCPn` |
| bSOL | `bSo13r4TkiE4KumL71LsHTPpL2euBYLFx6h9HP3piy1` |
| INF | `5oVNBeEEQvYi1cX3ir8Dx5n1P7pdxydbGF2X4TxVusJm` |

---

## Typical Marinade Workflow

```bash
# 1. Enable integration
glam integration enable <VAULT> MarinadeStaking

# 2. Stake SOL
glam marinade stake <VAULT> --amount 100

# 3. Later, unstake with instant liquidity
glam marinade unstake <VAULT> --amount 50

# Or use delayed unstake for no fee
glam marinade delayed-unstake <VAULT> --amount 50

# 4. After ~2 days, claim delayed unstake
glam marinade claim <VAULT> --ticket <TICKET>
```
