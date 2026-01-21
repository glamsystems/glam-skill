# CLI: Jupiter Commands

Token swaps via Jupiter aggregator.

**Prerequisite:** Enable `JupiterSwap` integration before using these commands.

```bash
glam integrations enable <VAULT_ADDRESS> JupiterSwap
```

## Commands

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

# Swap with 1% slippage
glam jupiter swap <VAULT> \
  --input-mint <INPUT> \
  --output-mint <OUTPUT> \
  --amount 1000 \
  --slippage 100

# Use direct routes only (less hops, potentially worse price)
glam jupiter swap <VAULT> \
  --input-mint <INPUT> \
  --output-mint <OUTPUT> \
  --amount 1000 \
  --only-direct-routes
```

### `glam jupiter view-policy`

View vault's Jupiter swap policy (max slippage, allowlisted tokens).

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
# Set max slippage to 1% (100 basis points)
glam jupiter set-max-slippage <VAULT> --max-slippage 100
```

### `glam jupiter allowlist-token`

Add token to swap allowlist.

```bash
glam jupiter allowlist-token <VAULT_ADDRESS> <MINT_ADDRESS>
```

---

## Slippage Reference

| BPS | Percentage |
|-----|------------|
| 10 | 0.1% |
| 50 | 0.5% |
| 100 | 1.0% |
| 200 | 2.0% |
| 500 | 5.0% |

---

## Common Token Mints

| Token | Mainnet Address |
|-------|-----------------|
| SOL | `So11111111111111111111111111111111111111112` |
| USDC | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| USDT | `Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB` |
| mSOL | `mSoLzYCxHdYgdzU16g5QSh3i5K3z3KZK7ytfqcJm7So` |
| jitoSOL | `J1toso1uCk3RLmjorhTtrVwY9HJ7X8V9yYac6Y7kGCPn` |
| bSOL | `bSo13r4TkiE4KumL71LsHTPpL2euBYLFx6h9HP3piy1` |
