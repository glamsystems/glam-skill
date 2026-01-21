---
name: glam
description: "Solana vault management via GLAM Protocol. Triggers: glam, glam-cli, glam-sdk, vault create/manage, tokenized vault, share class, DeFi vault, treasury, asset management, access control, delegate permissions, Jupiter swap, Drift perpetuals/spot/vaults, Kamino lending/borrow/vaults/farms, staking (Marinade/native/SPL/Sanctum/LST), Meteora DLMM, cross-chain USDC (CCTP), timelock, subscription/redemption, NAV pricing, token transfer. Supports CLI and TypeScript SDK."
---

# GLAM Protocol Skill

GLAM provides programmable investment infrastructure on Solana: vaults with access control, DeFi integrations, and tokenization.

## Quick Start

```bash
# Install CLI
npm install -g @glamsystems/glam-cli

# Configure (create ~/.config/glam/config.json)
cat > ~/.config/glam/config.json << 'EOF'
{
  "keypair_path": "~/.config/solana/id.json",
  "json_rpc_url": "https://api.mainnet-beta.solana.com"
}
EOF

# Create vault
glam vault create --name "My Vault"
```

## Critical: Integration Enablement

**You MUST enable integrations BEFORE using them.** This is the most common error.

```bash
# Enable before using
glam integration enable <VAULT> JupiterSwap    # Before any swap
glam integration enable <VAULT> DriftProtocol  # Before any Drift operation
glam integration enable <VAULT> KaminoLend     # Before any Kamino operation
glam integration enable <VAULT> MarinadeStaking # Before staking
```

Available: `JupiterSwap`, `DriftProtocol`, `KaminoLend`, `KaminoVault`, `KaminoFarm`, `MarinadeStaking`, `NativeStaking`, `SplStakePool`, `SanctumStaking`, `MeteoraDlmm`, `Invariant`, `Orca`.

---

## Workflows

### Basic Vault Setup

```
Checklist:
- [ ] Create vault
- [ ] Enable integrations
- [ ] Add assets to allowlist (if needed)
- [ ] Verify configuration
```

```bash
# 1. Create vault
glam vault create --name "Treasury" --assets SOL,USDC
# Save the vault address from output!

# 2. Enable integrations
glam integration enable <VAULT> JupiterSwap KaminoLend

# 3. Verify
glam vault view <VAULT>
```

### Tokenized Vault Setup

```
Checklist:
- [ ] Create vault with share class
- [ ] Enable integrations
- [ ] Set initial NAV price
- [ ] Configure swap policy
- [ ] Set up delegates (optional)
- [ ] Configure timelock (optional)
```

```bash
# 1. Create with share class
glam vault create --name "Alpha Fund" \
  --share-class-name "Alpha Shares" \
  --share-class-symbol "ALPHA" \
  --assets So11111111111111111111111111111111111111112,EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v

# 2. Enable integrations
glam integration enable <VAULT> JupiterSwap DriftProtocol KaminoLend

# 3. Set initial price (1.0 USDC per share)
glam manage price <VAULT> --share-class 0 --price 1.0

# 4. Configure swap policy
glam jupiter set-max-slippage <VAULT> --max-slippage 100

# 5. Set up trading delegate
glam delegate grant <VAULT> <TRADER_PUBKEY> --permissions Swap,DriftDeposit,DriftWithdraw,DriftPlaceOrders

# 6. Set timelock (24 hours)
glam timelock set <VAULT> --delay 86400
```

### Drift Trading Setup

```
Checklist:
- [ ] Enable DriftProtocol integration
- [ ] Initialize Drift user (required once)
- [ ] Deposit collateral
- [ ] Place trades
```

```bash
# 1. Enable
glam integration enable <VAULT> DriftProtocol

# 2. Initialize user (REQUIRED)
glam drift-protocol init-user <VAULT>

# 3. Deposit USDC as collateral
glam drift-protocol deposit <VAULT> --market-index 0 --amount 1000

# 4. Open position
glam drift-protocol perp <VAULT> --market-index 0 --amount 1 --direction long
```

### Kamino Lending Setup

```
Checklist:
- [ ] Enable KaminoLend integration
- [ ] Initialize Kamino (required once)
- [ ] Deposit to earn yield
```

```bash
# 1. Enable
glam integration enable <VAULT> KaminoLend

# 2. Initialize (REQUIRED)
glam kamino-lend init <VAULT>

# 3. Deposit
glam kamino-lend deposit <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 1000
```

---

## Decision Tree

**What do you want to do?**

**Swap tokens?**
→ Enable `JupiterSwap` → `glam jupiter swap`

**Earn yield on idle assets?**
→ Lending: Enable `KaminoLend` → `glam kamino-lend deposit`
→ Staking: Enable `MarinadeStaking` → `glam marinade stake`
→ Kamino Vaults: Enable `KaminoVault` → `glam kamino-vaults deposit`
→ Drift Vaults: Enable `DriftVaults` → `glam drift-vaults deposit`

**Trade perpetuals?**
→ Enable `DriftProtocol` → `glam drift-protocol init-user` → `glam drift-protocol deposit` → `glam drift-protocol perp`

**Trade spot on Drift?**
→ Enable `DriftProtocol` → `glam drift-protocol init-user` → `glam drift-protocol deposit` → `glam drift-protocol spot`

**Create tokenized vault with investors?**
→ `vault create --share-class-*` → `manage price` → investors use `invest subscribe`

**Bridge USDC cross-chain?**
→ `glam cctp bridge-usdc --destination-domain <CHAIN_ID>`

**Protect vault with timelock?**
→ `glam timelock set --delay <SECONDS>`

---

## Validation Patterns

Always verify operations succeeded:

### After Creating Vault
```bash
glam vault view <VAULT>
# Check: name, manager, integrations list
```

### After Enabling Integration
```bash
glam vault view <VAULT>
# Check: integration appears in list
```

### After Swap
```bash
glam vault balances <VAULT>
# Check: balances changed as expected
```

### After Drift Trade
```bash
glam drift-protocol list-positions <VAULT>
# Check: position appears
```

### After Kamino Deposit
```bash
glam kamino-lend list <VAULT>
# Check: position appears
```

---

## Common Errors Quick Reference

| Error | Cause | Solution |
|-------|-------|----------|
| "Signer is not authorized" | Wrong keypair or missing delegate permission | Check `vault view` for manager; grant delegate if needed |
| "Integration not enabled" | Forgot to enable | `glam integration enable <VAULT> <INTEGRATION>` |
| "Asset not in allowlist" | Token not in vault allowlist | `glam vault allowlist-asset <VAULT> <MINT>` |
| "User not initialized" | Drift/Kamino not initialized | Run `glam drift-protocol init-user` or `glam kamino-lend init` |
| "No route found" | Jupiter can't find swap path | Try smaller amount; check token liquidity |
| "Slippage exceeded" | Price moved too much | Increase `--slippage` or reduce amount |
| "Insufficient collateral" | Not enough collateral for position | Deposit more via `drift deposit` |

→ See [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) for detailed solutions.

---

## Common Mints

| Token | Mainnet Address |
|-------|-----------------|
| SOL | `So11111111111111111111111111111111111111112` |
| USDC | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| USDT | `Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB` |
| mSOL | `mSoLzYCxHdYgdzU16g5QSh3i5K3z3KZK7ytfqcJm7So` |
| jitoSOL | `J1toso1uCk3RLmjorhTtrVwY9HJ7X8V9yYac6Y7kGCPn` |
| bSOL | `bSo13r4TkiE4KumL71LsHTPpL2euBYLFx6h9HP3piy1` |

---

## SDK Quick Start

```typescript
import { GlamClient, COMMON_MINTS } from "@glamsystems/glam-sdk";
import { BN } from "@coral-xyz/anchor";

const client = new GlamClient({ keypair });

// Create vault
const { vaultPda } = await client.vault.create({
  name: "My Vault",
  assets: [COMMON_MINTS.SOL, COMMON_MINTS.USDC],
});

// Enable integration
await client.vault.enableIntegration(vaultPda, "JupiterSwap");

// Swap
await client.jupiterSwap.swap(vaultPda, {
  inputMint: COMMON_MINTS.USDC,
  outputMint: COMMON_MINTS.SOL,
  amount: new BN(100_000_000), // 100 USDC
  slippageBps: 50,
});
```

---

## Reference Documentation

### CLI Commands (by domain)
- [Vault & Config](./references/cli/vault.md) - Vault creation, configuration, integrations
- [Delegate](./references/cli/delegate.md) - Access control and permissions
- [Jupiter](./references/cli/jupiter.md) - Token swaps
- [Drift Protocol](./references/cli/drift-protocol.md) - Perpetuals and spot trading
- [Kamino Lend](./references/cli/kamino-lend.md) - Lending and borrowing
- [Kamino Vaults](./references/cli/kamino-vaults.md) - Automated yield vaults
- [Kamino Farms](./references/cli/kamino-farms.md) - Liquidity mining rewards
- [Drift Vaults](./references/cli/drift-vaults.md) - Managed Drift vaults
- [Staking](./references/cli/staking.md) - Marinade, native, SPL, Sanctum staking
- [LST](./references/cli/lst.md) - Liquid staking token operations
- [Invest](./references/cli/invest.md) - Investor subscription/redemption
- [Manage](./references/cli/manage.md) - Vault manager operations (price, fulfill, fees)
- [Transfer](./references/cli/transfer.md) - Token transfers with allowlist
- [Advanced](./references/cli/advanced.md) - CCTP bridging, timelock
- [ALT](./references/cli/alt.md) - Address lookup table management

### SDK Reference
- [GlamClient](./references/sdk/client.md) - Client setup, vault operations
- [Integrations](./references/sdk/integrations.md) - All integration clients

### Other
- [Examples](./EXAMPLES.md) - Practical usage patterns
- [Troubleshooting](./TROUBLESHOOTING.md) - Error solutions

## Resources

- GitHub: https://github.com/glamsystems
- Documentation: https://docs.glam.systems/
- CLI: https://www.npmjs.com/package/@glamsystems/glam-cli
- SDK: https://www.npmjs.com/package/@glamsystems/glam-sdk
