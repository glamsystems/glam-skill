# CLI: Advanced Commands

CCTP cross-chain bridging and timelock operations.

## CCTP Commands (Cross-Chain)

Bridge USDC between chains via Circle's Cross-Chain Transfer Protocol.

### `glam cctp bridge-usdc`

Bridge USDC to another chain.

```bash
glam cctp bridge-usdc <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--destination <ADDRESS>` | Destination address on target chain |
| `--amount <AMOUNT>` | USDC amount to bridge |
| `--destination-domain <DOMAIN>` | Target domain ID |

**Destination domains:**
| Domain | Chain |
|--------|-------|
| 0 | Ethereum |
| 1 | Avalanche |
| 2 | Optimism |
| 3 | Arbitrum |
| 6 | Base |

**Example:**

```bash
# Bridge 1000 USDC to Ethereum
glam cctp bridge-usdc <VAULT> \
  --destination 0x742d35Cc6634C0532925a3b844Bc9e7595f00000 \
  --amount 1000 \
  --destination-domain 0
```

### `glam cctp receive`

Receive bridged USDC (from another chain to Solana).

```bash
glam cctp receive <VAULT_ADDRESS> --message <MESSAGE_HEX>
```

### `glam cctp list`

List CCTP bridge transactions.

```bash
glam cctp list <VAULT_ADDRESS>
```

---

## CCTP Policy Commands

### `glam cctp view-policy`

View CCTP policy settings for the vault.

```bash
glam cctp view-policy <VAULT_ADDRESS>
```

### `glam cctp allowlist-destination`

Add a destination address/domain to the CCTP allowlist.

```bash
glam cctp allowlist-destination <VAULT_ADDRESS> --domain <DOMAIN_ID> --address <ADDRESS>
```

### `glam cctp remove-destination`

Remove a destination from the CCTP allowlist.

```bash
glam cctp remove-destination <VAULT_ADDRESS> --domain <DOMAIN_ID> --address <ADDRESS>
```

---

## Timelock Commands

Protect vault with timelocked configuration changes.

### `glam timelock view`

View current timelock status.

```bash
glam timelock view <VAULT_ADDRESS>
```

### `glam timelock set`

Set timelock delay. Change takes effect after current delay period.

```bash
glam timelock set <VAULT_ADDRESS> --delay <SECONDS>
```

**Common delays:**
| Seconds | Duration |
|---------|----------|
| 3600 | 1 hour |
| 86400 | 1 day |
| 259200 | 3 days |
| 604800 | 1 week |

**Example:**

```bash
# Set 24-hour timelock
glam timelock set <VAULT> --delay 86400
```

### `glam timelock apply`

Apply pending timelock change after delay period.

```bash
glam timelock apply <VAULT_ADDRESS>
```

### `glam timelock cancel`

Cancel pending timelock change.

```bash
glam timelock cancel <VAULT_ADDRESS>
```

---

## Timelock Workflow

```bash
# 1. View current status
glam timelock view <VAULT>

# 2. Set new delay (e.g., 24 hours)
glam timelock set <VAULT> --delay 86400

# 3. Wait for current delay period to pass...

# 4. Apply the change
glam timelock apply <VAULT>

# Or cancel if needed
glam timelock cancel <VAULT>
```

---

## Security Best Practices

1. **Use timelocks for production vaults** - Gives time to react to unauthorized changes
2. **Start with shorter delays** - 1 hour during setup, increase for production
3. **Monitor pending changes** - Check `timelock view` regularly
4. **Cross-chain transfers** - Double-check destination addresses (irreversible)
