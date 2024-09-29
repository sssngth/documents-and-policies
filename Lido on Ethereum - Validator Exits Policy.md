# Lido on Ethereum Standard Node Operator Protocol - Validator Exits

```markdown!
STATUS: V2.0 (Q3/2024 - Introduction of Triggerable Withdrawals: SR 2.0 Upgrade & Extension to SDVTM & CSM)
```

## A - Purpose
This Standard Node Operator Protocol (SNOP) outlines the validator exit mechanisms Ethereum offers (see [Appendix A](#Appendix-A-Ethereum-Validator-Exit-Mechanisms)), the role that validator exits play in Lido on Ethereum (LoE), the rules around the usage of the SNOP, the exit order being followed by LoE, the responsibilities participating Node Operators (NOs) have in processing validator exit requests, and the actions that can be expected in case of non-conformance with the SNOP.

In LoE, validator exits may be utilized for the following reasons:

* To satisfy withdrawal redemption requests from users -- e.g. if the requests are larger than the pool of stake predicted to be available from the [`depositBufferedEther`](), [`LidoExecutionLayerRewardsVault`](), and [`WithdrawalVault`]() fed by [partial withdrawals/skimming]()
* To reallocate stake across the NO set -- move stake from one or more NO(s) to another for a variety of reasons, e.g. substandard performance as per the guiding principles from the [Lido Operator Set Strategy](https://research.lido.fi/t/lido-operator-set-strategy/2139) or away from [unbonded validators]()
* To rotate signing keys for validators
* To maintain target shares -- i.e. for the [Staking Router (SR)]() to prioritize exits for over-allocated Staking Modules (SMs) 
* To eject validators regardless of demand in the [Withdrawal Queue (WQ)](WithdrawalQueueERC721) -- e.g. to protect LoE's security from a malicious NO, one with insufficient [collateral bond](), or in violation of the SNOP

## B - Scope
This SNOP applies to LoE, the NOs who participate in one or more of the SR’s [Curated Module (CM)](), [Simple Distributed Validator Technology Module (SDVTM)](), and [Community Staking Module (CSM)]() and the validators the NOs operate as part of the Liquid Staking Protocol (LSP).

The below stipulated operational flows and expectations apply to all above-mentioned SMs, except in cases where specific mentions are made to alternate flows or expectations for specific modules due to differences in their technical or operational characteristics.

**_NOTE:_** As terms can have different meanings based on how they are used and in different contexts, the below table aims to clarify the usage of various terms for the purposes of this SNOP and how they apply within the context of LoE. 

Term|Definition|Context
:---:|---|---
Node Operator|An entity, individual, or set thereof who operate(s) nodes while participating in one or more of LoE's SMs to run Ethereum validators|*CM & CSM [solo operation]()*: A single entity or individual operating validators</br>*SDVTM*: A cluster of independent entities and/or individuals operating validators as a group</br>*CSM [DVT operation]()*: An entity, individual or cluster thereof operating validators
Validator|An actor in Ethereum's consensus, who is attached to a node, represents an initial ETH stake, has individual public-private signing and withdrawal key pairs, and performs duties for the network -- attesting to and proposing blocks, participating in sync committees, and reporting on violations of the slashing rules|*CM & CSM solo operation*: A validator run for the SM by a single NO</br>*SDVTM*: A [Distributed Validator (DV)]() run for the SM by a cluster of independent NOs utilizing [Distributed Validator Technology (DVT)]()</br>*CSM DVT operation*: A DV run for the SM by an entity, individual or cluster thereof utilizing DVT

## C - Validator Exit Order
The exit sequence of LoE validators follows a deterministic order so exits can be computed independently and trustlessly if needed. The order is algorithmic and works to organically balance the stake allocation throughout the SMs and across NOs within modules, as those with higher numbers and long-running active validators, which have given some NOs a higher stake weight of historically accrued rewards, are prioritized for exit. Other considerations, such as the `targetShare`, `targetLimit`, and Validator Exits Performance also play a part in determining the order of validator exit requests.

In the SR 2.0, the priority of exits is determined by the following [sorting predicate](https://github.com/lidofinance/lido-oracle/blob/develop/src/services/exit_order_v2/iterator.py#L37).

Sorting|Staking Module|Node Operator|Validator
:---:|---|---|---
V||Lowest number of delayed validators|
V||Highest number of targeted validators to boosted exit|
V||Highest number of targeted validators to smooth exit|
V|Highest deviation from the exit share limit||
V||Highest stake weight|
V||Highest number of active validators|
V|||Lowest index

### C.1 - Target Share
Each SM has the concept of a `targetShare`. The `targetShare` is the maximum percentage of LoE's total stake that can be allocated to a specific module. Since the total stake in the LSP is dynamic as a result of inflows and outflows, it is possible that modules end up with a share of allocated stake higher than their `targetShare`. In such cases, validators of these modules are prioritized for exits, to bring each module's share below its `targetShare`.

### C.2 - Target Limit
Each SM registry has the concept of a `targetLimit` and [`targetLimitMode`](https://docs.lido.fi/staking-modules/csm/validator-exits#protocol-initiated-exits), which are applied by the SR at a per NO level within each module. The `targetLimitMode` sets the approach through which exits are requested by the LSP, and the `targetLimit` sets the target number of active validators for a NO.

With the `targetLimitMode` set to:
* `0`, i.e. disabled, the NO can receive stake without limitation up to the NO's capacity of [`depositableKeys`](), and the NO’s validators have the default exit priority.
* `1`, the NO is in Smooth Exit Mode, meaning that there is a limit to the NO's number of active validators. As long as the NO's validator amount does not exceed the specified `targetLimit`, the NO may be allocated stake as per the default SR [allocation algorithm](https://docs.lido.fi/contracts/staking-router#allocation-algorithm). However, once the value is reached, the allocation of new stake to the NO is stopped. If the NO's active keys exceeds the `targetLimit`, the NO’s validators are prioritized for exit when there is demand for withdrawal fulfilment that requires exits.
* `2`, the NO is in Boosted Exit Mode, meaning that should the NO’s active keys be or rise above the set `targetLimit`, then exits will be requested from the NO regardless of withdrawal demand, and the NO's validator exits will be prioritized over exits of NOs with a `targetLimitMode` of `0` or `1`.

Independent target limits may be implemented by each SM, but are set by LDO token holders via Easy Track or on-chain [governance votes](https://lido.fi/governance).

## D - Node Operator Responsibilities
LoE contains on-chain signalling mechanisms that serve to notify NOs who participate in the LSP when to process validator exits. If exits for keys under their operation have been requested through the [Validators Exit Bus Oracle (VEBO)](https://docs.lido.fi/contracts/validators-exit-bus-oracle/) smart contract, NOs have the duty to fully, correctly, and timely exit validators as determined by the requirements and rules set by and for any of the reasons described in the sections [A - Purpose](#A-Purpose) and [C - Validator Exit Order](#C-Validator-Exit-Order) as per the latest in force SNOP.

To determine whether NOs have appropriately executed the required actions, this SNOP seeks to outline the time NOs are afforded to do so, and to specify the consequences of non-conformance. Generally speaking, NOs are expected to exit the indicated validators in a reasonable time frame. The actual mechanisms for validators to be exited are at the discretion of the NOs, with open-source tooling to aid the NOs in identifying and processing signalled requests being available from [Appendix B](#Appendix-B-Tooling).

### D.1 - Out of Order Exits
Out of order exits refer to exits that are made by a NO of one of or both the CM and SDVTM when a validator exit request has not been made by LoE. In such cases, the NO must notify the community via the [Lido Research Forum](https://research.lido.fi) that an out of order exit has been processed, how many and which validators have been exited, and the reason for it. As the CSM is [permissionless]() and validators are bonded, this expectation is not extended to the module, CSM NOs are free to exit out of order at their discretion.

### D.2 - Business Continuity
If, at any time, a NO of one of or both the CM and SDVTM is unable to continue to participate in LoE, e.g. has become insolvent, the NO must notify the community via the Lido Research Forum of the circumstances and signal intent to exit all of the validators the NO operates for the LSP. If LDO token holders, via a ratified vote within 8 days, do not instruct the NO otherwise, the NO must proceed with triggering all of the exits.

### D.3 - Validator Exits Performance
The VEBO settles on-chain reports that indicate which validators should be exited by which NO, and constitutes the validator exit requests signal. As with the other sub-modules of the [Lido Oracle](), the VEBO's work is delineated by equal time periods called reporting frames, the duration of which is specified in an amount of Ethereum Consensus Layer (CL) epochs (~6.4 minutes) according to the respective instance of the Lido [`HashConsensus`](https://docs.lido.fi/contracts/hash-consensus/) smart contract. NOs have a duty to set up infrastructure to capture and process signalled exit requests pertaining to their validators as soon as possible.

For clarity, a validator exit request is considered:
* *signalled* once it is retrievable from a VEBO report,
* *processed* once it has been broadcast to the CL and included in a block proposed to the Beacon Chain (BC), and
* *fulfilled* once the validator has been fully exited and withdrawn.

Although the process can be largely automated, to account for differences in infrastructure, working hours, and mechanism timings, the below are the timing constraints regarding the processing of validator exits requests that NOs must adhere to. If NOs are processing signalled requests as soon as available from a VEBO report, the shortest possible time for a request to go from *signalled* to *processed* will be somewhere within the range of a few minutes to an hour.

With respect to the Validator Exits Performance, each NO may be considered to have one of the below three Validator Exit Request Statuses:

* *In good standing* -- validator exit requests are being processed fully, correctly, and timely
* *Delayed* -- validator exit requests are being processed incompletely, incorrectly, or not within the desired time frame
* *Delinquent* -- validator exit requests are being processed incompletely, incorrectly, or not within the acceptable time frame

**_NOTE:_** The temporal connotation of status *delayed* does not relativize the significance of the completeness and correctness in the processing of validator exit requests. Instead, it emphasizes that even the return to adherence with the technical requirements, i.e. completely and correctly resolving `stuckKeysCount > 0`, does not lead to an immediate recovery of Validator Exit Request Status. Once suffered, a NO's substandard status remains in force for the entire reporting frame concerned -- this equally applies to status *delinquent*. The time to recover from a substandard status depends on the reporting frame of the Lido Oracle sub-module processing the VEBO's updates of the `stuckKeysCount` for the SM the NO participates in. For the CM and SDVTM, updates are processed by the [Accounting Oracle (AO)](https://docs.lido.fi/contracts/accounting-oracle), for the CSM by the [Performance Oracle (PO)](https://docs.lido.fi/staking-modules/csm/rewards#performance-oracle). For every delinquent exit request, the NO’s `stuckKeysCount` counter increases by `1`.

To avoid NO delinquency, adhere to the following time frames.

Event|Requirement to not be considered delayed|Requirement to not be considered delinquent
---|---|---
Processing of signalled validator exit requests|All signalled requests are processed in less than 24 hours after a VEBO report|Some signalled requests are taking longer than 24 but less than 96 hours to process
Escalation of inability to execute signalled validator exit request with reason|ASAP but no longer than 24 hours|ASAP but no longer than 96 hours

## E - Consequences of Non-conformance
In case that a NO is not processing validator exit requests timely, the below actions shall be taken. Separate measures may be applied dependending on the technical characteristics of the SM within which the request originated. For example, while NOs are known for the [permissioned]() CM and SDVTM, NOs of the permissionless CSM may remain anonymous and reaching the NOs is only possible if a contact is voluntarily shared with the community.

The followig table outlines the consequences for non-adherence to validator exit request timing expectations, and for which SM each action applies.

Action|CM|SDVTM|CSM
---|:---:|:---:|:---:
If a NO has a status of delayed, contributors to the Node Operator Mechanisms (NOM) workstream will raise an issue and request remediative action by the NO.|:heavy_check_mark:|:heavy_check_mark:|If NO is identifiable
If a NO has a status of delayed or delinquent, the VEBO will assume that the NO is unresponsive and reroute newly incoming validator exit requests to NOs that are not considered delayed or delinquent. Due to the rerouting of exit requests, LDO token holders should consider - via an ad-hoc vote - overriding the total limit of active keys for the NO such that if the NO resumes in good standing, the NO is not benefiting at the expense of NOs who took over processing of rerouted exit requests.|:heavy_check_mark:|:heavy_check_mark:|:heavy_check_mark:
If a NO has a status of delinquent, contributors to the NOM workstream will raise a formal issue with the NO on the Lido Research Forum.|:heavy_check_mark:|:heavy_check_mark:|If NO is identifiable
While a NO has a status of delinquent, no new stake will be allocated to the NO.|:heavy_check_mark:|:heavy_check_mark:|:heavy_check_mark:
While a NO has a status of delinquent, the NO's share of rewards for the associated AO reporting frame(s) will halve, with the remainder accruing towards the [stETH rebase(s)]() instead.|:heavy_check_mark:|:heavy_check_mark:|:x:
While a NO has a status of delinquent, no operator rewards will accrue to the NO for the associated PO reporting frame(s), with all operator rewards accruing towards the stETH rebase(s) instead. The NO still receives rewards related to its bond rebase.|:x:|:x:|:heavy_check_mark:
Once a delinquent NO has processed all signalled exit requests and thus its `stuckKeysCount` in the next VEBO report is updated to `0`, the NO may recommence receiving requests. The NO's validator exit request status will revert to in good standing after successful expiry of a 120 hours [cooldown period](), long enough to determine whether, immediately after service restoration, subsequently received requests are processed timely. During these 5 days, the NO will continue to not be allocated new stake and receive halved rewards.|:heavy_check_mark:|:heavy_check_mark:|:x:
Once a delinquent NO has processed all signalled exit requests and thus its `stuckKeysCount` in the next VEBO report is updated to `0`, the NO may recommence receiving requests. The NO's validator exit request status will revert to in good standing after successful expiry of the current PO's reporting frame. During this period, the NO will continue to neither be allocated new stake, nor receive operator rewards. The NO still receives rewards related to its bond rebase.|:x:|:x:|:heavy_check_mark:
In the most egregious of cases, e.g. being delinquent for weeks on end, LDO token holders at any time may consider an on-chain vote to stop a misbehaving NO. A stopped NO will neither be allocated new stake, nor receive rewards. In case of a delinquent cluster, the [SDVTM Committee](https://research.lido.fi/t/simple-dvt-module-committee-multisig/6520) may simultaneously execute an [Easy Track Motion]() to set the cluster's active DVs to `0` and the Validator Ejector to pick up the associated keys for boosted exit. If the NO subsequently remains unresponsive to the requests of the contributors to LoE, the NO is considered to have been effectively off-boarded from the [CM](https://docs.lido.fi/deployed-contracts/#core-protocol), respectively [SDVTM Registry](https://docs.lido.fi/deployed-contracts/#core-protocol). LDO token holders should take further steps to formalize the exit and to allow compliant participants to form new clusters -- e.g. with NOs from the SDVTM [backup list]().|:heavy_check_mark:|:heavy_check_mark:|:x:
In case a NO, for any reason, cannot exit a validator, e.g. due to loss of the associated private signing key, the NO is expected to reimburse stakers by supplying the maximum irretrievable balance of the unrecoverable validator -- i.e. 32 ETH, since anything over that can be obtained via partial withdrawal of rewards. Doing so renders the validator in question reimbursed and does not count against the NO in terms of its validator exit request status.<br>**_NOTE:_** With the Ethereum Pectra upgrade, previously unrecoverable validators will become withdrawable through triggerable exits, reducing the cost for NOs to reimburse stakers to the gas needed to initiate the method on Ethereum's Execution Layer (EL).|:heavy_check_mark:|:heavy_check_mark:|:x:

# Appendix

## Appendix A - Ethereum Validator Exit Mechanisms
Currently, there are two ways a validator can exit from Ethereum’s CL. The first is a voluntary exit, a validator may choose to voluntarily stop performing duties for the network by submitting a voluntary exit message (VEM) to the BC. The second mechanism is forced ejection from the Ethereum protocol -- this could be triggered by slashing or an insufficient [effective balance](). Additionally, the upcoming [Pectra hardfork](https://eips.ethereum.org/EIPS/eip-7600) will introduce a third method of exiting validators, [EL 0x01 withdrawal credentials]() triggerable withdrawals.

### A.1 - Voluntary Exit
A validator can initiate a voluntary exit at any time, provided that it has not been slashed, is currently active, and has been active for at least 256 epochs (~27 hours). It does so by signing a VEM using the private validator key, and broadcasting the message to the network to have it processed -- either directly through a local CL client or indirectly, e.g. via an external CL node or API. Once the validator has reached the [exit epoch]() and the NO confirmed that the validator was not selected for [delayed sync committee participation](https://hackmd.io/1wM8vqeNTjqt4pC3XoCUKQ), the validator ceases to perform duties and stops receiving rewards and penalties, i.e. enters the [withdrawable state](). In this state, the validator waits until its index is parsed by the [withdrawals sweep operation]() and for its balance to be withdrawn to the specified 0x01 withdrawal credentials.

**_NOTE:_** Validators with [BLS 0x00 credentials]() will need to rotate to 0x01 for the withdrawal to be processed, otherwise they are skipped.

### A.2 - Forced Exit
Forced exits are not relevant for the purposes of this SNOP, but occur either as a result of a validator being slashed or of its effective balance falling below Ethereum's current [`EJECTION_BALANCE`](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/beacon-chain.md#validator-cycle) threshold of 16 ETH.

### A.3 - Triggerable Withdrawal
[EIP-7002](https://eips.ethereum.org/EIPS/eip-7002) introduces a new functionality to Ethereum that will allow EL withdrawal credentials to signal and initiate partial or full withdrawals of related validators on the CL. This will provide increased assurances with respect to the timely and orderly exit of validators for staking solutions, as well as potential countermeasures against malicious or ill-performing NOs. The downside, however, is that triggerable withdrawals incur variable on-chain gas fees for transactions that do not exist on the CL, whose costs are [calculated](https://eips.ethereum.org/EIPS/eip-7002#fee-calculation) based on a non-trivial equation dependent on parameters such as [`MAX_WITHDRAWAL_REQUESTS_PER_BLOCK`](https://eips.ethereum.org/EIPS/eip-7002#message-queue) and [`TARGET_WITHDRAWAL_REQUESTS_PER_BLOCK`](https://eips.ethereum.org/EIPS/eip-7002#target_withdrawal_requests_per_block-configuration-value) and are partially non-refundable in case of accidental underspending. This makes triggerable withdrawals less suitable than the CL-initiated variant for the average validator exit use case, nor are they a cure-all since validators may still misbehave while in the [exit queue](). For LoE, the introduction of triggerable withdrawals nevertheless represents a key functional advancement in the offering of bond-based permissionless modules like the CSM.

## Appendix B - Tooling
The tooling available from contributors to LoE consists of two components, which may be used by NOs to perform semi- or fully-automated processing of validator exit requests. NOs may instead choose to use any of the below or those [tools provided by the Lido community](https://operatorportal.lido.fi/existing-operator-portal/ethereum-onboarding/no-resources-tooling) with a custom implementation -- e.g. to utilize a just-in-time (JIT) approach to the generation of VEMs as opposed to pre-generating a certain percentage of them.

* [Keys API (KAPI)](https://keys-api.lido.fi/api/) -- a NO-hosted service that loads the NO's public validator keys, calculates the next keys to be exited, and can be hooked up to third-party tooling - e.g. [ethdo](https://github.com/wealdtech/ethdo), a custom script for [Web3Signer](https://docs.web3signer.consensys.io/), etc. - to automate the generation and signing of VEMs to be stored pre-signed and made available for use by the Validator Ejector
* [Validator Ejector](https://github.com/lidofinance/validator-ejector) -- a daemon that listens for VEBO reports, locates pre-signed or retrieves JIT-signed exit messages and processes the exit requests, i.e. submits the VEMs to a CL node for broadcasting
