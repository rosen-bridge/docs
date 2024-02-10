# ⛓ New chain integration

RosenBridge is designed with flexibility in mind, enabling the addition of any blockchain network that supports multisignature or threshold signature mechanisms. This adaptability allows RosenBridge to broaden its interoperability across various blockchain ecosystems, enhancing the bridge's utility and accessibility. To integrate a new network into the RosenBridge framework, the following components must be developed and deployed:

**Watchers**

* **Purpose:** Watchers play a crucial role in monitoring transactions within the network's multi-signature wallet. They are responsible for detecting and fetching information about incoming payments, ensuring that cross-chain transactions are recognized and processed efficiently.

**Guardian Wallets**

* **New Wallet Setup:** For each added network, a new wallet specifically for the Guards must be established. This wallet is essential for managing the operations and security protocols unique to the network, facilitating the safe and secure handling of cross-chain transactions.

**Transaction Verification Mechanism**

* **Guard Verification:** A robust transaction verification mechanism is essential for the Guards to authenticate cross-chain transactions accurately. This system must be capable of scrutinizing the transaction details against the network's security protocols, ensuring that only valid transactions are approved and processed.

**Bank Boxes on Ergo**

* **Infrastructure on Ergo:** The integration of a new network necessitates the setup of appropriate 'Bank boxes' on Ergo. These boxes are pivotal in managing the assets transferred across the bridge, serving as the transactional foundation for the cross-chain exchange process.

By implementing these components, RosenBridge can seamlessly extend its services to new blockchain networks, further enhancing its mission to provide a secure, efficient, and decentralized bridge solution.
