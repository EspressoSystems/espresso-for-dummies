# Deployment by example
The goal of this repo is to show minimal viable configs for running various types of espresso nodes.
Currently there are example docker compose files.

#
# Generate Mnemonics
#

Example to generate a new mnemonic: `cast wallet new-mnemonic`

Save the first as "ETHEREUM_MNEMONIC"
`endorse height distance biology hunt frame consider nothing hair pond enact impose`

Save the second as "ESPRESSO_NODE_KEY_MNEMONIC"
`length bag bounce coach exclude notable congress cactus pear kitchen swallow until` 

#
# Generate Espresso Keys from Mnemonic
#

`docker run --rm -it ghcr.io/espressosystems/espresso-network/espresso-node:main keygen --mnemonic "your espresso mnemonic goes here"`

Save the output of this somewhere helpful. We will need the BLS_VER_KEY for your metadata.json

#
# Pass env variables
#
```
ESPRESSO_NODE_KEY_MNEMONIC
ESPRESSO_NODE_L1_PROVIDER
ESPRESSO_NODE_L1_WS_PROVIDER
POSTGRES_PASSWORD
PUBLIC_IP
```

You will need to provide these either via the environment or via a .env file to docker-compose.

#
# Staking Contract
#
Metadata URI example [here](https://gist.githubusercontent.com/Ancient123/6c7e74b18ba3ddbe3478c06213efa7ff/raw/92478765168941228c92d1415962b749742f03e0/gistfile1.txt)

`p2p-addr` is your external accessible IP or domain name. This is used by cliquenet for nodes to communicate with each other. Domain names are encouraged to allow you to change ips without updating the contract.


This command will output a config file in a folder called `staking-config` in your current directory. This is help simplify running the next command.
```
docker run --rm -it -v ./staking-config/:/root/.config/espresso-staking-cli/ ghcr.io/espressosystems/espresso-network/staking-cli:main \
staking-cli init \
--network "decaf/mainnet" \
--mnemonic "your eth mnemonic goes here"
```

This command will register your validator to the contract.
```
docker run --rm -it -v ./staking-config/:/root/.config/espresso-staking-cli/ ghcr.io/espressosystems/espresso-network/staking-cli:main  \
staking-cli register-validator  \
--espresso-mnemonic "your espresso mnemonic goes here" \
--commission <Percentage number from 100 to 0.00> \
--p2p-addr "your external address" \
--metadata-uri "Link to your metadata.json"
```

#
# Starting the node
#
We start postgres first so it has time to intialize before the espresso node starts
```
docker compose up -d postgres
docker compose up -d
```
