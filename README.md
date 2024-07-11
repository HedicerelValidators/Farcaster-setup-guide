# Farcaster-setup-guide
Our Farcaster setup guide
Installation
We recommend running Hubble on an always-on server that has Docker installed.

Requirements
Hubble can be set up in less than 30 minutes. You'll need a machine that has:
```
- **16 GB of RAM**
- **4 CPU cores or vCPUs**
- **40 GB of free storage**
- **A public IP address with ports 2281 - 2283 exposed**
- **RPC endpoints for Ethereum and Optimism Mainnet.** (use Alchemy, Infura, or QuickNode)
```
See tutorials for instructions on how to set up cloud providers to run Hubble.

Install via Script
The install script is the simplest way to set up Hubble.

```
curl -sSL https://download.thehubble.xyz/bootstrap.sh | bash
```
If you're using macOS, you'll need to have docker installed and running.

Hubble will be installed into ~/hubble and will be run via Docker in the background, along with Grafana and Prometheus for monitoring. If you have trouble with the script, try installing via docker.

Upgrading Hubble
The Hubble script creates a crontab entry and will automatically upgrade the hub every week. To upgrade manually, run:

bash
cd ~/hubble && ./hubble.sh upgrade
Install via Docker
Hubble can also be set up by running the docker image directly. To do this:

Check out the hub-monorepo locally.
From the root of this folder navigate to apps/hubble
Generate your identity key pair with docker compose.
bash
docker compose run hubble yarn identity create
Create a .env file in apps/hubble with your Ethereum RPC endpoints:
bash
# Set this to your L1 Mainnet ETH RPC URL
ETH_MAINNET_RPC_URL=your-ETH-mainnet-RPC-URL

# Set this to your L2 Optimism Mainnet RPC URL
OPTIMISM_L2_RPC_URL=your-L2-optimism-RPC-URL

# Set this to your Farcaster FID
HUB_OPERATOR_FID=your-fid
Follow the instructions to set connect to a network.

Start Hubble with docker compose in detached mode:

bash
docker compose up hubble -d
Docker compose will start a Hubble container that exposes ports for networking and writes data to .hub and .rocks directories. Hubble will now sync with the contracts and other hubble instances to download all messages on the network.

To view the status of the sync and hubble, follow the logs
bash
docker compose logs -f hubble
Follow the instructions in the monitoring instructions to set up Grafana and view your Hub's status in real-time.
Upgrading Hubble
Navigate to apps/hubble in hub-monorepo and run:

bash
git checkout main && git pull
docker compose stop && docker compose up -d --force-recreate --pull always
Installing from source
Hubble can also be built and run directly from source without Docker.

Installing Dependencies
First, ensure that the following are installed globally on your machine:

Node.js 18.7+
Yarn
Foundry
Rust
Build
git clone https://github.com/farcasterxyz/hub-monorepo.git to clone the repo
cd hub-monorepo to enter the directory
yarn install to install dependencies
yarn build to build Hubble and its dependencies
yarn test to ensure that the test suite runs correctly
Running Hubble
To run the Hubble commands, go to the Hubble app (cd apps/hubble) and run the yarn commands.

yarn identity create to create a ID
Follow the instructions to set connect to a network
yarn start --eth-mainnet-rpc-url <your ETH-mainnet-RPC-URL> --l2-rpc-url <your Optimism-L2-RPC-URL> --hub-operator-fid <your FID>
Upgrading Hubble
To upgrade hubble, find the latest release tag and checkout that version and build.

bash
git fetch --tags # to fetch the latest tags
git checkout @farcaster/hubble@latest # Or use a specific version.
yarn install && yarn build # in the root folder
Running commands
Check the logs to ensure your hub is running successfully:

bash
docker compose logs -f hubble
Open up a shell inside the hubble container by running:

bash
docker compose exec hubble /bin/sh
Troubleshooting
If upgrading from a non-docker deployment, make sure .hub and .rocks directories are writable for all users.

If upgrading from 1.3.3 or below, please set ETH_MAINNET_RPC_URL=your-ETH-mainnet-RPC-URL (if using docker) or provide the --eth-mainnet-rpc-url flag (if not using docker)

If you're changing your Hub from one network to another, you'll need to delete your database contents:

bash
docker compose stop && docker compose run --rm hubble yarn dbreset
To pull the image yourself, you can run:
bash
# Get the latest image
docker pull farcasterxyz/hubble:latest

# Get a specific release (v1.4.0)
docker pull farcasterxyz/hubble@v1.4.0
To set the Hub operator FID
If you are running via docker or the script, please set this in your .env file: HUB_OPERATOR_FID=your-fid
If you are running via source yarn start --hub-operator-fid <your-fid>
