# CLI: Kamino Vaults

Deposit into Kamino's automated yield vaults.

> **Note:** This is different from `glam kamino` (lending/borrowing). Kamino Vaults are automated strategies that manage positions for you.

## Prerequisites

```bash
# Enable the integration first
glam integrations enable <VAULT> KaminoVault
```

## Commands

### `glam kvaults view-policy`

View allowlisted Kamino vaults.

```bash
glam kvaults view-policy
```

### `glam kvaults allowlist-vault`

Add a Kamino vault to the allowlist before depositing.

```bash
glam kvaults allowlist-vault <KAMINO_VAULT_ADDRESS>
```

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

### `glam kvaults remove-vault`

Remove a Kamino vault from the allowlist.

```bash
glam kvaults remove-vault <KAMINO_VAULT_ADDRESS>
```

### `glam kvaults deposit`

Deposit to a Kamino vault.

```bash
glam kvaults deposit <KAMINO_VAULT_ADDRESS> <AMOUNT>
```

**Example:**
```bash
# Deposit 1000 USDC to a Kamino vault
glam kvaults deposit KaminoVault111... 1000
```

### `glam kvaults withdraw`

Withdraw from a Kamino vault by burning vault shares.

```bash
glam kvaults withdraw <KAMINO_VAULT_ADDRESS> <SHARES_AMOUNT>
```

**Example:**
```bash
# Withdraw 500 shares worth of assets
glam kvaults withdraw KaminoVault111... 500
```

---

## Workflow

```bash
# 1. Enable integration
glam integrations enable <VAULT> KaminoVault

# 2. Allowlist the Kamino vault you want to use
glam kvaults allowlist-vault <KAMINO_VAULT_ADDRESS>

# 3. Deposit
glam kvaults deposit <KAMINO_VAULT_ADDRESS> 1000

# 4. When ready to exit: withdraw
glam kvaults withdraw <KAMINO_VAULT_ADDRESS> 500
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
