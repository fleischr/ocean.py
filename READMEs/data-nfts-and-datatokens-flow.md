<!--
Copyright 2022 Ocean Protocol Foundation
SPDX-License-Identifier: Apache-2.0
-->

# Quickstart: Publish Data NFT & Datatoken

## 1. Setup

### Prerequisites

-   Linux/MacOS
-   [Docker](https://docs.docker.com/engine/install/), [Docker Compose](https://docs.docker.com/compose/install/), [allowing non-root users](https://www.thegeekdiary.com/run-docker-as-a-non-root-user/)
-   Python 3.8.5+

### Download barge and run services

Ocean `barge` runs ganache (local blockchain), Provider (data service), and Aquarius (metadata cache).

In a new console:

```console
# Grab repo
git clone https://github.com/oceanprotocol/barge
cd barge

# Clean up old containers (to be sure)
docker system prune -a --volumes

# Run barge: start Ganache, Provider, Aquarius; deploy contracts; update ~/.ocean
# (For speed, we turn off components we don't need for quickstarts, like IPFS)
./start_ocean.sh --no-ipfs --no-dashboard --skip-subgraph-deploy
```

### Install the library

In a new console:

```console
# Create your working directory
mkdir my_project
cd my_project

# Initialize virtual environment and activate it. Install artifacts.
python3 -m venv venv
source venv/bin/activate

# Avoid errors for the step that follows
pip3 install wheel

# Install Ocean library. Allow pre-releases to get the latest v4 version.
pip3 install --pre ocean-lib
```

### Set envvars

In the same console (or another one with venv activated):
```console
export TEST_PRIVATE_KEY1=0x8467415bb2ba7c91084d932276214b11a3dd9bdb2930fefa194b666dd8020b99
export TEST_PRIVATE_KEY2=0x1d751ded5a32226054cd2e71261039b65afb9ee1c746d055dd699b1150a5befc
```

### Setup in Python

In the same console, run Python console:
```console
python
```

In the Python console:
```python
# Create Ocean instance
from ocean_lib.example_config import ExampleConfig
from ocean_lib.ocean.ocean import Ocean
config = ExampleConfig.get_config("http://127.0.0.1:8545")
ocean = Ocean(config)

# Create Alice's wallet
import os
from ocean_lib.web3_internal.wallet import Wallet
alice_private_key = os.getenv('TEST_PRIVATE_KEY1')
alice_wallet = Wallet(ocean.web3, alice_private_key, config["BLOCK_CONFIRMATIONS"], config["TRANSACTION_TIMEOUT"])
```

## 2. Publish Data NFT & Datatoken

### 2.1 Create a data NFT

In the same Python console:
```python
data_nft = ocean.create_data_nft('NFT1', 'NFT1', alice_wallet)
print(f"Created data NFT. Its address is {data_nft.address}")
```

Congrats, you've created your first Ocean data NFT!

### 2.2 Create a datatoken from the data NFT

In the same Python console:
```python
# Create datatoken related to the above NFT.

datatoken = data_nft.create_datatoken("Datatoken 1", "DT1", from_wallet=alice_wallet)
print(f"Created datatoken. Its address is {datatoken.address}")
```

Congrats, you've created your first Ocean datatoken! 🐋

## Appendix. Tips & Tricks

You can combine creating a data NFT and datatoken into a single call: `ocean.create_nft_with_erc20()`.

To learn more about some of the objects you created, here are some examples.
```python
# config
print(f"config.network_url = {config['RPC_URL']}")
print(f"config.block_confirmations = {config['BLOCK_CONFIRMATIONS']}")
print(f"config.metadata_cache_uri = {config['METADATA_CACHE_URI']}")
print(f"config.provider_url = {config['PROVIDER_URL']}")

# wallet
print(f"alice_wallet.address = '{alice_wallet.address}'")

# data NFT
print(f"data NFT name: {data_nft.token_name()}")
print(f"data NFT symbol: {data_nft.symbol()}")

# datatoken
print(f"datatoken name: {datatoken.token_name()}")
print(f"datatoken symbol: {datatoken.symbol()}")
```
