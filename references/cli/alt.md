# CLI: Address Lookup Tables (ALT)

Manage Address Lookup Tables to optimize transaction sizes.

> **Advanced feature**: ALTs reduce transaction size by storing frequently-used account addresses. Required for complex transactions that would otherwise exceed size limits.

## Commands

### `glam alt list`

List address lookup tables owned by the vault.

```bash
glam alt list
```

### `glam alt create`

Create a new address lookup table for the vault.

```bash
glam alt create
```

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

**Note:** Requires `GLAM_API` environment variable to be set.

### `glam alt extend`

Extend an existing lookup table with additional addresses.

```bash
glam alt extend <TABLE>
```

**Arguments:**
| Argument | Description |
|----------|-------------|
| `TABLE` | Address lookup table public key |

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

---

## When to Use ALTs

ALTs are useful when:
- Transactions fail due to size limits
- You're performing complex multi-protocol operations
- You want to reduce transaction fees (smaller = cheaper)

## Workflow

```bash
# 1. Check existing lookup tables
glam alt list

# 2. Create new lookup table if needed
glam alt create

# 3. Extend with more addresses if needed
glam alt extend <TABLE_ADDRESS>
```

---

## Important Notes

- **API required**: ALT creation requires the GLAM API (`GLAM_API` env var)
- **Advanced users**: Most users won't need to manage ALTs directly
- **Automatic handling**: Many CLI commands handle ALTs automatically
