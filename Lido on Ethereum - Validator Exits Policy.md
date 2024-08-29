# Lido on Ethereum Validator Exits Guideline

```markdown!
STATUS: V2.0 (Q3/2024 - Introduction of Triggerable Exits: Extension to SDVTM & CSM)
```

## A - Purpose
[Validator exits]() may be utilized by [staking protocol]() [Lido on Ethereum (LoE)]() for the following reasons:
* To satisfy [withdrawal redemption requests]() from [users]() -- e.g. if requests are larger than the amount of [stake]() that can be made available for redemption via only [partial withdrawals]() or [skimming]()
* To reallocate stake across the [Node Operator (NO)]() set -- move stake from one Node Operator to another for a variety of reasons, e.g. [substandard performance]() as per the guiding principles from the [Lido Operator Set Strategy](https://research.lido.fi/t/lido-operator-set-strategy/2139) or away from [unbonded validators]()
* To rotate [signing keys]() for [validators]() without changing the stake allocation
* To achieve the [Staking Modules']() [target shares]() -- i.e. for the [Staking Router (SR)]() to prioritize validator exits for unbalanced modules
* To eject validators regardless of demand in the [withdrawal queue (WQ)]() -- e.g. to protect the protocol's security from a Node Operator with insufficient collateral [bond](), severely misbehaving or repeatedly violating the rules

The [Lido V2]() upgrade put in place a series of mechanisms based on [on-chain]() [signalling]() that serve to notify Node Operators who participate in the protocol when they need to process validator exits.

## B - Scope
This guideline applies to Lido on Ethereum, the Node Operators that participate in one or more of the [Curated Module (CM)](), [Simple Distributed Validator Technology Module (SDVTM)]() and [Community Staking Module (CSM)]() and the validators they operate as a part of the protocol.

The below stipulated operational flows and expectations apply to all above-mentioned Staking Modules, except in cases where specific mention be made to alternate flows or expectations for specific modules due to differences in their technical or operational characteristics.

**_NOTE:_** While this document refers to Node Operators and validators throughout, in the context of different Node Operators coming together to form a [cluster]() and use [Distributed Validator Technology (DVT)]() to collectively run one or more [Distributed Validator (DV)]() [nodes](), those terms apply accordingly -- i.e. operations in the SDVTM and optional [Squad Staking]() in the CSM.

## C - Ethereum Validator Exit Mechanisms
Currently, there are two ways a validator can exit [Ethereum](). The first is a [voluntary exit](), a validator may choose to voluntarily stop performing [duties]() - [attesting to blocks](), [proposing blocks]() and engaging in [sync committees]() - by submitting a [voluntary exit message (VEM)]() to the [Beacon Chain (BC)](https://ethereum.org/roadmap/beacon-chain/#what-is-the-beacon-chain). The second is forced ejection from the protocol -- this could be triggered by [slashing]() or insufficient [effective balance](), of which the current threshold is 16 [ETH](). Additionally, Ethereum's upcoming [Pectra hardfork](https://eips.ethereum.org/EIPS/eip-7600) will introduce a third method of exiting validators, [execution layer (EL)]() [withdrawal credential]() [triggerable exits]().

### C.1 - Voluntary Exit
A validator can initiate a voluntary exit at any time, provided that it has not been slashed, is currently [active](), and has been active for at least 256 [epochs]() (~27 hours). It does so by signing a voluntary exit message using the [validator key](), and broadcasting it directly to the local [consensus layer (CL)]() [client]() or indirectly - e.g. via an external node or [API]() – to be processed. Once the validator has reached the [exit epoch]() and its Node Operator waited for a grace period of an additional 256 epochs or confirmed the validator was not selected as a future sync committee participant - which even after broadcasting a VEM and reaching the exit epoch is the case in rare instances - it ceases to perform duties and stops receiving [rewards]() and [penalties](), i.e. enters the [withdrawable state](). In this state, the validator waits until its index is parsed by the [withdrawals sweep operation]() for the balance of the validator to be withdrawn to its specified execution layer [0x01 withdrawal credentials]().

**_NOTE:_** Validators with [0x00 credentials]() will need to rotate to 0x01 for the withdrawal to be processed, otherwise they are skipped.

### C.2 - Forced Exit
[Forced exits]() are not relevant for the purposes of this guideline, but occur either as a result of a validator being slashed or of its effective balance falling below the [`EJECTION_BALANCE`](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/beacon-chain.md#validator-cycle) threshold of currently 16 ETH.

### C.3 - Triggerable Exit
[EIP-7002](https://eips.ethereum.org/EIPS/eip-7002) introduces a functionality into Ethereum that will allow execution layer withdrawal credentials to signal and initiate an exit for related validators. This will provide increased assurances with respect to the timely and orderly exit of validators for staking solutions, as well as potential countermeasures against malicious or ill-performing Node Operators. The downside, however, is that triggerable exits incur on-chain [gas costs]() for [transactions]() which do not exist on the consensus layer – this makes them less suitable than the CL-initiated variant for the average validator exit use case, nor are they a cure-all since validators may still misbehave while in the [exit queue](). For Lido on Ethereum, the introduction of triggerable exits nevertheless represents a key functional advancement in the future offering of bond-based [permissionless]() modules like the Community Staking Module.

## D - Validator Exit Order
The exit sequence of Lido on Ethereum validators, i.e. validators submitted to one of the Lido [Node Operators Registries]() which have been [deposited]() to, follows a deterministic order so exits can be computed independently and trustlessly if needed. 

### D.1 - Target Limit
[Staking Router V2]() introduces the [`targetLimitMode`]() mechanism on a per Node Operator and consideration of the [exit share limit]() on a per Staking Module basis. If a Node Operator's `targetLimitMode` is configured to `0`, i.e. disabled, it can receive stake without limitation and the validators it runs for the protocol have no additional priority in exiting. Set to `1`, the Node Operator is in [smooth exit mode](), meaning it has a limit on its number of [active validators](). As long as the Node Operator's number of active validators does not exceed the [`targetLimit`](), it receives stake under general conditions. However, once the value is reached, the Node Operator stops receiving new stake and if the NO's number of active keys exceeds the `targetLimit`, its validators are prioritized to exit. [Boosted exit mode]() `2` is similar to smooth exit mode but does not consider demand in the withdrawal queue when prioritizing the Node Operator's validators in the amount of [targeted validators]() requested for exit.

In the currently implemented version, the priority of exits is thus determined by the following [sorting predicate](https://github.com/lidofinance/lido-oracle/blob/develop/src/services/exit_order_v2/iterator.py#L37).

Sorting|Module|Node Operator|Validator
:---:|---|---|---
V||Lowest number of [delayed validators]()|
V||Highest number of targeted validators to boosted exit|
V||Highest number of targeted validators to smooth exit|
V|Highest deviation from the exit share limit||
V||Highest [stake weight]()|
V||Highest number of active validators|
V|||Lowest [index]()

## E - Node Operator Responsibilities
If exits for keys under their operation have been requested via on-chain signalling through the [Validators Exit Bus Oracle (VEBO)](), Node Operators who participate in Lido on Ethereum have a duty to correctly and timely exit validators as determined by the protocol's requirements and rules set by and for any of the reasons described in sections [“A-Purpose”](#A-Purpose) and ["D-Validator-Exit-Order"](#D-Validator-Exit-Order) as per the latest in force guideline.

To determine whether Node Operators have appropriately executed the required actions, this guideline seeks to outline the time Node Operators are afforded to do so, and what the penalties for non-conformance should be. Generally speaking, Node Operators are expected to exit the indicated validators in a reasonable time frame. The actual mechanisms for validators to be exited are at the discretion of the Node Operators, with open-source tooling available to aid them in identifying signalled [validator exit requests](), as well as in processing them.

The tooling consists of three components, which may be used by Node Operators to perform semi- or fully-automated processing of validator exit requests. Node Operators may instead choose to use any of the below modules with a custom implementation -- e.g. to utilize a just-in-time (JIT) approach to the generation of voluntary exit messages as opposed to pre-generating a certain percentage of them.

* [Keys API (KAPI)](https://keys-api.lido.fi/api/) -- a Node-Operator-hosted service that loads the Node Operator's [public validator keys](), calculates the next keys to be exited, and can be hooked up to other tooling - e.g. [ethdo](https://github.com/wealdtech/ethdo), a custom script for [Web3Signer](https://docs.web3signer.consensys.io/), etc. - to automate the generation and signing of voluntary exit messages to be stored pre-signed and made available for use by the [Validator Ejector]()
* [Validator Ejector](https://hackmd.io/@lido/BJvy7eWln) -- a daemon that listens for [VEBO reports](), locates pre-signed or retrieves JIT-signed exit messages and processes the exit requests, i.e. submits the voluntary exit messages to a consensus layer node for broadcasting
* [Validators Exit Bus Oracle](https://docs.lido.fi/contracts/validators-exit-bus-oracle/) -- a sub-module of the [Lido Oracle]() which settles an on-chain report that indicates which validators should be exited by which Node Operator(s) and constitutes the validator exit requests signal

### E.1 - Distributed Validator Technology Particularities
A cluster's operation of distributed validator nodes entails additional coordination effort and obligations compared to a solo operation. This involves each Node Operator's responsibility towards the other [cluster participants]() to be performant and reachable, and its familiarity with the specifics of the [Obol]() and [SSV Network]() DVT implementations. Particularly for the [Lido package](https://github.com/ObolNetwork/lido-charon-distributed-validator-node) of Obol's [middleware client]() [Charon](), it is required that each participant verifies the integrity of the voluntary exit messages automatically created for each validator upon [Distributed Key Generation (DKG)](). For validator exit requests to be processed successfully, it is crucial that each Node Operator keeps the Validator Ejector [sidecar]() running, up-to-date, and the VEMs accessible to it, so that at least the [threshold]() of [key shares]() is online and actively participating in [consensus reaching]() of the cluster at all times. If using DVT still leads to any operational difficulties in the processing of validator exit requests, the cluster participants shall refer to the contributors to Lido's [Node Operator Mechanisms (NOM)]() workstream and the developers of the infrastructure for troubleshooting.

## F - Validator Exits Performance

Validator exit requests are signalled to all Node Operators via VEBO reports every 75 Ethereum CL [slots]() (8 hours) at around 04:00, 12:00 and 20:00 UTC. Node Operators have a duty to set up infrastructure to capture signalled validator exit requests and process requests pertaining to their validators as soon as possible (ASAP).

For clarity, a validator exit request is considered:
* [signalled]() once it is retrievable from the on-chain VEBO report,
* [processed]() once it has been broadcast to the CL and included in a block proposed to the BC, and
* [fulfilled]() once the validator has been fully exited and withdrawn.

Although the process can be largely automated, to account for differences in infrastructure, working hours, and mechanism timings, the below are the timing constraints regarding processing of validator exits that Node Operators must adhere to.

If Node Operators are processing signalled validator exit requests as soon as they are available, the shortest possible time for an exit request to go from signalled to processed will be somewhere within the range of a few minutes to an hour. With respect to this [validator exits performance](), each Node Operator may be considered to have one of the below three [validator exit request statuses]().

* In good standing -- validator exit requests are being processed fully, correctly, and timely
* Delayed -- validator exit requests are being processed incompletely, incorrectly, or not within the desired time frame
* Delinquent -- validator exit requests are being processed incompletely, incorrectly, or not within the acceptable time frame

**_NOTE:_** The temporal connotation of the term delayed does not relativize the significance of the completeness and correctness in the processing of validator exit requests. Instead, it emphasizes that even the return to adherence with the technical requirements, i.e. completely and correctly resolving [`stuckKeysCount > 0`](), does not lead to an immediate recovery of the validator exits performance, but that the Node Operator's substandard validator exit request status will be in effect for at least the entire current [reporting frame]() -- this equally applies to status delinquent.

Event|Requirement to not be considered delayed|Requirement to not be considered delinquent
---|---|---
Processing of signalled validator exit requests|All signalled requests are processed in less than 24 hours after the VEBO report|Some signalled requests are taking longer than 24 but less than 96 hours to process
Escalation of inability to execute signalled validator exit request with reason|ASAP but no longer than 24 hours|ASAP but no longer than 96 hours

## G - Validator Exits Performance Monitoring & Penalties
In case that a Node Operator is not processing validator exit requests timely, the below actions shall be taken. Separate measures are applied dependent on the [entry type]() of the Staking Module. While the Node Operators and associated communication channels are known for the [permissioned]() CM and SDVTM, NOs of the permissionless CSM may remain anonymous and reaching them is only possible if a contact is voluntarily shared with the community -- which demands a different general approach and [code base]().

Action|CM|SDVTM|CSM
---|:---:|:---:|:---:
If a Node Operator has a status of delayed, the NOM workstream will raise an issue with it and request remediative action.|:heavy_check_mark:|:heavy_check_mark:|If NO is identifiable
If a Node Operator has a status of delayed or delinquent, the VEBO will assume that it is unresponsive and reroute newly incoming validator exit requests to Node Operators that are not considered delayed or delinquent. Due to the rerouting of exit requests, the [LDO token holders]() should consider - via an [ad-hoc vote]() - overriding the total limit of active keys for the relevant Node Operator such that if it resumes in good standing, it is not benefiting at the expense of Node Operators who took over processing of rerouted exit requests.|:heavy_check_mark:|:heavy_check_mark:|:heavy_check_mark:
If a Node Operator has a status of delinquent, the NOM workstream will raise a formal issue with it on the [Lido Research Forum](https://research.lido.fi).|:heavy_check_mark:|:heavy_check_mark:|If NO is identifiable
While a Node Operator has a status of delinquent, no new stake will be allocated to it.|:heavy_check_mark:|:heavy_check_mark:|:heavy_check_mark:
While a Node Operator has a status of delinquent, its share of rewards for the 225 Ethereum CL slots (24 hours) long [Accounting Oracle (AO)](https://docs.lido.fi/contracts/accounting-oracle/) frame - commencing daily at around 12:00 UTC - will halve, with the remaining half contributing towards the period’s [stETH]() [rebase]().|:heavy_check_mark:|:heavy_check_mark:|:x:
While a Node Operator has a status of delinquent, the reduced rewards distribution will continue for the duration of a 120 hours [cooldown period](), long enough to determine whether, immediately after service restoration by the Node Operator, subsequently received exit requests are processed timely.|:heavy_check_mark:|:heavy_check_mark:|:x:
While a Node Operator has a status of delinquent, no rewards will be shared with it in the 6300 Ethereum CL slots (28 days) long [Performance Oracle (PO)](https://docs.lido.fi/staking-modules/csm/rewards#performance-oracle) frame, with all rewards contributing towards the intermediate stETH rebases instead.|:x:|:x:|:heavy_check_mark:
Once a delinquent Node Operator has processed all signalled validator exit requests, and thus its stuck keys count in the next [AO report]() is updated to zero, the Node Operator may recommence receiving exit requests. Its status will revert to in good standing after 120 hours, provided any newly received validator exit requests are processed timely. During this 5 days cooldown period the Node Operator will continue to not receive new stake and receive halved rewards. |:heavy_check_mark:|:heavy_check_mark:|:x:
Once a delinquent Node Operator has processed all signalled validator exit requests, and thus its stuck keys count in the next PO report is updated to zero and its status reverted to in good standing, the Node Operator may recommence receiving exit requests.|:x:|:x:|:heavy_check_mark:
In the most egregious of cases, e.g. being delinquent for weeks at a time, LDO token holders at any time may consider an [on-chain vote]() to stop the Node Operator. A stopped Node Operator receives no new stake and no rewards from the protocol. If the Node Operator subsequently remains unresponsive to the requests of the Lido on Ethereum contributors, it is considered to have been effectively offboarded from the Node Operators Registry and the LDO token holders should take further steps to formalize the exit.|:heavy_check_mark:|:x:|:x:
In the most egregious of cases, e.g. at least one participant being delinquent for weeks at a time, the [Simple DVT Module Committee](https://research.lido.fi/t/simple-dvt-module-committee-multisig/6520) at any time may execute an [Easy Track motion]() to set a cluster's active validators to zero and the Validator Ejector to pick up the associated keys for boosted exit. Simultaneously the LDO token holders may consider an [on-chain vote]() to stop the delinquent Node Operator. A stopped Node Operator receives no new stake and no rewards from the protocol. If the Node Operator subsequently remains unresponsive to the requests of the Lido on Ethereum contributors, it is considered to have been effectively offboarded from the Node Operators Registry and the LDO token holders should take further steps to allow the remaining participants to start new clusters with Node Operators from the SDVTM's backup list.|:x:|:heavy_check_mark:|:x:
In case a Node Operator, for any reason, cannot exit a validator, e.g. loss of the associated private key, the Node Operator is expected to reimburse [stakers]() by supplying the maximum irretrievable balance of the [unrecoverable validator]() -- i.e. 32 ETH, since anything over that can be obtained via partial rewards. Doing so renders the validator in question [reimbursed]() and does not count against the Node Operator in terms of its validator exit request status.<br>**_NOTE:_** With the Ethereum Pectra upgrade, previously unrecoverable validators will become salvageable through triggerable exits, reducing the cost for Node Operators to reimburse stakers to the gas needed to initiate the method on the execution layer.|:heavy_check_mark:|:heavy_check_mark:|:heavy_check_mark:

## H - Out of Order Exits
[Out of order exits]() refer to exits that are made by a Node Operator of one of or both the Curated Module and Simple Distributed Validator Technology Module when a validator exit request has not been made by the protocol. In such a case, the Node Operator must notify the community via the Lido Research Forum that an out of order exit has been processed, how many and which validators have been exited, and the reason for it.

## I - Node Operator Business Continuity & Other Considerations
If at any time a Node Operator of one of or both the Curated Module and Simple Distributed Validator Technology Module is unable to continue to participate in the protocol, e.g. has become insolvent, it must notify the community via the Lido Research Forum of the circumstances and signal its intent to exit all of the validators it operates for Lido on Ethereum. If the LDO token holders, via a [ratified vote]() within 8 days, do not instruct the Node Operator otherwise, it must proceed with triggering all of the exits.
