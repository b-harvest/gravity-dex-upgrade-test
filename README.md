# Gravity-DEX Upgrade Test



This repository contains the procedures and results of the test to upgrade `Gravity-DEX` based on the exported states of the `cosmosub-4` chain.



Gaia Binary of cosmoshub-4: https://github.com/cosmos/gaia/releases/tag/v4.2.1

Gaia Binary to upgrade for Gravity-DEX:

- https://github.com/b-harvest/gravity-dex/releases/tag/v1.0.3

- latest commit of https://github.com/b-harvest/gravity-dex/tree/add-liquidity-module-to-gaia branch (PR859)

Github Issue: https://github.com/tendermint/liquidity/issues/403 

Gaia PR: https://github.com/cosmos/gaia/pull/859





# Genesis settings 

`exported_genesis_with_height_6659211_sorted_origin.json` is exported by `gaiad export ` command on 6659211 height of `cosmoshub-4 ` network

the exported is already attached on this repo, so It could be skipped



### Unzip the attached genesis file 

`tar xvzf exported_genesis_with_height_6659211_sorted_origin.json.tar.bz2`

`cat exported_genesis_with_height_6659211_sorted_origin.json | shasum -a 256`

> a955b5752f2f0e5384a1e99323ad2ca6c9db58c7b605123bb5896693882136f4



`tar xvzf genesis.json.tar.bz2`

`cat genesis.json | shasum -a 256`

> 1da7c1b0358f1bbe26d4e15ba60eb9fc00aee32381ec0d0a35380510477a1a1d

### Copy the exported genesis for modifying params, accounts, validators states, or skip this step and use already modified genesis.json file

`cp exported_genesis_with_height_6659211_sorted_origin.json genesis.json`



### Set variables, Change pubkey and address of two validators and two users

```bash
EXPORTED_GENESIS=genesis.json
BINARY=gaiad
CHAIN_ID=cosmoshub-4
CHAIN_DIR=./data

# change validator1
sed -i '' 's%cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM=%qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU=%g' $EXPORTED_GENESIS
sed -i '' 's%B00A6323737F321EB0B8D59C6FD497A14B60938A%D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA%g' $EXPORTED_GENESIS
sed -i '' 's%cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6%cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve%g' $EXPORTED_GENESIS



# change validator2
sed -i '' 's%W459Kbdx+LJQ7dLVASW6sAfdqWqNRSXnvc53r9aOx/o=%oi55Dw+JjLQc4u1WlAS3FsGwh5fd5/N5cP3VOLnZ/H0=%g' $EXPORTED_GENESIS
sed -i '' 's%83F47D7747B0F633A6BA0DF49B7DCF61F90AA1B0%7CB07B94FD743E2A8520C2B50DA4B03740643BF5%g' $EXPORTED_GENESIS
sed -i '' 's%cosmosvalcons1s0686a68krmr8f46ph6fklw0v8us4gdsm7nhz3%cosmosvalcons10jc8h98awslz4pfqc26smf9sxaqxgwl4vxpcrp%g' $EXPORTED_GENESIS


# change user1 address for voting
# cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl to cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6
# Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8 to AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i
sed -i '' 's%cosmos1dnxfxad3ag26l298f9pfv6u43nlt0madl3qsgl%cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6%g' $EXPORTED_GENESIS
sed -i '' 's%Am5HzAWtsyvoQy49DyM4Q1sZiZL6UvTgKSJW4ERAhCR8%AsM0d7NDJ/oFn+/WkeQKO2QIWE3SNBccoRLkrCK14T/i%g' $EXPORTED_GENESIS


# change user2 address for swap
# cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx to cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
# A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g to ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7
sed -i '' 's%cosmos1z98eg2ztdp2glyla62629nrlvczg8s7f0tm3dx%cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9%g' $EXPORTED_GENESIS
sed -i '' 's%A6apc7iThbRkwboKqPy6eXxxQvTH+0lNkXZvugDM9V4g%ApDOUyfcamDmnbEO7O4YKnKQQqQ93+gquLfGf7h5clX7%g' $EXPORTED_GENESIS



```



### Change power of the validator and staking delegation to over 67% power locally 

```bash


# power, delegation, staking
sed -i '' 's%13488360679504%6013488360679504%g' $EXPORTED_GENESIS
sed -i '' 's%"power": "13488360"%"power": "603488360"%g' $EXPORTED_GENESIS
sed -i '' 's%274699904554428%6274699904554428%g' $EXPORTED_GENESIS
sed -i '' 's%"25390741.000000000000000000"%"6000000025390741.000000000000000000"%g' $EXPORTED_GENESIS
sed -i '' 's%"191488844"%"6191488844"%g' $EXPORTED_GENESIS


```



### Adjust some gov parameters for fast testing

- minimum deposit amount
- quorum
- threshold
- voting_period

```bash

# params
sed -i '' 's%"amount": "64000000",%"amount": "1",%g' $EXPORTED_GENESIS
sed -i '' 's%"quorum": "0.400000000000000000",%"quorum": "0.000000000000000001",%g' $EXPORTED_GENESIS
sed -i '' 's%"threshold": "0.500000000000000000",%"threshold": "0.000000000000000001",%g' $EXPORTED_GENESIS
sed -i '' 's%"voting_period": "1209600s"%"voting_period": "60s"%g' $EXPORTED_GENESIS


```





### Add test validators and users accounts, Init app directory

after that, need to manual setup config.toml, app.toml for peering the nodes each other for local network 

```bash
EXPORTED_GENESIS=genesis.json
BINARY=gaiad-42
CHAIN_ID=cosmoshub-4
CHAIN_DIR=./data


VAL_1_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val1
VAL_1_MNEMONIC="guard cream sadness conduct invite crumble clock pudding hole grit liar hotel maid produce squeeze return argue turtle know drive eight casino maze host"
VAL_1_KEY_NAME="val1"
VAL_1_MONIKER="Validator One"

VAL_2_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
VAL_2_MNEMONIC="friend excite rough reopen cover wheel spoon convince island path clean monkey play snow number walnut pull lock shoot hurry dream divide concert discover"
VAL_2_KEY_NAME="val2"
VAL_2_MONIKER="Validator Two"



# Validator 1

echo "Initializing $CHAIN_ID..."
$BINARY --home $VAL_1_CHAIN_DIR init test --chain-id=$CHAIN_ID

echo "Adding genesis accounts..."
echo $VAL_1_MNEMONIC | $BINARY --home $VAL_1_CHAIN_DIR keys add $VAL_1_KEY_NAME --recover --keyring-backend=test 


# Validator 2

echo "Initializing $CHAIN_ID..."
$BINARY --home $VAL_2_CHAIN_DIR init test --chain-id=$CHAIN_ID

echo "Adding genesis accounts..."
echo $VAL_2_MNEMONIC | $BINARY --home $VAL_2_CHAIN_DIR keys add $VAL_2_KEY_NAME --recover --keyring-backend=test 



# Add users' key

USER_1_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
USER_1_MNEMONIC="render hire dirt bulk huge goat jungle number wear method check during menu goat accident scan noise nerve below target resource digital column flee"
USER_1_KEY_NAME="user1"

#- name: user1
#  type: local
#  address: cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6
#  pubkey: cosmospub1addwnpepqtpngaangvnl5pvlaltfreq28djqskzd6g6pw89pztj2cg44uyl7y6xgev8


USER_2_CHAIN_DIR=$CHAIN_DIR/$CHAIN_ID/val2
USER_2_MNEMONIC="junk appear guide guess bar reject vendor illegal script sting shock afraid detect ginger other theory relief dress develop core pull across hen float"
USER_2_KEY_NAME="user2"

#- name: user2
#  type: local
#  address: cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9
#  pubkey: cosmospub1addwnpepq2gvu5e8m34xpe5aky8wemsc9fefqs4y8h07s24cklr8lwrewf2lklysaen

echo $USER_1_MNEMONIC | $BINARY --home $USER_1_CHAIN_DIR keys add $USER_1_KEY_NAME --recover --keyring-backend=test
echo $USER_2_MNEMONIC | $BINARY --home $USER_2_CHAIN_DIR keys add $USER_2_KEY_NAME --recover --keyring-backend=test



# start validator nodes 
HOME1=./data/cosmoshub-4/val1
HOME2=./data/cosmoshub-4/val2

gaiad-42 start --home $HOME1 --x-crisis-skip-assert-invariants
gaiad-42 start --home $HOME2 --x-crisis-skip-assert-invariants


```





### Diff modified genesis.json

`diff exported_genesis_with_height_6659211_sorted_origin.json genesis.json -u`

```diff
--- exported_genesis_with_height_6659211_sorted_origin.json	2021-06-21 18:15:44.000000000 +0900
+++ genesis.json	2021-06-22 15:21:16.000000000 +0900
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





# Upgrade Test 

### Submit and vote upgrade proposal

```bash
# using gaia v4.2.1 as gaiad-42 binary
gaiad-42 version
# > v4.2.1

# submit upgrade proposal, with upgrade-height
gaiad-42 tx gov submit-proposal software-upgrade Gravity-DEX \
--deposit 1000uatom \
--title Gravity-DEX \
--upgrade-height 6659300 \
--upgrade-info v4.3.0-testhash \
--description testdescription \
--from user1 \
--keyring-backend test \
--chain-id cosmoshub-4 \
--home data/cosmoshub-4/val2 \
--node tcp://127.0.0.1:36657


# vote yes for the proposal, voting period, quorum and threshold is modified for test
gaiad-42 tx gov vote 50 yes -y \
--from user1 \
--keyring-backend test \
--chain-id cosmoshub-4 \
--home data/cosmoshub-4/val2 \
--node tcp://127.0.0.1:36657 

# query proposal for checking is passed
gaiad-42 query gov proposal 50 \
--chain-id cosmoshub-4 \
--home data/cosmoshub-4/val2 \
--node tcp://127.0.0.1:36657 


```

result of the proposal

```json
content:
  '@type': /cosmos.upgrade.v1beta1.SoftwareUpgradeProposal
  description: testdescription
  plan:
    height: "6659300"
    info: v4.3.0-testhash
    name: Gravity-DEX
    time: "0001-01-01T00:00:00Z"
    upgraded_client_state: null
  title: Gravity-DEX
deposit_end_time: "2021-07-06T02:36:15.536556Z"
final_tally_result:
  abstain: "0"
  "no": "0"
  no_with_veto: "0"
  "yes": "72642221"
proposal_id: "50"
status: PROPOSAL_STATUS_PASSED
submit_time: "2021-06-22T02:36:15.536556Z"
total_deposit:
- amount: "1000"
  denom: uatom
voting_end_time: "2021-06-22T02:37:15.536556Z"
voting_start_time: "2021-06-22T02:36:15.536556Z"
```





### When upgrade-height, the node is halted for upgrade,

### Restart node using new gaiad gravity-dex version,

```bash

# using gaiad as gravity-dex
gaiad version
# > add-liquidity-module-to-gaia-022f6eea5ec93995bc175c556e37e30cae22e1f6

gaiad start --home $HOME1 --x-crisis-skip-assert-invariants
gaiad start --home $HOME2 --x-crisis-skip-assert-invariants

```



### Test functionality of added liquidity module

```bash

# query liquidity parameters
gaiad query liquidity params \
--node tcp://127.0.0.1:36657


# create liquidity pool 
gaiad tx liquidity create-pool 1 1000000ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C,1000000uatom \
--from user2 --keyring-backend test \
--home data/cosmoshub-4/val2 \
--chain-id cosmoshub-4 \
--gas 300000 \
--node tcp://127.0.0.1:36657


# query created liquidity pool
gaiad query liquidity pools \
--node tcp://127.0.0.1:36657


# swap request 
gaiad tx liquidity swap 1 1 100000uatom ibc/1BE91D67775723D3230A9A5AC54BB29B92A5A51B4B8F20BBA37DF1CFA602297C 0.019 0.003 \
--from user2 --keyring-backend test \
--home data/cosmoshub-4/val2 \
--chain-id cosmoshub-4 \
--node tcp://127.0.0.1:36657


gaiad query auth account cosmos1w323u2q2f9h8nnhus0s9zmzfl4a3mft4xse2h6 \
--node tcp://127.0.0.1:36657 

gaiad query auth account cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9 \
--node tcp://127.0.0.1:36657 


# query balance after swap transacted 
gaiad query bank balances cosmos1wvvhhfm387xvfnqshmdaunnpujjrdxznr5d5x9 \
--node tcp://127.0.0.1:36657 
```



result of liquidity module params

```json
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



result of created liquidity pool

```json
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

