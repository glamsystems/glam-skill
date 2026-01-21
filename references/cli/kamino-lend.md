# CLI: Kamino Lend Commands (`glam kamino-lend`)

Lending and borrowing via Kamino Finance.

**Prerequisite:** Enable `KaminoLend` integration before using these commands.

```bash
glam integration enable <VAULT_ADDRESS> KaminoLend
```

## Commands

### `glam kamino-lend init`

Initialize Kamino lending for vault. **Required before any Kamino operations.**

```bash
glam kamino-lend init <VAULT_ADDRESS>
```

### `glam kamino-lend deposit`

Deposit to Kamino lending market.

```bash
glam kamino-lend deposit <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market <ADDRESS>` | Lending market address |
| `--mint <MINT>` | Token mint to deposit |
| `--amount <AMOUNT>` | Amount to deposit |

**Example:**

```bash
# Deposit 1000 USDC to main market
glam kamino-lend deposit <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 1000
```

### `glam kamino-lend withdraw`

Withdraw from Kamino lending market.

```bash
glam kamino-lend withdraw <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market <ADDRESS>` | Lending market address |
| `--mint <MINT>` | Token mint to withdraw |
| `--amount <AMOUNT>` | Amount to withdraw |

### `glam kamino-lend borrow`

Borrow from Kamino lending market.

```bash
glam kamino-lend borrow <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market <ADDRESS>` | Lending market address |
| `--mint <MINT>` | Token mint to borrow |
| `--amount <AMOUNT>` | Amount to borrow |

### `glam kamino-lend repay`

Repay Kamino loan.

```bash
glam kamino-lend repay <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market <ADDRESS>` | Lending market address |
| `--mint <MINT>` | Token mint to repay |
| `--amount <AMOUNT>` | Amount to repay |

### `glam kamino-lend list`

List Kamino lending positions.

```bash
glam kamino-lend list <VAULT_ADDRESS>
```

---

## Policy Commands

### `glam kamino-lend view-policy`

View Kamino lending policy settings for the vault.

```bash
glam kamino-lend view-policy <VAULT_ADDRESS>
```

### `glam kamino-lend allowlist-market`

Add a lending market to the allowlist.

```bash
glam kamino-lend allowlist-market <VAULT_ADDRESS> <MARKET_ADDRESS>
```

### `glam kamino-lend remove-market`

Remove a lending market from the allowlist.

```bash
glam kamino-lend remove-market <VAULT_ADDRESS> <MARKET_ADDRESS>
```

### `glam kamino-lend allowlist-borrowable-asset`

Add an asset to the borrowable allowlist.

```bash
glam kamino-lend allowlist-borrowable-asset <VAULT_ADDRESS> <MINT_ADDRESS>
```

### `glam kamino-lend remove-borrowable-asset`

Remove an asset from the borrowable allowlist.

```bash
glam kamino-lend remove-borrowable-asset <VAULT_ADDRESS> <MINT_ADDRESS>
```

---

## Kamino Market Addresses

| Market | Address | Description |
|--------|---------|-------------|
| Main Market | `7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF` | Primary lending market |
| JLP Market | `DxXdAyU3kCjnyggvHmY5nAwg5cRbbmdyX3npfDMjjMek` | JLP collateral market |
| Altcoins | `ByYiZxp8QrdN9qbdtaAiePN8AAr3qvTPppNJDpf5DVJ5` | Altcoin market |

---

## Typical Workflow

```bash
# 1. Enable integration
glam integration enable <VAULT> KaminoLend

# 2. Initialize (required once)
glam kamino-lend init <VAULT>

# 3. Deposit collateral
glam kamino-lend deposit <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 5000

# 4. Borrow against collateral
glam kamino-lend borrow <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint So11111111111111111111111111111111111111112 \
  --amount 10

# 5. Monitor positions
glam kamino-lend list <VAULT>

# 6. Repay loan
glam kamino-lend repay <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint So11111111111111111111111111111111111111112 \
  --amount 10

# 7. Withdraw collateral
glam kamino-lend withdraw <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 5000
```
