# token-unidirectional-audit
Contract audit repo for machinomy token unidirectional payment channels


## What is this?

Kava Labs is having the machinomy token unidirectional payment channel contract audited by [Quantstamp](https://quantstamp.com/). This repository will document the original contract and any updates that are made as a result of the audit, as well as the audit report and any other documents produced by the audit.

## What is the purpose of the audit?
Kava Labs is actively integrating ERC20 tokens into [Interledger](https://medium.com/interledger-blog/interledger-how-to-interconnect-all-blockchains-and-value-networks-2078c6aa0c0d). To fascilitate these integrations, we need a payment channel contract that supports ERC20s. We like the machinomy contract because of its simplity and well-written code. Given the potential security risks associated with contracts that interact with ERC20 tokens, we felt it prudent to have the contract audited for general security, as well as to make it future proof, so that it can support the functionality we know we will need.

##  Original code

We pulled the latest version `TokenUnidirectional.sol` [contract](https://github.com/machinomy/machinomy/commit/5e0b5dc88e699e5b28d095ab47b58de8d6d1bd95) from the machinomy monorepo on 02/08/2019. The contract is currently deployed to the Ropsten and Kovan testnets, but is not deployed on mainent, as far as we are aware.

### Contract Functionality
The ideal functionality of the contract is described here:
https://github.com/machinomy/machinomy/blob/97ad47d07a7989b1f38466b1d4b8414e3a978b24/doc/Contract.md


## Goals of the audit

1. Identify and remediate any security vulnerabilities with the token unidirectional contract.
2. In cases where the sender of the channel initiates settlement, modify the contract functionality to allow third parties to iniate the `claimIfSettling` function on behalf of the channel receiver. The resolution of the `claimIfSettling` function should still send the funds to the receiver address. This allows for future integrations with watchtower services.

In terms of the contract functionality described by machinomy (see [link](#contract-functionality)), we are proposing changing:
  > Settlement process that is initiated by the sender. It is driven by an assumption of an unresponsive receiver. The sender starts the settlement process by calling `startSettle` method, provides the expected payout to the receiver. If the latter does not respond indeed, the sender could finish the process by calling `finishSettle`. There is no constraint the payout amount provided by the sender. It could be zero. To mitigate that,  `createChannel`  accepts `settlementPeriod` param that specifies how long the channel waits for the receiver responds. The receiver is free not to accept payment and provide service, if she finds that period inadequate.

  To
  > Settlement process that is initiated by the sender. It is driven by the assumption of an unresposive receiver. The sender starts the settlement process by calling `startSettle` method, and provides the expected payout to the receiver. **During the `settlementPeriod` a receiver, or anyone possessing a valid claim, may call `claimIfSettling` with the valid claim and disperse funds according to the claim.** If no one responds, the sender could finish the process by calling `finishSettle`. There is no constraint on the payout amount provided by the sender. It could be zero. To mitigate that,  `createChannel`  accepts `settlementPeriod` param that specifies how long the channel waits for the receiver responds. The receiver is free not to accept payment and provide service, if she finds that period inadequate.

## Findings of the audit
This audit is being conducted independently from the machinomy project. Changes made as a result of the audit will be in this repo, and not necessariliy in the machionmy repo. We will communicate any major security vulnerabilities that are found to the machinomy team, and make any code changes to the contract available under the Apache 2.0 license.
