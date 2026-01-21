# CLI: Manage Commands

Vault management operations for tokenized vaults: pricing, fulfillment, fees, and subscription controls.

## Commands

### `glam manage price`

Price vault assets (update NAV).

```bash
glam manage price
```

Updates the vault's Net Asset Value based on current market prices.

### `glam manage fulfill`

Fulfill queued subscriptions and redemptions.

```bash
glam manage fulfill
```

Processes pending investor subscriptions and redemptions at current NAV.

### `glam manage claim-fees`

Claim accrued management fees.

```bash
glam manage claim-fees
```

### `glam manage update-min-subscription`

Set minimum subscription amount.

```bash
glam manage update-min-subscription <AMOUNT>
```

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

**Example:**
```bash
# Set minimum subscription to 100 USDC
glam manage update-min-subscription 100
```

### `glam manage update-min-redemption`

Set minimum redemption amount.

```bash
glam manage update-min-redemption <AMOUNT>
```

### `glam manage pause`

Pause subscription or redemption.

```bash
glam manage pause <ACTION>
```

**Arguments:**
| Argument | Values |
|----------|--------|
| `ACTION` | `subscription` or `redemption` |

**Example:**
```bash
# Pause new subscriptions
glam manage pause subscription

# Pause redemptions
glam manage pause redemption
```

### `glam manage unpause`

Resume subscription or redemption.

```bash
glam manage unpause <ACTION>
```

**Example:**
```bash
# Resume subscriptions
glam manage unpause subscription
```

---

## Manager Workflow

```bash
# Daily/periodic operations for tokenized vault managers:

# 1. Price vault (update NAV)
glam manage price

# 2. Process pending subscriptions/redemptions
glam manage fulfill

# 3. Claim fees (when desired)
glam manage claim-fees
```

---

## Emergency Controls

```bash
# Pause all new subscriptions (e.g., during market volatility)
glam manage pause subscription

# Resume when ready
glam manage unpause subscription
```

---

## Important Notes

- **For tokenized vaults only**: These commands apply to vaults with share classes
- **Pricing first**: Always price before fulfill to ensure accurate NAV
- **Pause controls**: Useful for maintenance windows or emergencies
