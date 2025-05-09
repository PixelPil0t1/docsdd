---
title: Using the Kit
---

:::warning
[ContractKit has been sunset](https://forum.celo.org/t/sunsetting-contractkit/5337) for external use. Please use viem or wagmi for connecting with the blockchain. 

Check out the [migration guide](./migrating-to-viem.md) for updating your dapp from ContractKit to viem.

To learn more visit the [Celo forum](https://forum.celo.org/t/sunsetting-contractkit/5337). 
:::

## Setting Default Tx Options

`kit` allows you to set default transaction options:

```ts
import { CeloContract } from "@celo/contractkit";

let accounts = await kit.web3.eth.getAccounts();
kit.defaultAccount = accounts[0];
// paid gas in cUSD
await kit.setFeeCurrency(CeloContract.StableToken);
```

## Set `feeCurrency` for a transaction

You can set the `feeCurrency` for each transaction individually by setting the `feeCurrency` field in the `.send()` method. The `feeCurrency` field accepts contract addresses of whitelisted fee currencies.

```js
let cUSDcontract = await kit.contracts.getStableToken();
let cUSDtx = await cUSDcontract
  .transfer(someAddress, amount)
  .send({ feeCurrency: cUSDcontract.address });
```

## Getting the Total Balance

This method from the `kit` will return the CELO, locked CELO, cUSD and total balance of the address

```ts
let totalBalance = await kit.getTotalBalance(myAddress);
```

## Deploy a contract

Deploying a contract with the default account already set. Simply send a transaction with no `to:` field. See more about sending custom transactions below.

You can verify the deployment on the [Alfajores block explorer here](https://celo-alfajores.blockscout.com/). Wait for the receipt and log it to get the transaction details.

```ts
let bytecode = "0x608060405234..."; // compiled Solidity deployment bytecode

let tx = await kit.sendTransaction({
  data: bytecode,
});

let receipt = tx.waitReceipt();
console.log(receipt);
```

## Sending Custom Transactions

The Celo transaction object is not the same as Ethereum's. There is a new optional field present:

- `feeCurrency` (address of the ERC20 contract to use to pay for gas)

`feeCurrency` enables transaction fees to be paid in currencies other than CELO. The currently supported fee currencies are CELO, cUSD and cEUR. You can specify the currency by passing the contract address of the currency you would like the transaction fees to be paid in.

Celo accepts original Ethereum type transactions as well, so you can use Ethereum signing tools (like Metamask) as well as Celo specific wallets and tools. You can read more about these transaction formats in [CIP 35](https://github.com/celo-org/celo-proposals/blob/master/CIPs/cip-0035.md).

For a raw transaction:

```ts
const tx = kit.sendTransaction({
  from: myAddress,
  to: someAddress,
  value: oneGold,
});
const hash = await tx.getHash();
const receipt = await tx.waitReceipt();
```

When interacting with a web3 contract object:

```ts
const goldtoken = await kit._web3Contracts.getGoldToken();
const oneGold = kit.web3.utils.toWei("1", "ether");

const txo = await goldtoken.methods.transfer(someAddress, oneGold);
const tx = await kit.sendTransactionObject(txo, { from: myAddress });
const hash = await tx.getHash();
const receipt = await tx.waitReceipt();
```

## Interacting with Custom contracts

You can use ContractKit to interact with any deployed smart contract, provided you have the contract address and the [ABI](https://docs.soliditylang.org/en/latest/abi-spec.html). To do so, you will initialize a new `web3` Contract instance. Then you can call functions on the contract instance to read state or send transactions to update the contract. You can see some code snippets below.

```ts
let cUSDcontract = await kit.contracts.getStableToken();
let contract = new kit.connection.web3.eth.Contract(ABI, address); // Init a web3.js contract instance
let name = await instance.methods.getName().call(); // Read contract state

// Specifying the 'from' account and 'feeCurrency' is optional
// Transactions with an unspecified feeCurrency field will default to paying fees in CELO
const tx = await instance.methods
  .setName(newName)
  .send({ from: account.address, feeCurrency: cUSDcontract.address });
```

## Selling CELO only if the rate is favorable

```ts
// This is at lower price I will accept in cUSD for every CELO
const favorableAmount = 100;
const amountToExchange = kit.web3.utils.toWei("10", "ether");
const oneGold = kit.web3.utils.toWei("1", "ether");
const exchange = await kit.contracts.getExchange();

const amountOfcUsd = await exchange.quoteGoldSell(oneGold);

if (amountOfcUsd > favorableAmount) {
  const goldToken = await kit.contracts.getGoldToken();
  const approveTx = await goldToken
    .approve(exchange.address, amountToExchange)
    .send();
  const approveReceipt = await approveTx.waitReceipt();

  const usdAmount = await exchange.quoteGoldSell(amountToExchange);
  const sellTx = await exchange.sellGold(amountToExchange, usdAmount).send();
  const sellReceipt = await sellTx.waitReceipt();
}
```

## Buying all the CELO I can, with the cUSD in my account

```ts
const stableToken = await this.contracts.getStableToken();
const exchange = await this.contracts.getExchange();

const cUsdBalance = await stableToken.balanceOf(myAddress);

const approveTx = await stableToken
  .approve(exchange.address, cUsdBalance)
  .send();
const approveReceipt = await approveTx.waitReceipt();

const goldAmount = await exchange.quoteUsdSell(cUsdBalance);
const sellTx = await exchange.sellDollar(cUsdBalance, goldAmount).send();
const sellReceipt = await sellTx.waitReceipt();
```
