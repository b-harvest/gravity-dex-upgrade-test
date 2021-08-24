# Gravity DEX Upgrade Simulation Test

## Versions

- Cosmos Hub Mainnet `cosmoshub-4` currently uses [gaia v5.0.5](https://github.com/cosmos/gaia/releases/tag/v5.0.5).
- [Upgrade `gaiad` version with Gravity DEX that is rebased to Cosmos SDK v0.43.0](https://github.com/b-harvest/gravity-dex/blob/upgrade-liquidity-module-based-sdk-43)

## Option 1: Fast track

We have prepared a genesis file `genesis.json.tar.bz2` which was obtained after going from "Step 1 ~ 5" in Option 2. Uncompress the genesis file and use it as the genesis data to mock the upgrade simulation test. You can go to "Step 5. Initialize local chain" to proceed.

```bash=
# uncompress the file
tar xvzf genesis.json.tar.bz2

# verify the hash
cat genesis.json | shasum -a 256
> 
238ce65ca9f1b112cf3480de99bd0fdd9a65c54cf71ea155f0ba9ea897cffc56
```

## Option 2: Step by step guide

We have prepared a genesis file `exported_genesis_with_height_7304500_sorted.json.tar.bz2` which was obtained by `gaia export --height 7304500` command from `cosmoshub-4` network and compressed the file by `tar -cvjSf exported_genesis_with_height_7304500.json.tar.bz2 exported_genesis_with_height_7304500.json`. Uncompress the genesis file and use it as the genesis data to mock the upgrade.

### Step 1. Uncompress the prepared genesis file

```bash
# uncompress the file
tar xvzf exported_genesis_with_height_7304500_sorted.json.tar.bz2

# verify the hash
cat exported_genesis_with_height_7304500_sorted.json | shasum -a 256
>
df10480978a9a211fbc2f82865659c1298f1ba97a3fee37512b299102fa1e9c3

# copy the file and name it to genesis.json
cp exported_genesis_with_height_7304500_sorted.json genesis.json
```

### Step 2. Substitue validator keys and accounts

In this step, we are going to swap 2 validators and add new 2 different accounts in the genesis file. We are also going to modify some parameters to improve test efficiency. The mnemonics for the 2 new different accounts and the following files are already prepared in this repository.

- `priv_validator_key_val1.json`: validator1's consensus key

- `priv_validator_key_val2.json`: validator2's consensus key

- `guard cream sadness conduct invite crumble clock pudding hole grit liar hotel maid produce squeeze return argue turtle know drive eight casino maze host`: The mnemonic for `validator1`.

- `friend excite rough reopen cover wheel spoon convince island path clean monkey play snow number walnut pull lock shoot hurry dream divide concert discover`: The mnemonic for `validator2`.

- `render hire dirt bulk huge goat jungle number wear method check during menu goat accident scan noise nerve below target resource digital column flee`: This account is `user1` that is used for sending governance proposal and voting the proposal.

- `junk appear guide guess bar reject vendor illegal script sting shock afraid detect ginger other theory relief dress develop core pull across hen float`: This account is `user2` that is used to test some cli interfaces for the liquidity module.

```bash
# change chain id
sed -i '' 's%"chain_id": "cosmoshub-4",%"chain_id": "cosmoshub-4-upgrade-testnet-1001",%g' genesis.json

# substitue "Certus One" validator1
sed -i '' 's%cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM=%qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU=%g' genesis.json
sed -i '' 's%B00A6323737F321EB0B8D59C6FD497A14B60938A%D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA%g' genesis.json
sed -i '' 's%cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6%cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve%g' genesis.json

# substitue "Binance Staking" validator2
sed -i '' 's%W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o=%oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0=%g' genesis.json
sed -i '' 's%83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0%7CB07B94FD743E2A8520C2B50DA4B03740643BF5%g' genesis.json
sed -i '' 's%cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3%cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp%g' genesis.json

# substitute user1 account
#
# cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl --> cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6
# Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8 --> AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i
#
sed -i '' 's%cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl%cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6%g' genesis.json
sed -i '' 's%Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8%AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i%g' genesis.json

# substitute user2 account
#
# cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx --> cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
# A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g --> ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7
#
sed -i '' 's%cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx%cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9%g' genesis.json
sed -i '' 's%A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g%ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7%g' genesis.json
```

### Step 3. Change voting power and staking delegation to over 67%

```bash
# fix delegation amount to over 67% 
sed -i '' 's%"25390741.000000000000000000"%"6000000025390741.000000000000000000"%g' genesis.json

# fix power of the validator
#
# Binance Staking validator's "delegator_shares" and "tokens"
#
sed -i '' 's%13991908901944%6013991908901944%g' genesis.json
sed -i '' 's%"power": "13991908"%"power": "6013991908"%g' genesis.json

# fix last_total_power
sed -i '' 's%"194093279"%"6194093279"%g' genesis.json

# fix total supply of uatom
sed -i '' 's%277549930240869%1006277549930240869%g' genesis.json

# fix balance of bonded_tokens_pool module account
#
# cosmos1fl48vsnmsdzcv85q5d2q4z5ajdha8yu34mf0eh
#
sed -i '' 's%194093338099942%6194093338099942%g' genesis.json

# fix the balance of a account to use as a faucet on testnet
#
# substituted user2 account: cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx --> cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
#
sed -i '' 's%"amount": "72177323"%"amount": "1000000000072177323"%g' genesis.json
```

### Step 4. Modify `gov` parameters for test efficiency

The following `gov` parameters are needed to be modified:

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
### Step 5. Initialize the chain

```bash
export EXPORTED_GENESIS=genesis.json
export BINARY=gaiad
export CHAIN_ID=cosmoshub-4-upgrade-testnet-1001
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

### Step 6. Configuration

Copy the genesis file and new validator consensus keys to the designated directories. And configure settings in both `config.toml` and `app.toml`

```bash
# copy genesis file to the designated directories and copy validator consensus key files
cp genesis.json $VAL_1_CHAIN_DIR/config/genesis.json
cp genesis.json $VAL_2_CHAIN_DIR/config/genesis.json
cp priv_validator_key_val1.json $VAL_1_CHAIN_DIR/config/priv_validator_key.json
cp priv_validator_key_val2.json $VAL_2_CHAIN_DIR/config/priv_validator_key.json

# configure parameters in config.toml and app.toml files
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

# verify the genesis hash
cat genesis.json  | shasum -a 256
> 
238ce65ca9f1b112cf3480de99bd0fdd9a65c54cf71ea155f0ba9ea897cffc56
```

### 7. Start the chain

Open up two terminals and start 2 validator nodes. 

```bash
#
# Terminal 1
#
export BINARY=gaiad
export CHAIN_ID=cosmoshub-4-upgrade-testnet-1001
export HOME1=./data/$CHAIN_ID/val1
$BINARY start --home $HOME1 --x-crisis-skip-assert-invariants

#
# Terminal 2
#
export BINARY=gaiad
export CHAIN_ID=cosmoshub-4-upgrade-testnet-1001
export HOME2=./data/$CHAIN_ID/val2
$BINARY start --home $HOME2 --x-crisis-skip-assert-invariants
```

### 8. Send an upgrade proposal to the network

Open up terminal 3 to send an upgrade proposal along with a deposit and a vote.

```bash
#
# Terminal 3
#
# make sure upgrade-height has enough time for the proposal to pass
gaiad tx gov submit-proposal software-upgrade vega \
--title vega \
--deposit 1000uatom \
--upgrade-height 7304520 \
--upgrade-info "upgrade-liquidity-module-based-sdk-43-e817e034edbe812d5debf8c2242bd0f655cc9d46" \
--description "This on-chain upgrade governance proposal is to adopt the Gravity DEX protocol on the Cosmos Hub. By voting YES to this proposal, you approve of adding the Gravity DEX protocol on the Cosmos Hub.\\n\\n### Background\\n\\nIn July 2020, the Iqlusion team developed ATOM 2021 to drive the direction of the Cosmos Hub after the completion of the Cosmos whitepaper and IBC. It became clear that providing liquidity to new IBC connected zones was core the Hub's mission. \\n\\nTendermint ([https://tendermint.com](https://tendermint.com/)) and B-Harvest ([https://bharvest.io](https://bharvest.io/))  joined forces to produce and develop a Liquidity Module ([https://github.com/tendermint/liquidity](https://github.com/tendermint/liquidity)). In 2021 March, they submitted a signal governance proposal to ask the Atom delegator community about Gravity DEX (Liquidity Module) adoption on the Cosmos Hub. Prop38 was very well approved by the community ([https://www.mintscan.io/cosmos/proposals/38](https://www.mintscan.io/cosmos/proposals/38)).\\n\\nThis proposal completes the first leg of ATOM 2021 and achieves the goals of the signaling proposal by bringing an IBC compatible DEX to the Hub.\\n\\n### Ready for Production\\n\\nWith continuous quality improvement of the codebase, very wide test coverage, a codebase audit from Least Authority and Simply VC, and subsequent follow-up codebase strengthening, along with extensive simulation processes, we are now very confident to be ready for production utilization of the Gravity DEX (Liquidity Module) on the Cosmos Hub. The Gaia branch for the new release with Gravity DEX feature can be found [here](https://github.com/cosmos/gaia/releases/tag/v5.0.0)). Please also check out the [github repository](https://github.com/b-harvest/gravity-dex-upgrade-test) for the launch testing of Gaia with Gravity DEX.\\n\\n### On-Chain Upgrade Process\\n\\nWhen the network reaches the halt height, the state machine program of the Cosmos Hub will be halted. And then, all validators and node operators have to substitute the existing state machine binary to the new binary with the Gravity DEX feature. Because it is an onchain upgrade process, the blockchain will be continued with all the accumulated history with continuous block height.\\n\\n### Potential Risk Factors\\n\\nAlthough Tendermint executed very extensive testing and simulation, and conducted in-depth audits, and followed up with the corresponding codebase improvement, there always still exists a risk that the Cosmos Hub might experience problems due to potential bugs or errors from the new Gravity DEX feature. In the case of serious problems, validators should stop operating the network immediately, and use the fixed state machine program provided by Tendermint." \
--gas 400000 \
--from user1 \
--keyring-backend test \
--chain-id cosmoshub-4-upgrade-testnet-1001 \
--home data/cosmoshub-4-upgrade-testnet-1001/val2 \
--node tcp://localhost:36657 \
--yes

# query the proposal to check the status. 
# the status should be PROPOSAL_STATUS_VOTING_PERIOD.
gaiad query gov proposal 54 \
--chain-id cosmoshub-4-upgrade-testnet-1001 \
--home data/cosmoshub-4-upgrade-testnet-1001/val2 \
--node tcp://localhost:36657

# vote yes for the proposal
# note that voting period, quorum, and threshold params are modified 
gaiad tx gov vote 54 yes \
--from user1 \
--keyring-backend test \
--chain-id cosmoshub-4-upgrade-testnet-1001 \
--home data/cosmoshub-4-upgrade-testnet-1001/val2 \
--node tcp://localhost:36657 \
--yes

# wait for a while for the proposal to pass

# query the proposal to check if it is passed
# the status should be PROPOSAL_STATUS_PASSED
gaiad query gov proposal 54 \
--chain-id cosmoshub-4-upgrade-testnet-1001 \
--home data/cosmoshub-4-upgrade-testnet-1001/val2 \
--node tcp://localhost:36657
```


Result of the proposal

```json
content:
  '@type': /cosmos.upgrade.v1beta1.SoftwareUpgradeProposal
  description: This on-chain upgrade governance proposal is to adopt the Gravity DEX
    protocol on the Cosmos Hub. By voting YES to this proposal, you approve of adding
    the Gravity DEX protocol on the Cosmos Hub.\n\n### Background\n\nIn July 2020,
    the Iqlusion team developed ATOM 2021 to drive the direction of the Cosmos Hub
    after the completion of the Cosmos whitepaper and IBC. It became clear that providing
    liquidity to new IBC connected zones was core the Hub's mission. \n\nTendermint
    ([https://tendermint.com](https://tendermint.com/)) and B-Harvest ([https://bharvest.io](https://bharvest.io/))  joined
    forces to produce and develop a Liquidity Module ([https://github.com/tendermint/liquidity](https://github.com/tendermint/liquidity)).
    In 2021 March, they submitted a signal governance proposal to ask the Atom delegator
    community about Gravity DEX (Liquidity Module) adoption on the Cosmos Hub. Prop38
    was very well approved by the community ([https://www.mintscan.io/cosmos/proposals/38](https://www.mintscan.io/cosmos/proposals/38)).\n\nThis
    proposal completes the first leg of ATOM 2021 and achieves the goals of the signaling
    proposal by bringing an IBC compatible DEX to the Hub.\n\n### Ready for Production\n\nWith
    continuous quality improvement of the codebase, very wide test coverage, a codebase
    audit from Least Authority and Simply VC, and subsequent follow-up codebase strengthening,
    along with extensive simulation processes, we are now very confident to be ready
    for production utilization of the Gravity DEX (Liquidity Module) on the Cosmos
    Hub. The Gaia branch for the new release with Gravity DEX feature can be found
    [here](https://github.com/cosmos/gaia/releases/tag/v5.0.0)). Please also check
    out the [github repository](https://github.com/b-harvest/gravity-dex-upgrade-test)
    for the launch testing of Gaia with Gravity DEX.\n\n### On-Chain Upgrade Process\n\nWhen
    the network reaches the halt height, the state machine program of the Cosmos Hub
    will be halted. And then, all validators and node operators have to substitute
    the existing state machine binary to the new binary with the Gravity DEX feature.
    Because it is an onchain upgrade process, the blockchain will be continued with
    all the accumulated history with continuous block height.\n\n### Potential Risk
    Factors\n\nAlthough Tendermint executed very extensive testing and simulation,
    and conducted in-depth audits, and followed up with the corresponding codebase
    improvement, there always still exists a risk that the Cosmos Hub might experience
    problems due to potential bugs or errors from the new Gravity DEX feature. In
    the case of serious problems, validators should stop operating the network immediately,
    and use the fixed state machine program provided by Tendermint.
  plan:
    height: "7304520"
    info: upgrade-liquidity-module-based-sdk-43-e817e034edbe812d5debf8c2242bd0f655cc9d46
    name: vega
    time: "0001-01-01T00:00:00Z"
    upgraded_client_state: null
  title: vega
deposit_end_time: "2021-09-07T02:08:31.975946Z"
final_tally_result:
  abstain: "0"
  "no": "0"
  no_with_veto: "0"
  "yes": "108058021"
proposal_id: "54"
status: PROPOSAL_STATUS_PASSED
submit_time: "2021-08-24T02:08:31.975946Z"
total_deposit:
- amount: "1000"
  denom: uatom
voting_end_time: "2021-08-24T02:09:31.975946Z"
voting_start_time: "2021-08-24T02:08:31.975946Z"
```

### 9. Restart node using new gaiad gravity-dex version,

The proposal has passed and it is all good to go. When `upgrade-height` is reached, the node must be halted for upgrade.

> ERR UPGRADE "vega" NEEDED at height: 7304520: upgrade-liquidity-module-based-sdk-43-e817e034edbe812d5debf8c2242bd0f655cc9d46
  ERR CONSENSUS FAILURE!!! err="UPGRADE \"vega\" NEEDED at height: 7304520: upgrade-liquidity-module-based-sdk-43-e817e034edbe812d5debf8c2242bd0f655cc9d46"
  ........................................................................................................
  ..................................................................................................................................

```bash
# build the gaia version that is rebased to Cosmos SDK v0.43.0
# latest commit of upgrade-liquidity-module-based-sdk-43 branch
git clone -b upgrade-liquidity-module-based-sdk-43 https://github.com/b-harvest/gravity-dex.git
cd gravity-dex
make install

# check version
gaiad version
> 
upgrade-liquidity-module-based-sdk-43-e817e034edbe812d5debf8c2242bd0f655cc9d46

# Terminal 1
gaiad start --home $HOME1 --x-crisis-skip-assert-invariants

# Terminal 2
gaiad start --home $HOME2 --x-crisis-skip-assert-invariants
```

### 10. Test liquidity module

Let's first query the values are set as liquidity parameter.

```bash
gaiad query liquidity params \
--node tcp://localhost:36657 \
--output json | jq
```

Result

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

```bash
# create liquidity pool 
gaiad tx liquidity create-pool 1 1000000ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C,1000000uatom \
--from user2 \
--keyring-backend test \
--home data/cosmoshub-4-upgrade-testnet-1001/val2 \
--chain-id cosmoshub-4-upgrade-testnet-1001 \
--gas 300000 \
--node tcp://localhost:36657 \
--yes

# query liquidity pool
gaiad query liquidity pools \
--node tcp://localhost:36657 \
--output json | jq

# swap request 
gaiad tx liquidity swap 1 1 100000uatom ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C 0.019 0.003 \
--from user2 --keyring-backend test \
--home data/cosmoshub-4-upgrade-testnet-1001/val2 \
--chain-id cosmoshub-4-upgrade-testnet-1001 \
--node tcp://localhost:36657

gaiad query auth account cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6 \
--node tcp://localhost:36657 \
--output json | jq

gaiad query auth account cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9 \
--node tcp://localhost:36657 \
--output json | jq

# withdraw request
gaiad tx liquidity withdraw 1 1000pool024B000726712F1093C7D24EC329DE498EBB85B4B2D37C59D4F37BC542020151 \
--from user2 --keyring-backend test \
--home data/cosmoshub-4-upgrade-testnet-1001/val2 \
--chain-id cosmoshub-4-upgrade-testnet-1001 \
--node tcp://localhost:36657

# query balance after swap transacted 
gaiad query bank balances cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9 \
--node tcp://localhost:36657 \
--output json | jq
```