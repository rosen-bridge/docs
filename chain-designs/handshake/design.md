# Handshake Rosen Bridge Integration Solution

**Document Version**: 1.0
**Author**: Aco Šmrkas
**Date**: February 2026
**Reference**: RCS-003 Requirements Mapping

**⚠️ Important Note: This document is provided for reference purposes only. Its structure may not reflect the expected design format, and its content may be outdated or incomplete. It should not be used as a reference for preparing other chain design documents.**

---

## Executive Summary

This document describes the Handshake chain integration for the Rosen Bridge

---

## Part 1: Requirements Satisfaction Map

### Base Requirements (2)

#### Requirement 1: Multi-Signer Addresses
**RCS-003 Requirement**: "All assets should be held in the bridge address that requires multiple signers (guards) to transfer from it."

**Status**: ✓ Implemented

**Implementation**:
- **File**: `rosen-bridge-rosen-chains/packages/chains/handshake/lib/handshakeChain.ts`
- **Mechanism**: Threshold Signature Scheme (TSS) signing
  - Lock address: Multi-sig capable
  - All locked HNS UTXOs are held by this bridge-controlled address
  - Transactions spending from lock address require TSS consensus from guard services
  - Signature generation: `signTransaction()` method calls `TssSignFunction` for each input
  - Aggregate public key: Configured in `HandshakeConfigs.aggregatedPublicKey`
  - Witness v0 (P2WPKH) script: `Script.fromPubkeyhash(lockAddress.hash)`
  - Each signature is DER-encoded + SIGHASH_ALL byte (0x01)

---

#### Requirement 2: Data Writing (UPDATE Covenant)
**RCS-003 Requirement**: "Bridge requests require writing metadata (fee, target chain, address) to the lock transaction alongside asset transfers."

**Status**: ✓ Implemented

**Implementation**:
- **File**: `rosen-bridge-ui/wallets/shake/src/wallet.ts`
- **Mechanism**: UPDATE covenant (type 7) output in lock transaction
  - Encodes: `toChain`, `toAddress`, `bridgeFee`, `networkFee`, aggregated public key
  - Format: Hex-encoded binary data stored in `covenant.items[2]`

**Data Extraction**:
- **File**: `rosen-bridge-utils/packages/rosen-extractor/lib/getRosenData/handshake/handshakeRosenExtractor.ts`
- Parser: `extractRawData()` method reads UPDATE covenant and extracts RosenData
- Validation: Checks for both UPDATE output (covenant.type === 7) AND lock address output before accepting transaction
- Target chains supported mapped via TokenMap

**Example Transaction Structure** (Mainnet Transaction ID: `46be8f22e40cc4aba0f16c64c82b38d31742a114aaeb3720ed5eb0c0cf79dfc3`):
```json
{
  "inputs": [
    {
      "value": 0,
      "covenant": {"type": 7, "action": "UPDATE"}
    },
    {
      "value": 125754707,
      "covenant": {"type": 0, "action": "NONE"}
    }
  ],
  "outputs": [
    {
      "value": 0,
      "covenant": {
        "type": 7,
        "action": "UPDATE",
        "items": [
          "75f07643d38484c30ae86f12cef80efaed461272217cb68c16846b51644b4089",
          "37bd0000",
          "03000000000007a12000000000000186a06761646472317139773267776c363936666b7873383230797a74786a6e75756571766e73326778613374766678676a766c70686179756766356d7a64746573706a71356c397267676d39676b6777767a3636736b6d7565776179757a6538367a7473667a7a757676"
        ]
      }
    },
    {
      "value": 1000000,
      "address": "hs1qmuk560r3q8cqv07584m8d9udxnr2dk5np6ls5d",
      "covenant": {"type": 0}
    },
    {
      "value": 124740707,
      "address": "hs1q5hr40krjqh2t6vhp0262suh24clar6yx7fu0xu",
      "covenant": {"type": 0}
    }
  ],
  "fee": 14000
}
```

---

### Optional Requirements (5)

#### Requirement 3: Token Support
**RCS-003 Requirement**: "The blockchain should support tokens if assets from other chains will be bridged into it."

**Status**: ✗ NOT Applicable

**Reasoning**:
- Handshake bridge only transfers HNS OUT to other chains, never receives foreign tokens
- Therefore, token support requirement does not apply

**Handshake Token Limitations**:
- Handshake native protocol does NOT support tokens
- Handshake only supports HNS as the single native currency
- Covenant system (UPDATE, REVEAL, BID, etc.) is exclusively for domain name management
- No token creation, minting, or token infrastructure exists on Handshake

---

#### Requirement 4: dApp Connector (User-Friendly Wallet Integration)
**RCS-003 Requirement**: "A wallet integration tool enabling users to automatically generate and sign bridge transactions without manual construction."

**Status**: ✓ Implemented

**Implementation**:
- **Primary File**: `rosen-bridge-ui/wallets/shake/src/wallet.ts`
- **Architecture**:
  1. Shake Wallet Browser Extension receives user intent via `performTransfer()`
  2. Wallet generates RosenData from bridge parameters (target chain, amount, fees, recipient)
  3. Creates MTX (Mutable Transaction) with:
     - Inputs from user's lock address UTXOs
     - Outputs: UPDATE covenant (RosenData) + lock address (HNS) + change
  4. Calls Shake extension's `sendRosenBridgeLock()` for signing
  5. Extension signs internally using user's private key
  6. Signed transaction broadcast to network

**Mempool-Aware Name Selection** (Enhanced Feature):

**What Are Locked Names?**
- Locked names are Handshake domain names that the bridge controls
- Each lock transaction MUST include a domain name as an UPDATE covenant input
- The domain name acts as an anchor for the UPDATE operation, carrying the RosenData metadata
- Example: `ccj3` is a bridge-controlled domain on Handshake

**Why Mempool Awareness Matters**:
- When a lock transaction is broadcast, it spends the domain name's owner UTXO
- While the transaction is pending in the mempool, that UTXO is marked as "spent"
- If another bridge transaction tries to spend the SAME domain name before the first one confirms, it fails - only 1 same name UPDATE per block
- With only one locked name (`ccj3`), the bridge can only process one lock transaction at a time
- Multiple simultaneous bridge requests = failed transactions (conflict on same UTXO)

**Solution - Multiple Locked Names + Mempool Checking**:
- **File**: `rosen-bridge-ui/wallets/shake/src/wallet.ts (private findFreeName())`
- Bridge maintains array of fallback domain names: `lockedNames: ['ccj3']` (expandable)
- Before creating transaction, `findFreeName()` queries the HSD node:
  1. Gets all pending transactions in mempool
  2. Extracts all spent output references (UTXOs being consumed)
  3. For each domain name in `lockedNames`:
     - Queries domain's owner UTXO hash:index
     - Checks if owner UTXO is already being spent in mempool
     - If NOT being spent → safe to use this domain
  4. Returns first available domain; throws error if all busy
- **Configuration**:
  - `lockedNames: ['ccj3']` - Can expand to `['ccj3', 'domain2', 'domain3']` for parallel capacity
  - `publicNodeUrl: 'https://api.handshakeapi.com/hsd'` - Public HSD node for mempool queries

**Benefit**:
- Multiple pending bridge transactions use different domains
- No conflicts on spent UTXOs
- Higher throughput for concurrent lock requests
- Graceful degradation when all names are busy (clear error vs transaction failure)

---

#### Requirement 5: Transaction Chaining
**RCS-003 Requirement**: "Ability to chain multiple sequential transactions to the same target, improving efficiency but potentially introducing deadlock risks."

**Status**: ✗ Not Implemented

**Reason**:
- **File**: `rosen-bridge-rosen-chains/packages/chains/handshake/lib/handshakeChain.ts`
- `getMempoolBoxMapping()` returns empty Map with comment: "chaining transaction won't be done in HandshakeChain due to long block times and heavy size of transactions in mempool, similar to Bitcoin"

---

#### Requirement 6: Event Distinguishability
**RCS-003 Requirement**: "Transactions must be uniquely identifiable to prevent duplicate payments or missed events when identical transactions occur simultaneously."

**Status**: ✓ Implemented

**Implementation**:
- **Transaction ID**: Calculated from serialized MTX via `mtx.txid()` (double SHA256)
  - **File**: `rosen-bridge-rosen-chains/packages/chains/handshake/lib/handshakeChain.ts`
- **Event ID**: Assigned per bridge request
  - **File**: `rosen-bridge-rosen-chains/packages/chains/handshake/lib/handshakeTransaction.ts`
- **Uniqueness**: Transaction hash is extracted by watcher and stored in ObservationEntity with DB unique constraint on `(requestId, extractor)` preventing duplicates
  - **File**: `rosen-bridge-scanner/packages/abstract-observation-extractor/lib/extractor/abstractObservationExtractor.ts`

---

#### Requirement 7: Fee Handling Flexibility
**RCS-003 Requirement**: "The system must accommodate transaction fee fluctuations between request initiation and payment execution, minimizing asset loss from volatile fees."

**Status**: ✓ Implemented

**Implementation**:
- **Dynamic Fee Estimation**:
  - **File**: `rosen-bridge-rosen-chains/packages/networks/handshake-rpc/lib/handshakeRpcNetwork.ts`
  - RPC Call: `estimatefee(nblocks=6)` - targets 6-block confirmation
  - RPC Returns: Fee rate in HNS/KB
  - Conversion: HNS/KB → dollarydoos (multiply by 1,000,000) → dollarydoos/byte (divide by 1024) → rounded up
- **Accurate Fee Calculation**:
  - **File**: `rosen-bridge-rosen-chains/packages/chains/handshake/lib/handshakeUtils.ts`
  - Uses hsd native `mtx.getVirtualSize()` for precise virtual size calculation
  - Accounts for all transaction components: inputs, outputs, covenants, witness data
  - Formula: `fee = ceil(vsize * feeRatio)` where feeRatio is dollarydoos/byte

**Change Management**:
- **File**: `handshakeChain.ts`
- Adds temporary change output to MTX before fee estimation
- Estimates fee on complete transaction structure
- Updates change output value with final amount

**Fee Slippage Tolerance**:
- **File**: `handshakeChain.ts`
- Config: `HandshakeConfigs.txFeeSlippage = 10%` (default)
- `verifyTransactionFee()` allows difference up to 10%
- Acceptable margin for network fee fluctuations

**Dust Limit Protection**:
- Minimum UTXO value: 1000 dollarydoos
- Config: `getMinimumNativeToken() = 1000n`

---

## Part 2: Lock Transaction Structure

### Overall Transaction Layout

A Handshake lock transaction follows this structure:

```
Transaction (MTX)
├── Inputs (2)
│   ├── Input[0]: Previous UPDATE Covenant
│   │   ├── Value: 0 dollarydoos
│   │   └── Covenant Type: 7 (UPDATE)
│   └── Input[1]: User's HNS for bridging
│       ├── Value: Amount requested (dollarydoos)
│       ├── Address Version: 0 (Witness v0)
│       └── Address Hash: User's address
│
└── Outputs (3)
    ├── Output[0]: UPDATE Covenant (RosenData)
    │   ├── Value: 0 dollarydoos
    │   ├── Covenant Type: 7 (UPDATE)
    │   └── Covenant Data: covenant.items[2] = Hex-encoded RosenData
    │
    ├── Output[1]: Lock Address (HNS transfer)
    │   ├── Value: Amount requested (dollarydoos)
    │   ├── Address Version: 0 (Witness v0)
    │   └── Address Hash: Bridge lock address
    │
    └── Output[2]: Change (optional)
        ├── Value: Remaining HNS after fees
        ├── Address Version: 0 (Witness v0)
        └── Address Hash: User's change address
```

## Part 3: Sample Lock Transactions

### Example 1: Real Transaction on Handshake

**TX Hash**: `46be8f22e40cc4aba0f16c64c82b38d31742a114aaeb3720ed5eb0c0cf79dfc3`

**API Link**: https://api.handshakeapi.com/hsd/tx/46be8f22e40cc4aba0f16c64c82b38d31742a114aaeb3720ed5eb0c0cf79dfc3

**Explorer Link**: https://shakeshift.com/transaction/46be8f22e40cc4aba0f16c64c82b38d31742a114aaeb3720ed5eb0c0cf79dfc3

**cURL RPC**: curl -X POST https://api.handshakeapi.com/hsd -d '{"method":"getrawtransaction","params":["46be8f22e40cc4aba0f16c64c82b38d31742a114aaeb3720ed5eb0c0cf79dfc3", true]}' -H 'Content-Type: application/json'
