# [Vega Upgrade]((https://github.com/cosmos/gaia/blob/main/docs/roadmap/cosmos-hub-roadmap-2.0.md#vega-upgrade-expected-q3-2021))

## Version

- `cosmoshub-4` current version: [Gaia v5.0.5](https://github.com/cosmos/gaia/releases/tag/v5.0.5)
- Upgrade test version: [Forked Gaia that includes liquidity module v1.4.1-rc1](https://github.com/b-harvest/gravity-dex/tree/tendermint/upgrade-sim-test-v6.0.1-rc1)

## Build

### Gaia

```bash
# Use git to clone the project and build it
git clone -b v5.0.5 https://github.com/cosmos/gaia.git
cd gaia
make build

# Name an executable file as "gaiad_old"
mv ./build/gaiad $GOBIN/gaiad_old
```

### Forked Gaia 

```bash
# Use git to clone the project and build it
git clone -b tendermint/upgrade-sim-test-v6.0.1-rc1 https://github.com/b-harvest/gravity-dex.git
cd gravity-dex
make install
```

## Option 1. Fast Track

The genesis file `genesis.json.tar.bz2` is already prepared for this option. It is obtained by proceeding from **Step 1~3** in **Option 2** below. Uncompress the genesis file and use it as the genesis data to simulate the upgrade test. You can directly go to **Step 4** to proceed if you choose to go with this option.

```bash
cd v6.0.1-rc1

# Uncompress the file
tar xvzf genesis.json.tar.bz2

# Verify the hash
cat genesis.json | shasum -a 256
> 

```

## Option 2. Step By Step Guide

`exported_genesis_with_height_7304500_sorted.json.tar.bz2` file inside `./data` folder in this repository is prepared. It is obtained by `$ gaia export --height 7304500` command from `cosmoshub-4` network and compressed the file by using `tar -cvjSf exported_genesis_with_height_7304500.json.tar.bz2 exported_genesis_with_height_7304500.json` command. Uncompress the exported file and use it as the genesis data to simulate the upgrade test.

```bash
cd v6.0.1-rc1

# Uncompress the file
tar xvzf ../data/exported_genesis_with_height_7304500_sorted.json.tar.bz2

# Verify the hash
cat exported_genesis_with_height_7304500_sorted.json | shasum -a 256
>
df10480978a9a211fbc2f82865659c1298f1ba97a3fee37512b299102fa1e9c3

# Copy the file and name it genesis.json
cp exported_genesis_with_height_7304500_sorted.json genesis.json
```

### (Option 2) Step 1. Substitute validator keys and accounts

In this step, we will swap 2 validators with two new accounts in the genesis file and we will also modify some parameters to improve test efficiency. The following files that contain validators' consensus keys and mnemonics for the two accounts are already prepared. 

- `priv_validator_key_val1.json`: validator1's consensus key

- `priv_validator_key_val2.json`: validator2's consensus key

- `guard cream sadness conduct invite crumble clock pudding hole grit liar hotel maid produce squeeze return argue turtle know drive eight casino maze host`: mnemonics for validator1

- `friend excite rough reopen cover wheel spoon convince island path clean monkey play snow number walnut pull lock shoot hurry dream divide concert discover`: mnemonics for validator2

- `render hire dirt bulk huge goat jungle number wear method check during menu goat accident scan noise nerve below target resource digital column flee`: `user1` account that is used for sending governance proposal to the network

- `junk appear guide guess bar reject vendor illegal script sting shock afraid detect ginger other theory relief dress develop core pull across hen float`: `user2` account that is used to test command-line interfaces (CLIs) for the liquidity module

```bash
# Change chain_id
sed -i '' 's%"chain_id": "cosmoshub-4",%"chain_id": "cosmoshub-4-upgrade-testnet-2002",%g' genesis.json

# Substitute "Certus One" with validator1
#
# cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM= --> qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU=
# B00A6323737F321EB0B8D59C6FD497A14B60938A --> D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA
# cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6 --> cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve
# 
sed -i '' 's%cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM=%qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU=%g' genesis.json
sed -i '' 's%B00A6323737F321EB0B8D59C6FD497A14B60938A%D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA%g' genesis.json
sed -i '' 's%cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6%cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve%g' genesis.json

# Substitute "Binance Staking" with validator2
#
# W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o= --> oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0=
# 83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0 --> 7CB07B94FD743E2A8520C2B50DA4B03740643BF5
# cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3 --> cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp
#
sed -i '' 's%W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o=%oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0=%g' genesis.json
sed -i '' 's%83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0%7CB07B94FD743E2A8520C2B50DA4B03740643BF5%g' genesis.json
sed -i '' 's%cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3%cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp%g' genesis.json

# Substitute user1 account
#
# cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl --> cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6
# Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8 --> AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i
#
sed -i '' 's%cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl%cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6%g' genesis.json
sed -i '' 's%Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8%AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i%g' genesis.json

# Substitute user2 account
#
# cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx --> cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
# A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g --> ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7
#
sed -i '' 's%cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx%cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9%g' genesis.json
sed -i '' 's%A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g%ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7%g' genesis.json
```

### (Option 2) Step 2. Change voting power and staking delegation to over 67%

```bash
# Fix delegation amount to over 67% to start the chain 
sed -i '' 's%"25390741.000000000000000000"%"6000000025390741.000000000000000000"%g' genesis.json

# Fix power of the validator
#
# Binance Staking validator's "delegator_shares" and "tokens"
#
sed -i '' 's%13991908901944%6013991908901944%g' genesis.json
sed -i '' 's%"power": "13991908"%"power": "6013991908"%g' genesis.json

# Fix last_total_power
sed -i '' 's%"194093279"%"6194093279"%g' genesis.json

# Fix total supply of 'uatom'
sed -i '' 's%277549930240869%1006277549930240869%g' genesis.json

# Fix balance of bonded_tokens_pool module account (cosmos1fl48vsnmsdzcv85q5d2q4z5ajdha8yu34mf0eh)
sed -i '' 's%194093338099942%6194093338099942%g' genesis.json

# Fix the balance of a account to use as a faucet on testnet
#
# Substitute user2 account: cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx --> cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
#
sed -i '' 's%"amount": "72177323"%"amount": "1000000000072177323"%g' genesis.json
```

### (Option 2) Step 3. Modify governance parameters 

In this step, we will modify governance parameters to improve test efficiency. The following `gov` parameters are going to be modified:

- minimum deposit amount
- quorum
- threshold
- voting_period

```bash
sed -i '' 's%"amount": "64000000",%"amount": "1",%g' genesis.json
sed -i '' 's%"quorum": "0.400000000000000000",%"quorum": "0.000000000000000001",%g' genesis.json
sed -i '' 's%"threshold": "0.500000000000000000",%"threshold": "0.000000000000000001",%g' genesis.json
sed -i '' 's%"voting_period": "1209600s"%"voting_period": "60s"%g' genesis.json
```

### Step 4. Initialize chain

In this step, we will use `gaiad_old` binary to initialize validators's and node's configuration files.

```bash
export EXPORTED_GENESIS=genesis.json
export BINARY=gaiad_old
export CHAIN_ID=cosmoshub-4-upgrade-testnet-2002
export CHAIN_DIR=./data

export VAL_1_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val1
export VAL_1_MNEMONIC="guard cream sadness conduct invite crumble clock pudding hole grit liar hotel maid produce squeeze return argue turtle know drive eight casino maze host"
export VAL_1_KEY_NAME="val1"
export VAL_1_MONIKER="val1"
export VAL_2_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
export VAL_2_MNEMONIC="friend excite rough reopen cover wheel spoon convince island path clean monkey play snow number walnut pull lock shoot hurry dream divide concert discover"
export VAL_2_KEY_NAME="val2"
export VAL_2_MONIKER="val2"

# Validator 1
$BINARY init test --home $VAL_1_CHAIN_DIR --chain-id=$CHAIN_ID 
echo $VAL_1_MNEMONIC | $BINARY --home $VAL_1_CHAIN_DIR keys add $VAL_1_KEY_NAME --recover --keyring-backend=test 

# Validator 2
$BINARY --home $VAL_2_CHAIN_DIR init test --chain-id=$CHAIN_ID
echo $VAL_2_MNEMONIC | $BINARY --home $VAL_2_CHAIN_DIR keys add $VAL_2_KEY_NAME --recover --keyring-backend=test 

# Account 1
export USER_1_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
export USER_1_MNEMONIC="render hire dirt bulk huge goat jungle number wear method check during menu goat accident scan noise nerve below target resource digital column flee"
export USER_1_KEY_NAME="user1"

# Account 2
export USER_2_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
export USER_2_MNEMONIC="junk appear guide guess bar reject vendor illegal script sting shock afraid detect ginger other theory relief dress develop core pull across hen float"
export USER_2_KEY_NAME="user2"

echo $USER_1_MNEMONIC | $BINARY --home $USER_1_CHAIN_DIR keys add $USER_1_KEY_NAME --recover --keyring-backend=test
echo $USER_2_MNEMONIC | $BINARY --home $USER_2_CHAIN_DIR keys add $USER_2_KEY_NAME --recover --keyring-backend=test

# 
# - name: user1
#   type: local
#   address: cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6
#   pubkey: cosmospub1addwnpepqtpngaangvnl5pvlaltfreq28djqskzd6g6pw89pztj2cg44uyl7y6xgev8
#   mnemonic: ""
#   threshold: 0
#   pubkeys: []
#
# - name: user2
#   type: local
#   address: cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
#   pubkey: cosmospub1addwnpepq2gvu5e8m34xpe5aky8wemsc9fefqs4y8h07s24cklr8lwrewf2lklysaen
#   mnemonic: ""
#   threshold: 0
#   pubkeys: []
```

### Step 5. Configuration

In this step, we will copy the genesis file and the validator consensus keys to the designated directories. We will also configure settings in both `config.toml` and `app.toml`.

```bash
# Copy genesis file to the designated directories and copy validator consensus key files
cp genesis.json $VAL_1_CHAIN_DIR/config/genesis.json
cp genesis.json $VAL_2_CHAIN_DIR/config/genesis.json
cp ../data/priv_validator_key_val1.json $VAL_1_CHAIN_DIR/config/priv_validator_key.json
cp ../data/priv_validator_key_val2.json $VAL_2_CHAIN_DIR/config/priv_validator_key.json

# Configure parameters in config.toml and app.toml files
export VAL_1_P2P_PORT=26656
export VAL_1_NODE_ID=$($BINARY tendermint --home $VAL_1_CHAIN_DIR show-node-id)
export VAL_2_P2P_PORT=36656
export VAL_2_RPC_PORT=36657
export VAL_2_API_PORT=1327
export VAL_2_GRPC_PORT=9080
export VAL_2_GRPC_WEB_SERVER_PORT=9081
export VAL_2_ROSETTA_API_PORT=8081
export VAL_2_PPROF_PORT=6061
export VAL_2_NODE_ID=$($BINARY tendermint --home $VAL_2_CHAIN_DIR show-node-id)

sed -i '' 's/enable = true/enable = false/g' $VAL_1_CHAIN_DIR/config/app.toml # disable all for val1 to prevent from colluding ports
sed -i '' 's#"tcp://127.0.0.1:26657"#"tcp://0.0.0.0:'"$VAL_2_RPC_PORT"'"#g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's#"tcp://0.0.0.0:26656"#"tcp://0.0.0.0:'"$VAL_2_P2P_PORT"'"#g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's#"tcp://0.0.0.0:1317"#"tcp://0.0.0.0:'"$VAL_2_API_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's#"0.0.0.0:9090"#"0.0.0.0:'"$VAL_2_GRPC_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's#"0.0.0.0:9091"#"0.0.0.0:'"$VAL_2_GRPC_WEB_SERVER_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's#":8080"#":'"$VAL_2_ROSETTA_API_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's/enable = false/enable = true/g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's/swagger = false/swagger = true/g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's/minimum-gas-prices = ""/minimum-gas-prices = "0stake"/g' $VAL_1_CHAIN_DIR/config/app.toml
sed -i '' 's/minimum-gas-prices = ""/minimum-gas-prices = "0stake"/g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's/persistent_peers = ""/persistent_peers = "'$VAL_2_NODE_ID'@'localhost':'$VAL_2_P2P_PORT'"/g' $VAL_1_CHAIN_DIR/config/config.toml
sed -i '' 's/persistent_peers = ""/persistent_peers = "'$VAL_1_NODE_ID'@'localhost':'$VAL_1_P2P_PORT'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's/unconditional_peer_ids = ""/unconditional_peer_ids = "'$VAL_1_NODE_ID'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's/unconditional_peer_ids = ""/unconditional_peer_ids = "'$VAL_2_NODE_ID'"/g' $VAL_1_CHAIN_DIR/config/config.toml
sed -i '' 's/pprof_laddr = "localhost:6060"/pprof_laddr = "localhost:'$VAL_2_PPROF_PORT'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's/addr_book_strict = true/addr_book_strict = false/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's/addr_book_strict = true/addr_book_strict = false/g' $VAL_1_CHAIN_DIR/config/config.toml
```

### Step 6.

### Step 7.

### Step 8.

