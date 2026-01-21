# CLI: LST (Liquid Staking Tokens)

> **⚠️ WARNING: Development Only**
>
> The `glam lst` commands are **unaudited** and require `NODE_ENV=development` flag to use. Do not use in production without thorough testing and security review.
>
> ```bash
> NODE_ENV=development glam lst stake <STAKEPOOL> 100
> ```

Stake SOL into SPL stake pools to receive liquid staking tokens.

> **Note:** This is for generic SPL stake pools. For Marinade-specific staking, see [staking.md](./staking.md).

## Prerequisites

```bash
# Enable the integration first
glam integration enable <VAULT> SplStakePool
```

## Commands

### `glam lst stake`

Stake SOL into a stake pool to receive LST.

```bash
glam lst stake <STAKEPOOL> <AMOUNT>
```

**Arguments:**
| Argument | Description |
|----------|-------------|
| `STAKEPOOL` | Stake pool address |
| `AMOUNT` | Amount of SOL to stake |

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

**Example:**
```bash
# Stake 10 SOL into a stake pool
glam lst stake StakePoolAddress111... 10
```

### `glam lst unstake`

Unstake LST to receive SOL in a stake account.

```bash
glam lst unstake <LST_MINT> <AMOUNT> [OPTIONS]
```

**Arguments:**
| Argument | Description |
|----------|-------------|
| `LST_MINT` | LST token mint address |
| `AMOUNT` | Amount of LST to unstake |

**Options:**
| Flag | Description |
|------|-------------|
| `-d, --deactivate` | Deactivate the stake account |
| `-y, --yes` | Skip confirmation prompt |

**Example:**
```bash
# Unstake 5 LST tokens
glam lst unstake LstMintAddress111... 5

# Unstake and deactivate stake account
glam lst unstake LstMintAddress111... 5 --deactivate
```

---

## Workflow

```bash
# 1. Enable integration
glam integration enable <VAULT> SplStakePool

# 2. Stake SOL to receive LST
glam lst stake <STAKEPOOL_ADDRESS> 100

# 3. Later: unstake to get SOL back
glam lst unstake <LST_MINT> 100 --deactivate
```

---

## Common Stake Pools

| Pool | Description |
|------|-------------|
| Marinade | mSOL - use `glam marinade` commands instead |
| Jito | jitoSOL |
| BlazeStake | bSOL |
| Sanctum | Various LSTs |

---

## Important Notes

- **Unstake delay**: Unstaking typically has an epoch delay (~2-3 days)
- **Stake accounts**: Unstaking creates stake accounts that need deactivation
- **Different from Marinade**: For Marinade, use `glam marinade` commands which have additional features
