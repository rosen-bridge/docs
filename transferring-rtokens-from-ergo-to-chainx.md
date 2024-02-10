# 🔄 Transferring rTokens from Ergo to ChainX

#### Transferring rTokens from Ergo to ChainX: A Professional Guide

The process of transferring representative tokens (rTokens) from Ergo back to ChainX, such as converting rBTC back into BTC, is meticulously designed to ensure security and efficiency. This involves burning the rTokens on Ergo and subsequently unlocking the original tokens on ChainX. Below is a step-by-step professional guide detailing each phase of the transfer:

**1. Initiating the Transfer on Ergo**

* **User Action:** The user initiates the transfer by sending the rTokens to a designated Bank contract on Ergo or by creating a new Bank contract through a transaction. This transaction includes metadata specifying the destination address on ChainX.

**2. Monitoring Bank Contract Events**

* **Watcher Role:** Watchers continuously monitor the Bank contract for any new transactions. Their role is crucial in identifying and initiating the process for transferring tokens back to ChainX.

**3. Event Box Creation on Ergo**

* **Event Detection:** Upon detecting a transaction with the required confirmations, each Watcher proceeds to create an 'event box' on Ergo. This serves as a record of the initiated transfer.

**4. Event Approval Process**

* **Consensus Building:** An 'approved event box' is created when consensus is reached among Watchers, indicated by the presence of sufficient 'event boxes' for the specific transaction.

**5. Validation and Signing by Guards**

* **Guard Verification:** Guards search for 'approved event boxes'. Upon finding one, they verify the event's validity on Ergo. Valid events are then signed off for the corresponding transaction on ChainX.

**6. Broadcasting the Transaction on ChainX**

* **Transaction Execution:** Once the transaction receives enough signatures from Guards, it is broadcasted on ChainX, facilitating the unlocking of the original tokens.

**7. Completion of Token Receipt on ChainX**

* **Token Receipt:** The user receives their original tokens on ChainX, completing the transfer process from Ergo.

**8. Ensuring Transaction Finality**

* **Guard Oversight:** Guards monitor ChainX to ensure the broadcasted transaction is mined and achieves the necessary confirmations, finalizing the transfer.
