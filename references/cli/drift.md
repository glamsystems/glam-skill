# CLI: Drift Commands

Perpetuals and spot trading via Drift Protocol.

**Prerequisite:** Enable `DriftProtocol` integration before using these commands.

```bash
glam integrations enable <VAULT_ADDRESS> DriftProtocol
```

## Commands

### `glam drift init-user`

Initialize Drift user account for vault. **Required before any Drift operations.**

```bash
glam drift init-user <VAULT_ADDRESS>
```

### `glam drift deposit`

Deposit collateral to Drift.

```bash
glam drift deposit <VAULT_ADDRESS> --market-index <INDEX> --amount <AMOUNT>
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market-index <INDEX>` | Spot market index (0=USDC, 1=SOL, etc.) |
| `--amount <AMOUNT>` | Amount to deposit |

### `glam drift withdraw`

Withdraw collateral from Drift.

```bash
glam drift withdraw <VAULT_ADDRESS> --market-index <INDEX> --amount <AMOUNT>
```

### `glam drift spot`

Place spot market order.

```bash
glam drift spot <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market-index <INDEX>` | Spot market index |
| `--amount <AMOUNT>` | Order size |
| `--direction <DIR>` | `long` or `short` |
| `--price <PRICE>` | Limit price (optional) |
| `--reduce-only` | Reduce only flag |

### `glam drift perp`

Place perpetual market order.

```bash
glam drift perp <VAULT_ADDRESS> [OPTIONS]
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
glam drift perp <VAULT> --market-index 0 --amount 1 --direction long

# Short 0.5 SOL-PERP with limit price
glam drift perp <VAULT> --market-index 0 --amount 0.5 --direction short --price 100

# Close position (reduce only)
glam drift perp <VAULT> --market-index 0 --amount 1 --direction short --reduce-only
```

### `glam drift list-positions`

List open positions and orders.

```bash
glam drift list-positions <VAULT_ADDRESS>
```

### `glam drift cancel`

Cancel Drift order(s).

```bash
glam drift cancel <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--order-id <ID>` | Specific order ID to cancel |
| `--market-index <INDEX>` | Cancel all orders for market |
| `--all` | Cancel all orders |

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
glam integrations enable <VAULT> DriftProtocol

# 2. Initialize user (required once)
glam drift init-user <VAULT>

# 3. Deposit collateral
glam drift deposit <VAULT> --market-index 0 --amount 1000  # 1000 USDC

# 4. Open position
glam drift perp <VAULT> --market-index 0 --amount 1 --direction long

# 5. Monitor positions
glam drift list-positions <VAULT>

# 6. Close position
glam drift perp <VAULT> --market-index 0 --amount 1 --direction short --reduce-only
```
