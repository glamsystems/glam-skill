# CLI: Token Transfers

Transfer SPL tokens from vault to allowlisted destinations.

## Commands

### `glam transfer <AMOUNT> <TO>`

Transfer tokens to a destination address.

```bash
glam transfer <AMOUNT> <TO> [OPTIONS]
```

**Arguments:**
| Argument | Description |
|----------|-------------|
| `AMOUNT` | Amount to transfer |
| `TO` | Destination address (must be allowlisted) |

**Options:**
| Flag | Description |
|------|-------------|
| `-t, --token <MINT>` | Token mint address (defaults to wSOL) |
| `-y, --yes` | Skip confirmation prompt |

**Examples:**
```bash
# Transfer 10 wSOL
glam transfer 10 <DESTINATION_PUBKEY>

# Transfer 100 USDC
glam transfer 100 <DESTINATION_PUBKEY> --token EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
```

### `glam transfer view-policy`

View the token transfer allowlist.

```bash
glam transfer view-policy
```

### `glam transfer allowlist-destination`

Add a destination address to the transfer allowlist.

```bash
glam transfer allowlist-destination <PUBKEY>
```

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

### `glam transfer remove-destination`

Remove a destination from the allowlist.

```bash
glam transfer remove-destination <PUBKEY>
```

---

## Workflow

```bash
# 1. Check current allowlist
glam transfer view-policy

# 2. Add destination to allowlist (required before transferring)
glam transfer allowlist-destination <DESTINATION_PUBKEY>

# 3. Transfer tokens
glam transfer 100 <DESTINATION_PUBKEY> --token <MINT>

# 4. Remove destination if no longer needed
glam transfer remove-destination <DESTINATION_PUBKEY>
```

---

## Important Notes

- **Allowlist required**: Destinations must be allowlisted before transfers
- **Default token**: If no `--token` specified, transfers wSOL
- **Security**: Use allowlist to prevent unauthorized fund movement
