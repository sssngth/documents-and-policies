# Lido on Ethereum Standard Node Operator Protocol - Block Proposals

```markdown!
STATUS: V3.0
```

## A - Purpose
This Standard Node Operator Protocol (SNOP) outlines the standards, Auxiliary Proposer Mechanisms (APMs, see [Appendix A](#appendix-a---ethereum-standards-auxiliary-proposer-mechanisms--infrastructure)) and infrastructure available for block proposals in Ethereum, the rules around the usage of this SNOP, the considerations that guide the expectations of the Lido on Ethereum protocol ("Lido") with regard to block proposals and APMs, the responsibilities Node Operators (NOs) using Lido have in performing block proposals utilizing APMs and distributing the associated rewards to stakers, and the actions that NOs can expect in case of non-conformance with this SNOP.

## B - Scope
This SNOP applies to Lido, the NOs who use one or more of the [Staking Router (SR)'s](https://docs.lido.fi/contracts/staking-router) [Curated Module (CM)](https://operatorportal.lido.fi/modules/curated-module), [Simple Distributed Validator Technology Module (SDVTM)](https://operatorportal.lido.fi/modules/simple-dvt-module), [Community Staking Module (CSM)](https://operatorportal.lido.fi/modules/community-staking-module), future Staking Modules (SMs) or staking avenues, and the validators the NOs operate as part of the protocol.

The below stipulated operational flows and expectations apply to all above-mentioned SMs, except in cases where specific mentions are made to alternate flows or expectations for specific SMs due to differences in their technical or operational characteristics.

**_NOTE:_** As terms can have different meanings based on how they are used and in different contexts, the table below aims to clarify the usage of various terms for the purposes of this SNOP and how they apply within the context of Lido.

Term|Definition|Context
:---:|---|---
Node Operator|An entity, individual, or set thereof who operate(s) nodes while using one or more of Lido's SMs to run Ethereum validators|CM & CSM solo operation:<br>A single entity or individual operating validators<br><br>SDVTM operation:<br>A cluster of independent entities and/or individuals operating validators as a group<br><br>CSM DVT operation:<br>An entity, individual, or cluster thereof operating validators
Validator|A validator, also referred to as a "key" in this SNOP, is the technical component of participating in Ethereum's consensus, that - if active - represents a maximum effective balance ("stake weight") of between 32 and - in increments of 1 - up to 2048 ETH, is controlled by a public-private validator signing key pair and performs duties for the network -- attesting to and proposing new blocks, participating in sync committees, and reporting network violations of the slashing rules|CM & CSM solo operation:<br>A validator run by a single NO<br><br>SDVTM operation:<br>A Distributed Validator (DV) run by a cluster of independent NOs utilizing [Distributed Validator Technology (DVT)](https://ethereum.org/en/staking/dvt/#distributed-validator-technology)<br><br>CSM DVT operation:<br>A DV run by a single NO or cluster utilizing DVT

## C - Guiding Considerations
As per the [Lido DAO Vibe](https://snapshot.org/#/s:lido-snapshot.eth/proposal/0x739fbe56425d355b5e41c22bb346b7a8217afd9e84aa49863648b1c641a482e3) and iterated upon by the community in [Guided Open Objective Setting Exercises (GOOSE)](https://snapshot.org/#/s:lido-snapshot.eth/proposal/0x58bbc5d540e46081043ef29d4d1ee56b7df4dc1b0369aa78e0c15a2403549353), the Lido DAO strives to contribute to the decentralization, accessibility, and censorship resistance of Ethereum. The following guiding considerations for NOs using Lido thus flow from these overarching goals.

### C.1 - Economic Security
Game-theoretically, to optimally protect Ethereum's Proof-of-Stake (PoS) consensus from 51% attacks and to facilitate the positive impact that Lido can have on the network's validator set, the protocol should not leave potential revenue on the table but focus on economic competitiveness. The rationale for this is set out in [Hasu's Maximal Extractable Value (MEV) proposal](https://research.lido.fi/t/proposal-optimal-mev-policy-for-lido/2489).

Advancing this goal entails the maximization of validator and thus protocol APR in balance with duty effectiveness by enabling NOs using Lido to fulfill the Ethereum validator duties - attesting to and proposing new blocks, participating in sync committees, and reporting network violations of the slashing rules - as reliably, timely, correctly, and free of slashable offenses as possible.

Additionally, it makes sense for the Lido ecosystem to enable participants to adopt the latest Ethereum-aligned standards and infrastructure that may transform staking or impact stakers financially.

Since NOs control (see [D.1](#d1---fee-recipient)), the destination for Execution Layer (EL) rewards, there is a possibility that NOs using Lido could try to redirect EL rewards received in an attempt to earn more than the programmatically intended share of the protocol rewards. In the best interest of stakers and honest NOs, rewards stealing through the use of Lido should be difficult to conceal and expensive to execute. Irrespective if for a [bonded SM](https://operatorportal.lido.fi/modules/community-staking-module#block-88e6d7eca6364a758541dc1ee66a278f) such as CSM or a module based on social reputation -- to ensure fair conditions in all SMs, it is important for NO activities to be transparently monitorable. For this purpose, a multitude of community-built open-source [tools](https://operatorportal.lido.fi/existing-operator-portal/ethereum-onboarding/no-resources-tooling) and [dashboards](https://operatorportal.lido.fi/operator-statistics-and-metrics) are openly available.

In the context of APMs, attention is to be paid to the potential implications that attaching new mechanisms to the protocol may have on the block construction and proposal value chain, specifically with respect to the resulting value and contents of blocks proposed by NOs using Lido.

### C.2 - Decentralization & Operational Diversity
Decentralization is one of the core values of the Lido DAO and its prevalence, just like that of economic security, an integral premise for the robustness of the Ethereum network. For this reason, the objective function of Lido should balance economic security with decentralization and operational diversity to promote the health and robustness of the Ethereum network.

Diverse setups, i.a., in terms of the geographic dispersion, infrastructure and clients utilized to run nodes, are critical to proactively mitigate risks of local outages, isolated bugs, and to globally reduce Ethereum p2p latencies. NOs using Lido are encouraged to drive these sustained efforts and historically Lido stakers and community participants have been immensely supportive of NOs expanding their infrastructure to underrepresented geographic regions and to diversified, multi-client setups.

Another important aspect of decentralization is [credible neutrality](https://nakamoto.com/credible-neutrality/). For NOs using Lido this generally entails to openly participate in and use a variety of perhaps competing software clients, infrastructure technologies and, in the case of APMs, mechanism/protocol implementations. Nevertheless, the principle of credible neutrality should be balanced against operational and technical security considerations as the community sets out guidelines around what tooling can be used and how.

### C.3 - Censorship Resistance
A third attribute to balance the dimensions of economic security, decentralization and operational diversity against, is censorship resistance.

Despite the theoretical possibility of out-of-protocol proposer-builder separation (PBS) to dissolve the validator monopoly on transaction inclusion and ordering (see [Appendix A.4.1 ff](#a41---proposer-builder-separation)), the optionality of the mechanism's application and of the MEV relays selection by the NOs mean that Ethereum users can still be censored. In fact, censorship most rarely takes place only at the [level of the validators](https://censorship.pics/), but rather already at the relays that refuse to broadcast payloads including transactions from certain addresses, at the builders that disregard such transactions when constructing payloads, or even before, e.g., at frontends that refuse to serve users from certain regions or application programming interfaces (APIs) that refuse to transmit certain transactions -- the latter going beyond the scope of this SNOP though.

As outlined in section Decentralization & Operational Diversity (see [C.2](#c2---decentralization--operational-diversity)) credible neutrality is a vital part of a healthy Ethereum and credible neutrality supported by participants that enable the censorship resistance of the network. As such, Lido is designed to allow for NOs to participate in a manner that's in accordance with their respective local laws and regulations, but also allows for NOs who are unconstrained to advance and bolster the network's properties of censorship resistance. Similarly, thanks to the protocol's transparency, stakers and the wider community can assess the [impact on the network's censorship resistance](https://eth.neutralitywatch.com/) and make decisions about how to best allocate their stake.

## D - Node Operator Responsibilities
Based on the guiding considerations, the following responsibilities arise for NOs using Lido in respect to the configuration of the validators run for the protocol to propose blocks. Moreover, this section outlines how potential breaches of the responsibilities are identified in the [Lido Fees Monitoring Dashboard](https://fees-monitoring.lido.fi/).

### D.1 - Fee Recipient
NOs have to configure for each validator run for the protocol in the consensus client (CC), validator client (VC) and/or signing service of choice the Lido Execution Layer Rewards Vault for the [relevant Ethereum network](https://docs.lido.fi/deployed-contracts/) as the `fee_recipient`.

In monitoring, proposed blocks labeled with unknown fee recipients (FRs) indicate either a misconfiguration of an NO's infrastructure in regard to the FR, or a possible stealing attempt.

### D.2 - APMs Allowed List
NOs are permitted to run APMs that have been vetted by the community and are included in the [APMs Allowed List](). This list comprises APMs that have been assessed as sufficiently de-risked and demonstrably beneficial to both Lido and the broader Ethereum ecosystem. Any APM seeking inclusion in the list must follow the standard evaluation and approval procedure.

NOs operating APMs, for each validator run for the protocol using an APM, have to adhere to all relevant configuration requirements, security best practices, and guidelines related to the distribution of associated rewards defined in this SNOP and the APMs Allowed List.

### D.3 - MEV Boost Relay Allowed List
NOs are required to participate in out-of-protocol PBS and use at least one of the relays included in the MEV Boost Relay Allowed List -- found on the [Node Operator Portal](https://enchanted-direction-844.notion.site/6d369eb33f664487800b0dedfe32171e?v=d255247c822c409f99c498aeb6a4e51d) or by querying the `get_relays` method of the smart contract for the [relevant Ethereum network](https://docs.lido.fi/deployed-contracts/). Any relay seeking inclusion in the list must follow the standard application process and successfully undergo the vetting for reliability and values-alignment by the [Relay Maintenance Committee (RMC)](https://research.lido.fi/t/lido-on-ethereum-identify-and-constitute-relay-maintenance-committee/3386).

NOs have to configure for each validator run for the protocol in the CC or out-of-protocol PBS sidecar of choice (see [Appendix A.4.1.4](#a414---sidecars)) the address(es) of at least one vetted relay labeled "must use some" and any number of entries labeled "may use" of the MEV Boost Relay Allowed List.

In monitoring, proposed blocks labeled with unknown payload sources (PSs) and such labeled with payloads sourced from "may use" relays, although at the beginning of the allocated slots there were more valuable payloads available through "must use some" relays, indicate the potential misconfiguration of an NO's infrastructure in regard to the MEV Boost Relay Allowed List.

### D.4 - Out-Of-Protocol PBS Troubleshooting
In cases vetted relays and/or builders experience operational issues - e.g. payloads being constructed incorrectly, payload headers, response messages and/or unblinded payloads not being propagated timely or crucial APIs being unresponsive - NOs are required to refrain from the usage of the affected infrastructure until either, the services are sufficiently and satisfactorily restored, or have been removed from the MEV Boost Relay Allowed List by the RMC at the request of the community.

If problems are discovered that have not yet been reported on, NOs are required to promptly inform the rest of the Lido NO community and contributors to the Node Operator Mechanisms (NOM) workstream through public - e.g. the Lido Research Forum or Discord - and/or private channels - e.g. Telegram group chats - and to support the operators of the affected infrastructure at troubleshooting the situation.

### D.5 - Local Block Building Allowance
NOs are permitted to configure their infrastructure on a per-validator basis to enable local block building (see [Appendix A.3](#a3---local-block-building)) in ways that most effectively balance the inclusion of otherwise disregarded transactions and the rewards foregone by Lido to strengthen Ethereum's censorship resistance. Given the evolving technical options and volatile market conditions of the network, the methods and values allowed to enable local block building are determined by social consensus between contributors to the Lido DAO and the remaining stakeholders of the protocol in the [Local Block Building Allowance thread]() on the Lido Research Forum.

NOs opting into the local block building allowance, for each validator run for the protocol using the allowance, have to adhere to all relevant configuration requirements defined in this SNOP and the Local Block Building Allowance thread.

In monitoring, proposed blocks labeled with unknown PSs indicate the potential misconfiguration of an NO's infrastructure in regard to the local block building allowance.

## E - Consequences of Non-Conformance
In case that an NO is flagged for potential misconfiguration of its block proposal infrastructure, the below actions shall be taken. Separate measures may be applied dependending on the technical characteristics of the SM or staking avenue within which the proposal originated. For example, while NOs are known for the permissioned CM and SDVTM, NOs of the permissionless CSM may remain anonymous and reaching the NOs is only possible if a contact is voluntarily shared with the community. Furthermore, the bonded CSM has more options for automated intervention than the social reputation-based CM and SDVT.

The following table outlines the consequences for non-conformance with the Node Operator Responsibilities in regard to block proposals in Lido, and for which SM each action applies.

Action|CM|SDVTM|CSM
---|:---:|:---:|:---:
Upon detection of an unknown FR, contributors to the NOM workstream will reach out to the NO and ask for a clarification of the situation. If force majeure can be ruled out as the cause of the incident and no plausible explanation be provided, it must be assumed that the FR has been incorrectly configured and/or the proposed block's rewards have been stolen. Consequently, depending on the severity of the offense, community members may consider raising a formal issue with the NO on the Lido Research Forum, requesting remediative action, and seeking the transfer of missing rewards to the Lido Execution Layer Rewards Vault (see [D.1](#d1---fee-recipient)).|:heavy_check_mark:|:heavy_check_mark:|:x:
Upon detection of an unknown FR, an amount of the affected NO's bond equal to the respective block's rewards and an additional disincentive of 0.1 ETH is algorithmically locked for 8 weeks, and the NO afforded a challenge period to discuss the situation in the [CSM Support category](https://research.lido.fi/c/csm-support/21) of the Lido Research Forum. Once the NO compensates Lido for the incident or upon expiry of the challenge period - if an agreement has been reached, that force majeure was the cause of the event - the lock is lifted. If neither a plausible explanation can be provided, nor a compensation is being transfered to the Lido Execution Layer Rewards Vault (see [D.1](#d1---fee-recipient)), the [CSM Committee](https://research.lido.fi/t/community-staking-module-committee/8333) will initiate an [Easy Track motion](https://lido.fi/governance#easy-track) to propose to the Lido DAO that the locked portion of the NO's bond be burned as a penalty.</br>Any of the NO's validators that fail to provide a full bond - i.e. become unbonded - as a result of this measure are [requested to exit the protocol](https://lido.mypinata.cloud/ipfs/QmZTMfmJZsYHz61f2FjhYdh5VNu6ifjYQJzYUGkysHs8Uu). If the requested validators are not exited, they are marked as stuck, and no operator rewards are distributed to the NO until the stuck keys are exited.</br>**_NOTE:_** After the implementation of [EIP-7002: Execution Layer Triggerable Withdrawals](https://eips.ethereum.org/EIPS/eip-7002) into Lido, stuck validators will instead be forcefully ejected at the protocol level.|:x:|:x:|:heavy_check_mark:
Upon detection of an unknown PS or a sequence of proposed blocks with payloads exclusively sourced from "may use" relays, although at the beginning of the allocated slots there were more valuable payloads available through "must use some" relays, contributors to the NOM workstream (in the CSM: CSM Committee) will reach out to the NO and ask for a clarification of the situation. If force majeure can be ruled out as the cause of the incident and no plausible explanation be provided, it must be assumed that an unvetted relay has been configured and/or the "must use some" selection ignored. Consequently, depending on the severity of the offense, community members may consider raising a formal issue with the NO on the Lido Research Forum, requesting remediative action, and seeking the transfer of missing rewards to the Lido Execution Layer Rewards Vault (see [D.1](#d1---fee-recipient)).</br>**_NOTE:_** For CSMv2, an automation of the responses to such non-conformances is being considered.|:heavy_check_mark:|:heavy_check_mark:|If NO is identifiable

# Appendix
## Appendix A - Ethereum Standards, Auxiliary Proposer Mechanisms & Infrastructure
### A.1 - Advancing Ethereum's State
Advancing Ethereum's state revolves around attaching new blocks to the blockchain and begins with the PoS consensus pseudo-randomly allocating the block proposal duty for an upcoming slot to one of the active validators. Once the allocated slot arrives, the validator compiles a [plurality of Consensus Layer (CL) and EL data](https://ethereum.org/en/developers/docs/blocks/#block-anatomy) into a block, signs, and broadcasts it to the network. The other validators then check the proposed block and, if the required supermajority attests to its validity, it is appended to the blockchain, updating the global state.

Sometimes a validator fails to propose a block -- the slot then remains empty, the state of Ethereum unchanged, and the affected block is referred to as "missed". Another exceptional case is a block that is temporarily part of the blockchain, but replaced by another block before finalization through events such as reorganizations ("reorgs") or "forks" of Ethereum's [Gasper consensus mechanism](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/gasper/). This can happen, e.g., because the block was proposed to the network too late to reach and obtain the attestations of a sufficient number of validators, because the blockchain split due to changes in the protocol rules, or because of a community disagreement about the future of Ethereum. Blocks that are abandoned during finalization are called "orphans".

### A.2 - Beacon Node API
[Application programming interface (API)](https://ethereum.github.io/beacon-APIs) exposed by beacon nodes (BNs) - either as CC or VC - to query and participate in the Beacon Chain (BC) according to [Ethereum's consensus specification](https://github.com/ethereum/consensus-specs). The Beacon Node API is relevant to this SNOP both as the standard required to enable nodes to fulfill fundamental validator duties and to allow NOs using Lido to customize their block proposal setups.

### A.3 - Local Block Building
In local block building, the validator allocated to the proposal duty of a slot selects the transactions to be included in the block from the local mempool of its execution client (EC) and orders them to its preference. The transactions can originate from the validator itself, from users who sent them to the validator for inclusion, or Ethereum's public mempool through the EL gossip network.

In the allocated slot and initiated by a request of its CC, the validator selects the transactions to be included, taking into account the block's gas limit. The EC then locally executes the selected transactions in the specified order to obtain an updated state tree, whose root it sends to the CC along a list of the included transactions -- the execution payload ("payload"). Following this, the CC takes the state root and payload received, and together with a range of additional data, wraps them into a block. In further steps, the block is forwarded to the VC handling the validator's private key for signing, and back to the CC for broadcasting to the network through the gossip protocol of the CL, so that finally the other validators can check the block's validity and vote on its appending. A block locally built by a validator is often referred to as "vanilla".

### A.4 - Auxiliary Proposer Mechanisms
APMs refer to supplementary mechanisms that validators can leverage to optimize the process of block proposals. These mechanisms are typically designed to enhance proposer decision-making, improve network efficiency, and capture additional value while maintaining alignment with Ethereum's core principles.

APMs can include block building pipelines, block auctions, proposer commitments, or other innovations that introduce optimizations for transaction inclusion and ordering.

The following APM framework provides a structured way to understand how block proposers can extend their default functionality using external mechanisms. It consists of three key components:

- Mechanisms, which define the high-level objectives -- e.g. block auctioning in PBS
- Protocols, which specify the rules governing the interactions between agents -- e.g. MEV-Boost protocol for out-of-protocol PBS
- Sidecars, which are software implementations enabling the participation in these protocols -- e.g. MEV-Boost as client-side relay tool

The framework allows for flexibility, as a single mechanism can have multiple protocol implementations, each supported by different sidecars.

#### A.4.1 - Proposer-Builder Separation
[PBS](https://notes.ethereum.org/@vbuterin/pbs_censorship_resistance) is a mechanism designated to solve the increasing complexity of Ethereum block proposals and the risk of their centralization by sophisticated actors, exacerbating inequalities and running counter to the decentralization goals of the ecosystem. PBS addresses this by separating two validator duties -- building the payloads and proposing the blocks for slots allocated by the protocol. The separation of roles reduces the complexity for the proposer, who - in contrast to local block building (see [Appendix A.3](#a3---local-block-building)) - only has to select the highest bid of specialized third-party builders competing for the construction of a slot's most valuable payload, wrap that payload in a block, and propose it to the network. Ethereum therefore ensures that even validators with limited resources can fulfill the block proposal duty and actively contribute to the security of the network and that, like builders and relays - the facilitators of the interaction between builders and proposers - they economically profit from the optimized extraction of MEV. In Lido, this also extends to stakers, who receive the majority of the rewards accrued from staking.

##### A.4.1.1 - Out-Of-Protocol PBS
In general, out-of-protocol implementations serve as functional equivalents and/or supplements to Ethereum in-protocol APMs. In the case of PBS, which - while being discussed since The Merge - has not yet been natively introduced to the protocol due to the extensive changes it would required to the consensus, out-of-protocol implementations represent the only available options until the arrival of, e.g., [enshrined proposer-builder separation (ePBS)](https://eips.ethereum.org/EIPS/eip-7732).

##### A.4.1.2 - MEV-Boost
[MEV-Boost](https://boost.flashbots.net/) is [Flashbot's reference design proposal](https://ethresear.ch/t/mev-boost-merge-ready-flashbots-architecture/11177) for a [PBS-friendly MEV marketplace](https://ethresear.ch/t/proposer-block-builder-separation-friendly-fee-market-designs/9725), whose architecture served as basis for the specification of Ethereum's Builder API (see [Appendix A.4.1.3](#a413---builder-api)). By making the optimized extraction of MEV accessible to all operators with the distribution of a sidecar that automatically selects the highest bid for a slot and permissionlessly handles the orchestration of the proposer's interactions with builders and relays, the MEV-Boost implementation has become the de facto standard for out-of-protocol PBS in Ethereum.

MEV-Boost has also addressed concerns about a weakness of PBS in terms of protecting Ethereum's censorship resistance that had already been expressed for in-protocol variants. The [minimum bid value ("min-bid")](https://www.flashbots.net/#9f18e7f297d6443c895d870df3ee5d2a) is a setting that allows proposers to forego a limited amount of rewards and, instead of sourcing externally constructed payloads for slots where the highest bid is below the min-bid configured by the proposer, create payloads locally -- ensuring the inclusion of otherwise censored transactions.

##### A.4.1.3 - Builder API
[Ethereum's temporary out-of-protocol PBS solution](https://ethereum.github.io/builder-specs/#/Builder), for validators to delegate the payload construction duty of allocated slots to external builders. Unlike the MEV-Boost protocol and compatible sidecars, which function as multiplexers between a single proposer and a multitude of builders and relays, the Builder API is specialized on establishing a direct one-to-one connection between a proposer and builder -- as configured in the former's CC.

##### A.4.1.4 - Sidecars
To ensure that the information provided to NOs regarding the solutions available to participate in out-of-protocol PBS is up-to-date despite the changing landscape in software offerings and implementations of technical features, contributors to the NOM workstream curate the sidecars and configuration methods vetted by the community for Lido in the [APMs Allowed List]().

On the list, APM sidecars - not only those for out-of-protocol PBS - are divided into the following categories:
* Consesus Clients
* APM-Specific Clients & Implementations
* Multi-Beacon Node Validator Clients
* Distributed Validator Clients & Middleware

##### A.4.1.5 - Builder & Relay Operator Responsibilities & Incident Management
Given the trusted nature of out-of-protocol PBS, it behooves builder and relay operators to ensure that they and the infrastructure they operate function effectively, performantly, and efficiently, and that any and all communications are made timely and professionally.

Builder and relay operators should notify relevant Lido stakeholders - e.g. NOs and the NOM workstream - as soon as possible upon identification of an issue with any of the infrastructure that they operate. Notification may be performed through public - e.g. the Lido Research Forum or Discord - and/or private channels - e.g. Telegram group chats - so that NOs can proceed with appropriate changes to their configurations.

During an incident, builder and relay operators should provide regular updates with regard to the status. Following the conclusion, the operators should provide a report or post-mortem that details at minimum the root cause, fixes performed, if/how soon the infrastructure will be back in service, and what steps have been undertaken to prevent the likelihood of similar incidents in the future. Additionally, in cases where block production was adversely affected (see [D.4](#d4---out-of-protocol-pbs-troubleshooting)), the operators should advise as to what steps will be taken - if any - to reimburse validators for any missed, invalid, etc. blocks and/or inaccurate bids.

Incidents will be reviewed on an ad-hoc basis by the NO and Lido communities. These reviews will inform DAO governance decisions regarding whether relay operators and the relays that they operate will continue to be considered vetted and ratified for use by NOs or not.

Relay operators which exhibit poor or otherwise unacceptable communication and especially performance of their relays - such as, but not limited to, inability to performantly and timely register validators or propagate bids and payloads, not verify bids, not simulate payload validity, etc. - and/or fail to conform with the expectations outlined above with regard to incident management, will be subject to removal from the MEV Boost Relay Allowed List (see [D.3](#d3---mev-boost-relay-allowed-list)).
