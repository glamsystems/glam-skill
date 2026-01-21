# GLAM Troubleshooting

Common errors and solutions for CLI and SDK operations.

## Table of Contents

- [Installation Issues](#installation-issues)
- [Configuration Errors](#configuration-errors)
- [Transaction Failures](#transaction-failures)
- [Permission Errors](#permission-errors)
- [RPC Issues](#rpc-issues)
- [Integration-Specific Issues](#integration-specific-issues)

---

## Installation Issues

### npm install fails with permission errors

**Error:**
```
EACCES: permission denied
```

**Solution:**
```bash
# Use npx instead of global install
npx @glamsystems/glam-cli vault list

# Or fix npm permissions
npm config set prefix ~/.npm-global
export PATH=~/.npm-global/bin:$PATH
npm install -g @glamsystems/glam-cli
```

### Module not found errors (SDK)

**Error:**
```
Cannot find module '@glamsystems/glam-sdk'
```

**Solution:**
```bash
# Ensure peer dependencies are installed
npm install @solana/web3.js @coral-xyz/anchor

# Verify installation
npm list @glamsystems/glam-sdk
```

---

## Configuration Errors

### Keypair not found

**Error:**
```
Error: Keypair file not found at /path/to/keypair.json
```

**Solution:**
```bash
# Create/edit config file
mkdir -p ~/.config/glam
cat > ~/.config/glam/config.json << 'EOF'
{
  "keypair_path": "~/.config/solana/id.json",
  "json_rpc_url": "https://api.mainnet-beta.solana.com"
}
EOF

# Verify keypair exists
ls -la ~/.config/solana/id.json

# Create new keypair if needed
solana-keygen new --outfile ~/.config/solana/id.json
```

### Invalid keypair format

**Error:**
```
Error: Invalid secret key
```

**Solution:**
The keypair file must be a JSON array of 64 numbers. Verify format:
```bash
cat ~/.config/solana/id.json | head -c 100
# Should start with: [123,45,67,...
```

### Configuration not persisted

**Error:**
```
Error: keypairPath is not configured
```

**Solution:**
```bash
# Create config file at ~/.config/glam/config.json
mkdir -p ~/.config/glam
cat > ~/.config/glam/config.json << 'EOF'
{
  "keypair_path": "~/.config/solana/id.json",
  "json_rpc_url": "https://api.mainnet-beta.solana.com"
}
EOF

# Verify configuration
glam env
```

---

## Transaction Failures

### Insufficient funds

**Error:**
```
Error: Attempt to debit an account but found no record of a prior credit
```

**Solution:**
- Ensure wallet has enough SOL for transaction fees
- Check vault has sufficient token balance for the operation

```bash
# Check wallet balance
solana balance

# Check vault balances
glam vault balances <VAULT_ADDRESS>
```

### Transaction simulation failed

**Error:**
```
Error: Transaction simulation failed: Error processing Instruction 0
```

**Solution:**
- Run with `--dry-run` to see detailed error
- Check that all required accounts exist
- Verify integration is enabled for the operation

```bash
glam jupiter swap <VAULT> --input-mint <A> --output-mint <B> --amount 100 --dry-run
```

### Blockhash expired

**Error:**
```
Error: Transaction was not confirmed in 60.00 seconds
```

**Solution:**
```bash
# Retry with higher priority fee
glam jupiter swap <VAULT> ... --priority-fee 50000

# Or use a faster RPC by editing ~/.config/glam/config.json
# Update json_rpc_url to a faster endpoint
```

### Compute budget exceeded

**Error:**
```
Error: exceeded CUs meter at BPF instruction
```

**Solution:**
For SDK, increase compute units:
```typescript
await client.jupiterSwap.swap(vaultPda, params, {
  computeUnits: 400000, // Increase from default
});
```

---

## Permission Errors

### Not authorized

**Error:**
```
Error: Signer is not authorized to perform this action
```

**Causes and solutions:**

1. **Wrong signer** - Ensure you're using the vault manager's keypair
   ```bash
   glam vault view <VAULT_ADDRESS>  # Check manager address
   ```

2. **Missing delegate permission** - Grant required permission
   ```bash
   glam delegate grant <VAULT> <DELEGATE> --permissions Swap
   ```

3. **Integration not enabled** - Enable the required integration
   ```bash
   glam integration enable <VAULT> JupiterSwap
   ```

### Delegate permission denied

**Error:**
```
Error: Delegate does not have required permission
```

**Solution:**
```bash
# List delegate permissions
glam delegate list <VAULT_ADDRESS>

# Grant missing permissions
glam delegate grant <VAULT> <DELEGATE> --permissions Swap,KaminoDeposit
```

### Asset not in allowlist

**Error:**
```
Error: Asset not in vault allowlist
```

**Solution:**
```bash
# Add asset to allowlist
glam vault allowlist-asset <VAULT_ADDRESS> <MINT_ADDRESS>
```

---

## RPC Issues

### Rate limited

**Error:**
```
Error: 429 Too Many Requests
```

**Solution:**
```bash
# Edit ~/.config/glam/config.json and update json_rpc_url to a paid endpoint
# Or use -C flag: glam -C /path/to/config.json <command>

# Or add delay between commands
```

### Connection refused

**Error:**
```
Error: Connection refused
```

**Solution:**
```bash
# Check current RPC URL
glam env

# Edit ~/.config/glam/config.json to fix json_rpc_url
```

### Account not found

**Error:**
```
Error: Account does not exist
```

**Causes:**
- Vault address is incorrect
- Vault was closed
- Using wrong cluster (mainnet vs devnet)

**Solution:**
```bash
# Verify vault exists
glam vault view <VAULT_ADDRESS>

# Check you're on the right cluster
glam env
```

---

## Integration-Specific Issues

### Jupiter: No route found

**Error:**
```
Error: No route found for swap
```

**Causes:**
- Very low liquidity for token pair
- Amount too large or too small
- Token not supported

**Solution:**
```bash
# Try smaller amount
glam jupiter swap <VAULT> ... --amount 10

# Use only direct routes
glam jupiter swap <VAULT> ... --only-direct-routes

# Check token is allowlisted
glam jupiter view-policy <VAULT>
```

### Jupiter: Slippage exceeded

**Error:**
```
Error: Slippage tolerance exceeded
```

**Solution:**
```bash
# Increase slippage tolerance
glam jupiter swap <VAULT> ... --slippage 100  # 1%

# Or use smaller trade size
```

### Drift: User not initialized

**Error:**
```
Error: Drift user account not found
```

**Solution:**
```bash
# Initialize Drift user first
glam drift-protocol init-user <VAULT_ADDRESS>
```

### Drift: Insufficient collateral

**Error:**
```
Error: Insufficient collateral for position
```

**Solution:**
```bash
# Deposit more collateral
glam drift-protocol deposit <VAULT> --market-index 0 --amount 1000

# Or reduce position size
```

### Kamino: Market not found

**Error:**
```
Error: Lending market not found
```

**Solution:**
Use correct market address. Main Kamino markets:
- Main Market: `7u3HeHxYDLhnCoErrtycNokbQYbWGzLs6JSDqGAv5PfF`
- JLP Market: `DxXdAyU3kCjnyggvHmY5nAwg5cRbbmdyX3npfDMjjMek`
- Altcoins: `ByYiZxp8QrdN9qbdtaAiePN8AAr3qvTPppNJDpf5DVJ5`

### Kamino: Obligation not initialized

**Error:**
```
Error: Obligation account not found
```

**Solution:**
```bash
# Initialize Kamino first
glam kamino-lend init <VAULT_ADDRESS>
```

---

## Getting Help

If you encounter issues not covered here:

1. Check vault state and integrations:
   ```bash
   glam vault view <VAULT_ADDRESS>
   ```

2. Run command with verbose output:
   ```bash
   glam <command> --verbose
   ```

3. Use dry-run to simulate:
   ```bash
   glam <command> --dry-run
   ```

4. Check GitHub issues: https://github.com/glamsystems/glam-cli/issues

5. Contact support via Discord or documentation site
