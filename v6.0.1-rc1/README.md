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

The genesis file `genesis.json.tar.bz2` is already prepared for this option. It is obtained by proceeding from **Step 1~5** in **Option 2** below. Uncompress the genesis file and use it as the genesis data to simulate the upgrade test. You can directly go to **Step 5** to proceed if you choose to go with this option.

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

In this step, we swap 2 validators with two new accounts in the genesis file and we also modify some parameters to improve test efficiency. The following mnemonics for the two accounts and files that contain validators' consensus keys are already prepared. 

- `priv_validator_key_val1.json`: validator1's consensus key

- `priv_validator_key_val2.json`: validator2's consensus key

- `guard cream sadness conduct invite crumble clock pudding hole grit liar hotel maid produce squeeze return argue turtle know drive eight casino maze host`: mnemonics for validator1

- `friend excite rough reopen cover wheel spoon convince island path clean monkey play snow number walnut pull lock shoot hurry dream divide concert discover`: mnemonics for validator2

- `render hire dirt bulk huge goat jungle number wear method check during menu goat accident scan noise nerve below target resource digital column flee`: `user1` account that is used for sending governance proposal to the network

- `junk appear guide guess bar reject vendor illegal script sting shock afraid detect ginger other theory relief dress develop core pull across hen float`: `user2` account that is used to test command-line interfaces (CLIs) for the liquidity module

```bash
# Change chain id
sed -i '' 's%"chain_id": "cosmoshub-4",%"chain_id": "cosmoshub-4-upgrade-testnet-2002",%g' genesis.json

# Substitue "Certus One" validator1
sed -i '' 's%cOQZvh/h9ZioSeUMZB/1Vy1Xo5x2sjrVjlE/qHnYifM=%qwiUMxz3llsy45fPvM0a8+XQeAJLvrX3QAEJmRMEEoU=%g' genesis.json
sed -i '' 's%B00A6323737F321EB0B8D59C6FD497A14B60938A%D5AB5E458FD9F9964EF50A80451B6F3922E6A4AA%g' genesis.json
sed -i '' 's%cosmosvalcons1kq9xxgmn0uepav9c6kwxl4yh599kpyu28e7ee6%cosmosvalcons16k44u3v0m8uevnh4p2qy2xm08y3wdf92xsc3ve%g' genesis.json

# Substitue "Binance Staking" validator2
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

### (Option 2) Step 3. 

### (Option 2) Step 4. 

### (Option 2) Step 5. 

