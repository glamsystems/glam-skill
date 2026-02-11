# GLAM Examples

Practical usage patterns for CLI and SDK operations.

## Table of Contents

- [Basic Examples](#basic-examples)
- [Intermediate Examples](#intermediate-examples)
- [Advanced Examples](#advanced-examples)

---

## Basic Examples

### Create and Configure a Vault (CLI)

```bash
# 1. Create vault from template
glam-cli vault create ./vault-template.json
# Output: Vault created: <VAULT_STATE_PUBKEY>

# 2. Set active vault
glam-cli vault set <VAULT_STATE_PUBKEY>

# 3. View vault details
glam-cli vault view

# 4. Add assets to allowlist
glam-cli vault allowlist-asset EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v --yes
glam-cli vault allowlist-asset Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB --yes

# 5. Enable Jupiter for swaps
glam-cli integration enable JupiterSwap

# 6. Check balances
glam-cli vault balances
```

### Create Vault (SDK)

```typescript
import { GlamClient, COMMON_MINTS, getProgramAndBitflagByProtocolName } from "@glamsystems/glam-sdk";

const client = new GlamClient({ wallet });

// Create vault
const { vaultPda, txId } = await client.vault.create({
  name: "My Treasury",
  assets: [COMMON_MINTS.SOL, COMMON_MINTS.USDC],
});

console.log("Vault created:", vaultPda.toBase58());
console.log("Transaction:", txId);

// Enable integrations
const perms = getProgramAndBitflagByProtocolName();
const [jupProgram, jupBitflag] = perms["JupiterSwap"];
await client.access.enableProtocols(vaultPda, jupProgram, parseInt(jupBitflag, 2));

const [kaminoProgram, kaminoBitflag] = perms["KaminoLend"];
await client.access.enableProtocols(vaultPda, kaminoProgram, parseInt(kaminoBitflag, 2));
```

### Simple Token Swap (CLI)

```bash
# Swap 100 USDC to SOL
glam-cli jupiter swap \
  EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  So11111111111111111111111111111111111111112 \
  100 \
  --slippage-bps 50 --yes
```

### Simple Token Swap (SDK)

```typescript
import { BN } from "@coral-xyz/anchor";

// Get quote first
const quote = await client.jupiterSwap.getQuote({
  inputMint: COMMON_MINTS.USDC,
  outputMint: COMMON_MINTS.SOL,
  amount: new BN(100_000_000), // 100 USDC (6 decimals)
  slippageBps: 50,
});

console.log("Expected output:", quote.outAmount.toString());

// Execute swap
const txId = await client.jupiterSwap.swap(vaultPda, {
  inputMint: COMMON_MINTS.USDC,
  outputMint: COMMON_MINTS.SOL,
  amount: new BN(100_000_000),
  slippageBps: 50,
});
```

---

## Intermediate Examples

### Set Up Delegate Permissions (CLI)

```bash
# Grant Jupiter swap permission to a delegate
glam-cli delegate grant <DELEGATE_PUBKEY> SwapAny --protocol JupiterSwap --yes

# Verify delegate was added
glam-cli delegate list

# Grant Drift trading permissions to same or another delegate
glam-cli delegate grant <DELEGATE_PUBKEY> Deposit Withdraw CreateModifyOrders CancelOrders --protocol DriftProtocol --yes

# Grant Kamino permissions to another delegate
glam-cli delegate grant <OTHER_DELEGATE> Deposit Withdraw Borrow Repay --protocol KaminoLend --yes
```

### Set Up Delegate Permissions (SDK)

```typescript
// Grant trading permissions (protocol-scoped)
await client.access.grantDelegatePermissions(vaultPda, {
  delegate: new PublicKey("<DELEGATE_PUBKEY>"),
  permissions: [
    "SwapAny",
    "DriftDeposit",
    "DriftWithdraw",
    "DriftCreateModifyOrders",
    "DriftCancelOrders",
    "DriftPerpMarkets",
    "DriftSpotMarkets",
  ],
});

// List all delegates
const delegates = await client.access.listDelegates(vaultPda);
for (const delegate of delegates) {
  console.log("Delegate:", delegate.pubkey.toBase58());
  console.log("Permissions:", delegate.permissions.join(", "));
}
```

### Deposit to Kamino Lending (CLI)

```bash
# 1. Enable Kamino integration
glam-cli integration enable KaminoLend

# 2. Initialize Kamino for vault
glam-cli kamino-lend init --yes

# 3. Deposit USDC to main market
glam-cli kamino-lend deposit \
  7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  1000 --yes

# 4. View positions
glam-cli kamino-lend list
```

### Deposit to Kamino Lending (SDK)

```typescript
// Enable and initialize
const perms = getProgramAndBitflagByProtocolName();
const [kaminoProgram, kaminoBitflag] = perms["KaminoLend"];
await client.access.enableProtocols(vaultPda, kaminoProgram, parseInt(kaminoBitflag, 2));
await client.kaminoLending.initUserMetadata(vaultPda);

// Deposit
const KAMINO_MAIN_MARKET = new PublicKey("7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF");

await client.kaminoLending.deposit(vaultPda, {
  market: KAMINO_MAIN_MARKET,
  mint: COMMON_MINTS.USDC,
  amount: new BN(1000_000_000), // 1000 USDC
});

// Check positions
const positions = await client.kaminoLending.getPositions(vaultPda);
console.log("Lending positions:", positions);
```

### Open Drift Perpetual Position (CLI)

```bash
# 1. Enable Drift integration
glam-cli integration enable DriftProtocol

# 2. Initialize Drift user
glam-cli drift-protocol init-user --yes

# 3. Deposit USDC as collateral
glam-cli drift-protocol deposit 0 1000 --yes

# 4. Open 1 SOL-PERP long
glam-cli drift-protocol perp long 0 1 0 --yes

# 5. View positions
glam-cli drift-protocol list-positions
```

---

## Advanced Examples

### Create Tokenized Vault with Full Configuration (CLI)

```bash
# 1. Create tokenized vault from JSON template (includes share class config)
glam-cli vault create ./tokenized-vault-template.json

# 2. Set active vault
glam-cli vault set <VAULT_STATE_PUBKEY>

# 3. Enable all required integrations
glam-cli integration enable JupiterSwap DriftProtocol KaminoLend

# 4. Set initial share price (update NAV)
glam-cli manage price

# 5. Set up a trading delegate (protocol-scoped)
glam-cli delegate grant <TRADER_PUBKEY> SwapAny --protocol JupiterSwap --yes
glam-cli delegate grant <TRADER_PUBKEY> Deposit Withdraw CreateModifyOrders PerpMarkets --protocol DriftProtocol --yes

# 6. Configure swap policy
glam-cli jupiter set-max-slippage 100 --yes
glam-cli jupiter allowlist-token So11111111111111111111111111111111111111112 --yes
glam-cli jupiter allowlist-token EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v --yes

# 7. Set timelock for security (24 hours)
glam-cli timelock set 86400 --yes
```

### Create Tokenized Vault (SDK)

```typescript
import { GlamClient, COMMON_MINTS, getProgramAndBitflagByProtocolName } from "@glamsystems/glam-sdk";
import { BN } from "@coral-xyz/anchor";

const client = new GlamClient({ wallet });

// Create vault with share class
const { vaultPda } = await client.vault.create({
  name: "GLAM Alpha Fund",
  shareClassName: "Alpha Shares",
  shareClassSymbol: "ALPHA",
  shareClassDecimals: 6,
  assets: [COMMON_MINTS.SOL, COMMON_MINTS.USDC],
});

// Enable integrations
const perms = getProgramAndBitflagByProtocolName();
for (const name of ["JupiterSwap", "DriftProtocol", "KaminoLend"]) {
  const [program, bitflag] = perms[name];
  await client.access.enableProtocols(vaultPda, program, parseInt(bitflag, 2));
}

// Set initial price
await client.fees.setPrice(vaultPda, {
  shareClassIndex: 0,
  price: new BN(1_000_000), // 1.0 USDC (6 decimals)
});

// Configure trading delegate
await client.access.grantDelegatePermissions(vaultPda, {
  delegate: traderPubkey,
  permissions: [
    "SwapAny",
    "DriftDeposit",
    "DriftWithdraw",
    "DriftCreateModifyOrders",
    "DriftPerpMarkets",
  ],
});

// Set swap policy
await client.jupiterSwap.setMaxSlippage(vaultPda, 100); // 1%
await client.jupiterSwap.allowlistToken(vaultPda, COMMON_MINTS.SOL);
await client.jupiterSwap.allowlistToken(vaultPda, COMMON_MINTS.USDC);
```

### Multi-Protocol Yield Strategy (CLI)

```bash
# Starting with 10,000 USDC in vault

# 1. Swap 50% to SOL for diversification
glam-cli jupiter swap \
  EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  So11111111111111111111111111111111111111112 \
  5000 \
  --slippage-bps 50 --yes

# 2. Deposit remaining USDC to Kamino for yield
glam-cli kamino-lend deposit \
  7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  5000 --yes

# 3. Deposit SOL as collateral on Drift
glam-cli drift-protocol deposit 1 50 --yes

# 4. Open delta-neutral position
glam-cli drift-protocol perp short 0 25 0 --yes

# 5. Check all positions
glam-cli vault holdings
glam-cli kamino-lend list
glam-cli drift-protocol list-positions
```

### Investor Subscription Flow (CLI)

```bash
# As investor (not vault manager):

# 1. Subscribe with 1000 USDC
glam-cli invest subscribe 1000 --yes

# 2. Wait for manager to fulfill subscription
# (Manager runs: glam-cli manage fulfill)

# 3. Claim shares
glam-cli invest claim-subscription

# 4. Later, redeem 100 shares
glam-cli invest redeem 100 --yes

# 5. Wait for fulfillment, then claim
glam-cli invest claim-redemption
```

### Cross-Chain USDC Bridge (CLI)

```bash
# Bridge USDC from Solana to Ethereum

# 1. Bridge 1000 USDC to Ethereum address (domain 0 = Ethereum)
glam-cli cctp bridge-usdc 1000 0 0x742d35Cc6634C0532925a3b844Bc9e7595f00000 --yes

# 2. List pending transfers
glam-cli cctp list

# To receive USDC from another chain:
glam-cli cctp receive <SOURCE_DOMAIN> --txHash <TX_HASH>
```

### Full Vault Management Script (SDK)

```typescript
import { GlamClient, COMMON_MINTS } from "@glamsystems/glam-sdk";
import { BN } from "@coral-xyz/anchor";
import { PublicKey } from "@solana/web3.js";

async function manageVault() {
  const client = new GlamClient({ wallet });
  const vaultPda = new PublicKey("<VAULT_STATE_PUBKEY>");

  // Get current state
  const state = await client.vault.fetch(vaultPda);
  console.log("Vault:", state.name);
  console.log("Integrations:", state.integrations);

  // Get balances
  const balances = await client.vault.getBalances(vaultPda);
  for (const bal of balances) {
    console.log(`${bal.mint.toBase58()}: ${bal.amount.toString()}`);
  }

  // Rebalance if needed
  const usdcBalance = balances.find(
    b => b.mint.toBase58() === COMMON_MINTS.USDC.toBase58()
  );

  if (usdcBalance && usdcBalance.amount.gt(new BN(10000_000_000))) {
    // Swap excess USDC to SOL
    const excessAmount = usdcBalance.amount.sub(new BN(5000_000_000));
    await client.jupiterSwap.swap(vaultPda, {
      inputMint: COMMON_MINTS.USDC,
      outputMint: COMMON_MINTS.SOL,
      amount: excessAmount,
      slippageBps: 50,
    });
    console.log("Rebalanced vault");
  }

  // Check lending positions
  const lendingPositions = await client.kaminoLending.getPositions(vaultPda);
  console.log("Lending positions:", lendingPositions);

  // Check Drift positions
  const driftPositions = await client.drift.getPositions(vaultPda);
  console.log("Drift positions:", driftPositions);
}

manageVault().catch(console.error);
```
