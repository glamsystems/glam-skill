# CLI: Kamino Commands

Lending and borrowing via Kamino Finance.

**Prerequisite:** Enable `KaminoLend` integration before using these commands.

```bash
glam integrations enable <VAULT_ADDRESS> KaminoLend
```

## Commands

### `glam kamino init`

Initialize Kamino lending for vault. **Required before any Kamino operations.**

```bash
glam kamino init <VAULT_ADDRESS>
```

### `glam kamino deposit`

Deposit to Kamino lending market.

```bash
glam kamino deposit <VAULT_ADDRESS> [OPTIONS]
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
glam kamino deposit <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 1000
```

### `glam kamino withdraw`

Withdraw from Kamino lending market.

```bash
glam kamino withdraw <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market <ADDRESS>` | Lending market address |
| `--mint <MINT>` | Token mint to withdraw |
| `--amount <AMOUNT>` | Amount to withdraw |

### `glam kamino borrow`

Borrow from Kamino lending market.

```bash
glam kamino borrow <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market <ADDRESS>` | Lending market address |
| `--mint <MINT>` | Token mint to borrow |
| `--amount <AMOUNT>` | Amount to borrow |

### `glam kamino repay`

Repay Kamino loan.

```bash
glam kamino repay <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market <ADDRESS>` | Lending market address |
| `--mint <MINT>` | Token mint to repay |
| `--amount <AMOUNT>` | Amount to repay |

### `glam kamino list`

List Kamino lending positions.

```bash
glam kamino list <VAULT_ADDRESS>
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
glam integrations enable <VAULT> KaminoLend

# 2. Initialize (required once)
glam kamino init <VAULT>

# 3. Deposit collateral
glam kamino deposit <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 5000

# 4. Borrow against collateral
glam kamino borrow <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint So11111111111111111111111111111111111111112 \
  --amount 10

# 5. Monitor positions
glam kamino list <VAULT>

# 6. Repay loan
glam kamino repay <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint So11111111111111111111111111111111111111112 \
  --amount 10

# 7. Withdraw collateral
glam kamino withdraw <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 5000
```
