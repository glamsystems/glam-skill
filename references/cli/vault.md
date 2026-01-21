# CLI: Vault Commands

## Configuration

The GLAM CLI uses a JSON configuration file.

### Config File Location

- **Default**: `~/.config/glam/config.json`
- **Docker**: `/workspace/config.json`
- **Override**: Use `-C, --config <path>` flag

### Config File Format

```json
{
  "keypair_path": "~/.config/solana/id.json",
  "json_rpc_url": "https://api.mainnet-beta.solana.com",
  "tx_rpc_url": "https://api.mainnet-beta.solana.com",
  "cluster": "mainnet-beta",
  "priority_fee": 10000,
  "jupiter_api_key": "optional-api-key"
}
```

### `glam env`

Display current configuration.

```bash
glam env
```

**Output shows:** keypair path, RPC URLs, cluster, priority fee, and other configured values.

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

**Keys:** `name`, `uri`

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

### `glam vault wrap` / `glam vault unwrap`

Wrap/unwrap SOL to wSOL in vault.

```bash
glam vault wrap <VAULT_ADDRESS> --amount <AMOUNT>
glam vault unwrap <VAULT_ADDRESS> --amount <AMOUNT>
```

### `glam vault allowlist-asset`

Add asset to vault allowlist.

```bash
glam vault allowlist-asset <VAULT_ADDRESS> <MINT_ADDRESS>
```

### `glam vault list-assets`

List assets in vault allowlist.

```bash
glam vault list-assets <VAULT_ADDRESS>
```

### `glam vault remove-asset`

Remove asset from vault allowlist.

```bash
glam vault remove-asset <VAULT_ADDRESS> <MINT_ADDRESS>
```

### `glam vault update-owner`

Transfer vault ownership to a new owner.

```bash
glam vault update-owner <VAULT_ADDRESS> <NEW_OWNER_PUBKEY>
```

### `glam vault set-enabled`

Enable or disable the vault.

```bash
glam vault set-enabled <VAULT_ADDRESS> <true|false>
```

### `glam vault extend`

Extend vault state account size (for additional data storage).

```bash
glam vault extend <VAULT_ADDRESS> <BYTES>
```

### `glam vault close`

Close a vault (must be empty).

```bash
glam vault close <VAULT_ADDRESS>
```

---

## Integration Commands

### `glam integration list`

List available integrations.

```bash
glam integration list
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

### `glam integration enable`

Enable integrations for a vault.

```bash
glam integration enable <VAULT_ADDRESS> <INTEGRATION>...
```

**Examples:**

```bash
# Enable single integration
glam integration enable <VAULT> JupiterSwap

# Enable multiple integrations
glam integration enable <VAULT> JupiterSwap DriftProtocol KaminoLend
```

### `glam integration disable`

Disable an integration.

```bash
glam integration disable <VAULT_ADDRESS> <INTEGRATION>
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
