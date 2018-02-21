---
layout: post
title: "Democratically Mutable DAO: A proposition for a fairer blockchain governance"
description: ""
date: 2018-02-21
tags: [blockchain, utility-token, utility, ampnet, ethereum, ether, smart contract, dao, dmdao, democracy, consensus, voting, rules, mutability, immutability, evm, solditiy]
comments: true
---

# Introduction

In my [last article](https://mislavjavor.github.io/2018-02-03/a-case-against-some-utility-tokens-on-the-blockchain/), I've discussed some of the most common types of blockchain tokens and the disadvantages of making everything a utility token. 

A critique is not worth much without offering a solution, so in this article I'll focus on a legacy inspired company governance on the blockchain. 

Some of the main traits a good token should have:
1. Usefulness: It seems silly to even say that you should have a useful token, but with so many solutions desperately grabbing for ICO money by "inventing" token solutions. It's a necessary-to-mention prerequisite
2. Fairness: Fairness is a tricky concept in classical finance, but you could sum it up to two sub-points
    1. Voting rights: The investors have a right to vote on certain company issues and veto others proportional to their holdings of shares/tokens
    2. Dividends: The investors have a right to a portion of the company revenue (this is not applicable to utility tokens, since you're basically not taking in revenue in anything other than token appreciation).
3. Upgradeability/Reversibility: This is a controversial thing to say the least when it comes to blockchain tokens ([Just take a look at the ETC/ECC shitshow](https://en.wikipedia.org/wiki/Ethereum_Classic)). However, I am of the opinion that no person or a group of persons can ever fully anticipate and prepare for future events. Highly precise approximations are possible (and reasonably common), but a system in which *code-is-law* in the sense that the system should work one way forever is impossible. Humans change over time and rivalries form, the economic climate changes and most importantly *the law changes*. This is why you must ensure some degree of upgradeability to your contracts. 

# Breakdown

Points 1. and 2. are more-or-less solved by implementing classical DAO (if you don't know what a DAO is, click [here](https://en.wikipedia.org/wiki/Decentralized_autonomous_organization)) systems. Point 2.2. especially if there is a system of transaction fees for the DAO. 

I won't linger on these points too much, since we already have fully working solutions for them. Point 3., however, is very tricky. 

How do you ensure both upgradeability and stability. In other words, how do you make sure that people who are using your contract have trust that the entire system they've put their livelihoods in won't fall under their feet *and* simultaneously ensure that the contract is flexible enough to adapt to changing circumstances? 

Turns out we've been solving that problem in classical law and governance for millenia. The best solution so far? 

## Democracy!

Out of all the other systems, democracy has shown incredible resilience to obsolescence. It's reasonably quick moving for changes to be implemented, but not so quick that it becomes destructive. 

The key reason for the effectiveness of democratic governance is it's emphasis on personal responsibility. Every decision made, has an influence on the life of the person that made it. 

This optimization technique has some drawbacks (like for instance, priority miscalculation leading to political and religious extremism), but they are generally outnumbered by the positive aspects. 

## *Limited* democracy

Since our DAOs are companies and not countries, the models which work for one aren't necessarily going to translate properly to the other. 

One *must* count on the fact that a portion of token holders and investors won't have the will or the competence to govern the system. 

This is specially the case with complex systems funded by ICOs. It's unreasonable to expect all of your investors (including people to whom your project is a minuscule part of their portfolio) to be bothered to research the latest news from your field. 

This problem is easily solved by limiting the democratic consensus to people who apply to vote *and* satisfy certain conditions. 

For example, the conditions for investors to be allowed to vote could be:
- Has been holding the token for over a year (prevents daytraders from usurping the voting system)
- Has *at least* a n% stake in the network (prevents uninvolved investors)

Also, another condition must be put in place to ensure that this system is not abused:
- Every trade of the token removes the voting rights from the token. 

This is to make sure that when the token is sold, the new owner must go through the same vetting process as the old one. 

These rules, of course, aren't the same for everyone and it is expected that every project would have a different set of rules. Also, the rules are subject to change by using the same DAO voting mechanism. 

### This means that the DAO is able to change itself democratically, dynamically adapting to market conditions. 

## Mutability

Now that we have covered *who* can vote, we need to cover *what they can vote on*. In classical DAOs, certain variables can be changed, new propositions can be made and acted upon accordingly. This system proves itself to be good enough for short term DAO governance. 

One thing that should scare DAOs (potentially - doesn't apply to all DAOs) is the *long-term* ability to provide quality governance. Specially since we're in a very early stage of the technology and standards, as well as laws regarding those standards, change almost daily (not to mention differ from jurisdiction to jurisdiction).

### The novel idea is to allow the entire contract to be democratically changed, *including* the rules governing voting for the changes themselves

In other words while we're working with *limited* democracy, we're working with *unlimited* mutability. 

This allows for various circuit breaker mechanisms (in case ICOs or wallets go wrong), allows for long-term plans of the network to develop step-by-step all without going through the complicated process of getting fuzzy real-world user consensus. 

The only thing that *never* changes, is the contract address on the ethereum network. All other things are subject to modification.

Every function of the smart contract can have its own limitations on change-consensus rules and the big functions can and should have extremely strict voting rules. 

For example a function changing some well-established basic functionality should have a consensus rating of >50% and a transition period of months instead of minutes. 

Luckily for us, the EVM (Ethereum Virtual Machine) is a turing-complete contraption so all of this can be implemented.

## Conclusion

By structuring your dApp as a Democratically Mutable DAO (DMD?), you're ensuring community control and market adaptability whilst retaining a degree of stability influenced by the inertia of passing a plurality vote.

Thus DMDs satisfy legacy rules of fairness whilst using the power of the blockchain to streamline governance.
