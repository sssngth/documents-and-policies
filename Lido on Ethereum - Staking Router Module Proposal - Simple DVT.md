# Staking Router Module Proposal: Simple DVT


The following proposal is the final draft that will be put forth for Snapshot vote to the Lido DAO on October 26th. Please see the Snapshot vote linked here to participate in the decision as to whether the module should be deployed on mainnet. 




# Proposal Summary 


This proposal seeks DAO approval for the addition of a new module that will utilize Distributed Validator Technology (DVT) on mainnet through Obol Network and SSV Network implementations. This module will be referred to as the “Simple DVT” module, due to the manual coordination and curation required to adopt this early-stage technology, and to reflect that this module is not intended for indefinite and “at-scale” use. 


DVT represents the fastest way to add many new Node Operators to the Lido Node Operator set with a more diverse profile of solo and community staker participants while benefiting from the technology’s inherent benefits such as increased resilience, distribution, and security. The Simple DVT module is intended to demonstrate that utilizing DVT on mainnet is possible, while furthering the diversification of the Lido Node Operator set on Ethereum and potentially setting the stage for more scalable and permissionless DVT-based modules in the near future.


Solo stakers, community stakers, existing node operators using Lido protocol, and other staking organizations would participate in the upcoming 3rd Lido DVT testnet utilizing Obol and SSV DVT solutions. Following a review of individual and cluster performance, the Lido Node Operator Subgovernance Group (LNOSG), with input from Obol and SSV, would be responsible for finalizing clusters to participate in a mainnet deployment.


The LNOSG would propose the cluster combinations for DAO discussion, and if no significant issues are identified, a committee known as the Simple DVT Module Committee would be responsible for adding these Node Operators to the mainnet registry and increasing validator limits per cluster. 


The number of clusters and amount of validators operated per cluster would initially be minimal (e.g. 24 clusters running 5 validators each), leaving time for the Simple DVT Module Committee and DAO to monitor performance and assess the impact to the protocol. If the distributed validators demonstrate performance in-line with the broader Ethereum network, additional clusters could be added to mainnet and the number of validators per cluster would be increased.


After at least three months of mainnet performance comparable to the overall operator set, the LNOSG would also meet to discuss and potentially recommend increasing the number of validators operated by clusters to more meaningful levels (e.g. 100+), considering factors such as cluster performance and the make-up of participants. 


The Staking Router technical documentation [outlines two reward share components](https://docs.lido.fi/contracts/staking-router/#fee-distribution): the module fee and treasury fee. To incentivize Node Operator participation and provide continued support to DVT providers, it is proposed that the treasury fee for the Simple DVT Module is set to 2% and the module fee (paid to Node Operators and the DVT provider) is set to 8%. This reflects the limited economic attractiveness of running a small number of validators with multiple members and the desire to support continued development of Distributed Validator Technology, while taking into account the limited amount of stake the Simple DVT Module will contain and the intent to wind-down the module in the medium-term.


During the discussion period of this proposal, the DAO should also consider the choice of two risk mitigation approaches that will be included in the vote: 


1. DAO approval for the current (or future) cover fund to be utilized in the case of material impact to stETH stakers, with a maximum cover of [up to 4 ETH per validator](https://research.lido.fi/t/risk-assessment-for-community-staking/5502?u=kimonsh).
2. Open an RFP process for interested cover providers to provide risk mitigation options with the subsequent DAO vote to choose between providers. 


In summary, this proposal would greenlight the creation of a secondary module on mainnet (contingent upon a successful testnet deployment achievement of minimum success metrics) that would be used to allow Node Operators participating in DVT clusters using SSV Network and Obol Network technologies to use the Lido protocol to run validators. This module would be initially capped at 0.5% of Lido stake, which could be increased later by DAO vote, and be expected to be wound-down and replaced by more scalable modules over time (e.g. within approximately 2-3 years). The module would have the rewards share set at 10%, with 2% allocated to the treasury fee, and 8% to the module fee via participating Node Operators and DVT providers. 


The proposal would also grant the Simple DVT Module Committee the approval to execute Easy Track governance motions (i.e. motions that can be objected to by DAO token holders) for aiding Simple DVT cluster operations (i.e. adding operators and setting validator limits) following LNOSG evaluations of upcoming DVT testnet trials. 


Following the discussion period and any potential modifications incorporating community feedback, this proposal will be put forth for Snapshot vote to the DAO on October 26th, 2023. 

# Full Proposal

## Motivation 


The Staking Router ([introduced in Lido V2](https://blog.lido.fi/lido-v2-launch/)) adds significant optionality in terms of the ways Node Operators might participate in the protocol, however wholesale newly-designed modules are likely still 8 - 18 months away from mainnet. 


In order to increase the scope of Node Operators who use the protocol and to support new technologies in the staking space such as DVT, this proposal seeks DAO consideration and approval for the creation of a secondary module on mainnet that utilizes both SSV and Obol DVT implementations to increase the number and type of Node Operators using the protocol.


This secondary module would set the groundwork for the continued expansion of the Lido Node Operator set while more permissionless modules are developed, in-line with the [Lido community’s commitment to decentralization, accessibility, and censorship resistance](https://research.lido.fi/t/lido-dao-vibe-alignment-purpose-mission-vision/4380), as well as the ability to lead the way in terms of new technologies adding to the robustness of Ethereum’s validator set.


Following two successful pilot integrations of Distributed Validator Technology based on implementations by [Obol Network](https://blog.lido.fi/lido-on-ethereum-obol-network-testing-v2/) and [SSV Network](https://blog.lido.fi/lido-on-ethereum-ssv-network-testing-v2/) on the Goerli testnet, additional steps can be taken to advance the adoption of DVT for the Lido protocol.


While DVT is still in the early stages of adoption, this technology can assist in adding new Node Operator participants to the protocol in the near-term while benefiting from the increased resilience, security, and decentralization that DVs provide. The Simple DVT module would play a role in helping battle-test this new technology while limiting the scale and potential impact from any issues that may arise. The proposed module would initially be capped at 0.5% of Lido stake and potentially be covered by a Lido DAO cover fund or third-party cover so as to address potential sub-optimal performance (e.g. unexpected DVT software bugs) associated with this experimental technology. 


These efforts would be encapsulated in the “Simple DVT” module, a module that leverages the existing design of the Curated Operator Module thereby keeping development scope manageable, and would incorporate the processes and mechanisms developed over the previous two DVT testnets as well as an upcoming third one. The “Simple DVT” module has the potential to see the first community stakers (what Lido DAO contributors use to refer to solo and small groups of stakers) participate in the Lido protocol as Node Operators on mainnet within early 2024.


This proposal suggests the new module would be: 
Initially capped at 0.5% of total Lido stake (with the option to increase the cap via DAO vote), 
The module cap should be re-assessed for possible increase by Q3/2024 and Q2/2025
require achievement of minimum success metrics on testnet (outlined below),
Not operated indefinitely, with the intention for the Simple DVT module to be superseded by more sophisticated DVT modules designed with scaling and permissionless participation in mind. 


By moving forward with a Simple DVT implementation, the Lido DAO can advance its mission to keep Ethereum decentralized and democratize access to staking in the near-term while more complex and scalable modules are developed over the coming months. 


## Why DVT


DVT has the potential to provide significant benefits in terms of decentralization, liveness, and security. It also represents the single fastest way to increase the number of independent Node Operators participating in running validators using the Lido protocol in a secure manner.


By combining existing Lido on Ethereum Node Operators with solo and community stakers participating in testnets operated through the Lido Node Operator registry on Goerli and soon, Holesky, trust requirements for community participants can be further minimized as the threshold structure of a DVT cluster reduces the risks of single operator infrastructure issues and malicious behavior. 


At the validator level, DVT enables high availability deployments, significantly improving liveness for the Ethereum validator set. It also enhances geographic, jurisdictional, client, and infrastructure diversity. [Furthermore, recent DVT research seems to indicate](https://mirror.xyz/0x934e6B4D7eee305F8C9C42b46D6EEA09CcFd5EDc/62s3XmsD6nGuJZCi58kChkSgKRj9XS1F86uFC2N1_lk) significant benefits to validator resilience, including possible reduction of slashing risk, through an active-active cluster redundancy configuration and the distributed key-generation process.


While a reduction in slashing risk should reduce the overall technical risks to validators utilizing the protocol over the long-term, given the early stage nature of the technology additional mitigation should be considered for this initial deployment (as discussed in the “Associated Risks & Mitigation” section). 


## Proposed Process for Cluster Organization


### Testnet Round Three 


To date, Lido on Ethereum DVT testnet deployments have included cluster configurations consisting of 3/4 thresholds, 5/7 thresholds, 7/10 thresholds, and 9/13 thresholds. 


The next round of testnet trials would be organized in a manner to replicate a potential mainnet deployments as closely as possible. The majority of clusters would be in a 5/7 threshold or greater, reflecting the benefits of increased resilience and redundancy larger clusters promote. 


To move forward with a mainnet deployment, the following success characteristics should be considered over a 30 to 45 day testing period:
1. Uptime of at least 95.00% 
2. Successful block proposal rate of at least 70% (some flexibility is needed due to the early stage of  MEV-Boost integration and outstanding questions regarding Holesky performance)  
3. Attestation effectiveness per Rated Network comparable with that of the broader Holesky validator set


Given that two separate DVT infrastructures (Obol Network and SSV Network) would underpin the distinct clusters, the success characteristics should be grouped by infra provider and considered independently.


Achievement of these characteristics and a successful on-chain DAO vote to deploy the mainnet Simple DVT module would greenlight a candidate evaluation by the LNOSG for participation on mainnet and the subsequent deployment of Node Operator clusters.


In the event these performance metrics are not achieved, one of two options should be taken: 
1. Run another testnet round before deploying on mainnet
2. Analysis of the testnet is presented to the DAO for discussion with an explanation of why specific criteria may not have been achieved, with a subsequent DAO vote to determine whether to proceed to mainnet or not
 
### Mainnet


In the event of a successful DAO vote to move forward with Simple DVT, mainnet deployment would operate with the following characteristics: 


Operator Additions to the Simple DVT Module 


Participants for the mainnet clusters of the Simple DVT Module will be sourced through the third round of Lido DVT testing. 


The proposal would grant a new multi-sig committee (known as the “Simple DVT Module Committee”) consisting of contributors from the Lido DAO, LNOSG, Obol Network, and SSV Network teams the ability to create and execute Easy Track motions that allow for the addition of new clusters, activation and deactivation of existing clusters, and changing of cluster properties for the Simple DVT Module.


The LNOSG will be responsible for ongoing evaluations of Simple DVT Node Operators and the cluster combinations that will be used to operate validators. Factors considered will include individual Node Operator performance, infrastructure type, and geographic location.


When the LNOSG reaches consensus, the proposed clusters will be transparently presented to the DAO via the Lido Research forums. If no major concerns are raised after a 7 day discussion period, the Simple DVT Module Committee will execute the addition of new Node Operators to the Simple DVT module. 


## Operator Selection for the Simple DVT Module 


For the initial implementation, the LNOSG would propose e.g. 24 cluster combinations (12 using Obol and 12 using SSV) of potentially different threshold sizes (but most likely 5/7) based on prior testnet experience for onboarding as distributed validator participants. 


The clusters would form multi-sigs that would form the basis of their entry in the Node Operator registry. For the initial stage of the deployment, each cluster could be approved for 5 validators and performance would be monitored in a transparent manner and reported to the DAO.


As an example, running this in a 5/7 scenario with 50%+ of the distributed nodes run by Node Operators from the Curated Module (not a requirement) would mean over 70 non-Lido Curated Set operators would be participating in running validators using the protocol (bringing the total number of Node Operators using the protocol to over 100). At the same time, with an initial limit of 5 validators per cluster, only 120 validators (0.04% of total Lido validators) would be initially used for testing. 


If after 30 days the participants demonstrated strong performance on mainnet and both DVT providers complete up-to-date codebase audits, the limit of validators run by each cluster could be increased (e.g. to 20) and additional distributed validator clusters could be onboarded.


Assuming 70 clusters run in this format by Q2 of 2024, 210+ net-new Node Operators could be onboarded to the protocol. While the exact number of net-new Node Operators would depend on the cluster threshold combinations, the majority of clusters would be in a 5/7 threshold or greater. 


These clusters would not only add to the number of Node Operators participating, but also seek to add additional client, geographic, and infrastructure diversity to the validators run as part of the protocol. 


## Further Increases to Validator Limits


While the primary intent behind the Simple DVT module is to test mainnet DVT and expand the Node Operator set, a drawback of clusters running a small number of validators is the economic reality of a group sharing rewards for only a minimal number of validators.


After sufficient battle-testing and performance monitoring of the DV clusters (at least three months after launch), the LNOSG will also consider for certain clusters to increase the number of validators operated to a higher threshold. 


It is suggested that the LNOSG consider a classification such as “Advanced Node Operators”. Advanced Node Operators would be participants that demonstrate consistently strong performance and ecosystem alignment. These Node Operators would be sourced from the mainnet clusters, DVT testnet trials, DVT provider verified operator lists, and prior LNOSG assessments. 


For clusters consisting of participants where Advanced Node Operators are 50%+ of the consensus threshold, validators limits would have the potential to be raised to operate a more meaningful number of validators (e.g. 100+). 


The increasing of validators to these levels would move forward following sufficient performance monitoring during the initial stage of the Simple DVT Module and an extensive update would be provided to the DAO for discussion. 


## Simple DVT Module Economics


When considering the addition of the Simple DVT Module, the DAO should examine  taking advantage of the ability to adjust rewards share parameters as described in the Staking Router documentation in order to increase the attractiveness for cluster members as well as to compensate the DVT providers for ongoing protocol development and services to the module. The Staking Router [technical documentation outlines two reward share components: the module fee and treasury fee](https://docs.lido.fi/contracts/staking-router/#fee-distribution).


It is proposed that the total reward share for the Simple DVT module is 10%, with the DAO treasury fee set to 2% and the module fee (paid to Node Operators and the DVT providers) set to 8%. This differs from the reward share structure of the Curated Operator Module where the shares are split 5%/5%. Due to the economic differences in DVT clusters and the intent to run a smaller number of validators for a 2-3 year period, the Simple DVT module should share a higher percentage of rewards with Node Operators to reflect these differences.


Participating in a cluster of multiple members that split rewards for a small number of validators is of limited economic attractiveness. To promote the longer-term alignment of participating cluster members and to work towards profitability of the participating Node Operators, the module fee should be higher than that of the fee paid to members of the Curated Operator set, where they receive a higher individual fee over a larger number of validators.


In the case of DVT providers, as their respective DVT solutions are now coming to mainnet, the Simple DVT Module presents the opportunity to demonstrate alignment with the providers in supporting their continued services to the Simple DVT Module and long-term DVT protocol development. 


### Obol Network Economics


Obol Network will receive rewards via stETH as part of a splitter contract used for reward disbursement to Node Operators. Rewards received will represent 10% of the total share for Obol-based clusters (i.e. 1% of net Obol cluster rewards).


This disbursement method will not require any changes to the core module codebase, and will utilize an Obol Network developed splitter contract based on 0xsplits. This contract will be audited with results publicly shared before deployment on mainnet.


Node Operator participation on the Obol Network does not currently require service fees, so no additional tokens are involved in the operation of the relevant clusters.


### SSV Network Economics


Usage of the SSV Network protocol requires Node Operators to pay SSV Network Fees and to deposit initial collateral denominated in the SSV token as outlined in the [SSV Network technical documentation to operate validators using the protocol](https://docs.ssv.network/learn/protocol-overview/tokenomics/fees).  


SSV Network will receive rewards denominated in the SSV token via SSV Network Fees. As outlined in the [SSV [DIP-11] Mainnet Proposal](https://snapshot.org/#/mainnet.ssvnetwork.eth/proposal/0x90af2241fb2b3b3242dfb98efb9185b5bafac00d1cb300542a107d0df4ef6e12), the SSV Network fee is currently 0.5% of Ethereum staking rewards. This will increase to 0.75% after 365 days pass from the launch of the initial configuration, and increase to 1% 730 days after the launch of the initial configuration. 


Node Operators participating in SSV based clusters will receive an additional 1% of the staking rewards (8% total) that will be used to cover ongoing SSV Network Fees. In addition, pending the SSV DAO grants committee’s approval, a grant will be provided to a multi-sig of SSV and Lido DAO contributors that will be responsible for allocating SSV tokens to clusters for covering the costs of the liquidation collateral and SSV network fees required for the first year of module operation, not exceeding 2,500 SSV (equals an optimistic estimation of 50 clusters running 100 validators each). If approved by the SSV grants committee, after the first year of module operation, the combined multi-sig of SSV and Lido DAO contributors will return any excess SSV tokens to the SSV DAO treasury. 


Members of this multi-sig would be determined pending the approval of the SSV grants committee to process the grant, following the [processes described in the Lido DAO Ops Multisig Policy](https://research.lido.fi/t/lido-dao-ops-multisigs-policy/4115), i.e. it would include at least 3 signers with a 50%+ threshold, have a dedicated forum post containing a stated purpose and operating rules, include the wallet address and participant addresses, and each participants would “apply” by sharing proof of address ownership. This forum thread will be created by the Lido DAO Operations Workstream and be cross-linked to the [Simple DVT Module proposal thread](https://research.lido.fi/t/staking-router-module-proposal-simple-dvt/5625). 


This disbursement method will not require any changes to the core module codebase.

| Simple DVT Rewards Share||||
| ----------- | ----------- | ----------- | ----------- |
| **Obol**        || **SSV** ||
| **Total stETH Reward Share**  | **10%**  | **Total stETH Reward Share** | **10%** |
| Treasury share           | 2%        | Treasury share | 2% |
| Obol share               | 1%        | SSV share* | 0% |
| Node Operators share     | 7%        | Node Operators share* | 8% |

##### *Note*: Cluster Node Operators pay for SSV collateral and Network Fees via their SSV Operator accounts. First year SSV fees may be covered by the SSV DAO grants committee subject to the grant committee’s approval. SSV will receive rewards via SSV Network Fees. *


## Associated Technical Risks & Mitigation


While Obol and SSV have both demonstrated significant progress and advancements in their implementation of DVT solutions, the technology is not yet battle-tested.


Potential issues across areas such as networking, execution layer and consensus layer client compatibility, latency, DVT clients or middleware, etc. all present possible pitfalls of a DVT deployment.


In addition to these risks, the DAO should consider that Obol and SSV are still in the mid-to-early stages of the audit process. SSV has undergone an [initial audit of the core contracts and client](https://docs.ssv.network/developers/security), and Obol is in the [process of starting its second set of audits over the remainder of the code base](https://docs.obol.tech/docs/next/sec/overview) (following a [Sigma Prime audit of the Charon client](https://obol.tech/charon_sigma_prime_assessment.pdf)). Both DVT providers are in the process of additional audits and have indicated that they will continue to share updates publicly. 


While the numerous benefits of such a deployment have been outlined above, these technical risks are worth consideration.


As a possible mitigating factor to these risks, this proposal seeks DAO consideration of the following options: 1. The existing cover fund (or a subsequent cover provision) be used in the case of slashing or otherwise above-normal reduced rewards to Lido stakers or 2. Sourcing third-party cover via e.g. Chainproof or Nexus Mutual for the validators run as part of this module.


During the discussion period of this proposal, DAO members are encouraged to discuss their preference among these options. 


#### Option 1: Use the existing cover fund to mitigate risk 


With 6,200 stETH currently in the cover fund vault contract, the risk to Lido stakers can be mitigated in all but a catastrophic slashing event (that would have much further impact) or if all clusters were to go offline permanently (unlikely considering the curation process for this proposed module, and the expected rollout of triggerable exits (EIP 7002) within the next year).


Per research from the Lido Analytics Contributor Workstream and as an example assuming 1400 validators (70 clusters running 20 validators each), the cover fund currently holds more than enough stETH to compensate for potential losses under most conservatively realistic scenarios, even assuming intentional malicious behavior of all participating NOs: (i.e. no more than 4 ETH loss per Validator, assuming no correlation penalty and triggerable exits implemented within the next year). 


Considering that a correlation penalty has to date never been assessed in any Ethereum network slashings and that clusters would be made up of Node Operators with demonstrated experience during testnet DVT trials, the scenarios that would exceed (or impact over 6,000 stETH) are extremely unlikely. 


Of note, a discussion regarding a longer-term Surplus Management Framework has begun, which over time could provide additional cover via DAO revenue. 


#### Option 2: Source third-party cover 


An open RFP process is held to source cover via a third-party provider for up to e.g. 4 ETH per validator. This process includes public proposals for the DAO to consider from these providers, and if this option is chosen, a subsequent DAO vote to select the provider. 


Two parties interested in providing third-party cover, Chainproof and Nexus Mutual, have responded to the forum thread sharing proposals to offer slashing cover for the Simple DVT Module.


The Lido Analytics & Finance contributor workstreams have also presented detailed analysis of the proposed options for the DAO to consider. Voters are encouraged to review the proposals and analysis within the forum thread before participating in the Snapshot vote. 


## Proposed Plan of Action


The proposed plan includes the following steps:


1. DAO discussion of the proposal and consideration of the Risk Mitigation measure to utilize if the proposal is accepted
2. Snapshot vote to signal DAO support for advancement of the Simple DVT module and choice between utilizing the Lido Cover fund or third-party cover
3. Deployment of a secondary module & related tooling on Holesky testnet (not dependent on Snapshot vote)
4. Third set of DVT testnet trials with Obol & SSV (not dependent on Snapshot vote)
5. On-chain DAO vote for deployment of secondary mainnet module, role delegation for the Simple DVT Module Committee, and the deployment of related tooling
6. LNOSG evaluation & review of testnet participants to propose for mainnet DV cluster creation 
7. Simple DVT Module Committee creation of mainnet DVT clusters up to 0.5% of total Lido validators (with option to increase this threshold of total Lido validators over time)


While subject to change, DAO contributors estimate Step 3 could take place by mid-to-late October, with the third round of DVT testnet trials being held in November/early December. 


Continued community feedback on the proposal and potential risk mitigation methods is crucial. Please feel free to share any questions or comments below. This is the final version of the proposal and will be put forth for Snapshot vote on October 26th, 2023. 





