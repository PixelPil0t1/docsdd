---
title: Celo Core Contracts (Wrapper/Registry)
description: How to interact with CELO assets using the wrapper and registry Celo Core Contracts.
---

# Core Contracts (Wrapper/Registry)

How to interact with CELO assets using the wrapper and registry Celo Core Contracts.

:::warning
[ContractKit has been sunset](https://forum.celo.org/t/sunsetting-contractkit/5337) for external use. Please use viem or wagmi for connecting with the blockchain. 

Check out the [migration guide](./migrating-to-viem.md) for updating your dapp from ContractKit to viem.

To learn more visit the [Celo forum](https://forum.celo.org/t/sunsetting-contractkit/5337). 
:::

---

## Interacting with CELO & cUSD

celo-blockchain has two initial coins: CELO and cUSD (Mento stabletoken).
Both implement the ERC20 standard, and to interact with them is as simple as:

```ts
const goldtoken = await kit.contracts.getGoldToken();

const balance = await goldtoken.balanceOf(someAddress);
```

To send funds:

```ts
const oneGold = kit.web3.utils.toWei("1", "ether");
const tx = await goldtoken.transfer(someAddress, oneGold).send({
  from: myAddress,
});

const hash = await tx.getHash();
const receipt = await tx.waitReceipt();
```

To interact with cUSD, is the same but with a different contract:

```ts
const stabletoken = await kit.contracts.getStableToken();
```

## Interacting with Other Celo Contracts

Apart from GoldToken and Mento stabletokens, there are many core contracts.

For the moment, we have contract wrappers for:

- Accounts
- Attestations
- BlockchainParameters
- DobleSigningSlasher
- DowntimeSlasher
- Election
- Escrow
- Exchange (Uniswap kind exchange between Gold and Stable tokens)
- GasPriceMinimum
- GoldToken
- Governance
- LockedGold
- Reserve
- SortedOracles
- Validators
- StableToken

## A Note About Contract Addresses

Celo Core Contracts addresses, can be obtained by looking at the `Registry` contract.
That's actually how `kit` obtain them.

We expose the registry api, which can be accessed by:

```ts
const goldTokenAddress = await kit.registry.addressFor(CeloContract.GoldToken);
```

## Accessing web3 contract wrappers

Some user might want to access web3 native contract wrappers.
_We encourage to use the Celo contracts instead to avoid mistakes._

To do so, you can:

```ts
const web3Exchange = await kit._web3Contracts.getExchange();
```

We expose native wrappers for all Web3 contracts.

The complete list is:

- Accounts
- Attestations
- BlockchainParameters
- DoubleSigningSlasher
- DowntimeSlasher
- Election
- EpochRewards
- Escrow
- Exchange
- FeeCurrencyWhiteList
- GasPriceMinimum
- GoldToken
- Governance
- LockedGold
- Random
- Registry
- Reserve
- SortedOracles
- StableToken
- Validators

## Debugging

If you need to debug `kit`, we use the well known [debug](https://github.com/visionmedia/debug) node library.

So set the environment variable `DEBUG` as:

```bash
DEBUG="kit:*,
```
