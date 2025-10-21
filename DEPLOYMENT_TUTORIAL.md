# MangroveGhostBook Custom Chain Deployment Tutorial

This tutorial walks you through deploying MangroveGhostBook to a custom chain.

## Prerequisites

- Foundry installed
- Soldeer installed
- Access to your custom chain RPC
- Private key for deployment
- Mangrove core contract deployed on your chain

## Step 1: Install Dependencies

First, install all required dependencies using Soldeer:

```bash
# Install Soldeer (if not already installed)
curl -L https://github.com/0xKitsune/soldeer/releases/download/v0.1.0/soldeer-v0.1.0-x86_64-apple-darwin.tar.gz | tar -xz
sudo mv soldeer /usr/local/bin/

# Navigate to project directory
cd /path/to/mgv-ghost-book

# Install dependencies
soldeer install

# Build project to verify everything compiles
forge build
```

## Step 2: Set Environment Variables

Set up your environment variables for deployment:

```bash
# Required variables
export MGV="0xabcdef"  # Your Mangrove contract address
export RPC_URL="https://your-custom-chain-rpc.com"      # Your custom chain RPC
export PRIVATE_KEY="0xabcdef..."                        # Your private key

# Optional variables
export ADMIN="0xabcdef" # Desired admin address
```

## Step 3: Deploy Core MangroveGhostBook

Deploy the main GhostBook contract:

```bash
forge script script/deployment/MangroveGhostBookDeployer.s.sol:MangroveGhostBookDeployer \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```

This will output the deployed GhostBook address. **Save this address** - you'll need it for the next steps.

Example output:
```
MangroveGhostBook deployed at: 0x1924EFD90DC53f6b37A31008EE00bde4ab1d1B9b
```

## Step 4: Deploy External Modules (Optional)

### Deploy Uniswap V3 Module

```bash
# Set the deployed GhostBook address
export GHOST_BOOK="0x1924EFD90DC53f6b37A31008EE00bde4ab1d1B9b"

# Deploy Uniswap V3 module
forge script script/deployment/modules/UniswapV3ModuleDeployer.s.sol:UniswapV3ModuleDeployer \
  --sig "run(bool,bool)" true true \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```

Parameters:
- First `true`: testMode (set to `false` for production)
- Second `true`: whitelistModule (automatically whitelists the module)

### Deploy Other Modules

You can deploy other available modules:

```bash
# Aerodrome Module (Base chain specific - update addresses for your chain)
forge script script/deployment/modules/AerodromeModuleDeployer.s.sol:AerodromeModuleDeployer \
  --sig "run(bool,bool)" true true \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast

# Uniswap V2 Module
forge script script/deployment/modules/UniswapV2ModuleDeployer.s.sol:UniswapV2ModuleDeployer \
  --sig "run(bool,bool)" true true \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast

# Balancer V2 Module
forge script script/deployment/modules/BalancerV2ModuleDeployer.s.sol:BalancerV2ModuleDeployer \
  --sig "run(bool,bool)" true true \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```

## Step 5: Manual Module Whitelisting (Alternative)

If you prefer to whitelist modules manually:

```bash
# Set module address (from previous deployment)
export MODULE_ADDRESS="0xYourModuleAddress"

# Whitelist the module
forge script script/actions/WhitelistModuleFromGhostbook.s.sol \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```

## Step 6: Verify Deployment

Check that everything is working:

```bash
# Verify GhostBook is deployed and configured
cast call $GHOST_BOOK "owner()" --rpc-url $RPC_URL

# Check if modules are whitelisted
cast call $GHOST_BOOK "whitelistedModules(address)" $MODULE_ADDRESS --rpc-url $RPC_URL
```

# DEPLOYED ADDRESSES

## SOMNIA

V3_SWAPPER_MODULE=0x9d4A99ccC048EB05b91DCE91968d9918ff082bcb
GHOST_BOOK=0x1924EFD90DC53f6b37A31008EE00bde4ab1d1B9b