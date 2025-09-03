# Drosera-hoodi
setting up your operator node via systemD and immortalizing your discord username onchain
**ALL THE COMMANDS IN THIS GUIDE ARE TO BE PASTED ONE BY ONE TO AVOID ERRORS**


# Drosera Trap & Operator Setup Guide (Hoodi Testnet)

This repository documents the setup and deployment of a **Drosera Trap** and a **Drosera Operator** node running on the Hoodi Ethereum testnet.


## Prerequisites

- Ubuntu/Linux environment (WSL Ubuntu works well)
- At least 4 CPU cores and 8GB RAM recommended
- Basic CLI knowledge
- Ethereum private key with funds on Hoodi testnet
- Open ports: `31313` and `31314` (or your configured ports)

# 🔥 [Need VPS under 3 USD? Click here](#-cheapest-vps-hosting-deals-under-3-per-month-perfect-for-testnets--lightweight-nodes)

## 🔹Hoodi Testnet ETH (Hoodi Token)

To mine or interact with contracts on the Hoodi Testnet, you'll need test ETH:

---

### 🛠️ Faucet Links

### 🔨 Mining Faucet:
[https://hoodi-faucet.pk910.de](https://hoodi-faucet.pk910.de)

### 💧 Public Faucets:
- [QuickNode Faucet](https://faucet.quicknode.com/ethereum/hoodi)  
- [Stakely Faucet](https://stakely.io/faucet/ethereum-hoodi-testnet-eth)

---

### 🔗 Hoodi Testnet Details

For more on the Hoodi testnet RPC, endpoints, and setup guides, refer to:  
👉 [https://github.com/eth-clients/hoodi](https://github.com/eth-clients/hoodi)

## **How to login VPS(If ANY)**  
```bash
ssh username@your.vps.ip.address
```  
*(Example: `ssh root@123.123.123.123`)*  

#### **. Enter Password**  
- Type/paste your **VPS password** (no visible typing) → Press `Enter`.  

#### **Success!**  
You’ll see a terminal prompt like:  
```bash
root@vps:~#
```

## 🔹Install Dependencies

```bash
sudo apt-get update && sudo apt-get upgrade -y
sudo apt install curl screen ufw iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
Create a screen with
screen -S drosera

To minimize your screen
ctrl then a + d to detach it

To resume screen
screen -r drosera

don't spam multiple screens to make your work easier to follow
---

# 🔹1. Drosera Trap Setup

### Install Required Tools

```bash
cd #ensure you in root directory

# Drosera CLI
curl -L https://app.drosera.io/install | bash
source ~/.bashrc
droseraup

# Foundry CLI (Solidity development)
curl -L https://foundry.paradigm.xyz | bash
source ~/.bashrc
foundryup

# Bun (JavaScript runtime)
curl -fsSL https://bun.sh/install | bash
source ~/.bashrc
```

### Initialize Trap Project

```bash
mkdir ~/my-drosera-trap
cd ~/my-drosera-trap

git config --global user.email "your_github_email@example.com"
git config --global user.name "your_github_username"

forge init -t drosera-network/trap-foundry-template
```
Replace your_github_email@example.com with your actual email used to open your github account, replace your_github_username with your github username 
### Build Trap

```bash
bun install
forge build
```
# 🔹2. Drosera Operator Setup
## A. Choose Docker or SystemD

Choose one Installation Method:
- [Method 1: Install using SystemD (click here)](#method-2-install-using-systemd)
# Method 1: Install using SystemD

### Remove Old Drosera SystemD Service (if any)

Before creating a new systemd service for Drosera, stop, disable, and remove any old service to avoid conflicts:
```bash
# Stop the old service if running
sudo systemctl stop drosera

# Disable the old service so it won't start on boot
sudo systemctl disable drosera

# Remove the old service file
sudo rm /etc/systemd/system/drosera.service

# Reload systemd to apply changes
sudo systemctl daemon-reload
```

---

### Prepare your environment variables

- `ETH_PRIVATE_KEY`: Your Ethereum private key (used for signing)
- `VPS_IP`: Your VPS public IP address (just the IP, no protocol or port)
- Use **Hoodi RPC URLs**:

```
https://ethereum-hoodi-rpc.publicnode.com
```

Backup URL can be the same or a different one if you have it.

---

### Create the SystemD service file

Run this command in terminal **after replacing** `PV_KEY` with your private key, and `VPS_IP` with your VPS IP:

```bash
sudo tee /etc/systemd/system/drosera.service > /dev/null <<EOF
[Unit]
Description=drosera node service
After=network-online.target

[Service]
User=$USER
Restart=always
RestartSec=15
LimitNOFILE=65535
ExecStart=$(which drosera-operator) node --db-file-path $HOME/.drosera.db --network-p2p-port 31313 --server-port 31314 \
    --eth-rpc-url https://ethereum-hoodi-rpc.publicnode.com \
    --eth-backup-rpc-url https://rpc.hoodi.ethpandaops.io \
    --drosera-address 0x91cB447BaFc6e0EA0F4Fe056F5a9b1F14bb06e5D \
    --eth-private-key PV_KEY \
    --listen-address 0.0.0.0 \
    --network-external-p2p-address VPS_IP \
    --disable-dnr-confirmation true \
    --eth-chain-id 560048

[Install]
WantedBy=multi-user.target
EOF
```

---

### Reload systemd and start the service

```bash
sudo systemctl daemon-reload
sudo systemctl enable drosera
sudo systemctl start drosera
```

---

### Check & Control Node

```bash
# Follow logs live
journalctl -u drosera.service -f

# Check node service status
sudo systemctl status drosera

# Stop the node service
sudo systemctl stop drosera

# Restart the node service
sudo systemctl restart drosera

```
---

## B. Register your Operator ([FAILED to register ? click here](#-problem-register-operator-transaction-fails))

```bash
drosera-operator register \
  --eth-rpc-url https://ethereum-hoodi-rpc.publicnode.com \
  --eth-private-key your_eth_private_key_here \
  --drosera-address 0x91cB447BaFc6e0EA0F4Fe056F5a9b1F14bb06e5D
```
replace your_eth_private_key_here with your wallet private key
## C. Firewall Configuration

```bash
sudo ufw allow ssh
sudo ufw allow 22
sudo ufw allow 31313/tcp
sudo ufw allow 31314/tcp
sudo ufw enable
```

<a name="get-cadet-role-discord"></a>
## 🔹GET CADET ROLE DISCORD (HOODI VERSION YEHOOOO) 🟥![cadet role](Asset/cadet%20role.png)

Assuming your Trap is deployed and your operator is running, let's set up a new Trap to submit your Discord username on-chain and unlock an exclusive Cadet role.

#### 1- Create a new DiscordNameTrap.sol file:

```bash
nano src/DiscordNameTrap.sol
```

#### 3- Paste the following contract code in it:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import {ITrap} from "drosera-contracts/interfaces/ITrap.sol";

interface IMockResponse {
    function isActive() external view returns (bool);
}

contract Trap is ITrap {
    // Updated response contract address
    address public constant RESPONSE_CONTRACT = 0x25E2CeF36020A736CF8a4D2cAdD2EBE3940F4608;
    string constant discordName = "YOURDISCORD"; // Replace with your Discord username

    function collect() external view returns (bytes memory) {
        bool active = IMockResponse(RESPONSE_CONTRACT).isActive();
        return abi.encode(active, discordName);
    }

    function shouldRespond(bytes[] calldata data) external pure returns (bool, bytes memory) {
        (bool active, string memory name) = abi.decode(data[0], (bool, string));
        if (!active || bytes(name).length == 0) {
            return (false, bytes(""));
        }

        return (true, abi.encode(name));
    }
}
```

Replace `YOURDISCORD` with your Discord username.  
To save: **Ctrl+X**, then **Y**, and **Enter**

---

### 2. Edit `drosera.toml` config

```bash
nano drosera.toml
```

Modify the values of the specified variables as follows:

```toml
ethereum_rpc = "https://ethereum-hoodi-rpc.publicnode.com"
drosera_rpc = "https://relay.hoodi.drosera.io"
eth_chain_id = 560048
drosera_address = "0x91cB447BaFc6e0EA0F4Fe056F5a9b1F14bb06e5D"

[traps]

[traps.mytrap]
path = "out/DiscordNameTrap.sol/Trap.json"
response_contract = "0x25E2CeF36020A736CF8a4D2cAdD2EBE3940F4608"
response_function = "respondWithDiscordName(string)"
cooldown_period_blocks = 33
min_number_of_operators = 1
max_number_of_operators = 2
block_sample_size = 10
private_trap = true
whitelist = ["YOUR_OPERATOR_ADDRESS"]
```

Your final `drosera.toml` file should match the example shown above.

---

### 3. Deploy Trap

#### 1- Compile your Trap's Contract:

```bash
forge build
```

If you get errors like: **command not found**, enter:

```bash
source /root/.bashrc
```

or reinstall dependencies from [here].

#### 2- Test the trap before deploying:

```bash
drosera dryrun
```

#### 3- Apply and Deploy the Trap:

```bash
DROSERA_PRIVATE_KEY=xxx drosera apply
```

Replace `xxx` with your EVM wallet private key (Ensure it's funded with Hoodi ETH).  
When prompted, write **ofc** and press Enter.

copy your trap address that  has been deployed and opt into the trap using this
## Opt-in your trap config ([use dashboard instead click here](#configure-through-dashboard-instead))

```bash
drosera-operator optin \
  --eth-rpc-url https://ethereum-hoodi-rpc.publicnode.com \
  --eth-private-key your_eth_private_key_here \
  --trap-config-address your_trap_address_here
```
replace your_trap_address_here with your generated trap address
---
---
### 4. Re-run Operator nodes

```bash
#Docker user
cd
cd ~/Drosera-Network
docker compose down
docker compose up -d

#SystemD user
sudo systemctl daemon-reload
sudo systemctl enable drosera
sudo systemctl restart drosera

```

### 5. View the List of submitted Discord Names

```bash
source /root/.bashrc
cast call 0x25E2CeF36020A736CF8a4D2cAdD2EBE3940F4608 "getDiscordNamesBatch(uint256,uint256)(string[])" 0 20000 --rpc-url https://ethereum-hoodi-rpc.publicnode.com | grep -E yourname

change the yourname to your discord username
```
---

## 🔹Useful Commands & Updates

```bash
# View operator logs
docker logs -f drosera-operator

# Stop operator
docker compose down

# Restart operator
docker compose restart

# Reapply trap config after changes
DROSERA_PRIVATE_KEY=your_eth_private_key_here drosera apply

# Update droseraup utility (run regularly)
curl -L https://app.drosera.io/install | bash
# or simply
droseraup
```

---

## Folder Structure Notes

Drosera Trap folder: `~/my-drosera-trap`\
Drosera Operator folder: `~/Drosera-Network`

---

## Contact and Support

Official docs: [https://dev.drosera.io/](https://dev.drosera.io/)\
Discord: [https://discord.com/invite/drosera](https://discord.com/invite/drosera)

# 🚀 Updating Drosera to v1.21.0 (LATEST)

This guide explains how to update your Drosera trapper/operator to the latest release (**v1.21.0**).

---

## 🔄 Update using `droseraup` (recommended)

If you already have the **droseraup** utility installed, you can update easily:

```bash
# Reload your bashrc (optional, to ensure PATH is updated)
source ~/.bashrc

# Navigate to your Drosera directory
cd ~/my-drosera-trap

# Run the update
droseraup
```

## 🔹💸 Cheapest VPS Hosting Deals under 3$ per month (Perfect for Testnets & Lightweight Nodes)

Looking for ultra-budget VPS options Here are two solid picks used by many in the blockchain and dev community:

**💰 Budget VPS (Crypto Accepted)**
1. **SpaceCore** - Contabo alternative, accepts crypto
   [CLICK HERE](https://billing.spacecore.pro/billmgr?from=63882)

2. **RackNerd** - From $10.96/year, 30+ crypto options  
   [CLICK HERE](https://my.racknerd.com/aff.php?aff=14994)

3. **HostVDS** - Russian servers, crypto payments  
   [CLICK HERE](https://hostvds.com/?affiliate_uuid=f3d517f2-6e58-4549-9ecd-d280fa8cea3c)

✅ Great for:
- Running lightweight validator or RPC nodes
- Experimenting with testnets
- Hosting low-traffic services

🌍 Suitable for developers on a tight budget or running long-term nodes with minimal cost.






