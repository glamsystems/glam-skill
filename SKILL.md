---
name: glam
description: "Solana vault management via GLAM Protocol. Triggers: glam, glam-cli, glam-sdk, vault create/manage, tokenized vault, share class, DeFi vault, treasury, asset management, access control, delegate permissions, Jupiter swap, Drift perpetuals/spot/vaults, Kamino lending/borrow/vaults/farms, staking (Marinade/native/SPL/Sanctum/LST), cross-chain USDC (CCTP), timelock, subscription/redemption, NAV pricing, token transfer. Supports CLI and TypeScript SDK."
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

# Create vault from template
glam-cli vault create ./vault-template.json

# Set active vault
glam-cli vault set <VAULT_STATE_PUBKEY>
```

## Critical: Integration Enablement

**You MUST enable integrations BEFORE using them.** This is the most common error.

```bash
# Enable before using (operates on active vault)
glam-cli integration enable JupiterSwap    # Before any swap
glam-cli integration enable DriftProtocol  # Before any Drift operation
glam-cli integration enable KaminoLend     # Before any Kamino operation
glam-cli integration enable Marinade         # Before staking (staging, requires --bypass-warning)
```

Available: `JupiterSwap`, `DriftProtocol`, `KaminoLend`, `KaminoVaults`, `KaminoFarms`, `DriftVaults`, `SplToken`, `CCTP`, `GlamMint`, `Marinade` (staging), `StakePool` (staging), `SanctumSingle` (staging), `SanctumMulti` (staging), `StakeProgram` (staging).

---

## Workflows

### Basic Vault Setup

```
Checklist:
- [ ] Create vault from template
- [ ] Set active vault
- [ ] Enable integrations
- [ ] Verify configuration
```

```bash
# 1. Create vault from template
glam-cli vault create ./vault-template.json
# Save the vault address from output!

# 2. Set active vault
glam-cli vault set <VAULT_STATE_PUBKEY>

# 3. Enable integrations
glam-cli integration enable JupiterSwap KaminoLend

# 4. Verify
glam-cli vault view
```

### Tokenized Vault Setup

```
Checklist:
- [ ] Create vault with share class (via JSON template)
- [ ] Set active vault
- [ ] Enable integrations
- [ ] Set initial NAV price
- [ ] Configure swap policy
- [ ] Set up delegates (optional)
- [ ] Configure timelock (optional)
```

```bash
# 1. Create with share class (use JSON template with mint config)
glam-cli vault create ./tokenized-vault-template.json

# 2. Set active vault
glam-cli vault set <VAULT_STATE_PUBKEY>

# 3. Enable integrations
glam-cli integration enable JupiterSwap DriftProtocol KaminoLend

# 4. Set initial price
glam-cli manage price

# 5. Configure swap policy
glam-cli jupiter set-max-slippage 100

# 6. Set up trading delegate (protocol-scoped)
glam-cli delegate grant <TRADER_PUBKEY> SwapAny --protocol JupiterSwap
glam-cli delegate grant <TRADER_PUBKEY> Deposit Withdraw CreateModifyOrders CancelOrders --protocol DriftProtocol

# 7. Set timelock (24 hours)
glam-cli timelock set 86400
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
glam-cli integration enable DriftProtocol

# 2. Initialize user (REQUIRED)
glam-cli drift-protocol init-user

# 3. Deposit USDC as collateral
glam-cli drift-protocol deposit 0 1000

# 4. Open position
glam-cli drift-protocol perp long 0 1 0
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
glam-cli integration enable KaminoLend

# 2. Initialize (REQUIRED)
glam-cli kamino-lend init

# 3. Deposit
glam-cli kamino-lend deposit \
  7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  1000
```

---

## Decision Tree

**What do you want to do?**

**Swap tokens?**
→ Enable `JupiterSwap` → `glam-cli jupiter swap`

**Earn yield on idle assets?**
→ Lending: Enable `KaminoLend` → `glam-cli kamino-lend deposit`
→ Staking: Enable `Marinade` → `NODE_ENV=development glam-cli marinade --bypass-warning stake` (staging, unaudited)
→ Kamino Vaults: Enable `KaminoVaults` → `glam-cli kamino-vaults deposit`
→ Drift Vaults: Enable `DriftVaults` → `glam-cli drift-vaults deposit`

**Trade perpetuals?**
→ Enable `DriftProtocol` → `glam-cli drift-protocol init-user` → `glam-cli drift-protocol deposit` → `glam-cli drift-protocol perp`

**Trade spot on Drift?**
→ Enable `DriftProtocol` → `glam-cli drift-protocol init-user` → `glam-cli drift-protocol deposit` → `glam-cli drift-protocol spot`

**Create tokenized vault with investors?**
→ `glam-cli vault create template.json` → `glam-cli manage price` → investors use `glam-cli invest subscribe`

**Manage share class tokens (freeze, issue, burn)?**
→ SDK only: `client.mint.fetchTokenHolders()`, `client.mint.setTokenAccountsStates()`, `client.mint.mint()`, `client.mint.burn()`, `client.mint.forceTransfer()`

**Bridge USDC cross-chain?**
→ `glam-cli cctp bridge-usdc <amount> <domain> <destination>` (domains: 0=ETH, 1=AVAX, 2=OP, 3=ARB, 6=BASE, 7=POLYGON)

**Protect vault with timelock?**
→ `glam-cli timelock set <duration>`

---

## Validation Patterns

Always verify operations succeeded:

### After Creating Vault
```bash
glam-cli vault view
# Check: name, owner, integrations list
```

### After Enabling Integration
```bash
glam-cli vault view
# Check: integration appears in list
```

### After Swap
```bash
glam-cli vault balances
# Check: balances changed as expected
```

### After Drift Trade
```bash
glam-cli drift-protocol list-positions
# Check: position appears
```

### After Kamino Deposit
```bash
glam-cli kamino-lend list
# Check: position appears
```

---

## Common Errors Quick Reference

| Error | Cause | Solution |
|-------|-------|----------|
| "Signer is not authorized" | Wrong keypair or missing delegate permission | Check `vault view` for owner; grant delegate if needed |
| "Integration not enabled" | Forgot to enable | `glam-cli integration enable <INTEGRATION>` |
| "Asset not in allowlist" | Token not in vault allowlist | `glam-cli vault allowlist-asset <MINT>` |
| "User not initialized" | Drift/Kamino not initialized | Run `glam-cli drift-protocol init-user` or `glam-cli kamino-lend init` |
| "No route found" | Jupiter can't find swap path | Try smaller amount; check token liquidity |
| "Slippage exceeded" | Price moved too much | Increase `--slippage-bps` or reduce amount |
| "Insufficient collateral" | Not enough collateral for position | Deposit more via `drift-protocol deposit` |
| "Account is frozen" | Token account frozen by manager | Manager must unfreeze via SDK: `client.mint.setTokenAccountsStates()` |
| "Missing jupiter_api_key" | Jupiter API key not configured | Add `jupiter_api_key` to config.json |

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
import { GlamClient, COMMON_MINTS, getProgramAndBitflagByProtocolName } from "@glamsystems/glam-sdk";
import { BN } from "@coral-xyz/anchor";

const client = new GlamClient({ wallet });

// Create vault
const { vaultPda } = await client.vault.create({
  name: "My Vault",
  assets: [COMMON_MINTS.SOL, COMMON_MINTS.USDC],
});

// Enable Jupiter integration
const perms = getProgramAndBitflagByProtocolName();
const [program, bitflag] = perms["JupiterSwap"];
await client.access.enableProtocols(vaultPda, program, parseInt(bitflag, 2));

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
- [Mint](./references/sdk/mint.md) - Share class token management (SDK only)
- [Jupiter](./references/cli/jupiter.md) - Token swaps
- [Drift Protocol](./references/cli/drift-protocol.md) - Perpetuals and spot trading
- [Kamino Lend](./references/cli/kamino-lend.md) - Lending and borrowing
- [Kamino Vaults](./references/cli/kamino-vaults.md) - Automated yield vaults
- [Kamino Farms](./references/cli/kamino-farms.md) - Liquidity mining rewards
- [Drift Vaults](./references/cli/drift-vaults.md) - Managed Drift vaults
- [Staking](./references/cli/staking.md) - Marinade, native, SPL, Sanctum staking (all staging, require `--bypass-warning`)
- [LST](./references/cli/lst.md) - Liquid staking token operations
- [Invest](./references/cli/invest.md) - Investor subscription/redemption
- [Manage](./references/cli/manage.md) - Vault manager operations (price, fulfill, fees)
- [Transfer](./references/cli/transfer.md) - Token transfers with allowlist
- [Advanced](./references/cli/advanced.md) - CCTP bridging, timelock
- [ALT](./references/cli/alt.md) - Address lookup table management

### SDK Reference
- [GlamClient](./references/sdk/client.md) - Client setup, vault operations, models
- [Integrations](./references/sdk/integrations.md) - All integration clients, policies
- [Multisig](./references/sdk/multisig.md) - Squads multisig integration

### Other
- [Concepts](./references/concepts.md) - Fees, subscriptions, NAV, programs, access control
- [Examples](./EXAMPLES.md) - Practical usage patterns
- [Troubleshooting](./TROUBLESHOOTING.md) - Error solutions

## Resources

- GitHub: https://github.com/glamsystems
- Documentation: https://docs.glam.systems/
- CLI: https://www.npmjs.com/package/@glamsystems/glam-cli
- SDK: https://www.npmjs.com/package/@glamsystems/glam-sdk
