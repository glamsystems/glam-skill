# SDK: Integration Clients

## Access Control

Access via `client.access`.

### grantDelegate

```typescript
await client.access.grantDelegate(vaultPda: PublicKey, {
  delegate: PublicKey;
  permissions: Permission[];
}, options?: TxOptions);
```

**Permission type:**
```typescript
type Permission =
  | "Swap" | "Stake" | "Unstake" | "LiquidUnstake"
  | "DriftInit" | "DriftDeposit" | "DriftWithdraw" | "DriftUpdateUser"
  | "DriftDeleteUser" | "DriftPlaceOrders" | "DriftCancelOrders"
  | "DriftPerpMarket" | "DriftSpotMarket"
  | "KaminoInit" | "KaminoDeposit" | "KaminoWithdraw" | "KaminoBorrow" | "KaminoRepay"
  | "MeteoraInit" | "MeteoraDeposit" | "MeteoraWithdraw";
```

### revokeDelegate

```typescript
await client.access.revokeDelegate(vaultPda: PublicKey, delegate: PublicKey, options?: TxOptions);
```

### listDelegates

```typescript
const delegates = await client.access.listDelegates(vaultPda: PublicKey);
// Returns: { pubkey: PublicKey, permissions: Permission[] }[]
```

---

## Jupiter Swap

Access via `client.jupiterSwap`.

### swap

```typescript
const txId = await client.jupiterSwap.swap(vaultPda: PublicKey, {
  inputMint: PublicKey;
  outputMint: PublicKey;
  amount: BN;
  slippageBps?: number;      // default: 50
  onlyDirectRoutes?: boolean;
}, options?: TxOptions);
```

### getQuote

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

```typescript
await client.jupiterSwap.setMaxSlippage(vaultPda: PublicKey, maxSlippageBps: number, options?: TxOptions);
```

### allowlistToken

```typescript
await client.jupiterSwap.allowlistToken(vaultPda: PublicKey, mint: PublicKey, options?: TxOptions);
```

---

## Drift Protocol

Access via `client.drift`.

### initUser

```typescript
await client.drift.initUser(vaultPda: PublicKey, options?: TxOptions);
```

### deposit / withdraw

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

```typescript
await client.drift.placeSpotOrder(vaultPda: PublicKey, {
  marketIndex: number;
  amount: BN;
  direction: "long" | "short";
  price?: BN;
  reduceOnly?: boolean;
}, options?: TxOptions);
```

### placePerpOrder

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

```typescript
await client.drift.cancelOrder(vaultPda: PublicKey, orderId: number, options?: TxOptions);
```

### getPositions

```typescript
const positions = await client.drift.getPositions(vaultPda: PublicKey);
```

---

## Kamino Lending

Access via `client.kaminoLending`.

### init

```typescript
await client.kaminoLending.init(vaultPda: PublicKey, options?: TxOptions);
```

### deposit

```typescript
await client.kaminoLending.deposit(vaultPda: PublicKey, {
  market: PublicKey;
  mint: PublicKey;
  amount: BN;
}, options?: TxOptions);
```

### withdraw

```typescript
await client.kaminoLending.withdraw(vaultPda: PublicKey, {
  market: PublicKey;
  mint: PublicKey;
  amount: BN;
}, options?: TxOptions);
```

### borrow

```typescript
await client.kaminoLending.borrow(vaultPda: PublicKey, {
  market: PublicKey;
  mint: PublicKey;
  amount: BN;
}, options?: TxOptions);
```

### repay

```typescript
await client.kaminoLending.repay(vaultPda: PublicKey, {
  market: PublicKey;
  mint: PublicKey;
  amount: BN;
}, options?: TxOptions);
```

### getPositions

```typescript
const positions = await client.kaminoLending.getPositions(vaultPda: PublicKey);
```

---

## Invest Operations

Access via `client.invest`.

### subscribe

```typescript
await client.invest.subscribe(vaultPda: PublicKey, {
  shareClassIndex: number;
  amount: BN;
}, options?: TxOptions);
```

### claimSubscription

```typescript
await client.invest.claimSubscription(vaultPda: PublicKey, shareClassIndex: number, options?: TxOptions);
```

### redeem

```typescript
await client.invest.redeem(vaultPda: PublicKey, {
  shareClassIndex: number;
  shares: BN;
}, options?: TxOptions);
```

### claimRedemption

```typescript
await client.invest.claimRedemption(vaultPda: PublicKey, shareClassIndex: number, options?: TxOptions);
```

---

## Fee Management

Access via `client.fees`.

### setPrice

```typescript
await client.fees.setPrice(vaultPda: PublicKey, {
  shareClassIndex: number;
  price: BN;
}, options?: TxOptions);
```

### fulfill

```typescript
await client.fees.fulfill(vaultPda: PublicKey, shareClassIndex: number, options?: TxOptions);
```

### claimFees

```typescript
await client.fees.claimFees(vaultPda: PublicKey, options?: TxOptions);
```

---

## Timelock

Access via `client.timelock`.

### get

```typescript
const timelock = await client.timelock.get(vaultPda: PublicKey);
```

### set

```typescript
await client.timelock.set(vaultPda: PublicKey, delaySeconds: number, options?: TxOptions);
```

### apply

```typescript
await client.timelock.apply(vaultPda: PublicKey, options?: TxOptions);
```

### cancel

```typescript
await client.timelock.cancel(vaultPda: PublicKey, options?: TxOptions);
```

---

## Drift Vaults

Access via `client.driftVaults`.

### deposit

```typescript
await client.driftVaults.deposit(driftVault: PublicKey, amount: BN, options?: TxOptions);
```

### requestWithdraw

```typescript
await client.driftVaults.requestWithdraw(driftVault: PublicKey, shares: BN, options?: TxOptions);
```

### cancelWithdrawRequest

```typescript
await client.driftVaults.cancelWithdrawRequest(driftVault: PublicKey, options?: TxOptions);
```

### withdraw

```typescript
await client.driftVaults.withdraw(driftVault: PublicKey, options?: TxOptions);
```

---

## Kamino Vaults

Access via `client.kaminoVaults`.

### deposit

```typescript
await client.kaminoVaults.deposit(kaminoVault: PublicKey, amount: BN, options?: TxOptions);
```

### withdraw

```typescript
await client.kaminoVaults.withdraw(kaminoVault: PublicKey, shares: BN, options?: TxOptions);
```

---

## Kamino Farms

Access via `client.kaminoFarm`.

### stake

```typescript
await client.kaminoFarm.stake(amount: BN, farmState: PublicKey, options?: TxOptions);
```

### unstake

```typescript
await client.kaminoFarm.unstake(amount: BN, farmState: PublicKey, options?: TxOptions);
```

### harvest

```typescript
await client.kaminoFarm.harvest(farmStates: PublicKey[], options?: TxOptions);
```

---

## Marinade Staking

Access via `client.marinade`.

### stake

```typescript
await client.marinade.stake(amount: BN, options?: TxOptions);
```

### unstake

```typescript
await client.marinade.unstake(amount: BN, options?: TxOptions);
```

### liquidUnstake

```typescript
await client.marinade.liquidUnstake(amount: BN, options?: TxOptions);
```

---

## SPL Stake Pool

Access via `client.stakePool`.

### depositSol

```typescript
await client.stakePool.depositSol(stakePool: PublicKey, amount: BN, options?: TxOptions);
```

### unstake

```typescript
await client.stakePool.unstake(lstMint: PublicKey, amount: BN, deactivate: boolean, options?: TxOptions);
```

---

## Price

Access via `client.price`.

### priceVaultIxs

```typescript
const instructions = await client.price.priceVaultIxs();
// Returns instructions to price all vault assets
```

---

## Mint

Access via `client.mint`.

### update

```typescript
await client.mint.update({
  minSubscription?: BN;
  minRedemption?: BN;
}, options?: TxOptions);
```

### pauseSubscription / unpauseSubscription

```typescript
await client.mint.pauseSubscription(options?: TxOptions);
await client.mint.unpauseSubscription(options?: TxOptions);
```

### pauseRedemption / unpauseRedemption

```typescript
await client.mint.pauseRedemption(options?: TxOptions);
await client.mint.unpauseRedemption(options?: TxOptions);
```

---

## Models

### StateModel

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
