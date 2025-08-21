# Aleo Development Guide

This guide helps you set up and deploy Aleo programs on a local development network.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Local Network Setup](#local-network-setup)
3. [TMUX Basics](#tmux-basics)
4. [Building and Testing](#building-and-testing)
5. [Deployment](#deployment)
6. [Executing Functions](#executing-functions)
7. [Troubleshooting](#troubleshooting)

## Prerequisites

Before starting, ensure you have:
- [Leo CLI](https://developer.aleo.org/guides/aleo/language/) installed
- [snarkOS](https://github.com/ProvableHQ/snarkOS) repository cloned
- [TMUX](https://tmux.github.io/) installed

### Installing TMUX
```bash
# macOS
brew install tmux

# Ubuntu
sudo apt-get install tmux
```

## Local Network Setup

### Step 1: Start the Local Devnet
```bash
cd snarkOS
./devnet.sh
```

When prompted:
- **Number of validators**: Press Enter for default (4)
- **Number of clients**: Press Enter for default (2)  
- **Network ID**: Press Enter for default (1)
- **Build binary**: Enter `y` (first time) or `n` (subsequent runs)
- **Clear ledger**: Enter `y` for clean start or `n` to continue

### Step 2: Verify Network is Running
The script will create a TMUX session with multiple windows:
- **Windows 0-3**: Validator nodes (validator-0, validator-1, validator-2, validator-3)
- **Windows 4-5**: Client nodes (client-0, client-1)

## TMUX Basics

TMUX is a terminal multiplexer that manages multiple terminal sessions. Your devnet runs inside TMUX.

### Navigation Commands
```bash
# List sessions
tmux list-sessions

# Attach to devnet session
tmux attach-session -t devnet

# Detach from session (keeps it running)
Ctrl+b, then d

# Navigate between windows
Ctrl+b, then w          # Show window list
Ctrl+b, then :          # Enter command mode
Ctrl+b, then Ctrl+n     # Next window
Ctrl+b, then Ctrl+p     # Previous window
```

### Window Management
```bash
# In command mode (Ctrl+b, then :)
select-window -t :0     # Go to window 0 (validator-0)
select-window -t :1     # Go to window 1 (validator-1)
kill-session            # Stop the entire devnet
```

### Scrolling in TMUX
```bash
Ctrl+b, then [          # Enter scroll mode
# Use arrow keys or Page Up/Down to scroll
q                       # Exit scroll mode
```

## Building and Testing

### Step 1: Build Your Program
```bash
cd your-aleo-project
leo build
```

### Step 2: Run Tests
```bash
leo test
```

Ensure all tests pass before deployment.

## Deployment

### Step 1: Get Validator Node 0 Credentials
Only **validator node 0** has initial testnet credits for deployment.

1. **Navigate to validator-0:**
   ```bash
   # In TMUX session
   Ctrl+b, then w        # Show window list
   # Select validator-0 (window 0)
   ```

2. **Find the private key:**
   Look for this line in the output:
   ```
   🔑 Your development private key for node 0 is APrivateKey1zkp8CZNn3yeCseEtxuVPbDCwSyhGW6yZKUYKfgXmcpoGPWH
   ```

3. **Copy the entire private key** (starts with `APrivateKey1...`)

### Step 2: Set Up Leo Account
1. **Detach from TMUX:**
   ```bash
   Ctrl+b, then d
   ```

2. **Import the validator-0 private key:**
   ```bash
   leo account import --write APrivateKey1zkp8CZNn3yeCseEtxuVPbDCwSyhGW6yZKUYKfgXmcpoGPWH
   ```

   This command:
   - Imports the private key
   - Sets it as the active account
   - Writes it to your project's `.env` file

### Step 3: Deploy Your Program
```bash
leo deploy --endpoint http://localhost:3030 --broadcast --consensus-version 1
```

**What happens:**
1. Leo compiles your program
2. Shows deployment cost breakdown
3. Prompts for confirmation
4. Broadcasts transaction to local devnet
5. Confirms deployment success

### Step 4: Verify Deployment
- ✅ Check for "Deployment confirmed!" message
- ✅ Note the transaction IDs
- ✅ Your program is now live on local devnet

## Executing Functions

### Basic Execution
```bash
leo execute --endpoint http://localhost:3030 --broadcast <function_name> <arguments>
```

### Examples
```bash
# Initialize program
leo execute --endpoint http://localhost:3030 --broadcast initialize <bridge_address> <owner_address>

# Mint tokens
leo execute --endpoint http://localhost:3030 --broadcast mint <owner> <amount>

# Transfer tokens
leo execute --endpoint http://localhost:3030 --broadcast transfer <recipient> <amount>
```

### Setting Default Endpoint
To avoid typing `--endpoint` every time, add to your `.env` file:
```bash
ENDPOINT=http://localhost:3030
```

## Troubleshooting

### Common Issues

**❌ "Invalid public balance for account"**
- **Cause**: Using wrong private key (client node instead of validator-0)
- **Solution**: Use validator-0's private key

**❌ "Failed to generate deployment transaction"**
- **Cause**: snarkOS devnet not running or REST API issues
- **Solution**: Check if devnet is running and ports are open

**❌ TMUX navigation not working**
- **Cause**: TMUX prefix not responding
- **Solution**: Try `Ctrl+b, then :` and use direct commands

**❌ "Failed to get consensus version"**
- **Cause**: REST API not responding
- **Solution**: Use `--consensus-version 1` flag

### Port Verification
```bash
# Check if ports are open
lsof -i :3030    # REST API
lsof -i :4130    # P2P Network
lsof -i :5000    # BFT Consensus
```

### Restarting Devnet
```bash
# Stop current devnet
tmux kill-session -t devnet

# Start fresh devnet
cd snarkOS
./devnet.sh
```

## Additional Resources

- [Aleo Language Guide](https://developer.aleo.org/guides/aleo/language/)
- [Leo CLI Documentation](https://developer.aleo.org/leo/cli/)
- [snarkOS Repository](https://github.com/ProvableHQ/snarkOS)
- [Deploying and Executing Programs in Aleo](https://concbon-mene.medium.com/deploying-and-executing-programs-in-aleo-4b535676e807)
