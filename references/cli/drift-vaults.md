# CLI: Drift Vaults

Deposit into Drift's managed vaults to earn yield from vault strategies.

> **Note:** This is different from `glam drift` (direct Drift Protocol trading). Drift Vaults are managed strategies run by third-party vault managers.

## Prerequisites

```bash
# Enable the integration first
glam integrations enable <VAULT> DriftVaults
```

## Commands

### `glam drift-vaults view-policy`

View allowlisted Drift vaults.

```bash
glam drift-vaults view-policy
```

### `glam drift-vaults allowlist-vault`

Add a Drift vault to the allowlist before depositing.

```bash
glam drift-vaults allowlist-vault <DRIFT_VAULT_ADDRESS>
```

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

### `glam drift-vaults list-depositors`

List your GLAM vault's positions in Drift vaults.

```bash
glam drift-vaults list-depositors
```

**Output shows:**
- Depositor address
- Drift vault name
- Share amount
- Deposit asset

### `glam drift-vaults deposit`

Deposit to a Drift vault.

```bash
glam drift-vaults deposit <DRIFT_VAULT_ADDRESS> <AMOUNT>
```

**Example:**
```bash
# Deposit 1000 USDC to a Drift vault
glam drift-vaults deposit DriftVault111... 1000
```

### `glam drift-vaults request-withdraw`

Request withdrawal from a Drift vault. Withdrawals have a delay period.

```bash
glam drift-vaults request-withdraw <DRIFT_VAULT_ADDRESS> <SHARES_AMOUNT>
```

**Example:**
```bash
# Request to withdraw 500 shares
glam drift-vaults request-withdraw DriftVault111... 500
```

### `glam drift-vaults cancel-withdraw`

Cancel a pending withdrawal request.

```bash
glam drift-vaults cancel-withdraw <DRIFT_VAULT_ADDRESS>
```

### `glam drift-vaults withdraw`

Claim withdrawal after the delay period has passed.

```bash
glam drift-vaults withdraw <DRIFT_VAULT_ADDRESS>
```

---

## Workflow

```bash
# 1. Enable integration
glam integrations enable <VAULT> DriftVaults

# 2. Allowlist the Drift vault you want to use
glam drift-vaults allowlist-vault <DRIFT_VAULT_ADDRESS>

# 3. Deposit
glam drift-vaults deposit <DRIFT_VAULT_ADDRESS> 1000

# 4. Check positions
glam drift-vaults list-depositors

# 5. When ready to exit: request withdrawal
glam drift-vaults request-withdraw <DRIFT_VAULT_ADDRESS> 500

# 6. After delay period: claim withdrawal
glam drift-vaults withdraw <DRIFT_VAULT_ADDRESS>
```

---

## Important Notes

- **Allowlist required**: Must allowlist each Drift vault before depositing
- **Withdrawal delay**: Withdrawals have a delay period set by the vault manager
- **Different from Drift Protocol**: This is for managed vaults, not direct perp/spot trading
