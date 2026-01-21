# CLI: Delegate Commands (`glam delegate`)

Manage access control and delegate permissions for vaults.

## Commands

### `glam delegate grant`

Grant delegate permissions.

```bash
glam delegate grant <VAULT_ADDRESS> <DELEGATE_PUBKEY> --permissions <PERMS>
```

**Options:**
| Flag | Description |
|------|-------------|
| `--permissions <PERMS>` | Comma-separated permissions |

**Available permissions:**

| Permission | Description |
|------------|-------------|
| `Swap` | Token swaps |
| `Stake` | Staking operations |
| `Unstake` | Unstaking operations |
| `LiquidUnstake` | Liquid unstaking |
| `DriftInit` | Initialize Drift account |
| `DriftDeposit` | Deposit to Drift |
| `DriftWithdraw` | Withdraw from Drift |
| `DriftUpdateUser` | Update Drift user |
| `DriftDeleteUser` | Delete Drift user |
| `DriftPlaceOrders` | Place Drift orders |
| `DriftCancelOrders` | Cancel Drift orders |
| `DriftPerpMarket` | Drift perp trading |
| `DriftSpotMarket` | Drift spot trading |
| `KaminoInit` | Initialize Kamino |
| `KaminoDeposit` | Deposit to Kamino |
| `KaminoWithdraw` | Withdraw from Kamino |
| `KaminoBorrow` | Borrow from Kamino |
| `KaminoRepay` | Repay Kamino loans |
| `MeteoraInit` | Initialize Meteora |
| `MeteoraDeposit` | Deposit to Meteora |
| `MeteoraWithdraw` | Withdraw from Meteora |

**Examples:**

```bash
# Grant swap and staking permissions
glam delegate grant <VAULT> <DELEGATE> --permissions Swap,Stake,Unstake

# Grant Drift trading permissions
glam delegate grant <VAULT> <DELEGATE> --permissions DriftDeposit,DriftWithdraw,DriftPlaceOrders,DriftPerpMarket

# Grant Kamino lending permissions
glam delegate grant <VAULT> <DELEGATE> --permissions KaminoDeposit,KaminoWithdraw,KaminoBorrow,KaminoRepay
```

### `glam delegate list`

List vault delegates and their permissions.

```bash
glam delegate list <VAULT_ADDRESS>
```

### `glam delegate revoke`

Revoke a specific delegate's permissions.

```bash
glam delegate revoke <VAULT_ADDRESS> <DELEGATE_PUBKEY>
```

### `glam delegate revoke-all`

Revoke all delegates.

```bash
glam delegate revoke-all <VAULT_ADDRESS>
```

---

## Common Patterns

### Trading Delegate Setup

```bash
# Full Drift trading permissions
glam delegate grant <VAULT> <TRADER> \
  --permissions Swap,DriftInit,DriftDeposit,DriftWithdraw,DriftPlaceOrders,DriftCancelOrders,DriftPerpMarket,DriftSpotMarket
```

### Yield Manager Setup

```bash
# Kamino lending permissions
glam delegate grant <VAULT> <MANAGER> \
  --permissions KaminoInit,KaminoDeposit,KaminoWithdraw,KaminoBorrow,KaminoRepay
```

### Staking Delegate Setup

```bash
# Staking permissions
glam delegate grant <VAULT> <STAKER> \
  --permissions Stake,Unstake,LiquidUnstake
```
