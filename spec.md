# Verana Indexer Specification

**Specification Status:** Pre-Draft

**Latest Draft:** [verana-labs/verana-indexer-spec](https://github.com/verana-labs/verana-indexer-spec)

**Editors:**

~ [Fabrice Rochette](https://www.linkedin.com/in/fabricerochette) (The Verana Foundation, 2060.io)

<!-- -->

**Participate:**

~ [GitHub repo](https://github.com/verana-labs/verana-indexer-spec)

~ [File a bug](https://github.com/verana-labs/verana-indexer-spec/issues)

~ [Commit history](https://github.com/verana-labs/verana-indexer-spec/commits/main)

---

## About this Document

This specification is for the Verana Indexer, a container-based App easily deployable locally, or in any modern cloud infrastructure.

## Introduction

*This section is non-normative.*

### Why We Need the Verana Indexer

1. **Ledger Storage Is Expensive**  
   * Every on-chain byte costs gas or block space.  
   * To keep fees low, a VPR stores only the **minimum viable record** (IDs, hashes, pointers, values).

2. **Limited Native Indexing**  
   * Blockchains are append-only logs, not SQL engines.  
   * Adding many on-chain indexes would explode storage costs and slow consensus.

3. **Query Complexity**  
   * Wallets and services still need fast look-ups:  
     - *Is this credential issued by an authorized issuer?*  
     - *Which credential schema contains the word "name"?*  
   * Without an indexer, you’d have to **scan the entire chain** for each query, which is costly and impractical.

4. **Trust Resolution**
   * Trust-resolving a DID with the [Verre] library takes some time.
   * Data stored in credentials is not present in the ledger.
   * Without the indexer, it would not be possible to search credential attributes.

### 🔧 Enter the Verana Indexer

| Feature | Purpose |
|---------|---------|
| **Ledger Listener** | Subscribes to new blocks & events. |
| **Selective Ingest** | Index only new information or modified information. |
| **Off-Chain Database** | Builds optimized tables & secondary indexes. |
| **Real-Time Updates** | Each ledger new block triggers an index refresh, keeping data in sync. |
| **Lightweight Container** | Runs anywhere: validator nodes, cloud, or self-hosted. |

With the Verana Indexer, the ledger stays lean, yet wallets, verifiers, and dashboards get millisecond-fast queries. It marries on-chain integrity with off-chain speed, and makes every credential uncovered during trust resolution instantly searchable.

## Terminology

TBW

## Specification

### [GENERAL] General

The Verana Indexer MUST be delivered as a container.

#### [GENERAL-DEPLOYMENT] General - Deployment

- [GENERAL-DEPLOYMENT-HUB] Container MUST be versioned and deployed to docker hub.
- [GENERAL-DEPLOYMENT-DOCKER] Documentation for running the container with docker MUST be provided.
- [GENERAL-DEPLOYMENT-KUBE] Documentation for deploying the container in Kubernetes MUST be provided.
- [GENERAL-DEPLOYMENT-HELM] Verana Indexer MUST be installable in Kubernetes by using helm install. Documentation MUST be provided.
- [GENERAL-DEPLOYMENT-LAMBDA] Verana Indexer MUST be runnable in Amazon Lambda. Documentation MUST be provided.

#### [GENERAL-ENV] General - Container Variables

| Type                   |Variable       | Description | Default Value (if unspecified) |
|------------------------|---------------|-----|----------------------------------|
| Network Configuration  | API_ENDPOINT  |     | https://api.verana.network       |
|                        | RPC_ENDPOINT  |     | https://rpc.verana.network       |
|                        | CHAIN_ID      |     | vna-mainnet-1       |
|                        | NETWORK_NAME  |     | Mainnet       |


## [IDX-GENERAL]

[IDX-GENERAL-DID-TR] Any found DID MUST be resolved and all its DID document attributes indexed.
[IDX-GENERAL-DID-DOC] Any found DID MUST be trust-resolved and all its credentials resolved and their attributes indexed.
[IDX-GENERAL-DID-LK] Links between DIDs (references) MUST be indexed. (ex: a credential presented by DID #1 has been issued by DID #2).

## [IDX-COMMONS]

Commons used cosmos-sdk modules and produced data MUST be indexed.

## [IDX-TR]

[IDX-TR-BASIC] Trust Registries MUST be indexed.
[IDX-TR-GF] Governance Framework documents MUST be indexed. Their registered hash MUST be verified and a verification flag MUST exist in the database.

## [IDX-CS]

Credential Schemas MUST be indexed.

## [IDX-PERM]

Permissions MUST be indexed.

## [IDX-SESS]

Permission Sessions MUST be indexed.

## [IDX-DD]

DID Directory MUST be indexed.

## [IDX-TD]

Trust Deposits MUST be indexed.

## Implementation

As several cosmos-sdk based chain indexer implementations exist, it will be faster to fork and extend one instead of rebuilding everything from scratch. You should do your own research and we should discuss the choice before starting to build.

Example: [horoscope-v2](https://github.com/aura-nw/horoscope-v2/)
