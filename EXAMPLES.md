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
# 1. Create vault
glam vault create --name "My Treasury"
# Output: Vault created: <VAULT_ADDRESS>

# 2. View vault details
glam vault view <VAULT_ADDRESS>

# 3. Add assets to allowlist
glam vault allowlist-asset <VAULT_ADDRESS> EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
glam vault allowlist-asset <VAULT_ADDRESS> Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB

# 4. Enable Jupiter for swaps
glam integration enable <VAULT_ADDRESS> JupiterSwap

# 5. Check balances
glam vault balances <VAULT_ADDRESS>
```

### Create Vault (SDK)

```typescript
import { GlamClient } from "@glamsystems/glam-sdk";
import { Keypair, PublicKey } from "@solana/web3.js";

const keypair = Keypair.fromSecretKey(/* your key */);
const client = new GlamClient({ keypair });

// Create vault
const { vaultPda, txId } = await client.vault.create({
  name: "My Treasury",
  assets: [
    new PublicKey("So11111111111111111111111111111111111111112"),  // SOL
    new PublicKey("EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"), // USDC
  ],
});

console.log("Vault created:", vaultPda.toBase58());
console.log("Transaction:", txId);

// Enable integrations
await client.vault.enableIntegration(vaultPda, "JupiterSwap");
await client.vault.enableIntegration(vaultPda, "KaminoLend");
```

### Simple Token Swap (CLI)

```bash
# Swap 100 USDC to SOL
glam jupiter swap <VAULT_ADDRESS> \
  --input-mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --output-mint So11111111111111111111111111111111111111112 \
  --amount 100 \
  --slippage 50
```

### Simple Token Swap (SDK)

```typescript
import { BN } from "@coral-xyz/anchor";

// Get quote first
const quote = await client.jupiterSwap.getQuote({
  inputMint: new PublicKey("EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"),
  outputMint: new PublicKey("So11111111111111111111111111111111111111112"),
  amount: new BN(100_000_000), // 100 USDC (6 decimals)
  slippageBps: 50,
});

console.log("Expected output:", quote.outAmount.toString());

// Execute swap
const txId = await client.jupiterSwap.swap(vaultPda, {
  inputMint: new PublicKey("EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"),
  outputMint: new PublicKey("So11111111111111111111111111111111111111112"),
  amount: new BN(100_000_000),
  slippageBps: 50,
});
```

---

## Intermediate Examples

### Set Up Delegate Permissions (CLI)

```bash
# Grant trading permissions to a delegate
glam delegate grant <VAULT_ADDRESS> <DELEGATE_PUBKEY> \
  --permissions Swap,DriftDeposit,DriftWithdraw,DriftPlaceOrders,DriftCancelOrders

# Verify delegate was added
glam delegate list <VAULT_ADDRESS>

# Grant Kamino permissions to another delegate
glam delegate grant <VAULT_ADDRESS> <OTHER_DELEGATE> \
  --permissions KaminoDeposit,KaminoWithdraw,KaminoBorrow,KaminoRepay
```

### Set Up Delegate Permissions (SDK)

```typescript
// Grant full trading permissions
await client.access.grantDelegate(vaultPda, {
  delegate: new PublicKey("<DELEGATE_PUBKEY>"),
  permissions: [
    "Swap",
    "DriftDeposit",
    "DriftWithdraw",
    "DriftPlaceOrders",
    "DriftCancelOrders",
    "DriftPerpMarket",
    "DriftSpotMarket",
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
glam integration enable <VAULT_ADDRESS> KaminoLend

# 2. Initialize Kamino for vault
glam kamino-lend init <VAULT_ADDRESS>

# 3. Deposit USDC to main market
glam kamino-lend deposit <VAULT_ADDRESS> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 1000

# 4. View positions
glam kamino-lend list <VAULT_ADDRESS>
```

### Deposit to Kamino Lending (SDK)

```typescript
// Enable and initialize
await client.vault.enableIntegration(vaultPda, "KaminoLend");
await client.kaminoLending.init(vaultPda);

// Deposit
const KAMINO_MAIN_MARKET = new PublicKey("7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF");
const USDC_MINT = new PublicKey("EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v");

await client.kaminoLending.deposit(vaultPda, {
  market: KAMINO_MAIN_MARKET,
  mint: USDC_MINT,
  amount: new BN(1000_000_000), // 1000 USDC
});

// Check positions
const positions = await client.kaminoLending.getPositions(vaultPda);
console.log("Lending positions:", positions);
```

### Open Drift Perpetual Position (CLI)

```bash
# 1. Enable Drift integration
glam integration enable <VAULT_ADDRESS> DriftProtocol

# 2. Initialize Drift user
glam drift-protocol init-user <VAULT_ADDRESS>

# 3. Deposit USDC as collateral
glam drift-protocol deposit <VAULT_ADDRESS> --market-index 0 --amount 1000

# 4. Open 1 SOL-PERP long
glam drift-protocol perp <VAULT_ADDRESS> --market-index 0 --amount 1 --direction long

# 5. View positions
glam drift-protocol list-positions <VAULT_ADDRESS>
```

---

## Advanced Examples

### Create Tokenized Vault with Full Configuration (CLI)

```bash
# 1. Create tokenized vault with share class
glam vault create \
  --name "GLAM Alpha Fund" \
  --share-class-name "Alpha Shares" \
  --share-class-symbol "ALPHA" \
  --share-class-decimals 6 \
  --assets So11111111111111111111111111111111111111112,EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v

# 2. Enable all required integrations
glam integration enable <VAULT_ADDRESS> JupiterSwap DriftProtocol KaminoLend

# 3. Set initial share price (1.0 USDC)
glam manage price <VAULT_ADDRESS> --share-class 0 --price 1.0

# 4. Set up a trading delegate
glam delegate grant <VAULT_ADDRESS> <TRADER_PUBKEY> \
  --permissions Swap,DriftDeposit,DriftWithdraw,DriftPlaceOrders,DriftPerpMarket

# 5. Configure swap policy
glam jupiter set-max-slippage <VAULT_ADDRESS> --max-slippage 100
glam jupiter allowlist-token <VAULT_ADDRESS> So11111111111111111111111111111111111111112
glam jupiter allowlist-token <VAULT_ADDRESS> EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v

# 6. Set timelock for security
glam timelock set <VAULT_ADDRESS> --delay 86400
```

### Create Tokenized Vault (SDK)

```typescript
import { GlamClient, COMMON_MINTS } from "@glamsystems/glam-sdk";
import { BN } from "@coral-xyz/anchor";

const client = new GlamClient({ keypair });

// Create vault with share class
const { vaultPda } = await client.vault.create({
  name: "GLAM Alpha Fund",
  shareClassName: "Alpha Shares",
  shareClassSymbol: "ALPHA",
  shareClassDecimals: 6,
  assets: [COMMON_MINTS.SOL, COMMON_MINTS.USDC],
});

// Enable integrations
await Promise.all([
  client.vault.enableIntegration(vaultPda, "JupiterSwap"),
  client.vault.enableIntegration(vaultPda, "DriftProtocol"),
  client.vault.enableIntegration(vaultPda, "KaminoLend"),
]);

// Set initial price
await client.fees.setPrice(vaultPda, {
  shareClassIndex: 0,
  price: new BN(1_000_000), // 1.0 USDC (6 decimals)
});

// Configure trading delegate
await client.access.grantDelegate(vaultPda, {
  delegate: traderPubkey,
  permissions: [
    "Swap",
    "DriftDeposit",
    "DriftWithdraw",
    "DriftPlaceOrders",
    "DriftPerpMarket",
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
glam jupiter swap <VAULT> \
  --input-mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --output-mint So11111111111111111111111111111111111111112 \
  --amount 5000 \
  --slippage 50

# 2. Deposit remaining USDC to Kamino for yield
glam kamino-lend deposit <VAULT> \
  --market 7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF \
  --mint EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v \
  --amount 5000

# 3. Deposit SOL as collateral on Drift
glam drift-protocol deposit <VAULT> --market-index 1 --amount 50

# 4. Open delta-neutral position
glam drift-protocol perp <VAULT> --market-index 0 --amount 25 --direction short

# 5. Check all positions
glam vault holdings <VAULT>
glam kamino-lend list <VAULT>
glam drift-protocol list-positions <VAULT>
```

### Investor Subscription Flow (CLI)

```bash
# As investor (not vault manager):

# 1. Subscribe with 1000 USDC
glam invest subscribe <VAULT_ADDRESS> --share-class 0 --amount 1000

# 2. Wait for manager to fulfill subscription
# (Manager runs: glam manage fulfill <VAULT> --share-class 0)

# 3. Claim shares
glam invest claim-subscription <VAULT_ADDRESS> --share-class 0

# 4. Later, redeem 100 shares
glam invest redeem <VAULT_ADDRESS> --share-class 0 --shares 100

# 5. Wait for fulfillment, then claim
glam invest claim-redemption <VAULT_ADDRESS> --share-class 0
```

### Cross-Chain USDC Bridge (CLI)

```bash
# Bridge USDC from Solana to Ethereum

# 1. Bridge 1000 USDC to Ethereum address
glam cctp bridge-usdc <VAULT_ADDRESS> \
  --destination 0x742d35Cc6634C0532925a3b844Bc9e7595f00000 \
  --amount 1000 \
  --destination-domain 0

# 2. List pending transfers
glam cctp list <VAULT_ADDRESS>

# To receive USDC from another chain:
glam cctp receive <VAULT_ADDRESS> --message <ATTESTATION_MESSAGE_HEX>
```

### Full Vault Management Script (SDK)

```typescript
import { GlamClient, COMMON_MINTS } from "@glamsystems/glam-sdk";
import { BN } from "@coral-xyz/anchor";

async function manageVault() {
  const client = new GlamClient({ keypair });
  const vaultPda = new PublicKey("<VAULT_ADDRESS>");

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
