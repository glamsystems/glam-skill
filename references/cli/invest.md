# CLI: Invest & Manage Commands

Tokenized vault subscription, redemption, and management operations.

## Investor Commands

### `glam invest subscribe`

Subscribe to a tokenized vault (deposit funds to receive shares).

```bash
glam invest subscribe <VAULT_ADDRESS> --share-class <INDEX> --amount <AMOUNT>
```

**Options:**
| Flag | Description |
|------|-------------|
| `--share-class <INDEX>` | Share class index (usually 0) |
| `--amount <AMOUNT>` | Subscription amount in base asset |

### `glam invest claim-subscription`

Claim processed subscription shares.

```bash
glam invest claim-subscription <VAULT_ADDRESS> --share-class <INDEX>
```

### `glam invest redeem`

Redeem shares from vault.

```bash
glam invest redeem <VAULT_ADDRESS> --share-class <INDEX> --shares <AMOUNT>
```

**Options:**
| Flag | Description |
|------|-------------|
| `--share-class <INDEX>` | Share class index |
| `--shares <AMOUNT>` | Number of shares to redeem |

### `glam invest claim-redemption`

Claim processed redemption proceeds.

```bash
glam invest claim-redemption <VAULT_ADDRESS> --share-class <INDEX>
```

---

## Manager Commands

### `glam manage price`

Set share class price (NAV).

```bash
glam manage price <VAULT_ADDRESS> --share-class <INDEX> --price <PRICE>
```

**Example:**

```bash
# Set NAV to 1.05 USDC per share
glam manage price <VAULT> --share-class 0 --price 1.05
```

### `glam manage fulfill`

Fulfill pending subscriptions/redemptions.

```bash
glam manage fulfill <VAULT_ADDRESS> --share-class <INDEX>
```

### `glam manage claim-fees`

Claim accrued management fees.

```bash
glam manage claim-fees <VAULT_ADDRESS>
```

### `glam manage pause` / `glam manage unpause`

Pause or unpause vault operations.

```bash
glam manage pause <VAULT_ADDRESS>
glam manage unpause <VAULT_ADDRESS>
```

---

## Subscription/Redemption Flow

### For Investors

```bash
# 1. Subscribe with USDC
glam invest subscribe <VAULT> --share-class 0 --amount 1000

# 2. Wait for manager to fulfill...

# 3. Claim your shares
glam invest claim-subscription <VAULT> --share-class 0

# Later, to exit:

# 4. Redeem shares
glam invest redeem <VAULT> --share-class 0 --shares 100

# 5. Wait for manager to fulfill...

# 6. Claim your USDC
glam invest claim-redemption <VAULT> --share-class 0
```

### For Managers

```bash
# 1. Update NAV price (should reflect current portfolio value)
glam manage price <VAULT> --share-class 0 --price 1.02

# 2. Fulfill pending subscriptions/redemptions
glam manage fulfill <VAULT> --share-class 0

# 3. Periodically claim fees
glam manage claim-fees <VAULT>
```

---

## Transfer Commands

### `glam transfer transfer`

Transfer assets from vault.

```bash
glam transfer transfer <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--destination <ADDRESS>` | Destination wallet address |
| `--mint <MINT>` | Token mint to transfer |
| `--amount <AMOUNT>` | Amount to transfer |

### `glam transfer view-policy`

View vault transfer policy.

```bash
glam transfer view-policy <VAULT_ADDRESS>
```

### `glam transfer allowlist-destination`

Add destination to transfer allowlist.

```bash
glam transfer allowlist-destination <VAULT_ADDRESS> <DESTINATION_ADDRESS>
```
