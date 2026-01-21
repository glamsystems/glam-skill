# GLAM SDK Reference

TypeScript SDK reference for `@glamsystems/glam-sdk`.

## Table of Contents

- [Installation](#installation)
- [GlamClient](#glamclient)
- [Vault Operations](#vault-operations)
- [Access Control](#access-control)
- [Jupiter Swap](#jupiter-swap)
- [Drift Protocol](#drift-protocol)
- [Kamino Lending](#kamino-lending)
- [Invest Operations](#invest-operations)
- [Fee Management](#fee-management)
- [Timelock](#timelock)
- [Transaction Options](#transaction-options)
- [Models](#models)

---

## Installation

```bash
npm install @glamsystems/glam-sdk
# or
yarn add @glamsystems/glam-sdk
# or
pnpm add @glamsystems/glam-sdk
```

**Peer dependencies:**
```bash
npm install @solana/web3.js @coral-xyz/anchor
```

---

## GlamClient

Main entry point for SDK operations.

### Constructor

```typescript
import { GlamClient } from "@glamsystems/glam-sdk";

const client = new GlamClient({
  cluster?: "mainnet-beta" | "devnet" | "localnet";
  connection?: Connection;
  wallet?: Wallet;
  keypair?: Keypair;
  provider?: AnchorProvider;
});
```

**Options:**

| Option | Type | Description |
|--------|------|-------------|
| `cluster` | string | Cluster to connect to (default: "mainnet-beta") |
| `connection` | Connection | Custom Solana connection |
| `wallet` | Wallet | Wallet adapter for signing |
| `keypair` | Keypair | Keypair for signing |
| `provider` | AnchorProvider | Custom Anchor provider |

### Properties

```typescript
// Sub-clients (lazy-loaded)
client.vault        // Vault operations
client.access       // Delegate/permission management
client.jupiterSwap  // Jupiter swap operations
client.drift        // Drift protocol operations
client.kaminoLending // Kamino lending operations
client.invest       // Subscription/redemption
client.fees         // Fee management
client.timelock     // Timelock operations

// Core properties
client.program      // Anchor program instance
client.provider     // Anchor provider
client.connection   // Solana connection
```

---

## Vault Operations

Access via `client.vault`.

### create

Create a new vault.

```typescript
const { vaultPda, txId } = await client.vault.create({
  name: string;
  assets?: PublicKey[];
  manager?: PublicKey;
  shareClassName?: string;
  shareClassSymbol?: string;
  shareClassDecimals?: number;
}, options?: TxOptions);
```

### fetch

Fetch vault state.

```typescript
const state = await client.vault.fetch(vaultPda: PublicKey);
// Returns: StateModel
```

### list

List vaults.

```typescript
const vaults = await client.vault.list({
  manager?: PublicKey;
  owner?: PublicKey;
});
// Returns: StateModel[]
```

### update

Update vault configuration.

```typescript
await client.vault.update(vaultPda: PublicKey, {
  name?: string;
  uri?: string;
}, options?: TxOptions);
```

### close

Close an empty vault.

```typescript
await client.vault.close(vaultPda: PublicKey, options?: TxOptions);
```

### getBalances

Get vault token balances.

```typescript
const balances = await client.vault.getBalances(vaultPda: PublicKey);
// Returns: { mint: PublicKey, amount: BN, decimals: number }[]
```

### wrapSol / unwrapSol

Wrap or unwrap SOL.

```typescript
await client.vault.wrapSol(vaultPda: PublicKey, amount: BN, options?: TxOptions);
await client.vault.unwrapSol(vaultPda: PublicKey, amount: BN, options?: TxOptions);
```

### allowlistAsset

Add asset to allowlist.

```typescript
await client.vault.allowlistAsset(vaultPda: PublicKey, mint: PublicKey, options?: TxOptions);
```

### enableIntegration / disableIntegration

Enable or disable integrations.

```typescript
await client.vault.enableIntegration(vaultPda: PublicKey, integration: Integration, options?: TxOptions);
await client.vault.disableIntegration(vaultPda: PublicKey, integration: Integration, options?: TxOptions);
```

**Integration enum:**
```typescript
type Integration =
  | "JupiterSwap"
  | "DriftProtocol"
  | "KaminoLend"
  | "KaminoVault"
  | "KaminoFarm"
  | "MarinadeStaking"
  | "NativeStaking"
  | "SplStakePool"
  | "SanctumStaking"
  | "MeteoraDlmm"
  | "Invariant"
  | "Orca";
```

---

## Access Control

Access via `client.access`.

### grantDelegate

Grant permissions to delegate.

```typescript
await client.access.grantDelegate(vaultPda: PublicKey, {
  delegate: PublicKey;
  permissions: Permission[];
}, options?: TxOptions);
```

**Permission enum:**
```typescript
type Permission =
  | "Swap"
  | "Stake"
  | "Unstake"
  | "LiquidUnstake"
  | "DriftInit"
  | "DriftDeposit"
  | "DriftWithdraw"
  | "DriftUpdateUser"
  | "DriftDeleteUser"
  | "DriftPlaceOrders"
  | "DriftCancelOrders"
  | "DriftPerpMarket"
  | "DriftSpotMarket"
  | "KaminoInit"
  | "KaminoDeposit"
  | "KaminoWithdraw"
  | "KaminoBorrow"
  | "KaminoRepay"
  | "MeteoraInit"
  | "MeteoraDeposit"
  | "MeteoraWithdraw";
```

### revokeDelegate

Revoke delegate permissions.

```typescript
await client.access.revokeDelegate(vaultPda: PublicKey, delegate: PublicKey, options?: TxOptions);
```

### listDelegates

List vault delegates.

```typescript
const delegates = await client.access.listDelegates(vaultPda: PublicKey);
// Returns: { pubkey: PublicKey, permissions: Permission[] }[]
```

---

## Jupiter Swap

Access via `client.jupiterSwap`.

### swap

Execute token swap via Jupiter.

```typescript
const txId = await client.jupiterSwap.swap(vaultPda: PublicKey, {
  inputMint: PublicKey;
  outputMint: PublicKey;
  amount: BN;
  slippageBps?: number;    // default: 50
  onlyDirectRoutes?: boolean;
}, options?: TxOptions);
```

### getQuote

Get swap quote without executing.

```typescript
const quote = await client.jupiterSwap.getQuote({
  inputMint: PublicKey;
  outputMint: PublicKey;
  amount: BN;
  slippageBps?: number;
});
// Returns: { inAmount, outAmount, priceImpactPct, ... }
```

### setMaxSlippage

Set maximum slippage policy.

```typescript
await client.jupiterSwap.setMaxSlippage(vaultPda: PublicKey, maxSlippageBps: number, options?: TxOptions);
```

### allowlistToken

Add token to swap allowlist.

```typescript
await client.jupiterSwap.allowlistToken(vaultPda: PublicKey, mint: PublicKey, options?: TxOptions);
```

---

## Drift Protocol

Access via `client.drift`.

### initUser

Initialize Drift user account.

```typescript
await client.drift.initUser(vaultPda: PublicKey, options?: TxOptions);
```

### deposit / withdraw

Deposit or withdraw collateral.

```typescript
await client.drift.deposit(vaultPda: PublicKey, {
  marketIndex: number;
  amount: BN;
}, options?: TxOptions);

await client.drift.withdraw(vaultPda: PublicKey, {
  marketIndex: number;
  amount: BN;
}, options?: TxOptions);
```

### placeSpotOrder

Place spot market order.

```typescript
await client.drift.placeSpotOrder(vaultPda: PublicKey, {
  marketIndex: number;
  amount: BN;
  direction: "long" | "short";
  price?: BN;           // limit price
  reduceOnly?: boolean;
}, options?: TxOptions);
```

### placePerpOrder

Place perpetual order.

```typescript
await client.drift.placePerpOrder(vaultPda: PublicKey, {
  marketIndex: number;
  amount: BN;
  direction: "long" | "short";
  price?: BN;
  reduceOnly?: boolean;
  postOnly?: boolean;
}, options?: TxOptions);
```

### cancelOrder

Cancel order.

```typescript
await client.drift.cancelOrder(vaultPda: PublicKey, orderId: number, options?: TxOptions);
```

### getPositions

Get open positions.

```typescript
const positions = await client.drift.getPositions(vaultPda: PublicKey);
```

---

## Kamino Lending

Access via `client.kaminoLending`.

### init

Initialize Kamino lending for vault.

```typescript
await client.kaminoLending.init(vaultPda: PublicKey, options?: TxOptions);
```

### deposit

Deposit to lending market.

```typescript
await client.kaminoLending.deposit(vaultPda: PublicKey, {
  market: PublicKey;
  mint: PublicKey;
  amount: BN;
}, options?: TxOptions);
```

### withdraw

Withdraw from lending market.

```typescript
await client.kaminoLending.withdraw(vaultPda: PublicKey, {
  market: PublicKey;
  mint: PublicKey;
  amount: BN;
}, options?: TxOptions);
```

### borrow

Borrow from lending market.

```typescript
await client.kaminoLending.borrow(vaultPda: PublicKey, {
  market: PublicKey;
  mint: PublicKey;
  amount: BN;
}, options?: TxOptions);
```

### repay

Repay borrowed amount.

```typescript
await client.kaminoLending.repay(vaultPda: PublicKey, {
  market: PublicKey;
  mint: PublicKey;
  amount: BN;
}, options?: TxOptions);
```

### getPositions

Get lending positions.

```typescript
const positions = await client.kaminoLending.getPositions(vaultPda: PublicKey);
```

---

## Invest Operations

Access via `client.invest`.

### subscribe

Subscribe to vault share class.

```typescript
await client.invest.subscribe(vaultPda: PublicKey, {
  shareClassIndex: number;
  amount: BN;
}, options?: TxOptions);
```

### claimSubscription

Claim subscription shares after processing.

```typescript
await client.invest.claimSubscription(vaultPda: PublicKey, shareClassIndex: number, options?: TxOptions);
```

### redeem

Redeem vault shares.

```typescript
await client.invest.redeem(vaultPda: PublicKey, {
  shareClassIndex: number;
  shares: BN;
}, options?: TxOptions);
```

### claimRedemption

Claim redemption proceeds.

```typescript
await client.invest.claimRedemption(vaultPda: PublicKey, shareClassIndex: number, options?: TxOptions);
```

---

## Fee Management

Access via `client.fees`.

### setPrice

Set share class NAV price.

```typescript
await client.fees.setPrice(vaultPda: PublicKey, {
  shareClassIndex: number;
  price: BN;
}, options?: TxOptions);
```

### fulfill

Fulfill pending subscriptions/redemptions.

```typescript
await client.fees.fulfill(vaultPda: PublicKey, shareClassIndex: number, options?: TxOptions);
```

### claimFees

Claim accrued fees.

```typescript
await client.fees.claimFees(vaultPda: PublicKey, options?: TxOptions);
```

---

## Timelock

Access via `client.timelock`.

### get

Get timelock status.

```typescript
const timelock = await client.timelock.get(vaultPda: PublicKey);
```

### set

Set timelock delay.

```typescript
await client.timelock.set(vaultPda: PublicKey, delaySeconds: number, options?: TxOptions);
```

### apply

Apply pending timelock.

```typescript
await client.timelock.apply(vaultPda: PublicKey, options?: TxOptions);
```

### cancel

Cancel pending timelock.

```typescript
await client.timelock.cancel(vaultPda: PublicKey, options?: TxOptions);
```

---

## Transaction Options

All methods that submit transactions accept `TxOptions`:

```typescript
interface TxOptions {
  // Priority fee in microlamports
  priorityFee?: number;

  // Skip preflight simulation
  skipPreflight?: boolean;

  // Commitment level
  commitment?: Commitment;

  // Additional signers
  signers?: Keypair[];

  // Compute unit limit
  computeUnits?: number;

  // Return instructions without sending
  dryRun?: boolean;
}
```

**Example:**

```typescript
await client.vault.create({
  name: "My Vault"
}, {
  priorityFee: 10000,
  commitment: "confirmed"
});
```

---

## Models

### StateModel

Vault state representation.

```typescript
interface StateModel {
  pubkey: PublicKey;
  name: string;
  uri: string;
  manager: PublicKey;
  owner: PublicKey;
  created: Date;
  integrations: Integration[];
  assets: PublicKey[];
  shareClasses: MintModel[];
  paused: boolean;
  timelockDelay: number;
}
```

### MintModel

Share class representation.

```typescript
interface MintModel {
  pubkey: PublicKey;
  name: string;
  symbol: string;
  decimals: number;
  supply: BN;
  price: BN;
}
```

---

## Utility Functions

### PDA Derivation

```typescript
import { deriveVaultPda, deriveShareClassPda } from "@glamsystems/glam-sdk";

const [vaultPda] = deriveVaultPda(manager: PublicKey, name: string);
const [shareClassPda] = deriveShareClassPda(vaultPda: PublicKey, index: number);
```

### Constants

```typescript
import { GLAM_PROGRAM_ID, COMMON_MINTS } from "@glamsystems/glam-sdk";

COMMON_MINTS.SOL;   // So11111111111111111111111111111111111111112
COMMON_MINTS.USDC;  // EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
COMMON_MINTS.USDT;  // Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB
```
