# Gravity-DEX Upgrade Test

This repository describes procedures and contains results of upgrade test for the `Gravity-DEX` on the exported state of `cosmoshub-4` chain.

To get more context about this repo, please reference the following [issue](https://github.com/tendermint/liquidity/issues/403) and [pull request](https://github.com/cosmos/gaia/pull/859).

# Version

- Build this [gaia v4.2.1](https://github.com/cosmos/gaia/releases/tag/v4.2.1) that is used in `cosmoshub-4`

- This [gaia v5.0.0](https://github.com/cosmos/gaia/releases/tag/v5.0.0) is used for `Gravity-DEX` upgrade

# Genesis configuration

- This `exported_genesis_with_height_6659211_sorted_origin.json` zip file is prepared in this repository. It is exported using `gaiad export` command on block height `6659211` from `cosmoshub-4` network. Unzip the attached genesis file to proceed the next steps in this guide.

- This `genesis.json.tar.bz2` zip file is the filw that already proceeded all the modifications and is ready to be used right away. You can skip the next steps and use this to proceed the upgrade test.

## Unzip the tar files and verify the hashes

As stated above, skip the below steps if you decide to use `genesis.json.tar.gz2`.

### 1. Verify the hash and copy exported state file

```bash
# unzip the file
tar xvzf exported_genesis_with_height_6659211_sorted_origin.json.tar.bz2

# verify the hash
# it should return a955b5752f2f0e5384a1e99323ad2ca6c9db58c7b605123bb5896693882136f4
cat exported_genesis_with_height_6659211_sorted_origin.json | shasum -a 256

# change the name to genesis.json
cp exported_genesis_with_height_6659211_sorted_origin.json genesis.json
```

### 2. Substitue validator keys and accounts

In this step, we're swapping two validators and add two different accounts. Also, we're going to modify some parameters for test efficiency. 

The following files and mnemonics for the two different accounts are already provided: 

- `priv_validator_key_val1.json`: validator1's consensus key

- `priv_validator_key_val2.json`: validator2's consensus key

- `guard cream sadness conduct invite crumble clock pudding hole grit liar hotel maid produce squeeze return argue turtle know drive eight casino maze host`: The mnemonic for account1. This account is used for validator1

- `friend excite rough reopen cover wheel spoon convince island path clean monkey play snow number walnut pull lock shoot hurry dream divide concert discover`: The mnemonic for account2. This account is used to test some cli interfaces for the liquidity module.

- `render hire dirt bulk huge goat jungle number wear method check during menu goat accident scan noise nerve below target resource digital column flee`: This account is `user1` that is used for sending governance proposal and voting the proposal.

- `junk appear guide guess bar reject vendor illegal script sting shock afraid detect ginger other theory relief dress develop core pull across hen float`: This account is `user2` that is used to test some cli interfaces for the liquidity module.

```bash
# configure variables
export EXPORTED_GENESIS=genesis.json
export BINARY=gaiad
export CHAIN_ID=cosmoshub-4-upgrade-testnet
export CHAIN_DIR=./data

sed -i '' 's%"chain_id": "cosmoshub-4",%"chain_id": "cosmoshub-4-upgrade-testnet",%g' $EXPORTED_GENESIS

# substitue validator1
sed -i '' 's%cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM=%qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU=%g' $EXPORTED_GENESIS
sed -i '' 's%B00A6323737F321EB0B8D59C6FD497A14B60938A%D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA%g' $EXPORTED_GENESIS
sed -i '' 's%cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6%cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve%g' $EXPORTED_GENESIS

# substitue validator2
sed -i '' 's%W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o=%oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0=%g' $EXPORTED_GENESIS
sed -i '' 's%83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0%7CB07B94FD743E2A8520C2B50DA4B03740643BF5%g' $EXPORTED_GENESIS
sed -i '' 's%cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3%cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp%g' $EXPORTED_GENESIS

# substitute user1 account
# cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl --> cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6
# Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8 --> AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i
sed -i '' 's%cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl%cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6%g' $EXPORTED_GENESIS
sed -i '' 's%Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8%AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i%g' $EXPORTED_GENESIS

# substitute user2 account
# cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx --> cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
# A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g --> ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7
sed -i '' 's%cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx%cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9%g' $EXPORTED_GENESIS
sed -i '' 's%A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g%ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7%g' $EXPORTED_GENESIS
```

### 3. Change voting power and staking delegation to over 67%

```bash
sed -i '' 's%13488360679504%6013488360679504%g' $EXPORTED_GENESIS
sed -i '' 's%"power": "13488360"%"power": "603488360"%g' $EXPORTED_GENESIS

sed -i '' 's%274699904554428%6274699904554428%g' $EXPORTED_GENESIS
# missed this modification on public testnet genesis
# sed -i '' 's%274699904554428%1006274699904554428%g' $EXPORTED_GENESIS

sed -i '' 's%"25390741.000000000000000000"%"6000000025390741.000000000000000000"%g' $EXPORTED_GENESIS
sed -i '' 's%"191488844"%"6191488844"%g' $EXPORTED_GENESIS
sed -i '' 's%"amount": "107120822"%"amount": "1000000000107120822"%g' $EXPORTED_GENESIS

```
### 4. Modify some gov parameters for test efficiency

The following parameters are needed to be modified:

- minimum deposit amount
- quorum
- threshold
- voting_period

```bash
sed -i '' 's%"amount": "64000000",%"amount": "1",%g' $EXPORTED_GENESIS
sed -i '' 's%"quorum": "0.400000000000000000",%"quorum": "0.000000000000000001",%g' $EXPORTED_GENESIS
sed -i '' 's%"threshold": "0.500000000000000000",%"threshold": "0.000000000000000001",%g' $EXPORTED_GENESIS
sed -i '' 's%"voting_period": "1209600s"%"voting_period": "60s"%g' $EXPORTED_GENESIS
```
### 5. Initialize local chain

```bash
export EXPORTED_GENESIS=genesis.json
export BINARY=gaiad
export CHAIN_ID=cosmoshub-4-upgrade-testnet
export CHAIN_DIR=./data

export VAL_1_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val1
export VAL_1_MNEMONIC="guard cream sadness conduct invite crumble clock pudding hole grit liar hotel maid produce squeeze return argue turtle know drive eight casino maze host"
export VAL_1_KEY_NAME="val1"
export VAL_1_MONIKER="Validator One"

export VAL_2_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
export VAL_2_MNEMONIC="friend excite rough reopen cover wheel spoon convince island path clean monkey play snow number walnut pull lock shoot hurry dream divide concert discover"
export VAL_2_KEY_NAME="val2"
export VAL_2_MONIKER="Validator Two"

# Validator 1
echo "Initializing $CHAIN_ID..."
$BINARY init test --home $VAL_1_CHAIN_DIR --chain-id=$CHAIN_ID 

echo "Adding genesis accounts..."
echo $VAL_1_MNEMONIC | $BINARY --home $VAL_1_CHAIN_DIR keys add $VAL_1_KEY_NAME --recover --keyring-backend=test 

# Validator 2
echo "Initializing $CHAIN_ID..."
$BINARY --home $VAL_2_CHAIN_DIR init test --chain-id=$CHAIN_ID

echo "Adding genesis accounts..."
echo $VAL_2_MNEMONIC | $BINARY --home $VAL_2_CHAIN_DIR keys add $VAL_2_KEY_NAME --recover --keyring-backend=test 

# Account 1
export USER_1_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
export USER_1_MNEMONIC="render hire dirt bulk huge goat jungle number wear method check during menu goat accident scan noise nerve below target resource digital column flee"
export USER_1_KEY_NAME="user1"

#- name: user1
#  type: local
#  address: cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6
#  pubkey: cosmospub1addwnpepqtpngaangvnl5pvlaltfreq28djqskzd6g6pw89pztj2cg44uyl7y6xgev8

# Account 2
export USER_2_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
export USER_2_MNEMONIC="junk appear guide guess bar reject vendor illegal script sting shock afraid detect ginger other theory relief dress develop core pull across hen float"
export USER_2_KEY_NAME="user2"

#- name: user2
#  type: local
#  address: cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
#  pubkey: cosmospub1addwnpepq2gvu5e8m34xpe5aky8wemsc9fefqs4y8h07s24cklr8lwrewf2lklysaen

echo $USER_1_MNEMONIC | $BINARY --home $USER_1_CHAIN_DIR keys add $USER_1_KEY_NAME --recover --keyring-backend=test
echo $USER_2_MNEMONIC | $BINARY --home $USER_2_CHAIN_DIR keys add $USER_2_KEY_NAME --recover --keyring-backend=test
```

### 6. Copy exported genesis file and configure `config.toml` and `app.toml`

```bash
# copy genesis file 
cp genesis.json $VAL_1_CHAIN_DIR/config/genesis.json
cp genesis.json $VAL_2_CHAIN_DIR/config/genesis.json

# copy validator keys
cp priv_validator_key_val1.json $VAL_1_CHAIN_DIR/config/priv_validator_key.json
cp priv_validator_key_val2.json $VAL_2_CHAIN_DIR/config/priv_validator_key.json

# configure config and app toml
export VAL_1_P2P_PORT=26656
export VAL_2_P2P_PORT=36656
export VAL_2_RPC_PORT=36657
export VAL_2_API_PORT=1327
export VAL_2_GRPC_PORT=9080
export VAL_2_PPROF_PORT=6061
sed -i '' 's#"tcp://127.0.0.1:26657"#"tcp://0.0.0.0:'"$VAL_2_RPC_PORT"'"#g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's#"tcp://0.0.0.0:26656"#"tcp://0.0.0.0:'"$VAL_2_P2P_PORT"'"#g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's#"tcp://0.0.0.0:1317"#"tcp://0.0.0.0:'"$VAL_2_API_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's#"0.0.0.0:9090"#"0.0.0.0:'"$VAL_2_GRPC_PORT"'"#g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's/enable = false/enable = true/g' $VAL_2_CHAIN_DIR/config/app.toml
sed -i '' 's/swagger = false/swagger = true/g' $VAL_2_CHAIN_DIR/config/app.toml

# configure peers
export VAL_1_NODE_ID=$($BINARY tendermint --home $VAL_1_CHAIN_DIR show-node-id)
export VAL_2_NODE_ID=$($BINARY tendermint --home $VAL_2_CHAIN_DIR show-node-id)
sed -i '' 's/persistent_peers = ""/persistent_peers = "'$VAL_2_NODE_ID'@'localhost':'$VAL_2_P2P_PORT'"/g' $VAL_1_CHAIN_DIR/config/config.toml
sed -i '' 's/persistent_peers = ""/persistent_peers = "'$VAL_1_NODE_ID'@'localhost':'$VAL_1_P2P_PORT'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's/unconditional_peer_ids = ""/unconditional_peer_ids = "'$VAL_1_NODE_ID'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's/unconditional_peer_ids = ""/unconditional_peer_ids = "'$VAL_2_NODE_ID'"/g' $VAL_1_CHAIN_DIR/config/config.toml
sed -i '' 's/pprof_laddr = "localhost:6060"/pprof_laddr = "localhost:'$VAL_2_PPROF_PORT'"/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's/addr_book_strict = true/addr_book_strict = false/g' $VAL_2_CHAIN_DIR/config/config.toml
sed -i '' 's/addr_book_strict = true/addr_book_strict = false/g' $VAL_1_CHAIN_DIR/config/config.toml
```
verify genesis hash after modification

`cat genesis.json  | shasum -a 256`

> `8be3029854293b0ccdfc7805e649360767ec4720620f585c44348629090a1974`



### 7. Let's start chain

```bash
# start validator nodes 
export HOME1=./data/cosmoshub-4/val1
export HOME2=./data/cosmoshub-4/val2

# use terminal 1
$BINARY start --home $HOME1 --x-crisis-skip-assert-invariants

# use terminal 2
$BINARY start --home $HOME2 --x-crisis-skip-assert-invariants
```
### 8. Upgrade test

```bash
# v4.2.1
gaiad version

# submit upgrade proposal 
# make sure upgrade-height has enough time for the proposal to pass
gaiad tx gov submit-proposal software-upgrade Gravity-DEX \
--deposit 1000uatom \
--title Gravity-DEX \
--upgrade-height {your target height, ex) 6910000} \
--upgrade-info "v5.0.0-4760cf1f1266accec7a107f440d46d9724c6fd08" \
--description "This on-chain upgrade governance proposal is to adopt the Gravity DEX protocol on the Cosmos Hub. By voting YES to this proposal, you approve of adding the Gravity DEX protocol on the Cosmos Hub.\\n\\n### Background\\n\\nIn July 2020, the Iqlusion team developed ATOM 2021 to drive the direction of the Cosmos Hub after the completion of the Cosmos whitepaper and IBC. It became clear that providing liquidity to new IBC connected zones was core the Hub's mission. \\n\\nTendermint ([https://tendermint.com](https://tendermint.com/)) and B-Harvest ([https://bharvest.io](https://bharvest.io/))  joined forces to produce and develop a Liquidity Module ([https://github.com/tendermint/liquidity](https://github.com/tendermint/liquidity)). In 2021 March, they submitted a signal governance proposal to ask the Atom delegator community about Gravity DEX (Liquidity Module) adoption on the Cosmos Hub. Prop38 was very well approved by the community ([https://www.mintscan.io/cosmos/proposals/38](https://www.mintscan.io/cosmos/proposals/38)).\\n\\nThis proposal completes the first leg of ATOM 2021 and achieves the goals of the signaling proposal by bringing an IBC compatible DEX to the Hub.\\n\\n### Ready for Production\\n\\nWith continuous quality improvement of the codebase, very wide test coverage, a codebase audit from Least Authority and Simply VC, and subsequent follow-up codebase strengthening, along with extensive simulation processes, we are now very confident to be ready for production utilization of the Gravity DEX (Liquidity Module) on the Cosmos Hub. The Gaia branch for the new release with Gravity DEX feature can be found [here](https://github.com/cosmos/gaia/releases/tag/v5.0.0)). Please also check out the [github repository](https://github.com/b-harvest/gravity-dex-upgrade-test) for the launch testing of Gaia with Gravity DEX.\\n\\n### On-Chain Upgrade Process\\n\\nWhen the network reaches the halt height, the state machine program of the Cosmos Hub will be halted. And then, all validators and node operators have to substitute the existing state machine binary to the new binary with the Gravity DEX feature. Because it is an onchain upgrade process, the blockchain will be continued with all the accumulated history with continuous block height.\\n\\n### Potential Risk Factors\\n\\nAlthough Tendermint executed very extensive testing and simulation, and conducted in-depth audits, and followed up with the corresponding codebase improvement, there always still exists a risk that the Cosmos Hub might experience problems due to potential bugs or errors from the new Gravity DEX feature. In the case of serious problems, validators should stop operating the network immediately, and use the fixed state machine program provided by Tendermint." \
--gas 400000 \
--from user1 \
--keyring-backend test \
--chain-id cosmoshub-4-upgrade-testnet \
--home data/cosmoshub-4/val2 \
--node tcp://localhost:36657

# vote yes for the proposal
# note that voting period, quorum, and threshold params are modified 
gaiad tx gov vote 50 yes -y \
--from user1 \
--keyring-backend test \
--chain-id cosmoshub-4-upgrade-testnet \
--home data/cosmoshub-4/val2 \
--node tcp://127.0.0.1:36657 

# query the proposal to check if it is passed
# the status should be PROPOSAL_STATUS_PASSED
gaiad query gov proposal 50 \
--chain-id cosmoshub-4-upgrade-testnet \
--home data/cosmoshub-4/val2 \
--node tcp://127.0.0.1:36657 
```

```json
// result of the proposal
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
    height: "6659310"
    info: v5.0.0-4760cf1f1266accec7a107f440d46d9724c6fd08
    name: Gravity-DEX
    time: "0001-01-01T00:00:00Z"
    upgraded_client_state: null
  title: Gravity-DEX
deposit_end_time: "2021-07-12T15:44:27.561917Z"
final_tally_result:
  abstain: "0"
  "no": "0"
  no_with_veto: "0"
  "yes": "72642221"
proposal_id: "50"
status: PROPOSAL_STATUS_PASSED
submit_time: "2021-06-28T15:44:27.561917Z"
total_deposit:
- amount: "1000"
  denom: uatom
voting_end_time: "2021-06-28T15:45:27.561917Z"
voting_start_time: "2021-06-28T15:44:27.561917Z"
```
### 9. Restart node using new gaiad gravity-dex version,

The proposal has passed and it is all good to go. When `upgrade-height` is reached, the node must be halted for upgrade.

> ERR UPGRADE "Gravity-DEX" NEEDED at height: 6659975: v4.3.0-testhash
> ERR CONSENSUS FAILURE!!! err="UPGRADE \"Gravity-DEX\" NEEDED at height: 6659975: v4.3.0-testhash" module=consensus stack="goroutine 496414...

```bash
# gaia version that includes gravity-dex module
# https://github.com/b-harvest/gravity-dex/releases/tag/v1.0.4
# latest add-liquidity-module-to-gaia branch
gaiad version
-> v5.0.0

gaiad start --home $HOME1 --x-crisis-skip-assert-invariants
gaiad start --home $HOME2 --x-crisis-skip-assert-invariants
```
### 10. Test liquidity module

```bash
# query liquidity parameters
gaiad query liquidity params \
--node tcp://127.0.0.1:36657

# create liquidity pool 
gaiad tx liquidity create-pool 1 1000000ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C,1000000uatom \
--from user2 --keyring-backend test \
--home data/cosmoshub-4/val2 \
--chain-id cosmoshub-4-upgrade-testnet \
--gas 300000 \
--node tcp://127.0.0.1:36657

# query liquidity pool
gaiad query liquidity pools \
--node tcp://127.0.0.1:36657

# swap request 
gaiad tx liquidity swap 1 1 100000uatom ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C 0.019 0.003 \
--from user2 --keyring-backend test \
--home data/cosmoshub-4/val2 \
--chain-id cosmoshub-4-upgrade-testnet \
--node tcp://127.0.0.1:36657

gaiad query auth account cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6 \
--node tcp://127.0.0.1:36657 

gaiad query auth account cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9 \
--node tcp://127.0.0.1:36657 

# withdraw request
gaiad tx liquidity withdraw 1 1000pool024B000726712F1093C7D24EC329DE498EBB85B4B2D37C59D4F37BC542020151 \
--from user2 --keyring-backend test \
--home data/cosmoshub-4/val2 \
--chain-id cosmoshub-4-upgrade-testnet \
--node tcp://127.0.0.1:36657

# query balance after swap transacted 
gaiad query bank balances cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9 \
--node tcp://127.0.0.1:36657 

```

```json
// result of liquidity module params
circuit_breaker_enabled: false
init_pool_coin_mint_amount: "1000000"
max_order_amount_ratio: "0.100000000000000000"
max_reserve_coin_amount: "0"
min_init_deposit_amount: "1000000"
pool_creation_fee:
- amount: "100000000"
  denom: uatom
pool_types:
- description: ""
  id: 1
  max_reserve_coin_num: 2
  min_reserve_coin_num: 2
  name: DefaultPoolType
swap_fee_rate: "0.003000000000000000"
unit_batch_height: 1
withdraw_fee_rate: "0.003000000000000000"
```

```json
// result of created liquidity pool
pagination:
  next_key: null
  total: "1"
pools:
- id: "1"
  pool_coin_denom: pool024B000726712F1093C7D24EC329DE498EBB85B4B2D37C59D4F37BC542020151
  reserve_account_address: cosmos1qf9sqpexwyh3py786f8vx2w7fx8thpd5wz79sf
  reserve_coin_denoms:
  - ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C
  - uatom
  type_id: 1
```


# Public Testnet info

### Join Validator

If you want to participate in the testnet as a validator, you can use the same account, validator key of the cosmoshub-4, or if you request an issue like [this](https://github.com/b-harvest/gravity-dex-upgrade-test/issues/1), we will send you some coins for the delegation manually.

The modification of the current version of Genesis has some errors, so the invariant check may fail. So please use `--x-crisis-skip-assert-invariants` flag when starting Gaia. If some issue occurs due to that invariant on this testnet, we may make a decision to restart the new Genesis based on community opinion.



### Chain-ID

`cosmoshub-4-upgrade-testnet`



### Genesis

`tar xvzf genesis.json.tar.gz2`

`cat genesis.json  | shasum -a 256`

> 8be3029854293b0ccdfc7805e649360767ec4720620f585c44348629090a1974



### Binary

The testnet was started based on [gaia v4.2.1](https://github.com/cosmos/gaia/releases/tag/v4.2.1) and upgraded to [gaia v5.0.0](https://github.com/cosmos/gaia/releases/tag/v5.0.0) on `6659230` Height by `SoftwareUpgradeProposal` [#50](http://49.247.196.44:1317/cosmos/gov/v1beta1/proposals/50).

So when you start to sync your node, you have to use the [gaia v4.2.1](https://github.com/cosmos/gaia/releases/tag/v4.2.1), And when it stops at the `6659230` height with the below panic, you should restart using [gaia v5.0.0](https://github.com/cosmos/gaia/releases/tag/v5.0.0).

```bash
ERR UPGRADE "Gravity-DEX" NEEDED at height: 6659230: v5.0.0-4760cf1f1266accec7a107f440d46d9724c6fd08
panic: UPGRADE "Gravity-DEX" NEEDED at height: 6659230: v5.0.0-4760cf1f1266accec7a107f440d46d9724c6fd08
```



### P2P

`791dd7d0d217e8a761261ab7a2d8105326f46604@49.247.196.44:26656`

`74ca644dd1cce437aed792000066d2752aa69904@49.247.196.46:26656`



### Endpoint

`49.247.196.44:1317`

`49.247.196.44:26657`

`49.247.196.44:9090`



`49.247.196.46:1317`

`49.247.196.46:26657`

`49.247.196.46:9090`



min gas = 0

p2p peers = 200





# (Research) Modified `genesis.json`

`diff exported_genesis_with_height_6659211_sorted_origin.json genesis.json -u`

```diff
--- exported_genesis_with_height_6659211_sorted_origin.json	2021-06-21 18:15:44.000000000 +0900
+++ genesis.json	2021-07-05 19:23:47.000000000 +0900
@@ -194199,10 +194199,10 @@
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
           "sequence": "203"
         },
@@ -1223676,10 +1223676,10 @@
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
           "sequence": "15"
         },
@@ -2999974,7 +2999974,7 @@
           ]
         },
         {
-          "address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "coins": [
             {
               "amount": "10000000",
@@ -2999993,7 +2999993,7 @@
               "denom": "ibc/EC4B5D87917DD5668D9998146F82D70FDF86652DB333D04CE29D1EB18E296AF5"
             },
             {
-              "amount": "107120822",
+              "amount": "1000000000107120822",
               "denom": "uatom"
             }
           ]
@@ -3621122,7 +3621122,7 @@
           "coins": []
         },
         {
-          "address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "coins": [
             {
               "amount": "1462406",
@@ -4626926,7 +4626926,7 @@
           "denom": "ibc/FEE3FB19682DAAAB02A0328A2B84A80E7DDFE5BA48F7D2C8C30AAC649B8DD519"
         },
         {
-          "amount": "274699904554428",
+          "amount": "6274699904554428",
           "denom": "uatom"
         }
       ]
@@ -4715562,7 +4715562,7 @@
           "validator_address": "cosmosvaloper1pjmngrwcsatsuyy8m3qrunaun67sr9x7z5r2qs"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "6655951",
             "previous_period": "616",
@@ -4888785,7 +4888785,7 @@
           "validator_address": "cosmosvaloper1tflk30mq5vgqjdly92kkhhq3raev2hnz6eete3"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "6655951",
             "previous_period": "392907",
@@ -5277153,7 +5277153,7 @@
           "validator_address": "cosmosvaloper1sjllsnramtg3ewxqwwrwjxfgc4n4ef9u2lcnj0"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "starting_info": {
             "height": "6391150",
             "previous_period": "71961",
@@ -5301687,7 +5301687,7 @@
           "validator_address": "cosmosvaloper1jlr62guqwrwkdt4m3y00zh2rrsamhjf9num5xr"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "6655951",
             "previous_period": "1457",
@@ -5325366,7 +5325366,7 @@
           "validator_address": "cosmosvaloper1n229vhepft6wnkt5tjpwmxdmcnfz55jv3vp77d"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "starting_info": {
             "height": "6391163",
             "previous_period": "26989",
@@ -5390427,7 +5390427,7 @@
           "validator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "starting_info": {
             "height": "6391150",
             "previous_period": "100085",
@@ -5433190,7 +5433190,7 @@
           "starting_info": {
             "height": "0",
             "previous_period": "27294",
-            "stake": "25390741.000000000000000000"
+            "stake": "6000000025390741.000000000000000000"
           },
           "validator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf"
         },
@@ -5449242,7 +5449242,7 @@
           "validator_address": "cosmosvaloper14l0fp639yudfl46zauvv8rkzjgd4u0zk2aseys"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "6655951",
             "previous_period": "23914",
@@ -5481786,7 +5481786,7 @@
           "validator_address": "cosmosvaloper1hjct6q7npsspsg3dgvzk3sdf89spmlpfdn6m9d"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "6655951",
             "previous_period": "5048",
@@ -5562516,7 +5562516,7 @@
           "validator_address": "cosmosvaloper1clpqr4nrk4khgkxj78fcwwh6dl3uw4epsluffn"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "6655951",
             "previous_period": "71384",
@@ -5573739,7 +5573739,7 @@
           "validator_address": "cosmosvaloper1clpqr4nrk4khgkxj78fcwwh6dl3uw4epsluffn"
         },
         {
-          "delegator_address": "cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl",
+          "delegator_address": "cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6",
           "starting_info": {
             "height": "6391150",
             "previous_period": "68153",
@@ -5602728,7 +5602728,7 @@
           "validator_address": "cosmosvaloper1ey69r37gfxvxg62sh4r0ktpuc46pzjrm873ae8"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "6655951",
             "previous_period": "252870",
@@ -5752803,7 +5752803,7 @@
           "validator_address": "cosmosvaloper1et77usu8q2hargvyusl4qzryev8x8t9wwqkxfs"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "starting_info": {
             "height": "6655951",
             "previous_period": "3996",
@@ -7470242,7 +7470242,7 @@
         "max_deposit_period": "1209600s",
         "min_deposit": [
           {
-            "amount": "64000000",
+            "amount": "1",
             "denom": "uatom"
           }
         ]
@@ -7471209,7 +7471209,7 @@
           "submit_time": "2021-06-02T17:30:15.614131648Z",
           "total_deposit": [
             {
-              "amount": "64000000",
+              "amount": "1",
               "denom": "uatom"
             }
           ],
@@ -7471244,8 +7471244,8 @@
       ],
       "starting_proposal_id": "50",
       "tally_params": {
-        "quorum": "0.400000000000000000",
-        "threshold": "0.500000000000000000",
+        "quorum": "0.000000000000000001",
+        "threshold": "0.000000000000000001",
         "veto_threshold": "0.334000000000000000"
       },
       "votes": [
@@ -7476031,7 +7476031,7 @@
         }
       ],
       "voting_params": {
-        "voting_period": "1209600s"
+        "voting_period": "60s"
       }
     },
     "ibc": {
@@ -9342270,7 +9342270,7 @@
           ]
         },
         {
-          "address": "cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3",
+          "address": "cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp",
           "missed_blocks": [
             {
               "index": "10",
@@ -9827071,7 +9827071,7 @@
           ]
         },
         {
-          "address": "cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6",
+          "address": "cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve",
           "missed_blocks": [
             {
               "index": "0",
@@ -10797296,7 +10797296,7 @@
           }
         },
         {
-          "address": "cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3",
+          "address": "cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp",
           "validator_signing_info": {
             "address": "",
             "index_offset": "6827172",
@@ -10797747,7 +10797747,7 @@
           }
         },
         {
-          "address": "cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6",
+          "address": "cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve",
           "validator_signing_info": {
             "address": "",
             "index_offset": "10061206",
@@ -10842841,42 +10842841,42 @@
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
           "shares": "16100000.000000000000000000",
           "validator_address": "cosmosvaloper1jlr62guqwrwkdt4m3y00zh2rrsamhjf9num5xr"
         },
         {
-          "delegator_address": "cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx",
+          "delegator_address": "cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9",
           "shares": "1000000.000000000000000000",
           "validator_address": "cosmosvaloper14l0fp639yudfl46zauvv8rkzjgd4u0zk2aseys"
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
@@ -11073821,22 +11073821,22 @@
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
           "shares": "845084.506445134833187816",
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
@@ -11445277,7 +11445277,7 @@
         },
         {
           "delegator_address": "cosmos1ll705078lwg6yksn3flktpvzpe56gwvh7xmynw",
-          "shares": "25390741.000000000000000000",
+          "shares": "6000000025390741.000000000000000000",
           "validator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf"
         },
         {
@@ -11445342,7 +11445342,7 @@
         }
       ],
       "exported": true,
-      "last_total_power": "191488844",
+      "last_total_power": "6191488844",
       "last_validator_powers": [
         {
           "address": "cosmosvaloper1qwl879nx9t6kef4supyazayf7vjhennyh568ys",
@@ -11445658,7 +11445658,7 @@
         },
         {
           "address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf",
-          "power": "13488360"
+          "power": "603488360"
         },
         {
           "address": "cosmosvaloper15urq2dtp9qce4fyc85m6upwm9xul3049e02707",
@@ -11518421,7 +11518421,7 @@
           },
           "consensus_pubkey": {
             "@type": "/cosmos.crypto.ed25519.PubKey",
-            "key": "cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM="
+            "key": "qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU="
           },
           "delegator_shares": "2792532348046.000000000000000000",
           "description": {
@@ -11523612,9 +11523612,9 @@
           },
           "consensus_pubkey": {
             "@type": "/cosmos.crypto.ed25519.PubKey",
-            "key": "W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o="
+            "key": "oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0="
           },
-          "delegator_shares": "13488360679504.000000000000000000",
+          "delegator_shares": "6013488360679504.000000000000000000",
           "description": {
             "details": "Exchange the world",
             "identity": "",
@@ -11523626,7 +11523626,7 @@
           "min_self_delegation": "1",
           "operator_address": "cosmosvaloper156gqf9837u7d4c4678yt3rl4ls9c5vuursrrzf",
           "status": "BOND_STATUS_BONDED",
-          "tokens": "13488360679504",
+          "tokens": "6013488360679504",
           "unbonding_height": "0",
           "unbonding_time": "1970-01-01T00:00:00Z"
         },
@@ -11526724,7 +11526724,7 @@
     "upgrade": {},
     "vesting": {}
   },
-  "chain_id": "cosmoshub-4",
+  "chain_id": "cosmoshub-4-upgrade-testnet",
   "consensus_params": {
     "block": {
       "max_bytes": "200000",
@@ -11526747,12 +11526747,12 @@
   "initial_height": "6659211",
   "validators": [
     {
-      "address": "B00A6323737F321EB0B8D59C6FD497A14B60938A",
+      "address": "D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA",
       "name": "Certus One",
       "power": "2792532",
       "pub_key": {
         "type": "tendermint/PubKeyEd25519",
-        "value": "cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM="
+        "value": "qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU="
       }
     },
     {
@@ -11527449,12 +11527449,12 @@
       }
     },
     {
-      "address": "83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0",
+      "address": "7CB07B94FD743E2A8520C2B50DA4B03740643BF5",
       "name": "Binance Staking",
-      "power": "13488360",
+      "power": "603488360",
       "pub_key": {
         "type": "tendermint/PubKeyEd25519",
-        "value": "W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o="
+        "value": "oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0="
       }
     },
     {

```
