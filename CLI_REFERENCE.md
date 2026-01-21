# GLAM CLI Reference

Complete command reference for `@glamsystems/glam-cli`.

## Table of Contents

- [Configuration](#configuration)
- [Vault Commands](#vault-commands)
- [Integration Commands](#integration-commands)
- [Delegate Commands](#delegate-commands)
- [Jupiter Commands](#jupiter-commands)
- [Drift Commands](#drift-commands)
- [Kamino Commands](#kamino-commands)
- [Invest Commands](#invest-commands)
- [Manage Commands](#manage-commands)
- [Transfer Commands](#transfer-commands)
- [CCTP Commands](#cctp-commands)
- [Timelock Commands](#timelock-commands)
- [Global Options](#global-options)

---

## Configuration

### `glam config view`

Display current configuration.

```bash
glam config view
```

### `glam config set`

Set a configuration value.

```bash
glam config set <KEY> <VALUE>
```

**Keys:**
- `keypairPath` - Path to Solana keypair JSON file (required)
- `rpcUrl` - Solana RPC endpoint URL
- `priorityFee` - Priority fee in microlamports
- `cluster` - Cluster name: `mainnet-beta`, `devnet`, `localnet`

**Examples:**

```bash
glam config set keypairPath ~/.config/solana/id.json
glam config set rpcUrl https://api.mainnet-beta.solana.com
glam config set priorityFee 10000
glam config set cluster devnet
```

---

## Vault Commands

### `glam vault create`

Create a new vault.

```bash
glam vault create [OPTIONS]
```

**Options:**
| Flag | Description | Default |
|------|-------------|---------|
| `--name <NAME>` | Vault name | Required |
| `--assets <MINTS>` | Comma-separated asset mints | SOL |
| `--share-class-name <NAME>` | Share class name (for tokenized vaults) | - |
| `--share-class-symbol <SYMBOL>` | Share class symbol | - |
| `--share-class-decimals <NUM>` | Share class decimals | 9 |
| `--manager <PUBKEY>` | Manager public key | Signer |

**Examples:**

```bash
# Basic vault
glam vault create --name "Treasury"

# Vault with multiple assets
glam vault create --name "Multi-Asset" --assets So11111111111111111111111111111111111111112,EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v

# Tokenized vault
glam vault create --name "Token Fund" --share-class-name "Fund Shares" --share-class-symbol "FUND"
```

### `glam vault view`

View vault details.

```bash
glam vault view <VAULT_ADDRESS>
```

**Output includes:**
- Vault name and address
- Manager and owner
- Enabled integrations
- Share classes (if tokenized)
- Asset allowlist

### `glam vault list`

List vaults owned by configured keypair.

```bash
glam vault list [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--all` | List all vaults (not just owned) |
| `--manager <PUBKEY>` | Filter by manager |

### `glam vault set`

Update vault configuration.

```bash
glam vault set <VAULT_ADDRESS> <KEY> <VALUE>
```

**Keys:**
- `name` - Vault name
- `uri` - Metadata URI

### `glam vault balances`

Show vault token balances.

```bash
glam vault balances <VAULT_ADDRESS>
```

### `glam vault holdings`

Show vault holdings with USD values.

```bash
glam vault holdings <VAULT_ADDRESS>
```

### `glam vault wrap`

Wrap SOL to wSOL in vault.

```bash
glam vault wrap <VAULT_ADDRESS> --amount <AMOUNT>
```

### `glam vault unwrap`

Unwrap wSOL to SOL in vault.

```bash
glam vault unwrap <VAULT_ADDRESS> --amount <AMOUNT>
```

### `glam vault allowlist-asset`

Add asset to vault allowlist.

```bash
glam vault allowlist-asset <VAULT_ADDRESS> <MINT_ADDRESS>
```

### `glam vault close`

Close a vault (must be empty).

```bash
glam vault close <VAULT_ADDRESS>
```

---

## Integration Commands

### `glam integrations list`

List available integrations.

```bash
glam integrations list
```

**Available integrations:**
- `JupiterSwap` - Token swaps via Jupiter aggregator
- `DriftProtocol` - Perpetuals and spot trading
- `KaminoLend` - Lending and borrowing
- `KaminoVault` - Kamino vaults
- `KaminoFarm` - Kamino farming
- `MarinadeStaking` - Marinade liquid staking
- `NativeStaking` - Native SOL staking
- `SplStakePool` - SPL stake pools
- `SanctumStaking` - Sanctum staking
- `MeteoraDlmm` - Meteora DLMM pools
- `Invariant` - Invariant DEX
- `Orca` - Orca DEX

### `glam integrations enable`

Enable integrations for a vault.

```bash
glam integrations enable <VAULT_ADDRESS> <INTEGRATION>...
```

**Examples:**

```bash
# Enable single integration
glam integrations enable <VAULT> JupiterSwap

# Enable multiple integrations
glam integrations enable <VAULT> JupiterSwap DriftProtocol KaminoLend
```

### `glam integrations disable`

Disable an integration.

```bash
glam integrations disable <VAULT_ADDRESS> <INTEGRATION>
```

---

## Delegate Commands

### `glam delegates grant`

Grant delegate permissions.

```bash
glam delegates grant <VAULT_ADDRESS> <DELEGATE_PUBKEY> --permissions <PERMS>
```

**Options:**
| Flag | Description |
|------|-------------|
| `--permissions <PERMS>` | Comma-separated permissions |

**Available permissions:**
- `Swap` - Token swaps
- `Stake` - Staking operations
- `Unstake` - Unstaking operations
- `LiquidUnstake` - Liquid unstaking
- `DriftInit` - Initialize Drift account
- `DriftDeposit` - Deposit to Drift
- `DriftWithdraw` - Withdraw from Drift
- `DriftUpdateUser` - Update Drift user
- `DriftDeleteUser` - Delete Drift user
- `DriftPlaceOrders` - Place Drift orders
- `DriftCancelOrders` - Cancel Drift orders
- `DriftPerpMarket` - Drift perp trading
- `DriftSpotMarket` - Drift spot trading
- `KaminoInit` - Initialize Kamino
- `KaminoDeposit` - Deposit to Kamino
- `KaminoWithdraw` - Withdraw from Kamino
- `KaminoBorrow` - Borrow from Kamino
- `KaminoRepay` - Repay Kamino loans
- `MeteoraInit` - Initialize Meteora
- `MeteoraDeposit` - Deposit to Meteora
- `MeteoraWithdraw` - Withdraw from Meteora

**Examples:**

```bash
glam delegates grant <VAULT> <DELEGATE> --permissions Swap,Stake,Unstake
glam delegates grant <VAULT> <DELEGATE> --permissions DriftDeposit,DriftWithdraw,DriftPlaceOrders
```

### `glam delegates list`

List vault delegates.

```bash
glam delegates list <VAULT_ADDRESS>
```

### `glam delegates revoke`

Revoke delegate permissions.

```bash
glam delegates revoke <VAULT_ADDRESS> <DELEGATE_PUBKEY>
```

### `glam delegates revoke-all`

Revoke all delegates.

```bash
glam delegates revoke-all <VAULT_ADDRESS>
```

---

## Jupiter Commands

### `glam jupiter swap`

Execute token swap via Jupiter.

```bash
glam jupiter swap <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description | Required |
|------|-------------|----------|
| `--input-mint <MINT>` | Input token mint | Yes |
| `--output-mint <MINT>` | Output token mint | Yes |
| `--amount <AMOUNT>` | Amount in token units | Yes |
| `--slippage <BPS>` | Slippage tolerance in basis points | No (default: 50) |
| `--only-direct-routes` | Use only direct routes | No |

**Examples:**

```bash
# Swap 100 USDC to SOL with 0.5% slippage
glam jupiter swap <VAULT> \
  --input-mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --output-mint So11111111111111111111111111111111111111112 \
  --amount 100 \
  --slippage 50

# Swap with direct routes only
glam jupiter swap <VAULT> \
  --input-mint <INPUT> \
  --output-mint <OUTPUT> \
  --amount 1000 \
  --only-direct-routes
```

### `glam jupiter view-policy`

View vault's Jupiter swap policy.

```bash
glam jupiter view-policy <VAULT_ADDRESS>
```

### `glam jupiter set-max-slippage`

Set maximum slippage for swaps.

```bash
glam jupiter set-max-slippage <VAULT_ADDRESS> --max-slippage <BPS>
```

**Example:**

```bash
glam jupiter set-max-slippage <VAULT> --max-slippage 100  # 1%
```

### `glam jupiter allowlist-token`

Add token to swap allowlist.

```bash
glam jupiter allowlist-token <VAULT_ADDRESS> <MINT_ADDRESS>
```

---

## Drift Commands

### `glam drift init-user`

Initialize Drift user account for vault.

```bash
glam drift init-user <VAULT_ADDRESS>
```

### `glam drift deposit`

Deposit collateral to Drift.

```bash
glam drift deposit <VAULT_ADDRESS> --market-index <INDEX> --amount <AMOUNT>
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market-index <INDEX>` | Spot market index (0=USDC, 1=SOL, etc.) |
| `--amount <AMOUNT>` | Amount to deposit |

### `glam drift withdraw`

Withdraw collateral from Drift.

```bash
glam drift withdraw <VAULT_ADDRESS> --market-index <INDEX> --amount <AMOUNT>
```

### `glam drift spot`

Place spot market order.

```bash
glam drift spot <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market-index <INDEX>` | Spot market index |
| `--amount <AMOUNT>` | Order size |
| `--direction <DIR>` | `long` or `short` |
| `--price <PRICE>` | Limit price (optional) |
| `--reduce-only` | Reduce only flag |

### `glam drift perp`

Place perpetual market order.

```bash
glam drift perp <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--market-index <INDEX>` | Perp market index (0=SOL-PERP) |
| `--amount <AMOUNT>` | Order size in base units |
| `--direction <DIR>` | `long` or `short` |
| `--price <PRICE>` | Limit price (optional) |
| `--reduce-only` | Reduce only flag |
| `--post-only` | Post only flag |

**Examples:**

```bash
# Long 1 SOL-PERP at market
glam drift perp <VAULT> --market-index 0 --amount 1 --direction long

# Short 0.5 SOL-PERP with limit price
glam drift perp <VAULT> --market-index 0 --amount 0.5 --direction short --price 100
```

### `glam drift list-positions`

List open positions and orders.

```bash
glam drift list-positions <VAULT_ADDRESS>
```

### `glam drift cancel`

Cancel Drift order.

```bash
glam drift cancel <VAULT_ADDRESS> --order-id <ID>
```

**Options:**
| Flag | Description |
|------|-------------|
| `--order-id <ID>` | Order ID to cancel |
| `--market-index <INDEX>` | Cancel all orders for market |
| `--all` | Cancel all orders |

---

## Kamino Commands

### `glam kamino init`

Initialize Kamino lending for vault.

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

List Kamino positions.

```bash
glam kamino list <VAULT_ADDRESS>
```

---

## Invest Commands

### `glam invest subscribe`

Subscribe to a tokenized vault.

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

Claim processed redemption.

```bash
glam invest claim-redemption <VAULT_ADDRESS> --share-class <INDEX>
```

---

## Manage Commands

### `glam manage price`

Set share class price (NAV).

```bash
glam manage price <VAULT_ADDRESS> --share-class <INDEX> --price <PRICE>
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

### `glam manage pause`

Pause vault operations.

```bash
glam manage pause <VAULT_ADDRESS>
```

### `glam manage unpause`

Unpause vault operations.

```bash
glam manage unpause <VAULT_ADDRESS>
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

---

## CCTP Commands

### `glam cctp bridge-usdc`

Bridge USDC to another chain via Circle CCTP.

```bash
glam cctp bridge-usdc <VAULT_ADDRESS> [OPTIONS]
```

**Options:**
| Flag | Description |
|------|-------------|
| `--destination <ADDRESS>` | Destination address on target chain |
| `--amount <AMOUNT>` | USDC amount to bridge |
| `--destination-domain <DOMAIN>` | Target domain (0=Ethereum, 1=Avalanche, etc.) |

**Destination domains:**
- `0` - Ethereum
- `1` - Avalanche
- `2` - Optimism
- `3` - Arbitrum
- `6` - Base

### `glam cctp receive`

Receive bridged USDC.

```bash
glam cctp receive <VAULT_ADDRESS> --message <MESSAGE_HEX>
```

### `glam cctp list`

List CCTP bridge transactions.

```bash
glam cctp list <VAULT_ADDRESS>
```

---

## Timelock Commands

### `glam timelock view`

View timelock status.

```bash
glam timelock view <VAULT_ADDRESS>
```

### `glam timelock set`

Set timelock delay (requires apply after delay period).

```bash
glam timelock set <VAULT_ADDRESS> --delay <SECONDS>
```

### `glam timelock apply`

Apply pending timelock change.

```bash
glam timelock apply <VAULT_ADDRESS>
```

### `glam timelock cancel`

Cancel pending timelock change.

```bash
glam timelock cancel <VAULT_ADDRESS>
```

---

## Global Options

These options apply to all commands:

| Flag | Description |
|------|-------------|
| `--rpc-url <URL>` | Override configured RPC URL |
| `--keypair <PATH>` | Override configured keypair path |
| `--priority-fee <FEE>` | Override priority fee in microlamports |
| `--skip-preflight` | Skip transaction preflight |
| `--dry-run` | Simulate transaction without sending |
| `--verbose` | Enable verbose output |
| `--json` | Output in JSON format |

**Examples:**

```bash
# Use different RPC
glam vault list --rpc-url https://my-rpc.com

# Dry run a swap
glam jupiter swap <VAULT> --input-mint <A> --output-mint <B> --amount 100 --dry-run

# JSON output for scripting
glam vault view <VAULT> --json
```
