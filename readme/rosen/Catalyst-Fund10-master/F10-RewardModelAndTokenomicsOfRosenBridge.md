# Reward Model and Tokenomic of Rosen Bridge

The reward model for Watchers in the Rosen Bridge ecosystem plays a crucial role in ensuring the security and functionality of the bridge while promoting active participation within the community. Watchers serve as a critical first line of defense in the multi-layered security architecture of the bridge, monitoring network activities and reporting valid bridge-related events. In recognition of their contributions, they are rewarded according to the following reward model:

# Reward Distribution

Bridge fees generated within the Rosen ecosystem are split between Watchers and Guards, with a distribution ratio of 70% to Watchers and 30% to Guards. These rewards are allocated based on a proof-of-event mechanism, as outlined in the tokenomics of the Rosen ecosystem. Rosen is built with flexible parameters to optimize the adoption process in new environments. Governance structures will be on-chain and managed in a transparent way to assist the bootstrapping process. 

# Tokenomics Allocation

To understand how the reward model fits into the tokenomics, let's break down the token allocation:


![](TokenAllocation.png)

- **Initial Liquidity Bootstrapping (Ergo and Cardano)**: 10% of the total token supply, aimed at providing liquidity for the bridge's initial launch.

- **Future Liquidity Bootstrapping (New Chains)**: 38.5% of the total token supply is reserved for liquidity on new chains, ensuring the bridge's expansion and interoperability. This fund is locked and will not be unlocked until the bridge grows to new ecosystems. In new ecosystems, this will be used to raise liquidity on public markets minimizing the friction and liquidity of RSN across major crypto assets. 

- **Event-Based Emission (Rewards)**: 25% of the total token supply is allocated for event-based rewards, with 70% of this portion going to Watchers. Rewards are a flexible parameter and can be changed to optimize rewards in different environments and accelerate the adoption of the Rosen framework. This is where the rewards for Watchers come from, incentivizing them to monitor and report on the bridge's activities.

- **Passive Staking**: 2.5% of the total token supply for staking rewards, which can also be an additional source of rewards for token holders who participate in staking.

- **Team Budget**: 10.5% of the total token supply is allocated for the team budget vested over 48 months, ensuring long-term development sustainability.
- **Treasury Budget:** 10.5% of the total token supply is allocated for the treasury budget vested over 48 months, ensuring media, marketing, partnership, and market adoption of the protocol. 

- **Ergo Foundation**: 3% of the total token supply allocated to the Ergo Foundation, also vested over 48 months, to support the development, security, and growth of the ecosystem.

# Role of Watchers

Watchers in the Rosen Bridge ecosystem are instrumental in maintaining security and transparency. They actively monitor network activities and report valid bridge-related events. Their rewards, comprising 70% of the event-based emission pool, serve as a powerful incentive for Watchers to diligently perform their duties. Rewards are a flexible parameter and can be changed to optimize rewards in different environments and accelerate the adoption of the Rosen framework.

By providing a mechanism for users to join as Watchers, as long as they meet the collateral requirements, the Rosen Bridge encourages active participation from the community, further enhancing its security and reliability. This aligns with the overall goal of the bridge to facilitate secure and efficient cross-chain interactions while creating value for its participants.

In summary, the reward model for Watchers in the Rosen Bridge ecosystem is a key component of its tokenomics, promoting security, transparency, and community engagement. It reflects the bridge's commitment to fairly compensating those who contribute to its successful operation while fostering a robust and vibrant ecosystem across multiple blockchain networks.

The security model of the Rosen Bridge framework is built on a combination of Sybil resistance mechanisms and the use of permits, which are designed to ensure the integrity of the network while providing economic benefits for honest information reporting.

# Sybil Resistance Mechanism

Sybil resistance is a fundamental aspect of the Rosen Bridge's security model. The goal is to impose a cost on individuals attempting to operate multiple instances simultaneously, thereby preventing malicious actors from overwhelming the network with fake or dishonest information. To achieve this, a significant upfront cost is required to run a Watcher node.

The `collateral` required to run a Watcher is around (due to market volatility) $1,000 in RSN tokens and $1,000 in Ergo. This substantial cost acts as a deterrent, limiting the number of Watchers any single entity can operate, thus preventing Sybil attacks. The initial collateral is not subject to seizure, it is locked and should be considered illiquid by Watchers until redemption occurs.

## Permits for Verification

To further ensure the honesty of information reporting, the framework introduces permits. A permit costs $100 and is valued at approximately 5,000 RSN tokens. These permits are slashable if dishonest reporting is detected. In this context, slashing means that a permit that is used in false reporting is seized. 

A unique aspect of the permit system is that it takes approximately 20 reports to verify a single transaction. This means that Watchers have a financial stake in providing accurate and honest information, as dishonest reporting could result in the loss of valuable permits.

# Economic Benefits of Watcher Reporting

The economic benefits of providing honest information within the Rosen Bridge ecosystem are significant. Here's how it works:

- **Fee Distribution:** Fees collected as tokens are bridged are distributed within the ecosystem. Watchers, who play a crucial role in monitoring and reporting on bridge-related events, are rewarded with RSN tokens. This creates a direct incentive for Watchers to participate honestly and diligently.

- **Permit Redemption:** Watchers can redeem permits when the transfers they've reported on are settled. This provides an additional source of income for Watchers as they are rewarded for honest reporting, encouraging them to continue participating in the ecosystem and reporting on events. Watchers are rewarded with a percentage of the bridge fees.  

- **Increased Revenue Potential:** Watchers can use their RSN token profits to purchase more reporting permits. This not only reinforces their commitment to honest reporting but also increases their potential revenue, creating a positive feedback loop for honest participation.

# Community Discussions and Optimization

The Rosen Bridge framework recognizes the importance of community input and discussions. Post-bootstrapping, there will be discussions to optimize the barrier of entry and the cost of reporting. These parameters will depend on factors such as transaction volume and settlement times, and they will be adjusted to ensure the network's sustainability and effectiveness.

# Flexible Fee Structure

The framework also emphasizes a flexible fee structure, with adjustable parameters for fee distribution between guards and watchers. This flexibility allows Rosen to adapt to different environments, optimize costs, and incentivize infrastructure development in new ecosystems, further enhancing its security and economic sustainability.

Rosen Bridge's security model combines sybil resistance mechanisms with the use of permits to incentivize honest reporting. The economic benefits of honest participation, including fee distribution and permit redemption, create a robust incentive structure that aligns the interests of network participants with the security and reliability of the bridge, ultimately promoting trust and integrity within the ecosystem.



# Focus on Long-Term Security

Rosen, with its open-source contracts and clear economic assumptions, has the potential to be a compelling alternative to existing bridges built on trust and opaque risk models. Here's how Rosen can stand out as a trustworthy and transparent bridge in the blockchain ecosystem:

**Open Source Contracts:**

- **Transparent Codebase:** Rosen's open-source contracts mean that the entire codebase governing the bridge's operation is available for scrutiny by anyone in the community. This transparency ensures that users can verify the bridge's functionality and security, reducing the need to trust the bridge operator blindly.

- **Community Audits:** With the code openly accessible, the community and independent auditors can review the smart contracts for vulnerabilities, ensuring a high level of security and reliability. This helps identify and mitigate risks more effectively.

**Clear Economic Assumptions:**

- **Well-Defined Tokenomics:** Rosen's tokenomics are clearly outlined, specifying how rewards are distributed, the costs associated with running Watchers, permit systems, and more. These economic assumptions provide users with a clear understanding of the incentives that drive the network and how they can participate to earn rewards.

- **Incentives for Honest Reporting:** The economic model in Rosen aligns incentives with honest reporting, ensuring that participants are financially motivated to act in the best interest of the network. This contrasts with trust-based bridges, where the motivations of the operators may not always align with the users' interests.

**Risk Mitigation:**

- **Permit System:** The use of permits, which can be slashed for dishonest reporting, acts as a strong disincentive against malicious behavior. This feature significantly reduces the risk of inaccurate information being reported, enhancing the overall reliability of the bridge.

- **Transparent Fee Distribution:** The fee distribution mechanism, clearly specified in the tokenomics, ensures that Watchers and Guards are rewarded for their roles in securing the network. This incentivizes active participation and ensures that the network remains secure and operational.

**Community Engagement and Governance:**

- **Community Input:** Rosen emphasizes community discussions to optimize various parameters, such as entry barriers and reporting costs. This allows users and stakeholders to have a say in how the network evolves, ensuring that it aligns with the community's needs and preferences.

- **Decentralized Governance:** As an open-source project, Rosen can implement decentralized governance mechanisms in the future. This would enable token holders to participate in decision-making processes, further enhancing transparency and trust within the ecosystem.

**Flexibility and Adaptability:**

- **Adjustable Parameters:** Rosen's ability to adjust parameters, such as fee distribution and collateral requirements, allows it to adapt to different blockchain environments and optimize costs. This flexibility is critical for staying competitive and resilient in a rapidly evolving ecosystem.

# The Goal of Rosen Bridge: Create A lower-cost alternative for market access, built with clear assumptions


Rosen is built with the idea of transparency and profit distribution in mind. Tokens can pay a fee for Rosen support and in the future can lock Rosen to lower the bridge fee for their project. The profits of bridge integration will be distributed to Guards, Active Watchers, and The Development Team members for ongoing maintenance and support. 

Using a bridge to access decentralized exchanges (DEXs) offers several advantages over paying a centralized exchange and market makers to manage the listing and accessibility of a project's token to the public. Here are some key reasons why using a bridge can be a better option:

**Leverage Decentralization:**

- **No Central Intermediaries:** Bridges, especially those built on blockchain technology, operate in a decentralized and open manner. They do not rely on single centralized intermediaries, which means there are no single points of failure or potential vulnerabilities associated with centralization.

**Security:**

- **Reduced Counterparty Risk:** By using a bridge to access DEXs, projects can reduce counterparty risk. Centralized exchanges often require trusting the exchange with custody of tokens, which can lead to security breaches or loss of funds. In contrast, DEXs allow users to retain control of their assets.

**Cost Efficiency:**

- **Lower Fees:** DEXs typically have lower trading fees compared to centralized exchanges. By avoiding the fees associated with listing on a centralized exchange and paying market makers, projects can save costs.

**Accessibility:**

- **Open Access:** DEXs are open and accessible to anyone with a compatible wallet. Listing on centralized exchanges often involves a selection process and may require projects to meet certain criteria, which can be restrictive. DEXs, on the other hand, provide equal access to all projects.

**Community Engagement:**

- **Community-Driven:** DEXs are often community-driven platforms where users have a say in which tokens get listed. This can help projects build a strong and engaged community of supporters who are more likely to contribute to the project's success.

**Decentralized Finance (DeFi) Integration:**

- **DeFi Ecosystem:** Many DEXs are an integral part of the broader DeFi ecosystem. By listing on DEXs, projects can tap into various DeFi protocols and services, such as decentralized lending, yield farming, and liquidity provision.

**Transparency:**

- **On-Chain Trading:** Trading on DEXs is typically executed on-chain, meaning all transactions are publicly verifiable. This transparency can enhance trust and confidence in the project.

**Global Reach:**

- **Global Audience:** DEXs have a global reach, making it easier for projects to attract users and investors from around the world without the need for a specific exchange's market access.

**Reduced Listing Requirements:**

- **Lower Barriers to Entry:** Listing on a centralized exchange often involves meeting specific requirements, including regulatory compliance. DEXs generally have fewer listing requirements, making it easier for newer or smaller projects to access liquidity.

**Adaptability and Independence:**

- **Token Control:** Projects maintain control over their tokens and can adapt to changing market conditions without relying on a centralized exchange's approval.

While using a bridge to access DEXs has its advantages, it's essential to consider the specific goals, target audience, and regulatory compliance requirements of the project. Centralized exchanges can still offer benefits such as higher liquidity and broader market exposure, making the choice between using a bridge and traditional exchange listing a decision that should be made based on the project's unique circumstances and objectives.

For some projects it may be economically beneficial to list wrapped tokens on Central exchanges as integration is faster, cheaper and more efficient for some blockchains. In this way Rosen also can facilitate lower listing costs for projects interested in using centralized exchanges. 

