# Firo Integration Specification for Rosen Bridge

**Date:** December 17, 2025  
**Author:** Firo Integration Team  
**Status:** Phase 1 Complete - Awaiting Infrastructure Deployment

**⚠️ Important Note: This document is provided for reference purposes only. Its structure may not reflect the expected design format, and its content may be outdated or incomplete. It should not be used as a reference for preparing other chain design documents.**

---

## Executive Summary

This document provides a comprehensive technical specification for integrating Firo blockchain into the Rosen Bridge ecosystem. All Phase 1 modules have been implemented and tested with both mock data and real Firo testnet nodes. This document addresses the RCS-003 requirements and demonstrates Firo's compatibility with the Rosen Bridge architecture.

---

## Table of Contents

1. [RCS-003 Requirements Compliance](#rcs-003-requirements-compliance)
2. [Lock Transaction Structure](#lock-transaction-structure)
3. [Sample Transactions](#sample-transactions)
4. [Complete Implementation Status](#complete-implementation-status)

---

## RCS-003 Requirements Compliance

### Base Requirements (2/2 ✅)

#### Requirement 1: Multi-Signer Addresses ✅

**Requirement Statement:**
> All assets should be held in the bridge address that requires multiple signers (guards) to transfer from it. Currently, two approaches are used by Rosen; Threshold Signature Scheme (TSS) and Multi-Signature (usually handled on the contract side)

**Firo Implementation: Threshold Signature Scheme (TSS)**

**How Firo Satisfies This:**

1. **ECDSA Signature Compatibility:**
   - Firo uses ECDSA (secp256k1) for transaction signatures, identical to Bitcoin
   - TSS protocols for ECDSA are well-established and production-ready (GG20, CGGMP)
   - Each guard holds a secret share; signing requires threshold cooperation (e.g., 5-of-7)

2. **Transparent Address as Lock Address:**
   - TSS ceremony generates a distributed private key
   - Public key derives a normal Firo transparent address (e.g., `TXh1Prwp...` on testnet)
   - From blockchain perspective, it's a regular P2PKH address
   - No one guard has full control; threshold signatures required for spending

**Technical Details:**
- **Lock Address Type:** P2PKH (Pay-to-PubKey-Hash) transparent address
- **Signature Algorithm:** ECDSA secp256k1
- **TSS Protocol:** Compatible with GG20/CGGMP threshold signature schemes
- **Threshold Configuration:** Configurable (e.g., 5-of-7, 7-of-10)

**Implementation Evidence:**
- `FiroChain` class implements TSS signing via `signFunction` parameter
- Guards coordinate off-chain for threshold signature generation
- No Firo protocol modifications required

---

#### Requirement 2: Data Writing ✅

**Requirement Statement:**
> To bridge an asset, in addition to transferring the asset to the bridge address, various data including fee, target chain and target address should be provided. The data is written on the transferring transaction which is also known as the lock transaction.

**Firo Implementation: OP_RETURN Output**

**How Firo Satisfies This:**

1. **OP_RETURN Support:**
   - Firo supports OP_RETURN opcode for embedding arbitrary data in transactions
   - Maximum size: 80 bytes of data per OP_RETURN output
   - Data is immutably stored on-chain and indexed by all nodes
   - Standard Bitcoin-like implementation

2. **Lock Transaction Structure:**
   ```
   Transaction:
     Inputs:
       - User's UTXO(s) with sufficient balance
     
     Outputs:
       - Output 0: Amount to lock address (e.g., 0.5 FIRO)
       - Output 1: OP_RETURN with encoded bridge data
       - Output 2: Change back to user (if applicable)
   ```

3. **OP_RETURN Data Encoding:**
   ```
   OP_RETURN structure:
   - OP_RETURN opcode (0x6a)
   - Length prefix
   - Version (1 byte): 0x01
   - Amount (8 bytes, little-endian): Amount being locked in satoshis
   - Bridge Fee (8 bytes, little-endian): Fee for bridge operation
   - Network Fee (8 bytes, little-endian): Fee for target chain transaction
   - Address Type prefix (1 byte): 0xa0 for address string
   - Address Length (varint)
   - Target Chain (string): e.g., "ergo"
   - Separator (1 byte): 0x7c (pipe character '|')
   - Target Address (string): Destination address on target chain
   ```

4. **Data Extractability:**
   - Watchers scan Firo blockchain via RPC
   - OP_RETURN data is in `scriptPubKey` field of transaction outputs
   - `FiroRosenExtractor` parses hex-encoded OP_RETURN to extract:
     * Target chain identifier
     * Destination address
     * Amount
     * Bridge and network fees

**RPC Methods Used:**
- `getblockchaininfo` - Get current blockchain height
- `getblockhash` - Get block hash by height
- `getblock` - Get block with transaction IDs
- `getrawtransaction` - Get full transaction details including OP_RETURN data

**Implementation Evidence:**
- `FiroRosenExtractor.extractRawData()` - Parses OP_RETURN from transactions
- `parseRosenData()` utility - Decodes OP_RETURN hex to structured data
- Successfully extracted 13 observations from Firo testnet

---

### Additional Requirements (5/5 ✅)

#### Requirement 3: Token Supporting ✅

**Requirement Statement:**
> The blockchain may or may not support tokens. If assets are to be transferred from other chains into it (e.g., transferring Erg from Ergo into Chain-X), it should support tokens.

**Firo Status: Native Token Only (Initial Phase)**

**How Firo Satisfies This:**

1. **Current Scope:**
   - **Phase 1:** Bridge native FIRO token only (Firo → Other chains)
   - Similar to Bitcoin integration approach
   - Native FIRO can be locked and bridged to Ergo/other chains as wrapped tokens (rsFIRO)

2. **Use Case:**
   - Users lock FIRO → Receive rsFIRO on Ergo
   - Users burn rsFIRO on Ergo → Receive FIRO back on Firo
   - Similar model to Bitcoin bridge (BTC ↔ wBTC)

**Implementation:**
- `FIRO_NATIVE_TOKEN` constant defined as `"firo"`
- TokenMap configuration includes FIRO native token
- No token-specific logic needed in scanner/extractor

---

#### Requirement 4: dApp Connector ✅

**Requirement Statement:**
> Users initiate a transfer through a transaction, which typically has a specific structure that may not be easy for them to generate. A convenient solution is to use dApp connectors to interact with users' wallets, enabling automatic generation and signing of transactions.

**Firo Implementation: Wallet Integration & UI**

**How Firo Satisfies This:**

1. **Wallet Support:**
   - `FiroWallet` class implemented in `ui/wallets/firo/`
   - Integrates with Firo wallet software (firo-qt, Electron wallet)
   - Handles transaction building, signing, and broadcasting

2. **UI Integration:**
   - `FiroNetwork` client implemented in `ui/networks/firo/`
   - Transaction builder creates properly formatted lock transactions
   - Automatic OP_RETURN generation with encoded bridge data
   - User-friendly interface for:
     * Amount selection
     * Target chain selection
     * Destination address input
     * Fee estimation

3. **Transaction Building:**
   - UTXO selection algorithm
   - Proper input/output construction
   - OP_RETURN data encoding
   - Change calculation
   - Fee estimation

4. **Implementation Details:**
   ```typescript
   FiroNetwork.submitTransaction():
   - Selects user's UTXOs
   - Creates output to lock address
   - Generates OP_RETURN with bridge data
   - Adds change output if needed
   - Returns unsigned transaction for wallet signing
   ```

**User Flow:**
1. User opens Rosen Bridge UI
2. Connects Firo wallet
3. Specifies: Amount, Target Chain, Destination Address
4. UI generates lock transaction with OP_RETURN
5. Wallet signs and broadcasts

**Implementation Evidence:**
- `ui/networks/firo/src/client.ts` - FiroNetwork class
- `ui/wallets/firo/src/wallet.ts` - FiroWallet class
- `ui/networks/firo/src/utils.ts` - Transaction building utilities

---

#### Requirement 5: Transaction Chaining ✅

**Requirement Statement:**
> As the bridge scales and the number of users and supported chains increases, multiple requests to the same target chain can arise simultaneously. Chaining these transactions can enhance event processing efficiency; however, it may also lead to deadlocks and delays in payments in some edge cases.

**Firo Implementation: UTXO Model with Change Management**

**How Firo Satisfies This:**

1. **UTXO Model Benefits:**
   - Firo uses UTXO (Unspent Transaction Output) model like Bitcoin
   - Multiple transactions can be created simultaneously without conflicts
   - No account nonce system (unlike Ethereum)
   - Parallel transaction submission supported

2. **Transaction Chaining Strategy:**
   - Guards can chain unlock transactions by using change outputs
   - Example:
     ```
     Unlock TX 1: Vault UTXO → User A (+ change UTXO)
     Unlock TX 2: Change UTXO → User B (+ change UTXO)
     Unlock TX 3: Change UTXO → User C (+ change UTXO)
     ```
   
3. **Change Output Management:**
   - `FiroChain.submitTransaction()` handles UTXO selection
   - Automatically creates change outputs for remaining balance
   - Change outputs can be immediately used in next transaction
   - Implements `BitcoinBoxSelection` for optimal UTXO selection

4. **Deadlock Prevention:**
   - UTXO model inherently prevents double-spending
   - Network-level mempool handles transaction ordering
   - Replace-by-Fee (RBF) support for stuck transactions
   - Proper UTXO tracking prevents selection conflicts

**Implementation Details:**
- Box selection algorithm: `@rosen-bridge/bitcoin-utxo-selection`
- Fee estimation: `estimateTxFee()` calculates based on input/output count
- Constants defined:
  ```typescript
  FIRO_INPUT_SIZE = 148 bytes
  FIRO_OUTPUT_SIZE = 34 bytes
  FIRO_TX_BASE_SIZE = 10 bytes
  ```

**Implementation Evidence:**
- `rosen-chains/packages/chains/firo/lib/firoChain.ts` - UTXO management
- `firoUtils.ts` - Fee estimation and UTXO selection
- Successfully tested with Bitcoin-style UTXO chaining logic

---

#### Requirement 6: Event Distinguishability ✅

**Requirement Statement:**
> It is possible for two identical events to occur at the same time, so the agreed transactions should be distinguishable to avoid double payments or missing an event.

**Firo Implementation: Transaction ID + Output Index**

**How Firo Satisfies This:**

1. **Unique Transaction Identifiers:**
   - Every Firo transaction has unique TXID (SHA256 hash)
   - TXID collision is cryptographically infeasible
   - Example: `2968410a04b1853f4fdf428dfd46433e994db1a8cee746c14d2bac035e42b0e6`

2. **Event Identification:**
   - Primary key: `sourceTxId` (Firo transaction hash)
   - Secondary key: Output index (which output contains lock amount)
   - Combined identifier: `txid:vout` (e.g., `2968410a...b0e6:0`)

3. **Handling Identical Events:**
   - Even if two users lock identical amounts to same destination:
     * Different transaction IDs
     * Different block heights or positions
     * Different input sources
   - Watcher tracks each event separately by TXID

4. **Database Tracking:**
   ```sql
   observation_entity table:
   - requestId (unique): Hash of event data
   - sourceTxId: Firo transaction ID
   - block: Block hash containing transaction
   - height: Block height
   - fromChain: "firo"
   - toChain: e.g., "ergo"
   - amount, bridgeFee, networkFee: Extracted from OP_RETURN
   ```

**Example Distinguishing Identical Events:**
```
Event 1: 0.5 FIRO → ergo → 3Wwek... (TX: abc123...)
Event 2: 0.5 FIRO → ergo → 3Wwek... (TX: def456...)
Result: Two separate observations, two separate payouts
```

**Implementation Evidence:**
- `observation_entity` table with `sourceTxId` as unique identifier
- Some observations tracked with unique TXIDs
- `FiroRosenExtractor` extracts transaction ID from each lock event

---

#### Requirement 7: Transaction Fee Handling ✅

**Requirement Statement:**
> The time between a bridge request and its payment on the target chain can be lengthy for various reasons. During this period, transaction fees may fluctuate significantly. The design should minimize the impact of these fluctuations to prevent asset loss and avoid unexpected issues.

**Firo Implementation: Pre-paid Fees + Dynamic Estimation**

**How Firo Satisfies This:**

1. **Fee Model:**
   - **Bridge Fee:** Charged upfront in OP_RETURN (covers operational costs)
   - **Network Fee:** User pays target chain fee upfront (encoded in OP_RETURN)
   - **Firo TX Fee:** Paid by user when locking (standard Firo transaction fee)

2. **Fee Collection in OP_RETURN:**
   ```
   Lock Transaction:
   - Amount: 0.5 FIRO (500,000 satoshis)
   - Bridge Fee: 0.01 FIRO (1,000,000 satoshis)
   - Network Fee: 0.01 FIRO (1,000,000 satoshis)
   
   User pays: 0.52 FIRO total + Firo tx fee
   Guards receive: 0.5 FIRO + 0.02 FIRO fees
   User receives: 0.5 rsFIRO on target chain
   ```

3. **Dynamic Fee Estimation:**
   - UI queries Firo network for current fee rates
   - `estimatesmartfee` RPC call for optimal fee calculation
   - Fee estimator: `generateFeeEstimator()` utility
   - Formula: `baseFee + (inputCount * inputSize + outputCount * outputSize) * feeRate`

4. **Unlock Transaction Fees:**
   - Guards use pre-collected network fees from OP_RETURN
   - Fee buffer to handle fluctuations
   - If fees spike: Guards use accumulated fee reserves
   - If fees drop: Excess fees stay with guards (operational costs)

5. **Fee Configuration:**
   - Minimum fee boxes on Ergo store fee configs (per chain)
   - Registers R4-R9 contain fee parameters
   - Guards query before processing events

**Implementation Evidence:**
- `FiroChain.estimateTxFee()` - Dynamic fee calculation
- OP_RETURN parser extracts `bridgeFee` and `networkFee` fields
- Test observations show fees: `bridgeFee=1000000, networkFee=1000000`
- `@rosen-bridge/bitcoin-utxo-selection` handles fee-aware UTXO selection

---

## Lock Transaction Structure

### Overview

A Firo lock transaction is a standard Firo transaction with specific outputs that encode bridge request data. The transaction follows Bitcoin-style UTXO model with OP_RETURN data embedding.

### Transaction Anatomy

**Real Testnet Transaction Example:**

```
┌──────────────────────────────────────────────────────────────────┐
│           FIRO LOCK TRANSACTION (Testnet)                        │
├──────────────────────────────────────────────────────────────────┤
│ TXID: 2968410a04b1853f4fdf428dfd46433e994db1a8cee746c14d2bac...  │
│ Size: 305 bytes                                                  │
│ Version: 1                                                       │
│ Locktime: 0                                                      │
│                                                                  │
│ INPUTS (1):                                                      │
│   [0] Previous TXID: 7fcd243b03171741453f59ffb50d032dc3c184...   │
│       Output Index: 0                                            │
│       Value: 31,250,000 satoshis (0.3125 FIRO)                   │
│       Address: TQSu3aMZZtBuBkAwKSj9YbAZp6NJuMyVLB (user)         │
│       ScriptSig: <signature> <pubkey>                            │
│                                                                  │
│ OUTPUTS (3):                                                     │
│   [0] LOCK OUTPUT                                                │
│       Value: 500,000 satoshis (0.005 FIRO)                       │
│       ScriptPubKey: 76a91447a4e7c1dd06e25d3bfd4ac6186e958b...    │
│       Type: P2PKH (pubkeyhash)                                   │
│       Address: TGW2XVxw86ZusKQXvwWHouBjrKiUyZUT9u (lock addr)    │
│                                                                  │
│   [1] OP_RETURN (Bridge Data)                                    │
│       Value: 0 satoshis                                          │
│       ScriptPubKey: 6a4500000000000f424000000000000f4240...      │
│       Type: nulldata (OP_RETURN)                                 │
│       Data: Bridge parameters (69 bytes)                         │
│                                                                  │
│   [2] CHANGE OUTPUT                                              │
│       Value: 30,740,000 satoshis (0.3074 FIRO)                   │
│       ScriptPubKey: 76a91478d05f5453ace45afe1c188699ed969e...    │
│       Type: P2PKH (pubkeyhash)                                   │
│       Address: TLz1i3bBeg4ohwyXKG7E8Rvz6A8MsQFac1 (user)         │
│                                                                  │
│ Block Height: 191081                                             │
│ Block Hash: e16088d33a686ed90e756a1a1f201800d45b391c894af...     │
│ Confirmations: 495+                                              │
│ Timestamp: 1765232403 (2025-12-04 12:00:03 UTC)                  │
└──────────────────────────────────────────────────────────────────┘

Transaction Flow:
Input: 0.3125 FIRO → Lock (0.005 FIRO) + Change (0.3074 FIRO) + Fee (~0.0001 FIRO)
```

### Output Details

#### Output 0: Lock Output
- Transfer assets to bridge's custody. It is a simple P2PKH type.
- **Example:**
  ```
  Value: 500000 satoshis
  ScriptPubKey: 76a914<hash160>88ac
  Address: TGW2XVxw86ZusKQXvwWHouBjrKiUyZUT9u
  ```

#### Output 1: OP_RETURN (Bridge Data)
- Encode bridge request parameters with OP_RETURN.
- **Data Structure:**
```
Byte Layout:
┌─────────┬─────────┬───────────┬──────────┬──────────────┬─────────────────┐
│ OP code │ Length  | BridgeFee │  NetFee  │  AddrLength  │    Address      │
├─────────┼─────────┼───────────┼──────────┼──────────────┤─────────────────│
│ 1 byte  │ 1 byte  │  8 bytes  │  8 bytes │    2 bytes   | base58 string   │
└─────────┴─────────┴───────────┴──────────┴──────────────┴─────────────────┘

Legend:
- OP code: OP_RETURN
- Length: Total length of following data
- BridgeFee: Bridge fee in satoshis (little-endian, 8 bytes)
- NetFee: Network fee for target chain (little-endian, 8 bytes)
- AddrLength: Length of target address (2 bytes)
- Address: Destination address on target chain
```
- **Example:**

**Full OP_RETURN hex:**
```
6a4500000000000f424000000000000f424034335777654b47576f3444735243434c4845324d5a5173653376366959346f65675143595a655162716a4d75364a50383345323957
```

**Breakdown:**
| Bytes         | Hex                                      | Description                                                        |
|-------------- |------------------------------------------|--------------------------------------------------------------------|
| 0             | 6a                                       | OP_RETURN opcode                                                   |
| 1             | 45                                       | Length: 69 bytes                                                   |
| 2-9           | 00000000000f4240                         | Bridge Fee: 1000000 (LE, 8 bytes)                                  |
| 10-17         | 00000000000f4240                         | Network Fee: 1000000 (LE, 8 bytes)                                 |
| 18            | 34                                       | AddrLength: 52 bytes                                               |
| 19-end        | 3357...3957                              | To Address: "3WweKGWo4DsRCCLHE2MZQse3v6iY4oegQCYZeQbqjMu6JP83E29W" |

**Decoded Fields:**
- Bridge Fee: 1000000 (hex `00000000000f4240`, little-endian)
- Network Fee: 1000000 (hex `00000000000f4240`, little-endian)
- To Address: `3WweKGWo4DsRCCLHE2MZQse3v6iY4oegQCYZeQbqjMu6JP83E29W` (from hex)

*Note: Amount is not present in this OP_RETURN, as the locked amount is validated from the lock output (vout[0]).*

#### Output 2: Change Output (Optional)
- **Purpose:** Return excess funds to user
- **Value:** `(Total Inputs) - (Lock Amount) - (TX Fee)`
- **Script Type:** P2PKH
- **Address:** User's address

### RPC Methods for Transaction Inspection

```bash
# Get transaction details
firo-cli getrawtransaction <txid> 1

# Get block containing transaction
firo-cli getblock <blockhash> 2

# Verify transaction confirmation
firo-cli gettransaction <txid>
```

---

## Sample Transactions

### Testnet Lock Transaction

**Transaction ID:**
```bash
export TX_ID=2968410a04b1853f4fdf428dfd46433e994db1a8cee746c14d2bac035e42b0e6
# Using Firo RPC
firo-cli -conf=testnet.conf getrawtransaction $TX_ID 1
```

**Explorer Link:**
```
https://testexplorer.firo.org/tx/2968410a04b1853f4fdf428dfd46433e994db1a8cee746c14d2bac035e42b0e6
```

**Transaction Details:**
- **Block Height:** 191081
- **Block Hash:** `e16088d33a686ed90e756a1a1f201800d45b391c894af1703bfe9e139df3c052`
- **Confirmations:** 6+ (confirmed)
- **Lock Address:** `TGW2XVxw86ZusKQXvwWHouBjrKiUyZUT9u` (testnet placeholder)

**Extracted Data:**
```json
{
  "sourceTxId": "2968410a04b1853f4fdf428dfd46433e994db1a8cee746c14d2bac035e42b0e6",
  "fromChain": "firo",
  "toChain": "ergo",
  "toAddress": "3WweKGWo4DsRCCLHE2MZQse3v6iY4oegQCYZeQbqjMu6JP83E29W",
  "amount": "500000",
  "bridgeFee": "1000000",
  "networkFee": "1000000",
  "sourceChainTokenId": "firo",
  "height": 191081,
  "blockHash": "e16088d33a686ed90e756a1a1f201800d45b391c894af1703bfe9e139df3c052"
}
```

### Additional Test Observations

The watcher has successfully extracted **13 observations** from Firo testnet, all following the same transaction structure:

- **Height Range:** 191081 - 191456 (375 blocks)
- **All transactions:** Firo → Ergo direction
- **Destination:** Same test address `3WweKGWo4DsRCCLHE2MZQse3v6iY4oegQCYZeQbqjMu6JP83E29W`
- **Amount per TX:** 0.005 FIRO (500,000 satoshis)
- **Fees:** Bridge=0.01 FIRO (1,000,000 satoshis), Network=0.01 FIRO (1,000,000 satoshis)
- **Status:** All successfully extracted and stored in `watcher.sqlite`

**Database Query:**
```sql
SELECT sourceTxId, amount, bridgeFee, networkFee, toChain, height 
FROM observation_entity 
WHERE fromChain='firo';
-- Returns 13 rows
```

---

## Complete Implementation Status

All RCS-003 modules have been fully implemented and tested with both mock data and real Firo testnet nodes. Below is the breakdown of each module along with their test file references.

#### 1. Scanner Module ✅

**Location:** `scanner/packages/scanners/firo-rpc-scanner/`

**Class:** `FiroRpcScanner`

**Module Test Files Reference:**
```
scanner/packages/scanners/firo-rpc-scanner/tests/
  ├── network
  ├──── firoRpcNetwork.mock.spec.ts
  ├──── firoRpcNetwork.production.spec.ts
  └── ...
```

---

#### 2. Address Codec Module ✅

**Location:** `utils/packages/address-codec/lib/`

**Module Test Files Reference:**
```
utils/packages/address-codec/tests/
  ├── decoder.spec.ts
  ├── encoder.spec.ts
  ├── validation.spec.ts
  └── ...
```

---

#### 3. Rosen Extractor ✅

**Location:** `utils/packages/rosen-extractor/lib/getRosenData/firo/`

**Classes:** `FiroRosenExtractor`, `FiroRpcRosenExtractor`

**Module Test Files Reference:**
```
utils/packages/rosen-extractor/tests/getRosenData/firo/
  ├── firoRosenExtractor.mock.spec.ts
  ├── firoRpcRosenExtractor.mock.spec.ts
  ├── firoRpcRosenExtractor.production.spec.ts
  └── ...
```

**Network Module Test Files:**
```
rosen-chains/packages/networks/firo-rpc/tests/
  ├── firoRpcNetwork.mock.spec.ts
  ├── firoRpcNetwork.testnet.spec.ts
  └── ...
```

---

#### 4. Observation Extractor Module ✅

**Location:** `scanner/packages/observation-extractors/firo-observation-extractor/`

**Class:** `FiroObservationExtractor`

**Module Test Files Reference:**
```
scanner/packages/observation-extractors/firo-observation-extractor/tests/
  └── firoRpcObservationExtractor.spec.ts
```

---

#### 5. Rosen Chain Module ✅

**Location:** `rosen-chains/packages/chains/firo/`

**Class:** `FiroChain`

**Module Test Files Reference:**
```
rosen-chains/packages/chains/firo/tests/
  ├── firoChain.mock.spec.ts
  ├── firoChain.mainnet.spec.ts
  ├── firoChain.testnet.spec.ts
  ├── firoTransaction.mock.spec.ts
  ├── firoTransaction.production.spec.ts
  └── ...
```

---

#### 6. Health Check Module ✅


**Location:** `health-check/packages/asset-check/lib/firo` and `health-check/packages/scanner-sync-check/lib/firo`

##### a) Asset Check
**Module Test Files Reference:**
```
health-check/packages/asset-check/tests/firo
  ├── rpc.spec.ts
  └── testFiro.ts
```

##### b) Scanner Sync Check
**Implementation:** Watcher service monitoring



