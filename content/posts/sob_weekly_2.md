+++
title = "SoB weekly 2!"
date = "2024-07-25"
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

**Addresses with value** are called `Unspent Transaction Ouptut` or shortly `UTXO`, everytime that we use an address we burn it and never use it anymore, right because its easier and safer to validate like this(and a wallet can have a bunch of them, like a lot). Actually you can let some value on a used address(if you hand-write your transaction) but any Node will not agree that it can be used again so we can shorten the `ChainState` storage and ensure the exposed data while publishing the transaction offers no risk to us, people that are making transactions.
The Rule for it is simple, if its the second time that were seeing te same `UTXO` as a input the node can consider it valid.
> Yes, second time, it was a output before! `Outputs` that has any value became a `Input` while spending, beeing called a `UTXO` until spent.

`Floresta` uses `Utreexo` to store the `ChainState`(all the `UTXO`s available in the blockchain), `Utreexo` being a accumulator of `UTXO`s we simply remove the spending `UTXO` from it. If any transactions tries to spend it again we'll not find it. Invalidating the input and the whole transaction.

The values logic are simple, you cannot spend more than the value included on inputs and the more you reserve for miners faster you transaction will be included in a block(If too low your coins will be stuck, but we already have a way to solve this using sequences and timelocks).

So we can assert that the `Spending Address`(Input or UTXO for now on) belong to someone thats trying to spend the address, we have a mini script language amazingly called **Bitcoin Script** (The bitcoin comunitty is not good with names) the language is intentionally not [Turing Complete](https://en.wikipedia.org/wiki/Turing_completeness) for safety reasons, but it more than enough to prove to someone that you have certain secret without reveling it, this secret being the private key that corresponds the bitcoin address.

> See [Keys & Addresses](https://learnmeabitcoin.com/beginners/guide/keys-addresses/) by Greg Walker. 

**Bitcoin Script** have it constraints too, so we have to validate it. The script is present on two camps across a transaction: `ScriptSig` and `ScriptPub`. You can think of `ScriptPub` as a lock and the `ScriptSig` as a key, anyone has access to both, but only the owner of the address can generate and insert the valid `ScriptSig` for the corresponding Addres, `Bitcoin Script` was developed with this functionality in mind, regards its use for a lot of things like [`TapRoot`](https://realeinherjar.github.io/2023-09-08-taproot/). 

Enough explanation, the constraints about the `Bitcoin Script`:
- Besides `Taproot`, it cannot have more than 520 bytes of lenght.

- And cannot do more than 80_000 [Signature Operation](https://bitcoin.stackexchange.com/questions/117356/what-is-sigop-signature-operation).

For a transaction be valid, all of them has to be executed and validated, but since reimplement all of the `Bitcoin Script` can carry a lot of bugs, we use the [`Rust-Bitcoin`](https://github.com/rust-bitcoin/rust-bitcoin)`s implementation that seems to be more than enough.

After that, the camps that still needs some validation is [`Locktime`](https://learnmeabitcoin.com/technical/transaction/locktime/) and [`Sequence`](https://learnmeabitcoin.com/technical/transaction/input/sequence/), but its implementation got blocked because we need the `Median Time Past` of the block and Floresta doesnt offer that info so easily, me and the mentor of the project agreed to let this validation be a separate PR and i will talk about it in a dierent blog post because it includes the asynchronous block validation of Floresta.

## After that

I still need to work on block validation, which is a little bit easier, this blog post is already enough for the " WeEKlY UpDATe " now you are apart of my work so far! Hope that you, reader, liked this post. Thanks for reading!