# Lido on Ethereum Block Proposer Rewards Policy

```markdown!
STATUS: v3.0 (23/Q1 - Release of In-Protocol Solution - PBS Lite)
```

## A. Overview
As a DAO and a protocol, Lido on Ethereum should have a transparent, enforceable, and monitorable policy with regards to how its constituent [Node Operators](#Node-Operator) are expected to behave with regards to rewards that accrue from the activity of producing blocks for [Validators](#Validator) that they run as a part of Lido, namely [Priority Fees](#Priority-Fees) and [MEV](#MEV) extraction, and how the protocol will distribute the rewards that accrue to the protocol as a result of these activities.

## B. Purpose
This policy aims to outline how Node Operators participating in Lido should distribute rewards obtained due to block production (including potential MEV rewards), what mechanisms or infrastructure may be used in execution of this, how rewards will be distributed, and how these activities will be monitored.

## C. Scope
This policy applies to the Lido on Ethereum protocol, the Node Operators that participate in the [Curated](#Curated-Module) and/or [Simple DVT Module](#Simple-DVT-Module) of [Lido V2's](#Lido-V2) [Staking Router](#Staking-Router), the Validators that they operate as a part of the protocol, and additional parties who play substantial roles in the process of block proposal as a result of provision of a service that is utilized by Node Operators in the execution of block proposals, such as Relays and Relay Operators.

## D. Policy Statement

### D.1 General Goals
There are two key questions that this policy addresses:
* How should Validators run by Node Operators propose blocks? and
* How should Validators run by Node Operators distribute the rewards that result from block proposals?

Since the consensus within the Ethereum community is to move towards a [PBS](#PBS) system where the building of blocks is separated from the proposal of blocks, Lido should seek to adopt an approach as close to a most likely draft of PBS as possible. Lido could become a testbed for this feature which is easier to roll back than a full-fledged implementation if something goes wrong.

On the rewards side, Validators are powered by deposits from [Stakers](#Staker), and thus Stakers should always receive the lion's share of rewards.

To that end, Lido as a protocol should:

1. Enforce - to the extent possible - that blocks are not built by Node Operators, but instead built by third parties and obtained from open and transparent builder markets.
2. Require that Node Operators ensure that relevant rewards are routed to the [`LidoExecutionLayerRewardsVault`](https://docs.lido.fi/guides/node-operators/el-rewards-configuration/) which, as per [LIP-12](https://research.lido.fi/t/lip-12-on-chain-part-of-the-rewards-distribution-after-the-merge/1625), will periodically be triggered to stake the rewards (less the Lido protocol fees), thereby essentially increasing Staker rewards.
3. Ensure - to the extent possible - that proposed blocks are valid and do not damage the proper and sustainable operation of the underlying protocol.
4. Monitor Node Operator behavior (monitoring will be publicly available) and penalize Node Operators who are found to not be in accordance with the above stated goals.

### D.2 Node Operator Requirements

#### D.2.I Priority Fees

##### Priority Fee Receipt & Distribution
Priority Fees are sent to the `feeRecipient` configured for a Validator. In general  Node Operators should ensure that Priority Fees are sent to the `LidoExecutionLayerRewardsVault`.

In case that the `feeRecipient` is otherwise overridden (e.g. due the execution of a block with extracted MEV where the `feeRecipient` points to a [Block Builder's](#Block-Builder) address instead), a transaction within the payload should exist from the Block Builder to the `LidoExecutionLayerRewardsVault` with at least the sum-total of Priority Fees for that block (for transactions sourced from the public mempool), plus any additional rewards from MEV extraction.

#### D.2.II MEV & MEV Rewards

The optimal MEV policy for Lido is to maximize staking APR and Ethereum security while minimizing [MEV hiding](https://writings.flashbots.net/writings/why-run-mevboost/#reason-5-mev-boost-prevents-mev-hiding) and decreasing the power that Lido and Node Operators have over the network by virtue of running Validators (i.e by separating block building from block proposal).

![](https://i.imgur.com/PshKLYm.jpg)


##### Maximize Staking APR
Extracting MEV is good for Lido because as a staking protocol it competes for deposits from Stakers on one side and quality Node Operators on the other. Both are maximized when staking APR is maximized. Under an alternative policy that doesn't maximize APR, Stakers would be more likely to delegate to a different provider, and Node Operators would be more likely to circumvent Lido and market to Stakers directly, and/or participate in Lido but attempt to extract the MEV in a hidden way and hide the rewards from Lido and Stakers (i.e. “MEV Hiding”).

#### Maximize Ethereum security
Extracted MEV [helps the economic security of the protocol](https://pdaian.com/blog/mev-wat-do/): “A system where validators 'leave MEV on the table' is one where an obvious attacker subsidy is readily available. This is self-defeating and degrades security in a pure-economic-rationality model, as it causes centralization/oligopoly.” To make blockchains maximally robust, all honest actors need to extract MEV at the maximum rate, otherwise they dramatically lower the cost for a dishonest actor to attack the system.

#### Minimize MEV Hiding
The MEV hiding problem is defined as follows: Node Operators handle the staking in return for a fee on the overall staking reward. However, because Node Operators can receive money in direct transfers or off-chain, the actual total rewards received by virtue of running a Validator can carry a lot of uncertainty. Lido can ensure that Stakers get their cut of the rewards it can see, but not of the rewards it cannot see -- thus, as a protocol it should attempt to ensure that all rewards are being recorded on-chain.

An example MEV hiding scenario is where Node Operators report 1 ETH reward per block to Lido (of which they receive 5%) but hide 0.5 ETH reward from Lido (of which they get to keep 100%). This is a large carrot for Node Operators at the expense of Lido and its Stakers.

The ability to surreptitiously steal decreases as confidence about the true size of the reward increases. In other words, it is an oracle problem. Staying with the above example, if Lido knows the true value of the block is 1.5 ETH, the Node Operator can still try to report 1 ETH to Lido, but the theft would be easily detectable.

To resolve the MEV hiding problem, you need a reliable oracle on the true block reward (or the block reward needs to be guaranteed in-protocol, e.g. via [two-slot PBS](https://ethresear.ch/t/two-slot-proposer-builder-separation/10980/25)). Until such a solution is possible in-protocol, infrastructure needs to be used to achieve similar outcomes to PBS but within the current technical limitations of the Ethereum protocol.

#### Minimize Power Over Blocks
Lido's potential to grow as a staking protocol is inversely proportional to the risk it poses potentially resulting from unchecked control over block production due to the amount of stake that runs through the protocol. By minimizing and separating the power (i.e. segregating proposal and building) that comes from a large number of Validators supporting one protocol, Lido can focus on its remit: maximizing staking rewards and cultivating an excellent Validator set. The underlying problem is not that Lido wants to hurt Ethereum Stakers but that it can't prove that it doesn't, or at some point won't; this is a trust problem. Therefore the optimal course of action is to minimize how much Stakers and the Ethereum ecosystem need to trust Lido and Node Operators.

The requirement that Node Operators source blocks from open markets effectively removes the potential for the protocol to coordinate or exercise influence over the content of the blocks being built by the Node Operators, and thus reduces the overall risk that the protocol may pose to the underlying network.

### D.3 Implementation
The DAO should periodically identify and evaluate what solutions are available on the market to achieve the above-stated goals. Additionally, the infrastructure and overall solution(s) adopted should:
* Be transparent and open (i.e. ideally open source) and monitorable
* Be fully tested by all Node Operators prior to usage on mainnet
* Not reduce the safety, security, or effective operation of the Lido protocol or the underlying network (Ethereum)

### D.3.I Available Infrastructure
As at the time of the most recent policy update, contributors to Lido have identified the following infrastructure that can satisfy the goals and implementation requirements outlined:

|Infrastructure|Description|Implementation Details|
|-|-|-|
|[MEV-Boost](https://boost.flashbots.net/) [Builder API](https://ethereum.org/developers/docs/mev#builder-api) [Vouch](https://www.attestant.io/posts/introducing-vouch/)|MEV-boost is an out-of-protocol implementation of PBS by [Flashbots](https://flashbots.net/), Builder API is Ethereum's temporary in-protocol solution ["for validator clients to source blocks built by external entities"](https://github.com/ethereum/builder-specs?tab=readme-ov-file#ethereum-builder-api-specification), and Vouch is a multi-node validator client built by [Attestant](https://www.attestant.io/) that ["acts as an MEV-boost server, talking to MEV relays and accepting requests from beacon nodes for execution payload headers."](https://github.com/attestantio/vouch/blob/master/docs/execlayer.md#mev-configuration) /> They work by separating concerns across at least three roles:<br /> * Block Builders (responsible for assembling block content and sending them along with bids to Relays)<br /> * Relays, operated by Relay Operators, (responsible for aggregating, sorting, and simulating bids from Block Builders, sending them to [Block Proposers](#Block-Proposer) (first encrypted, then revealed once they have been accepted right before the block is published)<br /> * Block Proposers (responsible for interfacing with Relays and selecting block proposals based on the bids that have been received).<br /><br /> Notes:<br />(1) There may be more actors (especially upstream of Block Builders) but they're not especially important for this policy.<br />(2) It's possible that a Node Operator may vertically integrate several of the above roles, but in the case of Lido, Node Operators are asked to not run their own Relays (as this might allow for MEV hiding or cheating).<br />(3) It's possible that a Relay Operator operates multiple Relays.|See [Appendix A.1](#Appendix-A.1-MEV-Boost)|
|Default block-building|Normal block building via the public mempool using standard/un-modified Execution Layer client software.|N/A|

### D.4 Monitoring & Penalties

#### Monitoring
Appropriate monitoring should be implemented (or leveraged, e.g. from third parties) in order:
* Determine whether the Node Operators are behaving as intended
* Participants (e.g. Block Builders, Relays) in the the block building process are acting in good faith and as expected
* Normal Validator and protocol operations are not adversely affected by the running of MEV-related infrastructure

#### Penalties
Node Operators who are found to be engaging in activities against the spirit of this policy and its stated goals will be subject to:
* Potential refund requests for amounts found to be incorrectly or inappropriately not distributed to the protocol
* Potential cessation of new stake deposits to their Validators
* Potential stake withdrawal and Validators being exited (voluntarily or via triggerable exits, when possible) and off-boarding from Lido on Ethereum
* Potential adverse effects on their general standing within the Lido ecosystem (e.g. having stake delegated to them in other Lido on X networks reduced, or being offboarded)

#### D.4.I Infrastructure & Availability
Monitoring with regards to Node Operator block rewards performance and possible MEV extracted may be made available through two avenues:
* Infrastructure managed by contributors to Lido; this infrastructure should be open and publicly accessible
* Infrastructure managed by third parties which includes Lido-related data; contributors to Lido should work with these third parties to ensure data related to Node Operators is easily accessible so that it may be in integrated into third party analytics and reporting tools

For details about monitoring implementations for different proposed infrastructure solutions, please see the relevant Appendix per infrastructure.

#### D.4.II Monitoring & Penalties Specification
Lido should monitor, record, and assess non-compliance with this policy. Monitoring and follow-up actions should include at least the following:
* Assessing whether the correct `feeRecipient` has been configured by the Node Operators for the Validators that they operate
* Assessing from which source proposed blocks by the Validators have been received (and if that source is appropriate)
* Assessing the general availability of the allowed sources for block bids around the time of each block being proposed
* Assessing, in cases where blocks were not constructed via chosen MEV infrastructure, if this was due to a `min-bid`-like setting and whether the value of the `min-bid` used in these cases conforms to the expectations set by the policy
* Assessing, in cases where blocks were not constructed via chosen MEV infrastructure, whether the block proposed contained transactions not available in the public mempool
* Recording, on a per-block basis, the most valuable known block bids offered via accepted MEV infrastructure (and block bid sources, e.g. Relays), i.e. the `reference block`
* Recording, on a per-block basis, the blocks actually produced by the Validators and their total value, i.e. the `actual block`
* Analyzing, on a per-block basis and historically, the difference between the `reference block` and the `actual block` for each Node Operator participating in Lido, having also properly taken into account the effect that `min-bid` usage would have on such a difference


Some leeway should be given to Node Operators for potential network problems, faulty Relays, `min-bid` usage, or buggy software outside their control. It should be enough for the Lido DAO to punish gross misbehavior when a Node Operator is deviating more than `ALLOWED_VALUE_DEV`% from the reference block, on a frequency basis more than `ALLOWED_FREQ_DEV`% of the time over a rolling time window `DEV_WINDOW`. These parameters can be reviewed and tweaked via governance, if needed.

__MEV Monitoring & Penalty Parameters__
|Variable|Value|Description|History|
|-|-|-|-|
|`ALLOWED_VALUE_DEV`|TBD|In percentage terms, how much less value an `actual block` must contain compared to the respective `reference block` in order to be considered inappropriate|
|`ALLOWED_FREQ_DEV`|TBD|In percentage terms, how often a Node Operator may deviate from the `reference block` within window `DEV_WINDOW` before being considered in breach of policy||
|`DEV_WINDOW`|TBD|In proposed blocks, the length of the rolling window|

#### D.5 Currently Prescribed Solutions

This section will be reviewed by the DAO and updated on an at-least half-yearly basis and more often if needed. It details which block production solutions may be used by Node Operators at the current time.

```markdown!
Applicability period: 24/Q1 - Release of In-Protocol Solution
(Unless otherwise overriden by a superceding DAO vote)

Summary: 2022/Q4 - 2024/Q1 MEV-Boost Full-scale Adoption & Transition to PBS Lite
```

Lido should aid Ethereum in moving towards its stated goal, PBS.

Following the Merge, Node Operators were requested to roll out and use MEV-Boost for Validators that they operate as part of the Lido protocol via a soft-rollout approach. They were asked to use as many of the [Relays which had signaled interest](https://research.lido.fi/t/lido-on-ethereum-call-for-relay-providers/2844) for inclusion as possible to test their efficacy and performance. In parallel to the soft-rollout, the DAO - with the same [Snapshot vote](https://snapshot.org/#/lido-snapshot.eth/proposal/0x7ac2431dc0eddcad4a02ba220a19f451ab6b064a0eaef961ed386dc573722a7f) as the previous version of this policy - gained approval to identify and constitute a set of multi-sig participants with the authority to determine which Relays were added to and moved between the initial "must-include" and "allow list" ("Relay lists") of the full-scale MEV-Boost adoption. The [Relay Maintenance Committee (RMC)](https://research.lido.fi/t/lido-on-ethereum-identify-and-constitute-relay-maintenance-committee/3386) has served this role under the conditions that it reacts to prospect Relays, a decline in performance, and the discontinuation of prescribed services timely, that decisions must be posted publicly on the Lido Research Forums prior to any action being taken on-chain, and that the DAO and community are given a reasonable amount of time to consider. Until the migration of the Relay lists management to Lido Easy Track scheduled for 2024/QX, the DAO retains the right to a final decision and community members may post a Snapshot vote to change or counteract RMC decisions and/or reconstitute the RMC with different participants. Since the successful adoption and until an in-protocol solution is released by Ethereum, Lido has entered the "PBS lite" phase, in which solutions that can achieve the goals outlined by this policy as an alternative to MEV-Boost are equally permitted - namely the implementations of the Builder API in various Validator clients and Vouch's MEV service.

Following and going forward, Node Operators are expected to adhere to the following:

1. Validators operated by Node Operators participating in the Lido protocol shall be configured to produce blocks via the MEV-Boost infrastructure or its equivalents as detailed in [Appendix A.1](#Appendix-A1-MEV-Boost), by obtaining sealed blocks from the maximum possible number of Relays from Lido's "must-include list" (those used will be determined by each Node Operator based on their own risk and legal assessment) and an optional number of Relays from the "allow list". *NOTE: the use of "must" here is effectively "must use some", not "must use all". The purpose is to promote Relay diversity (and thus Block Builder diversity). Node Operators may elect to not use all the Relays from the "must-include list", but the entirety of that list is used as a basis for the formation of the reference block as described in [Monitoring & Penalties](#D4-Monitoring--Penalties).*
2. Node Operators may also opt-in to configure a `min-bid` value ranging from `0` (default) to the preliminary upper maximum of XXX determined based on community alignment and agreement and published on the Lido Research Forums ([min-bid discussion thread](https://research.lido.fi/t/lido-node-operator-mev-boost-min-bid-guidance/3347)). The effects of usage of `min-bid` are incorporated into the allowable deviation parameters discussed in [Monitoring & Penalties](#D4-Monitoring--Penalties), such that only egregious and/or non-community aligned (including but not limited to values and usage that would be substantially detrimental to protocol APR) values of `min-bid` result in a Node Operator being penalized. The effects of `min-bid` implementation were analyzed and shared with the DAO to determine and codify a more specific policy. 
3. If using MEV extracting infrastructure leads to any operational failures/difficulties (such as, but not limited to: failing to produce valid blocks, failing to produce blocks at all, received rewards being incorrect, or lack of availability of appropriate Relays), the Node Operator shall refer to and act as per the guidelines in [A.1.IV Block Production & Relay Troubleshooting](#A1IV-Block-Production--Relay-Troubleshooting).
4. Blocks produced by the Validators will be monitored as per the [Monitoring & Penalties](#D4-Monitoring--Penalties) section and the monitoring section of the relevant Appendices. If at the time of the start of the applicability period the "MEV Monitoring & Penalty Parameters" have not yet been defined, a grace period will be instituted until such a time that the parameters are defined and ratified by the DAO, at which point this policy will be updated.
5. Node Operators acting in violation of the policy are subject to penalties as described in the [Monitoring & Penalties](#D4-Monitoring--Penalties) section.

At least every six months or whenever there are significant upgrades or new releases in Lido protocol, the underlying Ethereum or relevant infrastructure, the DAO will review and update (via a vote) this policy, in order to:
* re-confirm or amend the prescribed solution if deemed necessary and set the new applicability period for the next phase policy;
* stipulate or amend the values for the MEV Monitoring & Penalty parameters; and
* indicate any updates or refinements to be put in place regarding Monitoring Mechanisms.

## E. Definitions

##### Block Builder
The agent responsible for assembling the potential contents of a block (i.e. the set of transactions that makes up the "payload" of a block). In Proof-of-Work Ethereum it was the miner. In Proof-of-Stake Ethereum it is the Validator, but this activity can be separated from the Validator via PBS, in which case Block Builders need to submit bids for blocks to be included to the Block Proposer for inclusion.

##### Block Proposer
The agent responsible for proposing new blocks in a blockchain. In Proof-of-Work Ethereum it was the miner. In Proof-of-Stake Ethereum it is the Validator.

##### Curated Module
First [Staking Router](#Staking-Router) module utilizing a singular `NodeOperatorRegistry` contract for DAO-controlled registry of curated Node Operators as known from Lido V1.

##### Lido V2
See https://blog.lido.fi/lido-v2-launch/. Upgrade to the Lido protocol pushing towards increased decentralization by introduction of the [Staking Router](#Staking-Router).

##### MEV
See https://ethereum.org/developers/docs/mev

##### Node Operator
A person/organization/entity etc. that operates Validators. In the context of Lido, one that does so as a part of the Lido protocol.

##### PBS
Proposer Builder Separation, see https://notes.ethereum.org/@vbuterin/pbs_censorship_resistance , https://ethereum.org/roadmap/pbs#proposer-builder-separation , and https://members.delphidigital.io/reports/the-hitchhikers-guide-to-ethereum/ (section "In-protocol Proposer-Builder Separation")

##### Priority Fees
See https://ethereum.org/en/developers/docs/gas/#priority-fee

##### Validator
See https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/#validators . In the context of Lido, Validators operated by third party [Node Operators](#Node-Operator) for the Lido protocol.

##### Simple DVT Module
See https://blog.lido.fi/leveraging-distributed-validator-technology/. Second Staking Router module utilizing [Distributed Validator Technology (DVT)](https://ethereum.org/staking/dvt) through [Obol Network](https://obol.tech/) and [SSV Network](https://ssv.network/) implementations. This module is referred to as "Simple DVT" due to the manual coordination and curation required to adopt this early-stage technology and to reflect that this module is not intended for indefinite and "at-scale" use.

##### Staker
A person/organization/entity etc. which holds (w)stETH.

##### Staking Router
New modular architectural design to Lido protocol that allows for the development of on-ramps for new Node Operators, ranging from solo stakers to DAOs and DVT clusters.

## F. Related Documents & References

## I. Revision History

|Version|Date|Description|
|-|-|-|
|0.1|Aug 10, 2022|Initial policy creation (DRAFT)|
|1.0|Sep 7, 2022|First version of policy with MEV-Boost soft-rollout proposed to DAO|
|2.0|Oct 24, 2022|Second version of policy with MEV-Boost full-scale rollout|
|3.0|Q1, 2024|Third version of policy with introduction of Lido V2 and PBS lite operation|


# Appendix A - Detailed MEV Lido Implementations
## Appendix A.1 MEV-Boost
MEV-boost is an out-of-protocol implementation of PBS by Flashbots. It works by separating concerns across at least three roles:
* Block Builders (responsible for assembling block content and sending them along with bids to Relays)
* Relays, operated by Relay Operators, (responsible for aggregating, sorting, and simulating bids from Block Builders, sending them to Block Proposers (first encrypted, then revealed once they have been accepted right before the block is published)
* Block Proposers (responsible for interfacing with Relays and selecting block proposals based on the bids that have been received).

Notes:
(1) There may be more actors (especially upstream of Block Builders) but they're not especially important for this policy.
(2) It's possible that a Node Operator may vertically integrate several of the above roles, but in the case of Lido, Node Operators are asked to not run their own Relays (as this might allow for MEV hiding or cheating).
(3) It's possible that a Relay Operator operates multiple Relays.

### A.1.I Configuration
Node Operators should ensure that Validators that they are running for Lido are properly registered with the relevant Relays as explained in [A.1.II Relays](#A.1.II-Relays) and [D.5 Currently Prescribed Solutions](#D5-Currently-Prescribed-Solutions)

### A.1.II Relays
In MEV-Boost Relays can be configured at a per-validator level. The expectation is that Lido Node Operators would ensure that for all Validators that are being run as a part of the Lido on Ethereum protocol, the appropriate list of Relays is regularly updated and correctly configured.

Currently, MEV-boost software run by the Node Operator is not able to evaluate and assess (e.g. via simulation) whether the reward in the block received from the Block Builder is correct, so it cannot drop “bad” or “underpaid” blocks.

As an immediate practical solution, Relays will basically need to be trusted to do this, which is in part why Flashbots aims to build a [monitoring + reputation system](https://github.com/flashbots/mev-boost/issues/142) for Relays.

As a result, if using MEV-boost, Lido should adopt the following requirements with regards to use of Relays by Lido Node Operators:
1. The Lido DAO, either directly or via an appointed committee/sub-group, will maintain two lists of MEV-Boost relays:
    1. A "must-include list" of Relays - DAO-vetted relays that are considered trustworthy, well-operated, and reliable.
    2. An "allow list" of Relays - DAO-approved but perhaps less well-known or battle-tested Relays which can be trialled prior to inclusion in the "must-include list".
2. For Validators which they run as a part of the Lido protocol, Node Operators should configure their MEV-Boost instances to connect to as many of the relays included in the "must include list" as possible, based on each Node Operator's risk and legal assessment. Additionally and optionally, they may include any of the Relays included in the "allow list". Node Operators should not source blocks from Relays not included in either list.
3. Both the "must include list" and the "allow list" should be maintained in such a way that they are:
    1. Relatively easily amendable (in case Relays should be considered for addition, e.g. via public request, or Relays should be removed e.g. for bad performance), and
    2. Publicly listed and easily retrievable (by the public, by Block Builders, and by Node Operators).

As a starting point, it is highly suggested that relays considered for inclusion in these lists must be:
1. publicly available,
2. publicly listed & maintained,
4. open source,
5. transparent about what (if any) transaction or address filtering they enforce,
6. simulate blocks (or bundles) contained in bids submitted by Block Builders for validity and correctness, including rewards and fee values.

### A.1.III Monitoring

Spec is being refined as MEV-Boost specs and Relay API are being finalized, meanwhile please refer to https://hackmd.io/@george-avs/SyGqpItIc for the general gist.

### A.1.IV Block Production & Relay Troubleshooting

In case that operational issues are noticed (e.g. blocks not produced as expected, blocks not being timely unblinded, blocks not being timely created and propagated, blocks being invalid, relays not providing agreed upon data or monitoring APIs, etc.) the Node Operator should temporarily disable the relevant Relay(s) until such a time that:
* (a) service is sufficiently and satisfactorily restored, or 
* (b) the DAO and Node Operators collectively decide that the Relay(s) should no longer be used and removed from the relevant vetted lists, at which point a DAO vote should be made to do so.

In these cases, the Node Operator should also:
* timely inform the rest of the Lido Node Operator community about the issues identified,
* reach out to the Lido Node Operator Management contributors to notify them of the issues, and
* reach out to the Relay Operator to understand the source of the issue and report back

If a Node Operator is found to be "under-performing" as per the mechanism described in [D.4 Monitoring & Penalties](#D4-Monitoring--Penalties) but this is due to issues stemming from a bad Relay or malicious Block Builders interfering with block production, then the Node Operator will not be considered out of compliance with the policy. 

### A.1.V Relay & Relay Operator Responsibilities and Incident Management
Given the trusted nature of Relays in the MEV-Boost context, it behooves Relay Operators to ensure that they and the Relays that they operate function effectively, performantly, and efficiently, and that any and all communications are made timely and professionally. 

Relay Operators should notify relevant Lido protocol participants (e.g. Node Operators, the Node Operator Mechanisms Workstream) as soon as possible upon identification of an issue with any of the Relays that they operate. Notification may be performed through public ([Lido Research Forums](https://research.lido.fi/), Lido Discord, etc.) and/or private channels (e.g. group chats) available to them, so that Node Operators can proceed with appropriate changes to their MEV-Boost configurations.

During an incident, Relay Operators should provide regular updates with regards to the status of the incident. Following the conclusion of the incident, Relay Operators should provide an incident report (or post-mortem) that details at minimum the root cause, fixes performed, if/how soon the relay will be back in service, and what steps have been undertaken to prevent the likelihood of similar incidents in the future. Additionally, in cases where block production was adversely affected (see [A.1.IV Block Production & Relay Troubleshooting](#A1IV-Block-Production--Relay-Troubleshooting)), Relay Operators should advise as to what steps will be taken (if any) to reimburse Validators for any missed/lost/invalid/etc. blocks and/or incorrect/invalid/inaccurate bids.

Incidents will be reviewed on an ad-hoc basis by the Node Operator and Lido DAO communities. These reviews will inform DAO decisions (via vote) regarding whether Relay Operators and the Relays that they operate will continue to be considered vetted and ratified for use by Node Operators or not.

Relays which exhibit poor or otherwise unacceptable communication and especially performance (such as, but not limited to, inability to performantly and timely register Validators, inability to performantly and timely send/receive bids and payloads, not verify bids, not simulate payload validity etc.), and/or fail to conform with the expectations outlined above with regards to incident management (as determined by the DAO and Node Operator communities) will be subject to removal from the Lido-approved Relay lists.
