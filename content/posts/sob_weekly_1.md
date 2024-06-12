+++
title = "SoB weekly report!"
date = "2024-11-06"
description = "Weekly report of the Summer of Bitcoin work"
[taxonomies]
tags = ["blog post","Summer of Bitcoin", "Work", "Floresta", "Rust", "Bitcoin"]
+++

So, Summer of Bitcoin, the reason for my lack of sleep in the past month.

<iframe src="https://giphy.com/embed/udoce5CHY5kZSY031S" width="480" height="271" style="" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/ThisIsMashed-animation-animated-mashed-udoce5CHY5kZSY031S">via GIPHY</a></p>

In short words, there are some college programs of computer science that take the “summer” time of students to get’em to work and try to help them to enter the programming industry… We really need this help, after the pandemics and the six months courses that just spammed made the junior entry kinda bloated with some rookies that learned to write some code on **python** or **yavascript** and are trying to find a job. That's not my case for a bunch of reasons (Web Development never was my thing by example), anyway, just some bs that i have to complain about because a lot of people got fooled by those courses.

Summer of Bitcoin follows the same logic as the “Summer” programs:
 
1. Pass a test
2. Work in a project
3. win money
4. get referred to a job

Because this is a weekly and technical blog post about my work on Floresta, I'll share my personal experience another day… But was and is currently being just **awesome**.

QUICK TECHNOLOGY CONCEPTS RESUME!<br>

* Floresta, a full bitcoin node built in rust that uses Utreexo for validation.

* Utreexo is a merkle-tree based accumulator designed to store bitcoin chainstate.

* Rust is a programming language.

* Bitcoin is Bitcoin.

So, a brief story about rust and its Async-Runtime: **nuh huh**. Exactly, it does not have one but, a good language needs parallelism (I'd love to talk about parallelism) and the Rust foundation let this job for the community, helped with some types and traits(something like interfaces in c#), but, at the end, the **Rust language**, theoretically, does not have a specific Async-Runtime. That causes some performance and behavior differences on the code execution while using async at all, that was the case for Floresta that is actually using Async-std that loses in popularity and performance to tokio(Another Async-Runtime). With that said, one of the ideas that, [Davidson](https://github.com/Davidson-Souza) (Maintainer of the Floresta) had proposed to us, interns, was the change of the Async-std to Tokio and i tried to propose for that idea, made a proposal and got accepted. But, the maintainer has more important things to implement at floresta: 
The Consensus Rules.

**Bitcoin** works with blocks “in a linked list” that movement the transactions and change the chainstate but, its decentralization requires that everyone agree with the protocol, this protocol is the consensus rules that i mentioned earlier and its organized in “bips” at the bitcoin repository(a bip is added there when it's approved by the community with some kind of democracy that i don't know so much about to tell you). With that knowledge, I need to map which rules floresta already has and which don't, implement them and add some test batteries so we can ensure that floresta will never validate a wrong block. 


> Why is that important ? even if the floresta node validates a wrong block the whole chain will deny it, right ?

Very clever my guy, but nothing stops someone in the future who wants, for any reason, to create a whole blockchain for itself(like some web3 shit) with just florestas, remember that floresta is made to be cheap to run ? (Pretend that i told you that) With some very good tweaks floresta can be even cheaper than already is compared to bitcoin OG node, I will make another blog post talking specifically about floresta and its technologies, and if i didn't even implement the basics of the project is like:
**“What the fuck are we doing?!?”**

There’s no point in making bad software, like everything made in nodejs.

Truths apart, after some reading at the bitcoin core and at the floresta codebase, here’s the map about the consensus rules:

- In block transaction structure validation. ( Verifies if it has, at least, one transaction per block, if it has coinbase, etc...)

- Script validation. (by libbitcoin)
- PrevOut validation. (If it has a valid PrevOut).
- The lacking Rules are:
1. Values validation.
2. Output value is equal or less than total input (Implemented, just needs tests).
3. None of the values is negative.
4. Any value cannot be greater than the number in satoshis of the maximum possible bitcoins. (MAX_MONEY = 21000000 * 100 000 000).
5. Double Input Spend. (The case that has more than one equal input, mostly referring to the same utxo)
6. Inputs are spent, exactly, the declared satoshis including fee and UTXO’s.

7. Size validation

8. no more than 80000 sigops.

9. ScriptPubKey Size has to be >2 and <520 bytes
if coinbase, scriptSig has to be >2 and <100 bytes, unless it's taproot, in that case the scriptsig has no limits
10. Absolute and Relative Locktimes defined by sequence.
In regards to transactions, that’s it(And you can see more on the draft that I`m building, there’s the place where I maintain everything updated: [Transactions Consensus Rules](https://github.com/Davidson-Souza/Floresta/pull/165)). But now, blocks rules:

The block header consist on(in this order):

* Version

* Previous Block Hash

* Merkle Root

* TimeStamp

* Bits

* Nonce

* Txcount

1. First Tx has to be a coinbase
2. Coinbase needs a witness commitment
3. Coinbase value is, exactly, bitcoin prize (can vary at the time that’s calculated) + accumulated fee
4. Tx’s has to be ordered in chronological order
5. Size has to be valid (not more, but less than 4 000 000  vbytes).

May be it, one or another rule can appear and you can check the actual progress in this [Transactions Consensus Rules](https://github.com/Davidson-Souza/Floresta/pull/165) 

> And how we are sure that we implemented the rules right ? 

We just have to test it with the bitcoin core’s test battery that’s built in python but we can implement it on both Rust and python.

To enter the test topic with the right thoughts i want to introduce another project that is related to Vinteum org:

Bitcoin fuzz maintained by [Bruno Garcia](https://github.com/brunoerg), the vinteum’s director of education, he’s a cool guy but let’s talk about his project and literally repeating what he said to me, Bitcoin Fuzz generate a bunch of bitcoin related data and smashes the target project with them, with this you can find a lot of bugs… hope that became a standard on the bitcoin environment, that lil’dude(The project, not bruno) can give you more than 100% test coverage and ensures that your bitcoin related project is doing exactly what you want and we three: me, bruno and davidson are talking a long time about using it on floresta!
The fuzzing will be at the end of the whole project because we have some other tests to implement together with the consensus rules: Functional and Unitary on Rust and the bitcoin core(related to consensus obviously).

Phew
That's it! Hope next week I can bring more technical details for y’all, the project is walking slowly at the end of march and beginning of june because of my college, the end of semester is full of activities and some recaps that i need to do with some colleagues.
Until (approximately) next week!


