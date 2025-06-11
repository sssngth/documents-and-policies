# Lido on Ethereum Block Proposer Rewards Policy

```markdown!
STATUS: v2.0 (22/Q4 - 23/Q1 MEV-Boost Full-scale Adoption - Phase 1)
```

## A. Overview
As a DAO and a protocol, Lido on Ethereum should have a transparent, enforceable, and monitorable policy with regards to how its constituent [Node Operators](#Node-Operator) are expected to behave with regards to rewards that accrue from the activity of producing blocks for [validators](#Validator) that they run as a part of Lido, namely [priority fees](#Priority-Fees) and [MEV](#MEV) extraction, and how the protocol will distribute the rewards that accrue to the protocol as a result of these activities.

## B. Purpose
This policy aims to outline how Node Operators participating in Lido should distribute rewards obtained due to block production (including potential MEV rewards), what mechanisms or infrastructure may be used in execution of this, how rewards will be distributed, and how these activities will be monitored.

## C. Scope
This policy applies to the Lido on Ethereum protocol, the [Node Operators](#Node-Operator) that participate in the protocol, the validators that they operate as a part of the protocol, and additional parties who play substantial roles in the process of block proposal as a result of provision of a service that is utilized by Node Operators in the execution of block proposals, such as relays and relay operators.

## D. Policy Statement

### D.1 General goals
There are two key questions that this policy addresses:
* How should validators run by Node Operators propose blocks? and
* How should validators run by Node Operators distribute the rewards that result from block proposals?

Since the consensus within the Ethereum community is to move towards a [PBS](#PBS) system where the building of blocks is separated from the proposal of blocks, Lido should seek to adopt an approach as close to a most likely draft of PBS as possible. Lido could become a testbed for this feature which is easier to roll back than a full-fledged implementation if something goes wrong.

On the rewards side, validators are powered by deposits from [stakers](#Staker), and thus stakers should always receive the lion's share of rewards.

To that end, Lido as a protocol should:

1. Enforce - to the technical extent possible - that blocks are not built by Node Operators, but instead built by third parties and obtained from open and transparent builder markets.
2. Require that Node Operators ensure that relevant rewards are routed to the [`Lido Execution Layer Rewards vault`](https://docs.lido.fi/guides/node-operator-manual/#execution-layer-feesrewards-configuration-priority-fee-and-mev-rewards-collection-and-distribution) which, as per [LIP-12](https://research.lido.fi/t/lip-12-on-chain-part-of-the-rewards-distribution-after-the-merge/1625), will periodically be triggered to stake the rewards (less the Lido protocol fees), thereby essentially increasing staker rewards.
3. Ensure, to the extent possible, that proposed blocks are valid and do not damage the proper and sustainable operation of the underlying protocol.
4. Monitor Node Operator behavior (monitoring will be publicly available) and penalize Node Operators who are found to not be in accordance with the above stated goals.

### D.2 Node Operator requirements

#### D.2.I Priority Fees

##### Priority fee receipt and distribution
[Priority fees](#Priority-fees) are sent to the `fee recipient` configured for a validator. In general  Node Operators should ensure that priority fees should be sent to the `Lido Execution Layer Rewards Vault`.

In the case that the `fee recipient` is otherwise overridden (e.g. due the execution of a block with extracted MEV where the `fee recipient` points to a [block builder's](#Block-Builder) address instead), a transaction within the payload should exist from the block builder to the Lido Execution Layer Rewards Vault with at least the sum-total of priority fees for that block (for transactions sourced from the public mempool), plus any additional rewards from MEV extraction.

#### D.2.II MEV and MEV rewards

The optimal [MEV](#MEV) policy for Lido is to maximize staking APR and Ethereum security while minimizing [MEV hiding](https://writings.flashbots.net/writings/why-run-mevboost/#reason-5-mev-boost-prevents-mev-hiding) and decreasing the power that Lido and Node Operators have over the network by virtue of running validators (i.e by separating block building from block proposal).

![](https://i.imgur.com/PshKLYm.jpg)


##### Maximize Staking APR
Extracting [MEV](#MEV) is good for Lido because as a staking protocol it competes for stake from users on the one side and quality Node Operators on the other. Both are maximized when staking APR is maximized. Under an alternative policy that doesn’t maximize APR, stakers would be more likely to delegate to a different provider, and Node Operators would be more likely to circumvent Lido and market to users directly, and/or participate in Lido but attempt to extract the MEV in a hidden way and hide the rewards from Lido and stakers (i.e. “MEV hiding”).

#### Maximize Ethereum security
Extracted MEV [helps the economic security of the protocol](https://pdaian.com/blog/mev-wat-do/): “A system where validators 'leave MEV on the table' is one where an obvious attacker subsidy is readily available. This is self-defeating and degrades security in a pure-economic-rationality model, as it causes centralization/oligopoly.” To make blockchains maximally robust, all honest actors need to extract MEV at the maximum rate, otherwise they dramatically lower the cost for a dishonest actor to attack the system.

#### Minimize MEV hiding
The MEV hiding problem is defined as follows: Node Operators handle the staking in return for a fee on the overall staking reward. However, because Node Operators can receive money in direct transfers or off-chain, the actual total rewards received by virtue of running a validator can carry a lot of uncertainty. Lido can ensure that stakers get their cut of the rewards it can see, but not of the rewards it cannot see -- thus, as a protocol it should attempt to ensure that all rewards are being recorded on chain.

An example MEV-hiding scenario is where Node Operators report 1 ETH reward per block to Lido (of which they receive 5%) but hide 0.5 ETH reward from Lido (of which they get to keep 100%). This is a large carrot for Node Operators at the expense of Lido and its stakers.

The ability to surreptitiously steal decreases as confidence about the true size of the reward increases. In other words, it is an oracle problem. Staying with the above example, if Lido knows the true value of the block is 1.5 ETH, the Node Operator can still try to report 1 ETH to Lido, but the theft would be easily detectable.

To resolve the MEV-hiding problem, you need a reliable oracle on the true block reward (or the block reward needs to be guaranteed in-protocol, e.g. via [two-slot PBS](https://ethresear.ch/t/two-slot-proposer-builder-separation/10980/25)). Until such a solution is possible in-protocol, infrastructure needs to be used to achieve similar outcomes to PBS but within the current technical limitations of the Ethereum protocol.

#### Minimize power over blocks
Lido's potential to grow as a staking protocol is inversely proportional to the risk it poses potentially resulting from unchecked control over block production due to the amount of stake that runs through the protocol. By minimizing and separating the power (i.e. segregating proposal and building) that comes from a large number of validators supporting one protocol, Lido can focus on its remit: maximizing staking rewards and cultivating an excellent validator set. The underlying problem is not that Lido wants to hurt Ethereum users but that it can’t prove that it doesn’t, or at some point won't; this is a trust problem. Therefore the optimal course of action is to minimize how much users and the Ethereum ecosystem need to trust Lido and Node Operators.

The requirement that Node Operators source blocks from open markets effectively removes the potential for the protocol to coordinate or exercise influence over the content of the blocks being built by the Node Operators, and thus reduces the overall risk that the protocol may pose to the underlying network.

### D.3 Implementation
The DAO should periodically identify and evaluate what solutions are available on the market to achieve the above-stated goals. Additionally, the infrastructure and overall solution(s) adopted should:
* Be transparent and open (i.e. ideally open source) and monitorable
* Be fully tested by all Node Operators prior to usage on mainnet
* Not reduce the safety, security, or effective operation of the Lido protocol or the underlying network (Ethereum)

### D.3.I Available infrastructure
As at the time of the most recent policy update, contributors to Lido have identified the following infrastructure that can satisfy the goals and implementation requirements outlined:

|Infrastructure|Description|Implementation Details|
|-|-|-|
|[MEV-Boost](https://boost.flashbots.net/)|MEV-boost is an out-of-protocol implementation of [PBS](#PBS) by [Flashbots](https://flashbots.net/). It works by separating concerns across at least three roles: <br /> * Block Builders (responsible for assembling block content and sending them along with bids to relays) <br /> * Relays, operated by Relay Operators, (responsible for aggregating, sorting, and simulating bids from builders, sending them to Block Proposers (first encrypted, then revealed once they have been accepted right before the block is published)<br /> * Block Proposers (responsible for interfacing with relays and selecting block proposals based on the bids that have been received).<br /><br /> Notes:<br />(1) There may be more actors (especially upstream of Block Builders) but they're not especially important for this policy.<br />(2) It's possible that a Node Operator may vertically integrate several of the above roles, but in the case of Lido Node Operators are asked to not run their own relays (as this might allow for MEV hiding or cheating).|See [Appendix A.1](#Appendix-A.1-MEV-Boost)|
|Default block-building|Normal block building via the public mempool using standard / un-modified Execution Layer client software.|N/A|

### D.4 Monitoring & Penalties

#### Monitoring
Appropriate monitoring should be implemented (or leveraged, e.g. from third parties) in order:
* Determine whether the Node Operators are behaving as intended
* Participants (e.g. block builders, relays) in the the block building process are acting in good faith and as expected
* Normal validator and protocol operations are not adversely affected by the running of MEV-related infra

#### Penalties
Node Operators who are found to be engaging in activities against the spirit of this policy and its stated goals will be subject to:
* Potential refund requests for amounts found to be incorrectly or inappropriately not distributed to the protocol
* Potential cessation of new stake deposits to their validators
* Potential stake withdrawal and validators being exited (voluntarily or via triggerable exits, when possible) and off-boarding from the Lido on Ethereum set
* Potential adverse effects on their general standing within the Lido ecosystem (e.g. having stake delegated to them in other Lido on X networks reduced, or being offboarded)

#### D.4.I Infrastructure and Availability
Monitoring with regards to Node Operator block rewards performance and possible MEV extracted may be made available through two avenues:
* Infrastructure managed by contributors to Lido; this infrastructure should be open and publicly accessible
* Infrastructure managed by third parties which includes Lido-related data; contributors to Lido should work with these third parties to ensure data related to Node Operators is easily accessible so that it may be in integrated into third party analytics and reporting tools

For details about monitoring implementations for different proposed infrastructure solutions, please see the relevant Appendix per infrastructure.

#### D.4.II Monitoring & Penalties Specification
Lido should monitor, record, and assess non-compliance with this policy. Monitoring and follow-up actions should include at least the following:
* Assessing whether the correct `fee recipient` has been configured by the [Node Operators](#Node-Operator) for the validators that they operate
* Assessing from which source proposed blocks by the validators have been received (and if that source is appropriate)
* Assessing the general availability of the allowed sources for block bids around the time of each block being proposed
* Assessing, in cases where blocks were not constructed via chosen MEV infrastructure, if this was due to a `min-bid`-like setting and whether the value of the `min-bid` used in these cases conforms to the expectations set by the Policy
* Assessing, in cases where blocks were not constructed via chosen MEV infrastructure, whether the block proposed contained transactions not available in the public mempool
* Recording, on a per-block basis, the most valuable known block bids offered via accepted MEV infrastructure (and block bid sources, e.g. relays), i.e. the `reference block`
* Recording, on a per-block basis, the blocks actually produced by the validators and their total value, i.e. the `actual block`
* Analyzing, on a per-block basis and historically, the difference between the `reference block` and the `actual block` for each Node Operator participating in Lido, having also properly taken into account the effect that `min-bid` usage would have on such a difference


Some leeway should be given to Node Operators for potential network problems, faulty relays, `min-bid` usage, or buggy software outside their control. It should be enough for the Lido DAO to punish gross misbehavior when a Node Operator is deviating more than `ALLOWED_VALUE_DEV`% from the reference block, on a frequency basis more than `ALLOWED_FREQ_DEV`% of the time over a rolling time window `DEV_WINDOW`. These parameters can be reviewed and tweaked via governance, if needed.

__MEV Monitoring & Penalty parameters__
|Variable|Value|Description|History|
|-|-|-|-|
|`ALLOWED_VALUE_DEV`|TBD|In percentage terms, how much less value an `actual block` must contain compared to the respective `reference block` in order to be considered inappropriate|
|`ALLOWED_FREQ_DEV`|TBD|In percentage terms, how often a Node Operator may deviate from the `reference block` within window `DEV_WINDOW` before being considered in breach of policy||
|`DEV_WINDOW`|TBD|In proposed blocks, the length of the rolling window|

#### D.5 Currently prescribed solution(s)

This section will be reviewed by the DAO and updated on an at-least yearly basis and more often if needed. It details which block production solutions may be used by Node Operators at the current time.

```markdown!
Applicability period: 2022/11/1 - 2023/6/30
(Unless otherwise overriden by a superceding DAO vote)

Summary: 2022/Q4 - 2023/Q2 MEV-Boost Full-scale Adoption - Phase 1
```

Lido should aid Ethereum in moving towards its stated goal, PBS.

Following the Merge, Node Operators were requested to roll out and use MEV-Boost for validators that they operate as a part of the Lido protocol via a soft-rollout approach. They were asked to use as many of the relays which had signaled interest for inclusion as possible in order to test the efficacy and performance of the relays. Following the soft-rollout period and going forward, Node Operators are expected to adhere to the following:
1. The DAO will hold a Snapshot vote to: 
    * determine which MEV-Boost relays will be added to the initial "must-include list" and "allow list" ("relay lists")
    * for ease of governance operations given the number of new relays being created and the need for haste in certain scenarios, identify and approve a set of multi-sig participants who will manage the relay lists (the "Relay Maintenance Committee (RMC)") for the duration of Phase 1, or until the management of the relay lists can be moved to [Lido Easy Track](https://docs.lido.fi/guides/easy-track-guide)
    * delegate authority to the RMC to make decisions about which relays are added to or moved between the "relay lists", provided that RMC decisions are posted publicly on the Lido research forums prior to any on-chain actions being taken and giving the DAO and community a reasonable amount of time to consider. The DAO retains the right to a final decision and community members may post a Snapshot vote to change or counteract RMC decisions and/or reconstitute the RMC with different participants.
2. Validators operated by Node Operators participating in the Lido protocol shall be configured to produce blocks via the MEV-Boost infrastructure (or equivalent, such as Vouch's MEV-Service) as detailed in [Appendix A.1](#Appendix-A1-MEV-Boost), by obtaining sealed blocks from the maximum possible number of relays from Lido's "must-include list" (those used will be determined by each Node Operator based on their own risk and legal assessment) and an optional number of relays from the "allow list". *NOTE: the use of "must" here is effectively "must use some", not "must use all". The purpose is to promote relay diversity (and thus builder diversity). Node Operators may elect to not use all the relays from the "must-include list", but the entirety of that list is used as a basis for the formation of the reference block as described in [Monitoring & Penalties](#D4-Monitoring-amp-Penalties).*
3. In Phase 1, Node Operators may also opt-in to configure a `min-bid` value in MEV-Boost ranging from `0` (default) to a preliminary upper maximum to be determined based on community alignment and agreement and published on the Lido Research Forums ([min-bid discussion thread](https://research.lido.fi/t/lido-node-operator-mev-boost-min-bid-guidance/3347)). The effects of usage of `min-bid` shall be incorporated into the allowable deviation parameters discussed in [Monitoring & Penalties](#D4-Monitoring-amp-Penalties), such that only egregious and/or non-community aligned (including but not limited to values and usage that would be substantially detrimental to protocol APR) values of `min-bid` would result in a Node Operator being penalized. The effects of `min-bid` implementation will be analyzed and shared with the DAO during Phase 1 to determine and codify a more specific policy. 
4. If using MEV-boost infrastructure leads to any operational failures/difficulties (such as, but not limited to: failing to produce valid blocks, failing to produce blocks at all, received rewards being incorrect, or lack of availability of appropriate relays), the Node Operator shall refer to and act as per the guidelines in [A.1.IV Block Production & Relay Troubleshooting](#A1IV-Block-Production-amp-Relay-Troubleshooting).
5. Blocks produced by the validators will be monitored as per [Monitoring & Penalties](#D4-Monitoring-amp-Penalties) section and the monitoring section of the relevant Appendices. If as at the time of the start of the applicability period the "MEV Monitoring & Penalty parameters" have not yet been defined, a grace period will be instituted until such a time that the parameters are defined and ratified by the DAO, at which point this policy will be updated.
7. Node Operators acting in violation of the policy are subject to penalties as described in the [Monitoring & Penalties](#D4-Monitoring-amp-Penalties) section.

Prior to the end of the Phase 1 of the Full-Scale Adoption period, or as soon as possible thereafter, the DAO will review and update (via a vote) this policy, in order to:
* re-confirm or amend the prescribed solution if deemed necessary and set the new applicability period for the next phase policy;
* stipulate or amend the values for the MEV Monitoring & Penalty parameters; and
* indicate any updates or refinements to be put in place regarding Monitoring Mechanisms.

## E. Definitions

##### Block builder
The agent responsible for assembling the potential contents of a block (i.e. the set of transactions that makes up the "payload" of a block). In Proof of Work Ethereum it's the miner. In Proof of Stake Ethereum it's the validator, but this activity can separated from the validator via [PBS](#PBS), in which case builders need to submit bids for blocks to be included to the [block proposer](#Block-proposer) for inclusion.

##### Block proposer
The agent responsible for proposing new blocks in a blockchain. In Proof of Work Ethereum it's the miner. In Proof of Stake Ethereum it's the validator.

##### MEV
Originally "Miner Extractable Value" as applied to Proof of Work blockchains, the more all-encompassing "Maximal Extractable Value" terminology is now used to refer to the potential value that may be extracted from activity of the selection, ordering, and insertion of transactions into a block by block proposers. 

##### Node Operator
An organization/entity/person that operates [validators](#Validator). In the context of Lido, ones that do so as a part of the Lido protocol.

##### PBS
Proposer Builder Separation, see: https://notes.ethereum.org/@vbuterin/pbs_censorship_resistance and https://members.delphidigital.io/reports/the-hitchhikers-guide-to-ethereum/ (section "In-protocol Proposer-Builder Separation")

##### Priority Fees
See https://ethereum.org/en/developers/docs/gas/#priority-fee

##### Validator
See https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/#validators . In the context of Lido, validators operated by 3rd party [Node Operators](#Node-Operator) for the Lido protocol.

##### Staker
A user/organization/etc. which holds (w)stETH.

## F. Related Documents and References

## I. Revision History

|Version|Date|Description|
|-|-|-|
|0.1|Aug 10, 2022|Initial policy creation (DRAFT)|
|1.0|Sep 7, 2022|First version of policy with soft-rollout proposed to DAO|
|2.0|Oct 24, 2022|Second version of policy with full-scale rollout|


# Appendix A - Detailed MEV Lido Implementations
## Appendix A.1 MEV-Boost
MEV-boost is an out-of-protocol implementation of PBS by Flashbots. It works by separating concerns across at least three roles:
* Block Builders (responsible for assembling block content and sending them along with bids to relays)
* Relays, operated by Relay Operators, (responsible for aggregating, sorting, and simulating bids from builders, sending them to Block Proposers (first encrypted, then revealed once they have been accepted right before the block is published)
* Block Proposers (responsible for interfacing with relays and selecting block proposals based on the bids that have been received).

Notes:
(1) There may be more actors (especially upstream of Block Builders) but they’re not especially important for this policy.
(2) It’s possible that a Node Operator may vertically integrate several of the above roles, but in the case of Lido Node Operators are asked to not run their own relays (as this might allow for MEV hiding or cheating).
(3) It's possible that a Relay Operator operates multiple relays.

### A.1.I Configuration
Node Operators should ensure that validators that they are running for Lido are properly registered with the relevant relays as explained in [A.1.II Relays](#A.1.II-Relays) and [D.5 Currently Prescribed Solutions](#D5-Currently-prescribed-solutions)

### A.1.II Relays
In MEV-Boost relays can be configured at a per-validator level. The expectation is that Lido Node Operators would ensure that for all validators that are being run as a part of the Lido on Ethereum protocol, the appropriate list of relays is regularly updated and correctly configured.

Currently, MEV-boost software run by the Node Operator is not able to evaluate and assess (e.g. via simulation) whether the reward in the block received from the block builder is correct, so it cannot drop “bad” or “underpaid” blocks.

As an immediate practical solution, Relays will basically need to be trusted to do this, which is in part why Flashbots aims to build a [monitoring + reputation system](https://github.com/flashbots/mev-boost/issues/142) for relays.

As a result, if using MEV-boost, Lido should adopt the following requirements with regards to use of relays by Lido Node Operators:
1. The Lido DAO, either directly or via an appointed committee/sub-group, will maintain two lists of MEV-Boost relays:
    1. A "must-include list" of relays - DAO-vetted relays that are considered trustworthy, well-operated, and reliable.
    2. An "allow list" of relays - DAO-approved but perhaps less well-known or battle-tested relays which can be trialled prior to inclusion in the "must-include list".
2. For validators which they run as a part of the Lido protocol, Node Operators should configure their MEV-Boost instances to connect to as many of the relays included in the "must include list" as possible, based on each Node Operator's risk and legal assessment. Additionally and optionally, they may include any of the relays included in the "allow list". Node Operators should not source blocks from relays not included in either list.
3. Both the "must include list" and the "allow list" should be maintained in such a way that they are:
    1. Relatively easily amendable (in case relays should be considered for addition, e.g. via public request, or relays should be removed e.g. for bad performance), and
    2. Publicly listed and easily retrievable (by the public, by block-builders, and by Node Operators).

As a starting point, it is highly suggested that relays considered for inclusion in these lists must be:
1. publicly available,
2. publicly listed & maintained,
4. open source,
5. transparent about what (if any) transaction or address filtering they enforce,
6. simulate blocks (or bundles) contained in bids submitted by builders for validity and correctness, including rewards and fee values.

### A.1.III Monitoring

Spec is being refined as MEV-Boost specs and Relay API are being finalized, meanwhile please refer to https://hackmd.io/@george-avs/SyGqpItIc for the general gist.

### A.1.IV Block Production & Relay Troubleshooting

In the case that operational issues are noticed (e.g. blocks not produced as expected, blocks not being timely unblinded, blocks not being timely created and propagated, blocks being invalid are invalid, relays not providing agreed upon data or monitoring APIs, etc.) the Node Operator should temporarily disable the relevant relay(s) until such a time that:
* (a) service is sufficiently and satisfactorily restored, or 
* (b) the DAO and Node Operators collectively decide that the relay(s) should no longer be used and removed from the relevant vetted lists, at which point a DAO vote should be made to do so.

In these cases, the Node Operator should also:
* timely inform the rest of the Lido Node Operator community about the issues identified,
* reach out to the Lido Node Operator Management contributors to notify them of the issues, and
* reach out to the relay operator to understand the source of the issue and report back

If a Node Operator is found to be "under-performing" as per the mechanism described in [D.4 Monitoring & Penalties](#D4-Monitoring-amp-Penalties) but this is due to issues stemming from a bad relay or malicious builders interfering with block production, then the Operator will not considered out of compliance with the policy. 

### A.1.V Relay & Relay Operator Responsibilities and Incident Management
Given the trusted nature of relays in the MEV-Boost context, it behooves Relay Operators to ensure that they and the relays that they operate function effectively, performantly, and efficiently, and that any and all communications are made timely and professionally. 

Relay Operators should notify relevant Lido protocol participants (e.g. Node Operators, the Node Operator Management Workstream) as soon as possible upon identification of an issue with any of the relays that they operate. Notification may be performed through public ([Lido forum](https://research.lido.fi/), Lido discord, etc.) and/or private channels (e.g. group chats) available to them, so that Node Operators can proceed with appropriate changes to their MEV-Boost configurations.

During an incident, Relay Operators should provide regular updates with regards to the status of the incident. Following the conclusion of the incident, Relay Operators should provide an incident report (or post-mortem) that details at minimum the root cause, fixes performed, if/how soon the relay will be back in service, and what steps have been undertaken to prevent the likelihood of similar incidents in the future. Additionally, in cases where block production was adversely affected (see [A.1.IV Block Production & Relay Troubleshooting](#A1IV-Block-Production-amp-Relay-Troubleshooting)), Relay Operators should advise as to what steps will be taken (if any) to reimburse validators for any missed/lost/invalid/etc. blocks and/or incorrect/invalid/inaccurate bids.

Incidents will be reviewed on an ad-hoc basis by the Node Operator and Lido DAO communities. These reviews will inform DAO decisions (via vote) regarding whether Relay Operators and the relays that they operate will continue to be considered vetted and ratified for use by Node Operators or not.

Relays which exhibit poor or otherwise unacceptable communication and especially performance (such as, but not limited to, inability to performantly and timely register validators, inability to performantly and timely send/receive bids and payloads, not verify bids, not simulate payload validity etc.), and/or fail to conform with the expectations outlined above with regards to incident management (as determined by the DAO and Node Operator communities) will be subject to removal from the Lido-approved relay lists.
