---
tags: policy, validator exits, lido dao, node operators
---


# Lido on Ethereum Validator Exits Policy
```markdown!
STATUS: V2.0 (24/Q1 - Introduction of Triggerable Exits: Extension to Simple DVT module)
```


## A. Background
### Ethereum Validator Exit Mechanisms
Currently, there are two ways an Ethereum validator can exit. The first is a voluntary exit, a validator may choose to voluntarily stop performing duties - proposing blocks, attesting to blocks, and engaging in sync committees - by submitting a voluntary exit transaction to the [Beacon Chain](https://ethereum.org/roadmap/beacon-chain/#what-is-the-beacon-chain). The second is forced ejection from the protocol - this could be triggered by slashing or insufficient effective balance, of which the current threshold is 16 ETH. Additionally, there are ongoing discussions in the Ethereum community around introducing a third method for exiting validators, namely withdrawal credential based triggers of a validator.

#### Voluntary Exit
A validator can initiate a voluntary exit provided that it is currently active, has not been slashed, and has been active for at least 256 epochs (~27 hours). It does so by signing a voluntary exit message (VEM) using the validator key, and broadcasting it to the consensus layer (CL) client – or directly, e.g. via an API – to be processed. Once the validator’s exit message is processed, the validator joins the exit queue. After initiating a voluntary exit, the validator is expected to keep performing duties until it has successfully exited to avoid incurring any penalties. Once the validator has reached the exit epoch, it ceases to perform duties and stops receiving rewards and penalties, i.e. enters the "withdrawable” state. Once in the withdrawable state, the validator waits until its index is parsed by the withdrawals sweep operation for the balance of the validator to be withdrawn to its specified execution layer (EL) 0x01 withdrawal credentials -- note that validators with 0x00 withdrawal credentials will need to rotate to 0x01 for the withdrawal to be processed, otherwise they are skipped.

#### Forced Exit 
Forced exits are not relevant for the purposes of this policy, but occur either as a result of a validator being slashed or as a result of their effective balance falling below the [`EJECTION_BALANCE`](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/beacon-chain.md#validator-cycle) threshold of currently 16 ETH.

#### Triggerable Exit 
Proposals have been made to introduce functionality into Ethereum that would allow withdrawal credentials, from the EL, to signal and initiate an exit for related validators.  Such functionality would provide increased assurances with respect to the timely and orderly exit of validators for staking solutions, as well as potential countermeasures against malicious or ill-performing node operators. The downside to this, however, is that triggerable exits incur on-chain gas costs for transactions which do not exist on the CL side – this makes them less suitable than the CL-initiated variant for the average validator exit use case, nor are they a cure-all since validators may still misbehave while in the exit queue. Once/if triggerable exits become available on Ethereum, this policy will need to be updated to detail how, and in which cases, they can be utilized by the protocol.

## B. Policy 
### B.1 Goals

Validator exits may be utilized by fully-functioning staking protocols, including Lido, for the following reasons:
* To satisfy withdrawal redemption requests from users, e.g. if a withdrawal request is larger than the amount of stake that can be made available for redemption via only partial withdrawals/skimming
* To reallocate stake across the operator set - move stake from one operator to another - for a variety of reasons, e.g. substandard performance, guiding principles from the operator set strategy
* To rotate signing keys for some validators without changing the stake allocation

The V2 upgrade of Lido on Ethereum protocol put in place a series of mechanisms based on on-chain signalling that serve to notify node operators who participate in the protocol when they need to process validator exits. 


### B.2 Scope
This policy applies to the Lido on Ethereum protocol, the node operators that participate in the [Curated](#Curated-Module) and/or [Simple DVT module](#Simple-DVT-Module) of [Lido V2's](#Lido-V2) [Staking Router](#Staking-Router) and the validators that they operate as a part of the protocol.

### B.3 Policy Statement

#### B.3.I Validator Exit Order
The exit sequence of Lido on Ethereum validators, i.e. validators submitted to Lido node operator registries which have been deposited to, follows a deterministic order so that exits can be computed independently and trustlessly if needed. The currently implemented order corresponds to the "combined approach" as discussed in [Withdrawals: on Validator Exiting Order](https://research.lido.fi/t/withdrawals-on-validator-exiting-order/3048/), the priority of which is determined by the following [sorting predicate](https://github.com/lidofinance/lido-oracle/blob/develop/src/services/exit_order_iterator.py#L24):
* Validator whose operator has the lowest number of delayed validators
* Validator whose operator has the highest number of targeted validators to exit
* Validator whose operator has the highest stake weight
* Validator whose operator has the highest number of predictable validators
* Validator with the lowest index of the operator

#### B.3.II Performance Expectations Over Time

As this is a new policy concerning new functionality of the Ethereum protocol, the proposed enforcement mechanisms, service level expectations, and associated timeframes detailed in this first version of the policy are mild enough to allow for working out initial kinks without an unreasonable penalty. 
Once the processes and mechanisms around the automation of validators exits have matured, this policy should be revised. In particular, time windows for service level expectations should be tightened and penalties for non-performance should be increased.

#### B.3.III Node Operator Responsibilities
Node operators who participate in the Lido on Ethereum protocol have a duty to correctly exit validators in a timely manner as determined by the protocol’s requirements and rules, as set by the DAO. If they are requested to exit validators, via signalling using oracles or the exit daemon infrastructure, by the protocol for any of the reasons described in the  section [“B1-Goals” and as per the validator exit order approved by the DAO.](#B1-Goals)

To determine whether node operators have appropriately executed the required actions, this policy seeks to outline the time that node operators are afforded to do so, and what the penalties for non-conformance should be.

Generally speaking, node operators are expected to exit the indicated validators in a reasonable time frame. The actual mechanisms for validators to be exited are at the discretion of node operators, and open-source tooling will be available to aid node operators in identifying signalled validator exit requests, as well as in processing these requests.

The tooling shall consist of three components, which may be used by node operators to operate semi- or fully-automated processing of validator exit requests. Node operators may instead choose to use any of the below modules with a custom implementation -- e.g. to utilize a just-in-time (JIT) approach to the generation of VEMs as opposed to pre-generating a certain percentage of them.

* Keys API (KAPI) Service -- a node operator-hosted service that loads the operator’s public validator keys, calculates the next keys to be exited, and can be hooked up to other tooling - e.g. eth-do, a custom script for web3 signer, etc. - to automate the generation and signing of VEMs to be stored, as pre-signed messages, and made available for use by the Validator Ejector
* Validator Ejector -- a daemon that listens for Exit Bus Oracle reports, locates pre-signed, or retrieves JIT signed, exit messages for exiting validators and processes the validator exit requests, i.e. submits the VEMs to the CL for broadcast
* Exit Bus Oracle reports -- a sub-module of the Lido Oracle which settles an on-chain report multiple times a day, this report indicates which validators should be exited by which node operators and constitutes the validator exit requests signal


Validator exit requests are signalled to all node operators via the on-chain Exit Bus Oracle reports multiple times a day. Node operators have a duty to set up infrastructure to capture signalled validator exit requests and process relevant, i.e. pertaining to their validators, requests as soon as possible.

For clarity:
* A validator exit request is considered "signalled" once it is retrievable from the on-chain Exit Bus Oracle report -- finalized on-chain multiple times per day
* A validator exit request is considered "processed" once it has been broadcast to the CL and included in a proposed Beacon Chain slot
* A validator exit request is considered "fulfilled" once the validator has been fully exited and withdrawn

#### B.3.IV Monitoring & Penalties
Additionally, there will be another piece of tooling dubbed the "Monitor Daemon" which will serve to reconcile signalled validator exit requests with processed exits by the Beacon Chain to determine if validators have been exited in a timely manner. The results of this monitoring will be publicly available to enable the DAO to access to the data it needs to understand the rate, flow, and efficacy of validator exits.

Although the process can be largely automated, to account for differences in infrastructure, working hours, and mechanism timings, the below are the required service levels for validator exits that Node Operators must adhere to.

If Node Operators are processing signalled validator exit requests as soon as they are available, the shortest possible time for a validator exit request to go from “signalled” to “processed” will be somewhere within the range of a few minutes to an hour. With respect to validator exit performance, each Node Operator may be considered to have one of the below three statuses.

* In good standing - validator exit requests are being processed fully, correctly, and timely.
* Delayed - validator exit requests are being processed incompletely, incorrectly, or not within the desired time frame.
* Delinquent - validator exit requests are being processed incompletely, incorrectly, or not within the maximum acceptable time frame.



|Event|Requirement to not be considered Delayed|Requirement not be considered Delinquent|
|---|---|---|
|Processing of signalled validator exit requests|All signalled requests are processed ASAP (no longer than 1 day)|Some signalled requests are taking longer than 1 but less than 4 days to process|
|Escalation of inability to execute signalled validator exit request with reason|ASAP but no longer than 1 day|ASAP but no longer than 4 days

In the case that Node Operators are not processing validator exit requests in a timely manner, the below actions shall be taken:
If a Node Operator has a status of Delayed, the NOM workstream will raise an issue in with the Node Operator and request remediative action.

* If a Node Operator has a status of Delinquent, NOM workstream will raise a formal issue with the Node Operator on the Lido research forum. While a Node Operator has a status of Delinquent:
    - no new stake will be allocated to the Node Operator (this will happen automatically);
    - the daily rewards sent to the Node Operator will halve (with the remaining half sent towards that day’s rebase) (this will happen automatically). Reduced rewards will continue for the duration of a cooldown period long enough to determine whether, immediately after service restoration by the Node Operator, subsequently received validator exit requests are processed in a timely manner.
* If a Node Operator has a status of Delayed or Delinquent, the Exit Bus Oracle will assume that the Node Operator is unresponsive and re-route new incoming validator exit requests to operators that are not considered delinquent or delayed. Due to the re-routing of validator exit requests, the DAO should consider (via an ad-hoc vote) overriding the total limit of active validators for the relevant Node Operator such that if/when they resume a status of in good standing, they are not benefiting at the expense of Node Operators who took over the processing of the re-routed exits requests.
* Once a Delinquent Node Operator has processed all signalled validator exit requests (and thus their number of Delinquent validators in next Accounting Oracle report is updated to 0), they will recommence receiving validator exit requests. Their status shall revert to “In good standing” after 5 days (i.e. provided any newly received validator exit requests are processed timely). During this 5 day “cooldown period” they will continue to not receive new stake and receive halved rewards.
* In the most egregious of cases (e.g. delinquency for weeks at a time) the DAO may consider an on-chain vote to “stop” the Node Operator which has the effect of setting the fees that they receive to zero (the DAO may consider such a vote at any time). If the Node Operator is unresponsive to the DAO’s requests, then the Node Operator is considered to have been effectively “off boarded” from the Curated Node Operator registry and the DAO should take further steps to formalize the exit of the Node Operator.

In the case that a Node Operator cannot, for any reason, exit a validator (e.g. loss of the private key associated with that validator), they are expected to reimburse stakers by supplying the maximum irretrievable balance of the validator (i.e. 32 ETH, since anything over that can be obtained via partial rewards). Doing so renders the validator in question “unrecoverable and reimbursed” and does not count against the Node Operator in terms of assessing its validator exit request status.


#### B.3.V Out of Order Exits & NO Business Continuity

##### Out of Order Exits

Out of Order exits refers to exits that are made by a Node Operator when a validator exit request has not been made for a (set of) validator(s) by the Lido protocol. In the case of processing such an exit, the Node Operator must notify the DAO via the Lido Research Forums (https://research.lido.fi) that such an exit has been processed, how many and which validators have been exited, and the reason for the exit.

##### Business Continuity & Other Considerations

If at any time a Node Operator is unable to continue to participate in the Lido on Ethereum protocol (e.g. has become insolvent), it must notify the DAO via the Lido Research Forums (https://research.lido.fi) of the circumstances and signal its intent to exit all of the validators that it operates as a part of the protocol. If the DAO does not otherwise instruct the Node Operator via a ratified vote within 8 days, it must proceed with triggering the exit of all of the validators.
