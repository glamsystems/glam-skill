# SDK: GlamClient

Main entry point for SDK operations.

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

## Constructor

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

## Properties

```typescript
// Sub-clients (lazy-loaded)
client.vault          // Vault operations
client.state          // Vault state account management
client.access         // Delegate/permission management
client.jupiterSwap    // Jupiter swap operations
client.drift          // Drift protocol operations
client.driftVaults    // Drift managed vault operations
client.kaminoLending  // Kamino lending operations
client.kaminoVaults   // Kamino automated vault operations
client.kaminoFarm     // Kamino farm/staking operations
client.marinade       // Marinade staking operations
client.stakePool      // SPL stake pool operations
client.stake          // Native staking operations
client.invest         // Subscription/redemption
client.fees           // Fee management
client.price          // NAV pricing operations
client.mint           // Share class mint operations
client.timelock       // Timelock operations
client.cctp           // Cross-chain USDC bridging

// Core properties
client.program        // Anchor program instance
client.provider       // Anchor provider
client.connection     // Solana connection
```

---

## Vault Operations

Access via `client.vault`.

### create

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

```typescript
const state = await client.vault.fetch(vaultPda: PublicKey);
// Returns: StateModel
```

### list

```typescript
const vaults = await client.vault.list({
  manager?: PublicKey;
  owner?: PublicKey;
});
// Returns: StateModel[]
```

### update

```typescript
await client.vault.update(vaultPda: PublicKey, {
  name?: string;
  uri?: string;
}, options?: TxOptions);
```

### close

```typescript
await client.vault.close(vaultPda: PublicKey, options?: TxOptions);
```

### getBalances

```typescript
const balances = await client.vault.getBalances(vaultPda: PublicKey);
// Returns: { mint: PublicKey, amount: BN, decimals: number }[]
```

### wrapSol / unwrapSol

```typescript
await client.vault.wrapSol(vaultPda: PublicKey, amount: BN, options?: TxOptions);
await client.vault.unwrapSol(vaultPda: PublicKey, amount: BN, options?: TxOptions);
```

### allowlistAsset

```typescript
await client.vault.allowlistAsset(vaultPda: PublicKey, mint: PublicKey, options?: TxOptions);
```

### enableIntegration / disableIntegration

```typescript
await client.vault.enableIntegration(vaultPda: PublicKey, integration: Integration, options?: TxOptions);
await client.vault.disableIntegration(vaultPda: PublicKey, integration: Integration, options?: TxOptions);
```

**Integration type:**
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

## State Operations

Access via `client.state`.

### initialize

```typescript
const { statePda, txId } = await client.state.initialize({
  name: string;
  manager?: PublicKey;
  // ... additional state params
}, options?: TxOptions);
```

### update

```typescript
await client.state.update(vaultPda: PublicKey, {
  name?: string;
  uri?: string;
  // ... additional fields
}, options?: TxOptions);
```

### extend

```typescript
await client.state.extend(vaultPda: PublicKey, newBytes: number, options?: TxOptions);
```

### close

```typescript
await client.state.close(vaultPda: PublicKey, options?: TxOptions);
```

---

## CCTP Operations (Cross-Chain)

Access via `client.cctp`.

### bridgeUsdc

```typescript
const txId = await client.cctp.bridgeUsdc(vaultPda: PublicKey, {
  amount: BN;
  destinationDomain: number;
  recipient: string; // Hex address
}, options?: TxOptions);
```

### receiveUsdc

```typescript
const txId = await client.cctp.receiveUsdc(vaultPda: PublicKey, {
  sourceDomain: number;
  message: Buffer;
  attestation: Buffer;
}, options?: TxOptions);
```

### getIncomingBridgeEvents

```typescript
const events = await client.cctp.getIncomingBridgeEvents(vaultPda: PublicKey, {
  limit?: number;
});
```

### getOutgoingBridgeEvents

```typescript
const events = await client.cctp.getOutgoingBridgeEvents(vaultPda: PublicKey, {
  limit?: number;
});
```

---

## Transaction Options

All methods that submit transactions accept `TxOptions`:

```typescript
interface TxOptions {
  priorityFee?: number;       // Priority fee in microlamports
  skipPreflight?: boolean;    // Skip preflight simulation
  commitment?: Commitment;    // Commitment level
  signers?: Keypair[];        // Additional signers
  computeUnits?: number;      // Compute unit limit
  dryRun?: boolean;           // Return instructions without sending
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
