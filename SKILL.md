---
name: glam
description: Manages GLAM Protocol vaults and DeFi operations on Solana. Use when the user mentions glam, glam-cli, glam-sdk, vault management, tokenized vaults, Solana DeFi integrations (Drift, Kamino, Jupiter), delegate permissions, treasury management, or asset management with access control. Supports CLI commands and TypeScript SDK operations.
---

# GLAM Protocol - Solana Asset Management

GLAM provides programmable investment infrastructure on Solana through vaults with built-in access control, DeFi integrations, and tokenization capabilities.

## Quick Start

### Install CLI

```bash
npm install -g @glamsystems/glam-cli
```

### Configure

```bash
# Set keypair path (required)
glam config set keypairPath /path/to/keypair.json

# Set RPC URL (optional, defaults to mainnet)
glam config set rpcUrl https://api.mainnet-beta.solana.com

# Verify configuration
glam config view
```

### Create First Vault

```bash
# Create a basic vault
glam vault create --name "My Vault"

# View vault details
glam vault view <VAULT_ADDRESS>

# List your vaults
glam vault list
```

## Core Operations

### Vault Management

```bash
# Create vault with custom settings
glam vault create --name "Treasury" --assets SOL,USDC

# Check balances
glam vault balances <VAULT_ADDRESS>

# View holdings with prices
glam vault holdings <VAULT_ADDRESS>

# Add asset to allowlist
glam vault allowlist-asset <VAULT_ADDRESS> <MINT_ADDRESS>

# Wrap/unwrap SOL
glam vault wrap <VAULT_ADDRESS> --amount 1.0
glam vault unwrap <VAULT_ADDRESS> --amount 1.0
```

### Enable Integrations

```bash
# List available integrations
glam integrations list

# Enable Jupiter swap
glam integrations enable <VAULT_ADDRESS> JupiterSwap

# Enable multiple integrations
glam integrations enable <VAULT_ADDRESS> DriftProtocol KaminoLend

# Disable integration
glam integrations disable <VAULT_ADDRESS> JupiterSwap
```

Available integrations: `JupiterSwap`, `DriftProtocol`, `KaminoLend`, `KaminoVault`, `KaminoFarm`, `MarinadeStaking`, `NativeStaking`, `SplStakePool`, `SanctumStaking`, `MeteoraDlmm`, `Invariant`, `Orca`.

### Token Swaps (Jupiter)

```bash
# Swap tokens
glam jupiter swap <VAULT_ADDRESS> --input-mint <INPUT_MINT> --output-mint <OUTPUT_MINT> --amount 100 --slippage 50

# View swap policy
glam jupiter view-policy <VAULT_ADDRESS>

# Set max slippage (basis points)
glam jupiter set-max-slippage <VAULT_ADDRESS> --max-slippage 100

# Allowlist token for swaps
glam jupiter allowlist-token <VAULT_ADDRESS> <MINT_ADDRESS>
```

### Lending (Kamino)

```bash
# Initialize Kamino lending
glam kamino init <VAULT_ADDRESS>

# Deposit to lending market
glam kamino deposit <VAULT_ADDRESS> --market <MARKET_ADDRESS> --mint <MINT_ADDRESS> --amount 1000

# Withdraw from lending
glam kamino withdraw <VAULT_ADDRESS> --market <MARKET_ADDRESS> --mint <MINT_ADDRESS> --amount 500

# Borrow assets
glam kamino borrow <VAULT_ADDRESS> --market <MARKET_ADDRESS> --mint <MINT_ADDRESS> --amount 100

# Repay borrowed assets
glam kamino repay <VAULT_ADDRESS> --market <MARKET_ADDRESS> --mint <MINT_ADDRESS> --amount 100

# List lending positions
glam kamino list <VAULT_ADDRESS>
```

### Perpetuals (Drift)

```bash
# Initialize Drift user
glam drift init-user <VAULT_ADDRESS>

# Deposit collateral
glam drift deposit <VAULT_ADDRESS> --market-index 0 --amount 1000

# Open spot position
glam drift spot <VAULT_ADDRESS> --market-index 1 --amount 100 --direction long

# Open perp position
glam drift perp <VAULT_ADDRESS> --market-index 0 --amount 1 --direction long

# List positions
glam drift list-positions <VAULT_ADDRESS>

# Cancel orders
glam drift cancel <VAULT_ADDRESS> --order-id 123
```

### Delegate Management

```bash
# Grant delegate permissions
glam delegates grant <VAULT_ADDRESS> <DELEGATE_PUBKEY> --permissions Swap,Stake

# List delegates
glam delegates list <VAULT_ADDRESS>

# Revoke delegate
glam delegates revoke <VAULT_ADDRESS> <DELEGATE_PUBKEY>

# Revoke all delegates
glam delegates revoke-all <VAULT_ADDRESS>
```

Available permissions: `Swap`, `Stake`, `Unstake`, `LiquidUnstake`, `DriftInit`, `DriftDeposit`, `DriftWithdraw`, `DriftUpdateUser`, `DriftDeleteUser`, `DriftPlaceOrders`, `DriftCancelOrders`, `DriftPerpMarket`, `DriftSpotMarket`, `KaminoInit`, `KaminoDeposit`, `KaminoWithdraw`, `KaminoBorrow`, `KaminoRepay`, `MeteoraInit`, `MeteoraDeposit`, `MeteoraWithdraw`.

### Transfers

```bash
# Transfer assets from vault
glam transfer transfer <VAULT_ADDRESS> --destination <DEST_ADDRESS> --mint <MINT_ADDRESS> --amount 100

# View transfer policy
glam transfer view-policy <VAULT_ADDRESS>

# Allowlist destination
glam transfer allowlist-destination <VAULT_ADDRESS> <DEST_ADDRESS>
```

### Cross-Chain (CCTP)

```bash
# Bridge USDC to another chain
glam cctp bridge-usdc <VAULT_ADDRESS> --destination <DEST_ADDRESS> --amount 1000 --destination-domain 0

# Receive bridged USDC
glam cctp receive <VAULT_ADDRESS> --message <MESSAGE_HEX>

# List CCTP transactions
glam cctp list <VAULT_ADDRESS>
```

## Workflows

### Create Tokenized Vault

A tokenized vault allows investors to subscribe and receive share tokens.

```bash
# 1. Create vault with share class
glam vault create --name "Token Fund" --share-class-name "FUND" --share-class-symbol "FUND"

# 2. Enable required integrations
glam integrations enable <VAULT_ADDRESS> JupiterSwap KaminoLend

# 3. Set subscription/redemption policies
glam manage price <VAULT_ADDRESS> --share-class 0 --price 1.0

# 4. Grant delegate permissions (optional)
glam delegates grant <VAULT_ADDRESS> <MANAGER_PUBKEY> --permissions Swap,KaminoDeposit,KaminoWithdraw
```

### Investor Operations

```bash
# Subscribe to vault (as investor)
glam invest subscribe <VAULT_ADDRESS> --share-class 0 --amount 1000

# Claim subscription after processing
glam invest claim-subscription <VAULT_ADDRESS> --share-class 0

# Redeem shares
glam invest redeem <VAULT_ADDRESS> --share-class 0 --shares 100

# Claim redemption after processing
glam invest claim-redemption <VAULT_ADDRESS> --share-class 0
```

### DeFi Strategy Example

Execute a yield strategy using multiple integrations:

```bash
# 1. Swap USDC to SOL
glam jupiter swap <VAULT_ADDRESS> --input-mint USDC_MINT --output-mint SOL_MINT --amount 5000 --slippage 50

# 2. Deposit SOL to Kamino lending
glam kamino deposit <VAULT_ADDRESS> --market <MARKET> --mint SOL_MINT --amount 10

# 3. Borrow USDC against SOL collateral
glam kamino borrow <VAULT_ADDRESS> --market <MARKET> --mint USDC_MINT --amount 2000
```

### Timelock Operations

Protect vault with timelocked operations:

```bash
# View timelock status
glam timelock view <VAULT_ADDRESS>

# Set timelock delay (seconds)
glam timelock set <VAULT_ADDRESS> --delay 86400

# Apply pending timelock
glam timelock apply <VAULT_ADDRESS>

# Cancel pending timelock
glam timelock cancel <VAULT_ADDRESS>
```

## SDK Usage

For programmatic access, use the TypeScript SDK:

```typescript
import { GlamClient } from "@glamsystems/glam-sdk";

const client = new GlamClient({
  cluster: "mainnet-beta",
  // Optional: provide keypair for signing
});

// Create vault
const { vaultPda, txId } = await client.vault.create({
  name: "My Vault",
  assets: [SOL_MINT, USDC_MINT],
});

// Fetch vault state
const state = await client.vault.fetch(vaultPda);

// Swap via Jupiter
await client.jupiterSwap.swap(vaultPda, {
  inputMint: USDC_MINT,
  outputMint: SOL_MINT,
  amount: 1000_000_000,
  slippageBps: 50,
});
```

## Common Mints

| Token | Mainnet Address |
|-------|-----------------|
| SOL | `So11111111111111111111111111111111111111112` |
| USDC | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| USDT | `Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB` |
| wSOL | `So11111111111111111111111111111111111111112` |
| mSOL | `mSoLzYCxHdYgdzU16g5QSh3i5K3z3KZK7ytfqcJm7So` |
| jitoSOL | `J1toso1uCk3RLmjorhTtrVwY9HJ7X8V9yYac6Y7kGCPn` |
| bSOL | `bSo13r4TkiE4KumL71LsHTPpL2euBYLFx6h9HP3piy1` |

## Reference Documentation

- [CLI Reference](./CLI_REFERENCE.md) - Complete CLI command documentation
- [SDK Reference](./SDK_REFERENCE.md) - TypeScript SDK API reference
- [Examples](./EXAMPLES.md) - Practical usage patterns and workflows
- [Troubleshooting](./TROUBLESHOOTING.md) - Common errors and solutions

## Resources

- GitHub: https://github.com/glamsystems
- Documentation: https://glam.systems/docs
- NPM CLI: https://www.npmjs.com/package/@glamsystems/glam-cli
- NPM SDK: https://www.npmjs.com/package/@glamsystems/glam-sdk
