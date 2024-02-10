# 🔁 Token Transfer from ChainX to Ergo: A Step-by-Step Guide

Transferring tokens from ChainX to Ergo involves a secure and systematic process that ensures the safe locking of tokens on the source chain (e.g., BTC on Bitcoin) and the minting of corresponding representative tokens (rBTC) on Ergo. This process leverages a multisignature wallet and involves the coordinated efforts of Watchers and Guards within the RosenBridge ecosystem. Here's a detailed breakdown of each step:

**1. Initiating the Transfer**

* **User Action:** The user initiates the transfer by sending tokens to the bridge's multisignature wallet. This transaction includes metadata specifying the destination Ergo address. Users can generate this transaction through the RosenBridge user interface (UI) for ease and convenience.

**2. Monitoring for Events**

* **Watcher Role:** Watchers continuously monitor the multisignature wallet for incoming transactions. Their primary task is to identify events indicating that a user has initiated a token transfer.

**3. Creating Event Boxes on Ergo**

* **Event Box Generation:** Upon detecting a payment with the requisite number of confirmations, each Watcher proceeds to create a new 'event box' on the Ergo blockchain. This event box serves as a record of the transfer initiation.

**4. Approving the Event**

* **Consensus Among Watchers:** An 'approved event box' is created when there is a consensus among Watchers, indicated by the presence of sufficient 'event boxes' for a specific transfer event.

**5. Validation by Guards**

* **Guard Verification:** Guards constantly scan for 'approved event boxes'. Upon finding one, each Guard verifies the event's validity on ChainX. If the event is confirmed as valid, Guards sign the corresponding transaction on Ergo.

**6. Broadcasting the Transaction on Ergo**

* **Transaction Finalization:** Once the transaction garners enough signatures from Guards, it is broadcasted on the Ergo blockchain, marking the completion of the token transfer process.

**7. Receipt of rTokens by the User**

* **User Receives rTokens:** The user receives the representative tokens (rTokens) on Ergo, equivalent to the locked tokens on ChainX.

**8. Ensuring Transaction Confirmation**

* **Guard Oversight:** Guards monitor the Ergo blockchain to ensure that the broadcasted transaction is mined and receives an adequate number of confirmations, securing the transfer's completion.
