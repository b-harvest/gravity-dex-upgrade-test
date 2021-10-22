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

In this step, we will swap 2 validators with two new accounts in the genesis file and we will also modify some parameters to improve test efficiency. The following files that contain validators' consensus keys and mnemonics for the two accounts are already prepared. Why do we need to swap 2 validators? See [this issue](https://github.com/cosmos/cosmos-sdk/issues/7505) for reference.

- `priv_validator_key_val1.json`: validator1's consensus key

- `priv_validator_key_val2.json`: validator2's consensus key

- `guard cream sadness conduct invite crumble clock pudding hole grit liar hotel maid produce squeeze return argue turtle know drive eight casino maze host`: mnemonics for validator1

- `friend excite rough reopen cover wheel spoon convince island path clean monkey play snow number walnut pull lock shoot hurry dream divide concert discover`: mnemonics for validator2

- `render hire dirt bulk huge goat jungle number wear method check during menu goat accident scan noise nerve below target resource digital column flee`: `user1` account that is used for sending governance proposal to the network

- `junk appear guide guess bar reject vendor illegal script sting shock afraid detect ginger other theory relief dress develop core pull across hen float`: `user2` account that is used to test command-line interfaces (CLIs) for the liquidity module

```bash
# Check OS for sed -i option value
export SED_I=""
if [[ "$OSTYPE" == "darwin"* ]]; then 
    export SED_I="''"
fi 

# Change chain_id
sed -i $SED_I 's%"chain_id": "cosmoshub-4",%"chain_id": "cosmoshub-4-upgrade-testnet-2002",%g' genesis.json

# Substitute "Certus One" with validator1
#
# cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM= --> qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU=
# B00A6323737F321EB0B8D59C6FD497A14B60938A --> D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA
# cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6 --> cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve
# 
sed -i $SED_I 's%cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM=%qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU=%g' genesis.json
sed -i $SED_I 's%B00A6323737F321EB0B8D59C6FD497A14B60938A%D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA%g' genesis.json
sed -i $SED_I 's%cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6%cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve%g' genesis.json

# Substitute "Binance Staking" with validator2
#
# W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o= --> oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0=
# 83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0 --> 7CB07B94FD743E2A8520C2B50DA4B03740643BF5
# cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3 --> cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp
#
sed -i $SED_I 's%W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o=%oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0=%g' genesis.json
sed -i $SED_I 's%83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0%7CB07B94FD743E2A8520C2B50DA4B03740643BF5%g' genesis.json
sed -i $SED_I 's%cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3%cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp%g' genesis.json

# Substitute user1 account
#
# cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl --> cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6
# Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8 --> AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i
#
sed -i $SED_I 's%cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl%cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6%g' genesis.json
sed -i $SED_I 's%Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8%AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i%g' genesis.json

# Substitute user2 account
#
# cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx --> cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
# A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g --> ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7
#
sed -i $SED_I 's%cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx%cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9%g' genesis.json
sed -i $SED_I 's%A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g%ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7%g' genesis.json
```

### (Option 2) Step 2. Change voting power and staking delegation to over 67%

```bash
# Check OS for sed -i option value
export SED_I=""
if [[ "$OSTYPE" == "darwin"* ]]; then 
    export SED_I="''"
fi 

# Fix delegation amount to over 67% to start the chain 
sed -i $SED_I 's%"25390741.000000000000000000"%"6000000025390741.000000000000000000"%g' genesis.json

# Fix power of the validator
#
# Binance Staking validator's "delegator_shares" and "tokens"
#
sed -i $SED_I 's%13991908901944%6013991908901944%g' genesis.json
sed -i $SED_I 's%"power": "13991908"%"power": "6013991908"%g' genesis.json

# Fix last_total_power
sed -i $SED_I 's%"194093279"%"6194093279"%g' genesis.json

# Fix total supply of 'uatom'
sed -i $SED_I 's%277549930240869%1006277549930240869%g' genesis.json

# Fix balance of bonded_tokens_pool module account (cosmos1fl48vsnmsdzcv85q5d2q4z5ajdha8yu34mf0eh)
sed -i $SED_I 's%194093338099942%6194093338099942%g' genesis.json

# Fix the balance of user2 account (cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9)
sed -i $SED_I 's%"amount": "72177323"%"amount": "1000000000072177323"%g' genesis.json
```

### (Option 2) Step 3. Modify governance parameters 

In this step, we will modify governance parameters to improve test efficiency. The following `gov` parameters are going to be modified:

- minimum deposit amount
- quorum
- threshold
- voting_period

```bash
# Check OS for sed -i option value
export SED_I=""
if [[ "$OSTYPE" == "darwin"* ]]; then 
    export SED_I="''"
fi 

sed -i $SED_I 's%"amount": "64000000",%"amount": "1",%g' genesis.json
sed -i $SED_I 's%"quorum": "0.400000000000000000",%"quorum": "0.000000000000000001",%g' genesis.json
sed -i $SED_I 's%"threshold": "0.500000000000000000",%"threshold": "0.000000000000000001",%g' genesis.json
sed -i $SED_I 's%"voting_period": "1209600s"%"voting_period": "60s"%g' genesis.json
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

# Check OS for sed -i option value
export SED_I=""
if [[ "$OSTYPE" == "darwin"* ]]; then 
    export SED_I="''"
fi

sed -i $SED_I 's/enable = true/enable = false/g' $VAL_1_CHAIN_DIR/config/app.toml # disable this to prevent from port collision
sed -i $SED_I 's/minimum-gas-prices = ""/minimum-gas-prices = "0stake"/g' $VAL_1_CHAIN_DIR/config/app.toml
sed -i $SED_I 's/persistent_peers = ""/persistent_peers = "'$VAL_2_NODE_ID'@'localhost':'$VAL_2_P2P_PORT'"/g' $VAL_1_CHAIN_DIR/config/config.toml
sed -i $SED_I 's/unconditional_peer_ids = ""/unconditional_peer_ids = "'$VAL_2_NODE_ID'"/g' $VAL_1_CHAIN_DIR/config/config.toml
sed -i $SED_I 's/addr_book_strict = true/addr_book_strict = false/g' $VAL_1_CHAIN_DIR/config/config.toml

sed -i $SED_I 's#"tcp://127.0.0.1:26657"#"tcp://0.0.0.0:'"$VAL_2_RPC_PORT"'"#g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i $SED_I 's#"tcp://0.0.0.0:26656"#"tcp://0.0.0.0:'"$VAL_2_P2P_PORT"'"#g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i $SED_I 's#"tcp://0.0.0.0:1317"#"tcp://0.0.0.0:'"$VAL_2_API_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i $SED_I 's#"0.0.0.0:9090"#"0.0.0.0:'"$VAL_2_GRPC_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i $SED_I 's#"0.0.0.0:9091"#"0.0.0.0:'"$VAL_2_GRPC_WEB_SERVER_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i $SED_I 's#":8080"#":'"$VAL_2_ROSETTA_API_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i $SED_I 's/enable = false/enable = true/g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i $SED_I 's/swagger = false/swagger = true/g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i $SED_I 's/minimum-gas-prices = ""/minimum-gas-prices = "0stake"/g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i $SED_I 's/persistent_peers = ""/persistent_peers = "'$VAL_1_NODE_ID'@'localhost':'$VAL_1_P2P_PORT'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i $SED_I 's/unconditional_peer_ids = ""/unconditional_peer_ids = "'$VAL_1_NODE_ID'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i $SED_I 's/pprof_laddr = "localhost:6060"/pprof_laddr = "localhost:'$VAL_2_PPROF_PORT'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i $SED_I 's/addr_book_strict = true/addr_book_strict = false/g' $VAL_2_CHAIN_DIR/config/config.toml
```

### Step 6. Start the chain

In this step, we will open two terminals and start two validator nodes.  It requires 2+ validators to start the network that is exported from live chain. Read [this issue](https://github.com/cosmos/cosmos-sdk/issues/7505) for more information about it. You can start the chain with `--x-crisis-skip-assert-invariants` flag to skip invariant checks since it takes quite a long time. It takes more than an hour to start the chain. Of course, it depends on the resources of your local machine.

```bash
#
# Terminal 1 (note that you should run the below command from directory where data folder is located)
#
export BINARY=gaiad_old
export CHAIN_ID=cosmoshub-4-upgrade-testnet-2002
export HOME1=./data/$CHAIN_ID/val1
$BINARY start --home $HOME1

#
# Terminal 2 (note that you should run the below command from directory where data folder is located)
#
export BINARY=gaiad_old
export CHAIN_ID=cosmoshub-4-upgrade-testnet-2002
export HOME2=./data/$CHAIN_ID/val2
$BINARY start --home $HOME2 

#
# (With --x-crisis-skip-assert-invariants flag) Terminal 1 
#
# skip invariant checks to start the chain
export BINARY=gaiad_old
export CHAIN_ID=cosmoshub-4-upgrade-testnet-2002
export HOME1=./data/$CHAIN_ID/val1
$BINARY start --home $HOME1 --x-crisis-skip-assert-invariants

#
# (With --x-crisis-skip-assert-invariants flag) Terminal 2
#
# skip invariant checks to start the chain
export BINARY=gaiad_old
export CHAIN_ID=cosmoshub-4-upgrade-testnet-2002
export HOME2=./data/$CHAIN_ID/val2
$BINARY start --home $HOME2 --x-crisis-skip-assert-invariants

#
# Reset the blockchain database and its state
#
$BINARY unsafe-reset-all --home $HOME1
$BINARY unsafe-reset-all --home $HOME2
```

### Step 7.  Send an upgrade proposal to the network

In this step, we will open up `Terminal 3` to send an upgrade governance proposal along with a deposit.

```bash
#
# Terminal 3 (note that you should run the below command from directory where data folder is located)
#
export BINARY=gaiad_old
export CHAIN_ID=cosmoshub-4-upgrade-testnet-2002

# **CAUTION**
# name: it is case sentitive. it should match with upgrade name; otherwise it won't start
# upgrade-height: make sure it has enough time for the proposal to pass
#
$BINARY tx gov submit-proposal software-upgrade Vega \
--title Vega \
--deposit 1000uatom \
--upgrade-height 7304550 \
--upgrade-info "tendermint/upgrade-sim-test-v6.0.1-rc1-bee608176f5fb513eb24a202090c89ff641e2583" \
--description "Vega Upgrade" \
--gas 400000 \
--from user1 \
--keyring-backend test \
--chain-id $CHAIN_ID \
--home data/$CHAIN_ID/val2 \
--node tcp://localhost:36657 \
--yes -b block \
-o json | jq

# Query the proposal to check the status. 
# the status should be PROPOSAL_STATUS_VOTING_PERIOD.
$BINARY query gov proposal 54 \
--chain-id $CHAIN_ID \
--home data/$CHAIN_ID/val2 \
--node tcp://localhost:36657 \
-o json | jq

# Vote yes for the proposal
# Note that voting period, quorum, and threshold params are modified 
$BINARY tx gov vote 54 yes \
--from user1 \
--keyring-backend test \
--chain-id $CHAIN_ID \
--home data/$CHAIN_ID/val2 \
--node tcp://localhost:36657 \
--yes -b block \
-o json | jq

# Wait for a while for the proposal to pass

# Query the proposal to check if it is passed
# The status should be PROPOSAL_STATUS_PASSED
$BINARY query gov proposal 54 \
--chain-id $CHAIN_ID \
--home data/$CHAIN_ID/val2 \
--node tcp://localhost:36657 \
-o json | jq
```

Result of the proposal

```json
{
  "proposal_id": "54",
  "content": {
    "@type": "/cosmos.upgrade.v1beta1.SoftwareUpgradeProposal",
    "title": "Vega",
    "description": "Vega Upgrade",
    "plan": {
      "name": "Vega",
      "time": "0001-01-01T00:00:00Z",
      "height": "7304550",
      "info": "tendermint/upgrade-sim-test-v6.0.1-rc1-bee608176f5fb513eb24a202090c89ff641e2583",
      "upgraded_client_state": null
    }
  },
  "status": "PROPOSAL_STATUS_PASSED",
  "final_tally_result": {
    "yes": "75958321",
    "abstain": "0",
    "no": "0",
    "no_with_veto": "0"
  },
  "submit_time": "2021-10-20T00:16:43.961420Z",
  "deposit_end_time": "2021-11-03T00:16:43.961420Z",
  "total_deposit": [
    {
      "denom": "uatom",
      "amount": "1000"
    }
  ],
  "voting_start_time": "2021-10-20T00:16:43.961420Z",
  "voting_end_time": "2021-10-20T00:17:43.961420Z"
}
```

### Step 8. Restart node using `gaiad`

The proposal has successfully passed and the chain is halted for upgrade when `upgrade-height` is reached.

> ERR UPGRADE "Vega" NEEDED at height: 7304550: tendermint/upgrade-sim-test-v6.0.1-rc1-bee608176f5fb513eb24a202090c89ff641e2583
> ERR CONSENSUS FAILURE!!! err="UPGRADE \"Vega\" NEEDED at height: 7304550: tendermint/upgrade-sim-test-v6.0.1-rc1-bee608176f5fb513eb24a202090c89ff641e2583" module=consensus stack="goroutine 59 [running]:\nruntime/debug.Stack()\n\truntime/debug/stack.go:24 +0x65\ngithub.com/tendermint/tendermint/consensus.(*State)...

In this step, we will use `gaiad` that includes the liquidity module `v1.4.1-rc1`.

```bash
#
# Terminal 1
#
gaiad start --home $HOME1 --x-crisis-skip-assert-invariants

#
# Terminal 2
#
gaiad start --home $HOME2 --x-crisis-skip-assert-invariants
```

## Test Gravity DEX

### Query params

Let's first query the values are set as liquidity parameter.

```bash
gaiad query liquidity params \
--node tcp://localhost:36657 \
--output json | jq
```

```json
{
  "pool_types": [
    {
      "id": 1,
      "name": "StandardLiquidityPool",
      "min_reserve_coin_num": 2,
      "max_reserve_coin_num": 2,
      "description": "Standard liquidity pool with pool price function X/Y, ESPM constraint, and two kinds of reserve coins"
    }
  ],
  "min_init_deposit_amount": "1000000",
  "init_pool_coin_mint_amount": "1000000",
  "max_reserve_coin_amount": "0",
  "pool_creation_fee": [
    {
      "denom": "uatom",
      "amount": "40000000"
    }
  ],
  "swap_fee_rate": "0.003000000000000000",
  "withdraw_fee_rate": "0.000000000000000000",
  "max_order_amount_ratio": "0.100000000000000000",
  "unit_batch_height": 1,
  "circuit_breaker_enabled": false
}
```

### Change `InitPoolCoinMintAmount` parameter 

Now, let's change `init_pool_coin_mint_amount` through parameter change governance proposal

```json
{
  "title": "Liquidity Param Change",
  "description": "Update Initial PoolCoin Mint Amount",
  "changes": [
    {
      "subspace": "liquidity",
      "key": "InitPoolCoinMintAmount",
      "value": "1000000000000"
    }
  ],
  "deposit": "10000uatom"
}
```

```bash
# Submit a parameter changes proposal to create a budget plan
gaiad tx gov submit-proposal param-change proposal.json \
--from user1 \
--keyring-backend test \
--chain-id $CHAIN_ID \
--home data/$CHAIN_ID/val2 \
--node tcp://localhost:36657 \
--yes -b block \
-o json | jq

# Query the proposal to check the status. 
# The status should be PROPOSAL_STATUS_VOTING_PERIOD.
$BINARY query gov proposal 55 \
--chain-id $CHAIN_ID \
--home data/$CHAIN_ID/val2 \
--node tcp://localhost:36657 \
-o json | jq

# Vote yes for the proposal
# Note that voting period, quorum, and threshold params are modified 
$BINARY tx gov vote 55 yes \
--from user1 \
--keyring-backend test \
--chain-id $CHAIN_ID \
--home data/$CHAIN_ID/val2 \
--node tcp://localhost:36657 \
--yes -b block \
-o json | jq

# Wait for a while for the proposal to pass

# Query the proposal to check if it is passed
# The status should be PROPOSAL_STATUS_PASSED
$BINARY query gov proposal 55 \
--chain-id $CHAIN_ID \
--home data/$CHAIN_ID/val2 \
--node tcp://localhost:36657 \
-o json | jq

# Query the values are set as liquidity parameter to see if it is changed
gaiad query liquidity params \
--node tcp://localhost:36657 \
--output json | jq
```

### Liquidity Command-line Interfaces

```bash
export CHAIN_ID=cosmoshub-4-upgrade-testnet-2002
export HOME1=./data/$CHAIN_ID/val1
export HOME2=./data/$CHAIN_ID/val2

# Query user2 balance
# http://localhost:1327/cosmos/bank/v1beta1/balances/cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
gaiad q bank balances cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9 -o json | jq

# Create pool 
gaiad tx liquidity create-pool 1 1000000000uatom,9000000ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C \
--home $HOME2 \
--chain-id $CHAIN_ID \
--from user2 \
--keyring-backend test \
--gas 300000 \
--node tcp://localhost:36657 \
--yes -b block -o json | jq

# Query pool with pool id 10
gaiad q liquidity pool 10 --node tcp://localhost:36657 -o json | jq

# Query reserve pool balances
gaiad q bank balances cosmos1qf9sqpexwyh3py786f8vx2w7fx8thpd5wz79sf -o json | jq

# Withdraw some pool coins
# Note that withdrawing a small amount of pool coin will fail
gaiad tx liquidity withdraw 10 100000000pool024B000726712F1093C7D24EC329DE498EBB85B4B2D37C59D4F37BC542020151 \
--home $HOME2 \
--chain-id $CHAIN_ID \
--from user2 \
--keyring-backend test \
--node tcp://localhost:36657 \
--yes -b block -o json | jq

# Deposit 
gaiad tx liquidity deposit 10 100000000uatom,900000ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C \
--home $HOME2 \
--chain-id $CHAIN_ID \
--from user2 \
--keyring-backend test \
--node tcp://localhost:36657 \
--yes -b block -o json | jq

# Swap 
gaiad tx liquidity swap 10 1 10000000uatom ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C 0.0089 0.003 \
--home $HOME2 \
--chain-id $CHAIN_ID \
--node tcp://localhost:36657 \
--from user2 \
--keyring-backend test \
--yes -b block -o json | jq

#
# Debugging if the transaction is successfully deposited, withdrew, or swapped requires 
# you to look up the block height result that the trasaction is included.
# Reference this link to find out the block height result
# http://localhost:36657/block_results?height={height}
# 
# Copy and paste the result into this site that conveniently decodes keys and values from base64 format. 
# https://hallazzang.github.io/tendermint-toolkit/
#
```

## Modified parameters in `genesis.json` file

```bash
diff exported_genesis_with_height_7304500_sorted.json genesis.json -u
```

```diff
--- exported_genesis_with_height_7304500_sorted.json    2021-08-23 21:52:43.000000000 +0900
+++ genesis.json        2021-10-19 21:32:41.000000000 +0900
@@ -1,6 +1,6 @@
 {
   "genesis_time": "2019-12-11T16:11:34Z",
-  "chain_id": "cosmoshub-4",
+  "chain_id": "cosmoshub-4-upgrade-testnet-2002",
   "initial_height": "7304501",
   "consensus_params": {
     "block": {
@@ -22,10 +22,10 @@
   },
   "validators": [
     {
-      "address": "B00A6323737F321EB0B8D59C6FD497A14B60938A",
+      "address": "D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA",
       "pub_key": {
         "type": "tendermint/PubKeyEd25519",
-        "value": "cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM="
+        "value": "qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU="
       },
       "power": "2589549",
       "name": "Certus One"
@@ -724,12 +724,12 @@
       "name": "DragonStake"
     },
     {
-      "address": "83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0",
+      "address": "7CB07B94FD743E2A8520C2B50DA4B03740643BF5",
       "pub_key": {
         "type": "tendermint/PubKeyEd25519",
-        "value": "W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o="
+        "value": "oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0="
       },
-      "power": "13991908",
+      "power": "6013991908",
       "name": "Binance Staking"
     },
     {
@@ -225551,10 +225551,10 @@
         {
           "@type": "/cosmos.auth.v1beta1.BaseAccount",
           "account_number": "27720",
-          "address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "pub_key": {
             "@type": "/cosmos.crypto.secp256k1.PubKey",
-            "key": "A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g"
+            "key": "ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7"
           },
           "sequence": "221"
         },
@@ -1411127,10 +1411127,10 @@
         {
           "@type": "/cosmos.auth.v1beta1.BaseAccount",
           "account_number": "10668",
-          "address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "pub_key": {
             "@type": "/cosmos.crypto.secp256k1.PubKey",
-            "key": "Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8"
+            "key": "AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i"
           },
           "sequence": "22"
         },
@@ -3459808,7 +3459808,7 @@
           ]
         },
         {
-          "address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "coins": [
             {
               "amount": "10000000",
@@ -3459827,7 +3459827,7 @@
               "denom": "ibc/EC4B5D87917DD5668D9998146F82D70FDF86652DB333D04CE29D1EB18E296AF5"
             },
             {
-              "amount": "72177323",
+              "amount": "1000000000072177323",
               "denom": "uatom"
             }
           ]
@@ -3951624,7 +3951624,7 @@
           "address": "cosmos1fl48vsnmsdzcv85q5d2q4z5ajdha8yu34mf0eh",
           "coins": [
             {
-              "amount": "194093338099942",
+              "amount": "6194093338099942",
               "denom": "uatom"
             }
           ]
@@ -4177422,7 +4177422,7 @@
           "coins": []
         },
         {
-          "address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "coins": [
             {
               "amount": "1450862",
@@ -5340100,7 +5340100,7 @@
           "denom": "poolF2805980C54E1474BDCCF70EF5FE881F3B8EFCF8BA3198765C01D91904521788"
         },
         {
-          "amount": "277549930240869",
+          "amount": "1006277549930240869",
           "denom": "uatom"
         }
       ]
@@ -5440529,7 +5440529,7 @@
           "validator_address": "cosmosvaloper1pjmngrwcsatsuyy8m3qrunaun67sr9x7z5r2qs"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "7248467",
             "previous_period": "745",
@@ -5645036,7 +5645036,7 @@
           "validator_address": "cosmosvaloper1tflk30mq5vgqjdly92kkhhq3raev2hnz6eete3"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "7248467",
             "previous_period": "465175",
@@ -6050612,7 +6050612,7 @@
           "validator_address": "cosmosvaloper1sjllsnramtg3ewxqwwrwjxfgc4n4ef9u2lcnj0"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "7274551",
             "previous_period": "82115",
@@ -6061790,7 +6061790,7 @@
           "validator_address": "cosmosvaloper1sjllsnramtg3ewxqwwrwjxfgc4n4ef9u2lcnj0"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "starting_info": {
             "height": "7257832",
             "previous_period": "81861",
@@ -6089357,7 +6089357,7 @@
           "validator_address": "cosmosvaloper1jlr62guqwrwkdt4m3y00zh2rrsamhjf9num5xr"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "7248467",
             "previous_period": "1584",
@@ -6114566,7 +6114566,7 @@
           "validator_address": "cosmosvaloper1n229vhepft6wnkt5tjpwmxdmcnfz55jv3vp77d"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "starting_info": {
             "height": "7257947",
             "previous_period": "35398",
@@ -6185549,7 +6185549,7 @@
           "validator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "starting_info": {
             "height": "7257832",
             "previous_period": "124464",
@@ -6232533,7 +6232533,7 @@
           "starting_info": {
             "height": "0",
             "previous_period": "27294",
-            "stake": "25390741.000000000000000000"
+            "stake": "6000000025390741.000000000000000000"
           },
           "validator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf"
         },
@@ -6286646,7 +6286646,7 @@
           "validator_address": "cosmosvaloper1hjct6q7npsspsg3dgvzk3sdf89spmlpfdn6m9d"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "7248467",
             "previous_period": "5860",
@@ -6381587,7 +6381587,7 @@
           "validator_address": "cosmosvaloper1clpqr4nrk4khgkxj78fcwwh6dl3uw4epsluffn"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "7248467",
             "previous_period": "80368",
@@ -6394043,7 +6394043,7 @@
           "validator_address": "cosmosvaloper1clpqr4nrk4khgkxj78fcwwh6dl3uw4epsluffn"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "starting_info": {
             "height": "7257832",
             "previous_period": "80551",
@@ -6425687,7 +6425687,7 @@
           "validator_address": "cosmosvaloper1ey69r37gfxvxg62sh4r0ktpuc46pzjrm873ae8"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "7248467",
             "previous_period": "269308",
@@ -6581441,7 +6581441,7 @@
           "validator_address": "cosmosvaloper1et77usu8q2hargvyusl4qzryev8x8t9wwqkxfs"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "7248467",
             "previous_period": "4225",
@@ -8477588,7 +8477588,7 @@
         "max_deposit_period": "1209600s",
         "min_deposit": [
           {
-            "amount": "64000000",
+            "amount": "1",
             "denom": "uatom"
           }
         ]
@@ -8478740,7 +8478740,7 @@
           "submit_time": "2021-06-02T17:30:15.614131648Z",
           "total_deposit": [
             {
-              "amount": "64000000",
+              "amount": "1",
               "denom": "uatom"
             }
           ],
@@ -8478861,7 +8478861,7 @@
           "submit_time": "2021-07-11T21:10:26.141197124Z",
           "total_deposit": [
             {
-              "amount": "64000000",
+              "amount": "1",
               "denom": "uatom"
             }
           ],
@@ -8478896,13 +8478896,13 @@
       ],
       "starting_proposal_id": "54",
       "tally_params": {
-        "quorum": "0.400000000000000000",
-        "threshold": "0.500000000000000000",
+        "quorum": "0.000000000000000001",
+        "threshold": "0.000000000000000001",
         "veto_threshold": "0.334000000000000000"
       },
       "votes": [],
       "voting_params": {
-        "voting_period": "1209600s"
+        "voting_period": "60s"
       }
     },
     "ibc": {
@@ -12037724,7 +12037724,7 @@
           ]
         },
         {
-          "address": "cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3",
+          "address": "cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp",
           "missed_blocks": [
             {
               "index": "10",
@@ -12512117,7 +12512117,7 @@
           ]
         },
         {
-          "address": "cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6",
+          "address": "cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve",
           "missed_blocks": [
             {
               "index": "0",
@@ -13540393,7 +13540393,7 @@
           }
         },
         {
-          "address": "cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3",
+          "address": "cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp",
           "validator_signing_info": {
             "address": "",
             "index_offset": "7472462",
@@ -13540844,7 +13540844,7 @@
           }
         },
         {
-          "address": "cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6",
+          "address": "cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve",
           "validator_signing_info": {
             "address": "",
             "index_offset": "10706496",
@@ -13590926,42 +13590926,42 @@
           "validator_address": "cosmosvaloper1tflk30mq5vgqjdly92kkhhq3raev2hnz6eete3"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "3000000.000000000000000000",
           "validator_address": "cosmosvaloper1pjmngrwcsatsuyy8m3qrunaun67sr9x7z5r2qs"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "5000000.000000000000000000",
           "validator_address": "cosmosvaloper1tflk30mq5vgqjdly92kkhhq3raev2hnz6eete3"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "1000000.000000000000000000",
           "validator_address": "cosmosvaloper1sjllsnramtg3ewxqwwrwjxfgc4n4ef9u2lcnj0"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "16100000.000000000000000000",
           "validator_address": "cosmosvaloper1jlr62guqwrwkdt4m3y00zh2rrsamhjf9num5xr"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "1000000.000000000000000000",
           "validator_address": "cosmosvaloper1hjct6q7npsspsg3dgvzk3sdf89spmlpfdn6m9d"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "3000000.000000000000000000",
           "validator_address": "cosmosvaloper1clpqr4nrk4khgkxj78fcwwh6dl3uw4epsluffn"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "999999.999433247914197563",
           "validator_address": "cosmosvaloper1ey69r37gfxvxg62sh4r0ktpuc46pzjrm873ae8"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "2000000.000000000000000000",
           "validator_address": "cosmosvaloper1et77usu8q2hargvyusl4qzryev8x8t9wwqkxfs"
         },
@@ -13845146,22 +13845146,22 @@
           "validator_address": "cosmosvaloper196ax4vc0lwpxndu9dyhvca7jhxp70rmcvrj90c"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "shares": "5717570.000000000000000000",
           "validator_address": "cosmosvaloper1sjllsnramtg3ewxqwwrwjxfgc4n4ef9u2lcnj0"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "shares": "4161516.137544609690354305",
           "validator_address": "cosmosvaloper1n229vhepft6wnkt5tjpwmxdmcnfz55jv3vp77d"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "shares": "10238742.000000000000000000",
           "validator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "shares": "55840909.000000000000000000",
           "validator_address": "cosmosvaloper1clpqr4nrk4khgkxj78fcwwh6dl3uw4epsluffn"
         },
@@ -14254917,7 +14254917,7 @@
         },
         {
           "delegator_address": "cosmos1ll705078lwg6yksn3flktpvzpe56gwvh7xmynw",
-          "shares": "25390741.000000000000000000",
+          "shares": "6000000025390741.000000000000000000",
           "validator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf"
         },
         {
@@ -14254987,7 +14254987,7 @@
         }
       ],
       "exported": true,
-      "last_total_power": "194093279",
+      "last_total_power": "6194093279",
       "last_validator_powers": [
         {
           "address": "cosmosvaloper1qwl879nx9t6kef4supyazayf7vjhennyh568ys",
@@ -14255303,7 +14255303,7 @@
         },
         {
           "address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf",
-          "power": "13991908"
+          "power": "6013991908"
         },
         {
           "address": "cosmosvaloper15urq2dtp9qce4fyc85m6upwm9xul3049e02707",
@@ -14255967,7 +14255967,7 @@
           "validator_src_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "entries": [
             {
               "completion_time": "2021-09-02T16:58:32.491727097Z",
@@ -14336532,7 +14336532,7 @@
           },
           "consensus_pubkey": {
             "@type": "/cosmos.crypto.ed25519.PubKey",
-            "key": "cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM="
+            "key": "qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU="
           },
           "delegator_shares": "2589549881077.000000000000000000",
           "description": {
@@ -14341810,9 +14341810,9 @@
           },
           "consensus_pubkey": {
             "@type": "/cosmos.crypto.ed25519.PubKey",
-            "key": "W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o="
+            "key": "oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0="
           },
-          "delegator_shares": "13991908901944.000000000000000000",
+          "delegator_shares": "6013991908901944.000000000000000000",
           "description": {
             "details": "Exchange the world",
             "identity": "",
@@ -14341824,7 +14341824,7 @@
           "min_self_delegation": "1",
           "operator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf",
           "status": "BOND_STATUS_BONDED",
-          "tokens": "13991908901944",
+          "tokens": "6013991908901944",
           "unbonding_height": "0",
           "unbonding_time": "1970-01-01T00:00:00Z"
         },
@@ -14345108,4 +14345108,4 @@
     "upgrade": {},
     "vesting": {}
   }
-}
\ No newline at end of file
+}
```

