## Create wallet or recover one

### Option 1 - generate new wallet

```
celestia-appd config keyring-backend test
```

```
KEY_NAME=validator
celestia-appd keys add $KEY_NAME
```

Sample output

```
- address: celestia1q4qtpgw9m6l4ezj6v0pc7l79eq7fpd83c9hnjq
  name: validator
  xxxxx
  xxxxx
```


Copy the wallet seed phrase and keep in secure place

Another IMPORTANT but optional action is backup your Validator_priv_key:

```
tar -czvf validator_key.tar.gz .celestia-app/config/*_key.json
gpg -o validator_key.tar.gz.gpg -ca validator_key.tar.gz
rm validator_key.tar.gz
``` 

### Option 2 - recover existing wallet

USE THIS WALLET since you already submitted a wallet information during blockspacerace and your wallet is already in genesis.json

```
KEY_NAME=validator-mainnet
celestia-appd keys add $KEY_NAME --recover
```

Next type BIP mnemonic of the key to recover it.

## Check list of commands for wallet

```
celestia-appd keys
```

```Available Commands:
  add         Add an encrypted private key (either newly generated or recovered), encrypt it, and save to <name> file
  delete      Delete the given keys
  export      Export private keys
  import      Import private keys into the local keybase
  list        List all keys
  migrate     Migrate keys from amino to proto serialization format
  mnemonic    Compute the bip39 mnemonic for some input entropy
  parse       Parse address from hex to bech32 and vice versa
  rename      Rename an existing key
  show        Retrieve key information by name or address

Flags:
  -h, --help                     help for keys
      --home string              The application home directory (default "/home/spidey/.celestia-app")
      --keyring-backend string   Select keyring's backend (os|file|test) (default "test")
      --keyring-dir string       The client Keyring directory; if omitted, the default 'home' directory will be used
      --output string            Output format (text|json) (default "text")

Global Flags:
      --log-to-file string   Write logs directly to a file. If empty, logs are written to stderr
      --log_format string    The logging format (json|plain) (default "plain")
      --log_level string     The logging level (trace|debug|info|warn|error|fatal|panic) (default "info")
      --trace                print out full stack trace on errors

Use "celestia-appd keys [command] --help" for more information about a command.
```

## Delete a local wallet copy (AFTER BACKING UP SEED safely)

```
celestia-appd keys delete <KEY_NAME>
```

### Rename an exisitng wallet 

```
celestia-appd keys rename <OLD_NAME> <NEW_NAME>
```

### List all wallets

```
celestia-appd keys list
```


### Staking transaction

celestia-appd tx staking delegate \
celestiavaloper1st4h4ymu52hwtl3s0n298t6adj0vputx8jw8xt 58320000000utia \
--from=celestia1ezlgzq7s2qk8lzrqsdm7hnl77j93k6dnghwqpw --chain-id=celestia \
--fees=21000utia


## Adding identity to existing validator

```
VALIDATOR_WALLET=celestia1st4h4ymu52hwtl3s0n298t6adj0vputxzdv7sd

celestia-appd tx staking edit-validator --identity=$PGP_KEY_FROM_KEYBASE --chain-id celestia --from $VALIDATOR_WALLET --fees=21000utia
```

## Adding description to existing validator

```
celestia-appd tx staking edit-validator --details="Celestia Blockspace Race (Incentivized Testnet) Top 20 Ranking, Full Stack Developer, Expert and Collaborative Professional Node Operator and Tendermint Community Supporter at your service" --chain-id celestia --from $VALIDATOR_WALLET --fees=21000utia
```

## Adding website to existing validator

```
celestia-appd tx staking edit-validator --website "https://github.com/spidey-169" --chain-id celestia --from $VALIDATOR_WALLET --fees=21000utia
```


### Init Bridge Node

celestia bridge init --core.ip 192.168.0.102 --core.rpc.port 26657 --p2p.network celestia


### Backup Bridge keys 

tar -czvf bridge_node_key.tar.gz tar ~/.celestia-bridge-mainnet/keys/*
gpg -o bridge_node_key.tar.gz.gpg -ca bridge_node_key.tar.gz
rm bridge_node_key.tar.gz


### Systemd bridge

sudo tee <<EOF >/dev/null /etc/systemd/system/celestia-bridge.service
[Unit]
Description=celestia-bridge Cosmos daemon
After=network-online.target

[Service]
User=$USER
ExecStart=/home/celestia/celestia-node/build/celestia bridge start --core.ip 192.168.0.102 --core.rpc.port 26657 --p2p.network celestia --metrics.tls=true --metrics --metrics.endpoint otel.celestia.observer
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF

## Get node info

NODE_TYPE=bridge
AUTH_TOKEN=$(celestia $NODE_TYPE auth admin --p2p.network celestia)