# Lido on Ethereum Validator Exits Guideline

```markdown!
STATUS: V2.0 (Q3/2024 - Introduction of Triggerable Exits: Extension to SDVTM & CSM)
```

## A. Purpose
[Validator exits]() may be utilized by fully-functioning [staking protocols](), including [Lido on Ethereum (LoE)](), for the following reasons:
* To satisfy [withdrawal redemption requests]() from [users]() -- e.g. if requests are larger than the amount of [stake]() that can be made available for redemption via only [partial withdrawals]() or [skimming]()
* To reallocate stake across the [node operator (NO)]() set -- move stake from one operator to another for a variety of reasons, e.g. substandard performance as per the guiding principles from the [Lido Operator Set Strategy](https://research.lido.fi/t/lido-operator-set-strategy/2139) or away from [unbonded validators]()
* To rotate [signing keys]() for some [validators]() without changing the stake allocation
* To eject validators of an operator severely misbehaving and / or repeatedly violating the rules of the protocol to maintain its security

The [Lido V2]() upgrade put in place a series of mechanisms based on [on-chain]() [signalling]() that serve to notify node operators who participate in the protocol when they need to process validator exits.

## B. Scope
This guideline applies to Lido on Ethereum, the node operators that participate in one or more of the [Curated Module (CM)](), [Simple Distributed Validator Technology Module (SDVTM)]() and upcoming [Community Staking Module (CSM)]() and the validators they operate as a part of the protocol.

## C. Mechanisms
Currently, there are two ways a validator can exit [Ethereum](). The first is a [voluntary exit](), a validator may choose to voluntarily stop performing [duties]() - [attesting to blocks](), [proposing blocks]() and engaging in [sync committees]() - by submitting a [voluntary exit message (VEM)] to the [Beacon Chain (BC)](https://ethereum.org/roadmap/beacon-chain/#what-is-the-beacon-chain). The second is forced ejection from the protocol -- this could be triggered by [slashing]() or insufficient [effective balance](), of which the current threshold is 16 [ETH](). Additionally, [Ethereum's upcoming Pectra hardfork](https://eips.ethereum.org/EIPS/eip-7600) will introduce a third method of exiting validators, [execution layer (EL)]() [withdrawal credential]() [triggerable exits]().

### C.1 Voluntary Exit
A validator can initiate a voluntary exit at any time, provided that it has not been slashed, is currently [active](), and has been active for at least 256 epochs (~27 hours). It does so by signing a voluntary exit message using the [validator key](), and broadcasting it directly to the local [consensus layer (CL)]() [client]() or indirectly - e.g. via an external [node]() or [API]() – to be processed. Once the validator has reached the [exit epoch]() and its node operator waited for a grace period of an additional 256 epochs or confirmed the validator was not selected as a future sync committee participant - which even after broadcasting a VEM and reaching the exit epoch is the case in rare instances - it ceases to perform duties and stops receiving [rewards]() and [penalties](), i.e. enters the [withdrawable state](). In this state, the validator waits until its index is parsed by the [withdrawals sweep operation]() for the balance of the validator to be withdrawn to its specified execution layer [0x01 withdrawal credentials]().

**_NOTE:_** Validators with [0x00 credentials]() will need to rotate to 0x01 for the withdrawal to be processed, otherwise they are skipped.

### C.2 Forced Exit
[Forced exits]() are not relevant for the purposes of this guideline, but occur either as a result of a validator being slashed or of its effective balance falling below the [`EJECTION_BALANCE`](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/beacon-chain.md#validator-cycle) threshold of currently 16 ETH.

### C.3 Triggerable Exit
[EIP-7002](https://eips.ethereum.org/EIPS/eip-7002) introduces a functionality into Ethereum that will allow execution layer withdrawal credentials to signal and initiate an exit for related validators. This will provide increased assurances with respect to the timely and orderly exit of validators for staking solutions, as well as potential countermeasures against malicious or ill-performing node operators. The downside, however, is that triggerable exits incur on-chain [gas costs]() for [transactions]() which do not exist on the consensus layer – this makes them less suitable than the CL-initiated variant for the average validator exit use case, nor are they a cure-all since validators may still misbehave while in the [exit queue](). For Lido on Ethereum, the introduction of triggerable exits nevertheless represents a key functional advancement in the future offering of [bond-based (author's note: clarify in definition bond is meant as security deposit to Ethereum network and not a bond in a traditional financial sense)]() [permissionless]() [modules]() like the Community Staking Module.

## D. Validator Exit Order
The exit sequence of Lido on Ethereum validators, i.e. validators submitted to the Lido [Node Operators Registry]() which have been [deposited]() to, follows a deterministic order so exits can be computed independently and trustlessly if needed. The currently implemented order corresponds to the *combined approach* as discussed in [Withdrawals: on Validator Exiting Order](https://research.lido.fi/t/withdrawals-on-validator-exiting-order/3048/), the priority of which is determined by the following [sorting predicate](https://github.com/lidofinance/lido-oracle/blob/develop/src/services/exit_order/iterator.py#L19):
* Validator whose operator has the lowest number of [delayed validators]()
* Validator whose operator has the highest number of targeted validators to exit
* Validator whose operator has the highest [stake weight]()
* Validator whose operator has the highest number of [predictable validators]()
* Validator whose operator has the lowest [index]()

## E. Node Operator Performance Expectations Over Time
As this guideline is concerning a relatively new functionality of the Ethereum protocol, the proposed enforcement mechanisms, service level expectations, and associated timeframes detailed are mild enough to allow for working out initial kinks without unreasonable penalty. 
Once the processes and mechanisms around the automation of validator exits have matured, this guideline should be revised. In particular, time windows for service level expectations should be tightened and penalties for non-performance should be increased.

## F. Node Operator Responsibilities
If requested to via signalling - using [oracles]() or the [exit daemon]() infrastructure - node operators who participate in Lido on Ethereum have a duty to correctly and in a timely manner exit validators as determined by the protocol's requirements and rules set by and for any of the reasons described in sections [“A-Purpose”](#A-Purpose) and ["D-Validator-Exit-Order"](#D-Validator-Exit-Order) as approved by the [LDO token holders]().

To determine whether node operators have appropriately executed the required actions, this guideline seeks to outline the time operators are afforded to do so, and what the penalties for non-conformance should be.

Generally speaking, node operators are expected to exit the indicated validators in a reasonable time frame. The actual mechanisms for validators to be exited are at the discretion of the operators, with open-source tooling available to aid them in identifying signalled [validator exit requests](), as well as in processing them.

## G. Tooling
The tooling consists of three components, which may be used by node operators to perform semi- or fully-automated processing of validator exit requests. Operators may instead choose to use any of the below modules with a custom implementation -- e.g. to utilize a just-in-time (JIT) approach to the generation of voluntary exit messages as opposed to pre-generating a certain percentage of them.

* [Keys API (KAPI)](https://keys-api.lido.fi/api/) -- a node-operator-hosted service that loads the operator’s [public validator keys](), calculates the next keys to be exited, and can be hooked up to other tooling, e.g. [ethdo](https://github.com/wealdtech/ethdo), a custom script for [Web3Signer](https://docs.web3signer.consensys.io/), etc., to automate the generation and signing of voluntary exit messages to be stored pre-signed and made available for use by the [Validator Ejector]()
* [Validator Ejector](https://hackmd.io/@lido/BJvy7eWln) -- a daemon that listens for [`ValidatorsExitBusOracle` (VEBO) reports](), locates pre-signed or retrieves JIT-signed exit messages and processes the exit requests, i.e. submits the voluntary exit messages to the consensus layer for broadcasting
* [ValidatorsExitBusOracle](https://docs.lido.fi/contracts/validators-exit-bus-oracle/) -- a sub-module of the [Lido Oracle]() which settles an on-chain report that indicates which validators should be exited by which node operator(s) and constitutes the validator exit requests signal

Validator exit requests are signalled to all node operators via VEBO reports multiple times a day. Operators have a duty to set up infrastructure to capture signalled validator exit requests and process requests pertaining to their validators as soon as possible (ASAP).

For clarity:
* A validator exit request is considered [*signalled*]() once it is retrievable from the on-chain VEBO report
* A validator exit request is considered [*processed*]() once it has been broadcast to the CL and included in a block proposed to the BC
* A validator exit request is considered [*fulfilled*]() once the validator has been fully exited and withdrawn

## H. Monitoring & Penalties
Although the process can be largely automated, to account for differences in infrastructure, working hours, and mechanism timings, the below are the required service levels for validator exits that node operators must adhere to.

If operators are processing signalled validator exit requests as soon as they are available, the shortest possible time for an exit request to go from *signalled* to *processed* will be somewhere within the range of a few minutes to an hour. With respect to [validator exits performance](), each node operator may be considered to have one of the below three [validator exit request statuses]().

* *In good standing* -- validator exit requests are being processed fully, correctly, and timely
* *Delayed* -- validator exit requests are being processed incompletely, incorrectly, or not within the desired time frame
* *Delinquent* -- validator exit requests are being processed incompletely, incorrectly, or not within the maximum acceptable time frame

|Event|Requirement to not be considered *delayed*|Requirement to not be considered *delinquent*|
|---|---|---|
|Processing of *signalled* validator exit requests|All *signalled* requests are processed in less than 24h after the VEBO report|Some *signalled* requests are taking longer than 24 but less than 96h to process|
|Escalation of inability to execute *signalled* validator exit request with reason|ASAP but no longer than 24h|ASAP but no longer than 96h

In case node operators are not processing validator exit requests in a timely manner, the below actions shall be taken:

* If an node operator has a status of *delayed*, the Node Operator Mechanisms (NOM) workstream will raise an issue with the operator and request remediative action
* If a node operator of the CM or SDVTM has a status of *delinquent* (in CSM terms *`stuckKeysCount > 0`*), the NOM workstream will raise a formal issue with the operator on the [Lido Research Forum](https://research.lido.fi)
* While a node operator has a status of *delinquent*:
    - no new stake will be allocated to the operator -- this happens automatically and applies equally to the CM, SDVTM and CSM
    - daily rewards sent to the operator will halve in the CM and SDVTM with the remaining half sent towards that day’s [rebase](); for the CSM no rewards will be distributed to the operator until `stuckKeysCount` is back to `0` -- either will happen automatically
    - reduced rewards distribution will continue for the duration of a [cooldown period]() long enough to determine whether, immediately after service restoration by the operator, subsequently received validator exit requests are processed in a timely manner
* If a node operator has a status of *delayed* or *delinquent*, the VEBO will assume that the operator is unresponsive and reroute newly incoming validator exit requests to operators that are not considered *delinquent* or *delayed*. Due to the rerouting of exit requests, the LDO token holders should consider - via an [ad-hoc vote]() - overriding the total limit of active validators for the relevant operator such that if it resumes a status of *in good standing*, it is not benefiting at the expense of operators who took over processing of rerouted exit requests.
* Once a *delinquent* node operator has processed all *signalled* validator exit requests, and thus its number of *delinquent* validators in the next [`AccountingOracle` (AO)](https://docs.lido.fi/contracts/accounting-oracle/) [report]() is updated to `0`, the operator will recommence receiving exit requests. In the CM and SDVTM, its status will revert to *in good standing* after 120h, i.e. provided any newly received validator exit requests are processed timely. During this 5 day cooldown period the node operator will continue to not receive new stake and receive halved rewards; in contrast, in the CSM the rewards distribution to [stakers]() is reevaluated on a daily basis.
* In the most egregious of cases - e.g. *delinquency* for weeks at a time or a CSM node operator missing the [performance threshold]() for 3 within 6 [frames](), therefore being treated as bad performer violating the [rule of good performance]() within the protocol, the LDO token holders at any time may consider an [on-chain vote]() to stop the operator. This has the effect of setting the [rewards share]() the operator receives to 0 or to eject its validators using a permissionless method. If the node operator subsequently remains unresponsive to the requests of the Lido on Ethereum contributors, the operator is considered to have been effectively offboarded from the [permissioned]() Node Operators Registry and the LDO token holders should take further steps to formalize the exit.

In case a node operator, for any reason, cannot exit a validator, e.g. loss of the private key associated, the operator is expected to reimburse stakers by supplying the maximum irretrievable balance of the validator -- i.e. 32 ETH, since anything over that can be obtained via partial rewards. Doing so renders the validator in question [*unrecoverable*]() and [*reimbursed*]() and does not count against the node operator in terms of its validator exit request status. For CSM there is also the option to confiscate from the node operators' validator bonds to cover for [missed rewards](). However, this approach is still under consideration.


## I. Out of Order Exits
[Out of order exits]() refer to exits that are made by a node operator of a permissioned Lido on Ethereum module when a validator exit request has not been made by the protocol. In such a case, the operator must notify the protocol's contributors via the Lido Research Forum that an out of order exit has been processed, how many and which validators have been exited, and the reason for it.

## J. Node Operator Business Continuity & Other Considerations
If at any time a node operator of a permissioned Lido on Ethereum module is unable to continue to participate in the protocol, e.g. has become insolvent, it must notify the protocol's contributors via the Lido Research Forum of the circumstances and signal its intent to exit all of the validators that it operates for LoE. If the LDO token holders, via a [ratified vote]() within 8 days, do not instruct the operator otherwise, it must proceed with triggering the exit of all of its validators.
