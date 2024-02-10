# 💰 RosenBridge Fees & Dust collection

**Fee Structure on Ergo**

RosenBridge employs a fee mechanism that is applied on the Ergo blockchain. This ensures that transactions facilitated by the bridge incur processing fees, which are essential for the maintenance and operation of the bridge infrastructure. These fees are automatically deducted during the transaction process on the Ergo side, aligning with the network's fee policies.

**Dust Collection Process**

During the operation of RosenBridge, certain transactions on Ergo may result in the underutilization of 'event boxes', leading to the creation of residual balances or "dust". To address this, RosenBridge implements a dust collection strategy, which involves:

* **Periodic Review:** A dedicated dust collector function is activated at regular intervals to scan for and identify these underutilized 'event boxes'.
* **Consolidation of Residuals:** Once identified, the dust collector aggregates these residuals, effectively spending the underutilized boxes to reclaim and consolidate the dust.
* **Comprehensive Collection:** Beyond 'event boxes', the dust collection mechanism also targets other types of residual balances that may accumulate over time, ensuring a clean and efficient blockchain environment.

This dust collection process is a critical component of RosenBridge's operational efficiency, ensuring that the Ergo blockchain remains free of clutter and that all resources are optimally utilized. It reflects RosenBridge's commitment to maintaining a streamlined, effective, and user-friendly cross-chain bridge service.
