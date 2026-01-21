# CLI: Kamino Vaults

Deposit into Kamino's automated yield vaults.

> **Note:** This is different from `glam kamino-lend` (lending/borrowing). Kamino Vaults are automated strategies that manage positions for you.

## Prerequisites

```bash
# Enable the integration first
glam integration enable <VAULT> KaminoVault
```

## Commands

### `glam kamino-vaults view-policy`

View allowlisted Kamino vaults.

```bash
glam kamino-vaults view-policy
```

### `glam kamino-vaults allowlist-vault`

Add a Kamino vault to the allowlist before depositing.

```bash
glam kamino-vaults allowlist-vault <KAMINO_VAULT_ADDRESS>
```

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

### `glam kamino-vaults remove-vault`

Remove a Kamino vault from the allowlist.

```bash
glam kamino-vaults remove-vault <KAMINO_VAULT_ADDRESS>
```

### `glam kamino-vaults deposit`

Deposit to a Kamino vault.

```bash
glam kamino-vaults deposit <KAMINO_VAULT_ADDRESS> <AMOUNT>
```

**Example:**
```bash
# Deposit 1000 USDC to a Kamino vault
glam kamino-vaults deposit KaminoVault111... 1000
```

### `glam kamino-vaults withdraw`

Withdraw from a Kamino vault by burning vault shares.

```bash
glam kamino-vaults withdraw <KAMINO_VAULT_ADDRESS> <SHARES_AMOUNT>
```

**Example:**
```bash
# Withdraw 500 shares worth of assets
glam kamino-vaults withdraw KaminoVault111... 500
```

---

## Workflow

```bash
# 1. Enable integration
glam integration enable <VAULT> KaminoVault

# 2. Allowlist the Kamino vault you want to use
glam kamino-vaults allowlist-vault <KAMINO_VAULT_ADDRESS>

# 3. Deposit
glam kamino-vaults deposit <KAMINO_VAULT_ADDRESS> 1000

# 4. When ready to exit: withdraw
glam kamino-vaults withdraw <KAMINO_VAULT_ADDRESS> 500
```

---

## Popular Kamino Vaults

Kamino vaults are identified by their public key. Common vault types include:
- SOL-USDC liquidity vaults
- Stablecoin yield vaults
- LST yield vaults

Check Kamino's documentation for current vault addresses.

---

## Important Notes

- **Allowlist required**: Must allowlist each Kamino vault before depositing
- **Different from Kamino Lending**: This is for automated vaults, not direct lending/borrowing
- **Shares vs Amount**: Withdrawals are specified in share amounts, not underlying token amounts
