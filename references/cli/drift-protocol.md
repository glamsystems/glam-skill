# CLI: Drift Protocol Commands (`glam drift-protocol`)

Perpetuals and spot trading via Drift Protocol.

**Prerequisite:** Enable `DriftProtocol` integration before using these commands.

```bash
glam integration enable <VAULT_ADDRESS> DriftProtocol
```

## Commands

### `glam drift-protocol init-user`

Initialize Drift user account for vault. **Required before any Drift operations.**

```bash
glam drift-protocol init-user <VAULT_ADDRESS>
```

### `glam drift-protocol deposit`

Deposit collateral to Drift.

```bash
glam drift-protocol deposit <VAULT_ADDRESS> --market-index <INDEX> --amount <AMOUNT>
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market-index <INDEX>` | Spot market index (0=USDC, 1=SOL, etc.) |
| `--amount <AMOUNT>` | Amount to deposit |

### `glam drift-protocol withdraw`

Withdraw collateral from Drift.

```bash
glam drift-protocol withdraw <VAULT_ADDRESS> --market-index <INDEX> --amount <AMOUNT>
```

### `glam drift-protocol spot`

Place spot market order.

```bash
glam drift-protocol spot <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market-index <INDEX>` | Spot market index |
| `--amount <AMOUNT>` | Order size |
| `--direction <DIR>` | `long` or `short` |
| `--price <PRICE>` | Limit price (optional) |
| `--reduce-only` | Reduce only flag |

### `glam drift-protocol perp`

Place perpetual market order.

```bash
glam drift-protocol perp <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market-index <INDEX>` | Perp market index (0=SOL-PERP) |
| `--amount <AMOUNT>` | Order size in base units |
| `--direction <DIR>` | `long` or `short` |
| `--price <PRICE>` | Limit price (optional) |
| `--reduce-only` | Reduce only flag |
| `--post-only` | Post only flag |

**Examples:**

```bash
# Long 1 SOL-PERP at market
glam drift-protocol perp <VAULT> --market-index 0 --amount 1 --direction long

# Short 0.5 SOL-PERP with limit price
glam drift-protocol perp <VAULT> --market-index 0 --amount 0.5 --direction short --price 100

# Close position (reduce only)
glam drift-protocol perp <VAULT> --market-index 0 --amount 1 --direction short --reduce-only
```

### `glam drift-protocol list-positions`

List open positions and orders.

```bash
glam drift-protocol list-positions <VAULT_ADDRESS>
```

### `glam drift-protocol list-orders`

List open orders.

```bash
glam drift-protocol list-orders <VAULT_ADDRESS>
```

### `glam drift-protocol list-users`

List Drift sub-accounts for the vault.

```bash
glam drift-protocol list-users <VAULT_ADDRESS>
```

### `glam drift-protocol cancel`

Cancel Drift order(s).

```bash
glam drift-protocol cancel <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--order-id <ID>` | Specific order ID to cancel |
| `--market-index <INDEX>` | Cancel all orders for market |
| `--all` | Cancel all orders |

---

## Policy Commands

### `glam drift-protocol view-policy`

View Drift policy settings for the vault.

```bash
glam drift-protocol view-policy <VAULT_ADDRESS>
```

### `glam drift-protocol allowlist-market`

Add a market to the Drift allowlist.

```bash
glam drift-protocol allowlist-market <VAULT_ADDRESS> <MARKET_INDEX>
```

### `glam drift-protocol remove-market`

Remove a market from the Drift allowlist.

```bash
glam drift-protocol remove-market <VAULT_ADDRESS> <MARKET_INDEX>
```

### `glam drift-protocol allowlist-borrowable-asset`

Add an asset to the borrowable allowlist.

```bash
glam drift-protocol allowlist-borrowable-asset <VAULT_ADDRESS> <MARKET_INDEX>
```

### `glam drift-protocol remove-borrowable-asset`

Remove an asset from the borrowable allowlist.

```bash
glam drift-protocol remove-borrowable-asset <VAULT_ADDRESS> <MARKET_INDEX>
```

---

## User Management Commands

### `glam drift-protocol margin`

Enable or disable margin trading for the Drift user.

```bash
glam drift-protocol margin <VAULT_ADDRESS> <true|false>
```

### `glam drift-protocol settle`

Settle PnL for a market.

```bash
glam drift-protocol settle <VAULT_ADDRESS> --market-index <INDEX>
```

### `glam drift-protocol delete-user`

Delete a Drift user account.

```bash
glam drift-protocol delete-user <VAULT_ADDRESS>
```

### `glam drift-protocol update-user-pool-id`

Update the user pool ID.

```bash
glam drift-protocol update-user-pool-id <VAULT_ADDRESS> <POOL_ID>
```

---

## Market Indices

### Spot Markets
| Index | Asset |
|-------|-------|
| 0 | USDC |
| 1 | SOL |
| 2 | mSOL |
| 3 | wBTC |
| 4 | wETH |

### Perp Markets
| Index | Market |
|-------|--------|
| 0 | SOL-PERP |
| 1 | BTC-PERP |
| 2 | ETH-PERP |

---

## Typical Workflow

```bash
# 1. Enable integration
glam integration enable <VAULT> DriftProtocol

# 2. Initialize user (required once)
glam drift-protocol init-user <VAULT>

# 3. Deposit collateral
glam drift-protocol deposit <VAULT> --market-index 0 --amount 1000  # 1000 USDC

# 4. Open position
glam drift-protocol perp <VAULT> --market-index 0 --amount 1 --direction long

# 5. Monitor positions
glam drift-protocol list-positions <VAULT>

# 6. Close position
glam drift-protocol perp <VAULT> --market-index 0 --amount 1 --direction short --reduce-only
```
