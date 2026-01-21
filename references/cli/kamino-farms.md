# CLI: Kamino Farms

Stake tokens in Kamino farms to earn rewards.

## Prerequisites

```bash
# Enable the integration first
glam integrations enable <VAULT> KaminoFarm
```

## Commands

### `glam kfarms list`

List Kamino farms where your vault has unclaimed rewards.

```bash
glam kfarms list
```

**Output shows:**
- Vault farm user address
- Reward token mint
- Unclaimed reward amount

### `glam kfarms stake`

Stake tokens to a Kamino farm.

```bash
glam kfarms stake <FARM_STATE> <AMOUNT>
```

**Arguments:**
| Argument | Description |
|----------|-------------|
| `FARM_STATE` | Farm state public key |
| `AMOUNT` | Amount of farm token to stake |

**Options:**
| Flag | Description |
|------|-------------|
| `-y, --yes` | Skip confirmation prompt |

**Example:**
```bash
# Stake 100 tokens to a farm
glam kfarms stake FarmState111... 100
```

### `glam kfarms unstake`

Unstake tokens from a Kamino farm.

```bash
glam kfarms unstake <FARM_STATE> <AMOUNT>
```

**Example:**
```bash
# Unstake 50 tokens from a farm
glam kfarms unstake FarmState111... 50
```

### `glam kfarms harvest`

Harvest (claim) rewards from one or more farms.

```bash
glam kfarms harvest <FARM_STATE...>
```

**Example:**
```bash
# Harvest from single farm
glam kfarms harvest FarmState111...

# Harvest from multiple farms at once
glam kfarms harvest FarmState111... FarmState222... FarmState333...
```

---

## Workflow

```bash
# 1. Enable integration
glam integrations enable <VAULT> KaminoFarm

# 2. Stake tokens to earn rewards
glam kfarms stake <FARM_STATE> 1000

# 3. Check unclaimed rewards
glam kfarms list

# 4. Harvest rewards
glam kfarms harvest <FARM_STATE>

# 5. When done: unstake
glam kfarms unstake <FARM_STATE> 1000
```

---

## Important Notes

- **Different from Kamino Lending/Vaults**: This is for liquidity mining rewards
- **Multiple harvests**: You can harvest from multiple farms in one transaction
- **Reward tokens**: Rewards are typically protocol tokens or other incentives
