+++
title = "SoB weekly 2!"
date = "2024-15-07"
description = "Another SoB (Kinda) weekly update! I`ll talk about the last merged PR and the new one"
[taxonomies]
tags = ["blog post","Summer of Bitcoin", "Work", "Floresta", "Rust", "Bitcoin"]
+++
So, Almost a month has passed since the last post about my internship...
*sorry meme gif
Worth Waiting but here some quick updates:
- The Transaction Rules PR was merged! Floresta now validates all the transaction rules but the timelock and sequence one. This was a regression, but has a good reason. Read More Here.

- Now working on the Block Rules PR. This one is a bit more complex right because it involves a *Desing Change* on `Floresta-Chain`. Read more here.

## Transaction Rules
So, Transactions exist to address value to another owner, we use for more things nowaday but it structure was develop to reach this purpose. To make a transaction, we take our address with value, specify the value reserving a fee for the miner, tell the receiver address, prove that the spending address is ours and wait for the transaction to be included on some block.

I'll superficially pass trough this steps explaining the rules that they have to accomplish.

*Addresses with value* are called `Unspent Transaction Ouptut` or shortly `UTXO`, everytime that we use an address we burn it and never use it anymore, right because its easier to validate like this(and we have a bunch of them, like a lot). Actually you can let some value on a used address(if you hand-write your transaction) but any Node will agree that it can be used again so we can shorten the `ChainState` storage and ensure the exposed data while publishing the transaction offers no risk to us.
The Rule for it is simple, if its the second time that were seeing te same `UTXO` the node can consider it valid.
> Yes, second time, it was a output before! `Outputs` became `Inputs` while spending, beeing called a `UTXO` until are spent.

`Floresta` uses `Utreexo` to store the `ChainState`(all the `UTXO`s available in the blockchain), `Utreexo` being a accumulator of `UTXO`s we simply remove the spending `UTXO` from it. If any transactions tries to spend it again we'll not find it. Invalidating the whole transaction.

The values logic are simple, you cannot spend more than the value included on inputs and the more you reserve for miners faster you transaction will be included in a block(If too low your coins will be stuck, but we already have a way to solve this).
