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

:::note
examples are given for understanding only and are non-normative.
:::

## [IDX-COMMONS]

### Basic Cosmos-SDK data

Commons used cosmos-sdk modules and produced data MUST be indexed.

### [IDX-COMMONS-VERANA] Verana Data

#### [IDX-COMMONS-VERANA-REQUEST] Mandatory Request Parameters

All Verana methods MUST include the following mandatory parameters:

- `query_at` (timestamp) (*optional*): if set, query *at* `query_at`, else query *at* now(). Used to query the ledger state at a previous datetime.

:::warning
Be careful with the `query_at`. Returned data MUST be properly calculated. Example: if a permission has been revoked today and a query returns the same permission *at* yesterday, permission MUST NOT appear revoked in the future in the returned data, it MUST be returned as it was at the specific datetime. For slashed perms, it implies having a copy of all slashs to properly recalculate the permission state at an earlier date.
:::

#### [IDX-COMMONS-VERANA-RESPONSE] Mandatory Response Parameters

All query response MUST include at least:

- `index_ts` (timestamp): a timestamp that matches the last indexed block of the chain;
- `height` (number): the block height;
- `query_at` (timestamp): the datetime *at* which the query response is valid.

## [IDX-TR]

## [IDX-CS]

## [IDX-PERM]

### [IDX-PERM-GET-ACCOUNT-REPUTATION] Get Account Reputation

#### Get Account Reputation - parameters

Added to [IDX-COMMONS-VERANA-REQUEST], this method MUST provide the following request parameters:

- `account` (account) (*mandatory*)
- `tr_id` (number) (*optional*): filter by trust registry id.
- `schema_id` (number) (*optional*): filter by schema_id.
- `include_slash_details` (boolean) (*optional*): if we include the detail of slashs/repayments.

If user specifies `tr_id`, we will include info of all credential schemas of this trust registry. If user specifies `schema_id`, we will include info of this credential schema of the controller trust registry. If nothing is specified, we'll return info of all trust registries and their credential schemas.

#### Get Account Reputation - returned data

Returns reputation of a given account. Can filter by ecosystem (trust registry) or credential schema.

Added to [IDX-COMMONS-VERANA-RESPONSE], this method MUST provide the following response parameters:

**Global data:**

- the account address;
- the account balance;
- the trust deposit of this account;
- the slashed trust deposit amount of this account;
- the repaid trust deposit amount of this account;
- the number of slashs of this account;
- the date of the first interaction of this account with an ecosystem (trust registry) or the did directory;
- the total number of trust registries controlled by this account;
- the total number of credential schemas controlled by this account;

if `include_slash_details` is true:

- a list of slash_details:

**Slash Details**

- slashed_ts:
- slashed_by:
- repaid_ts:
- repaid_by:

Then what's follows depend on filter.

For each Trust Registry: (depends on filter), if account have had at least one interaction linked to one credential schema of this trust registry:

**Trust registry data:**

For each considered trust registry, a list of credential schema related data, if account have had at least one interaction with the schema:

**Credential schema data:**

- the calculated permission-based trust deposit of this account in the context of the credential schema;
- the calculated permission-based slashed trust deposit amount of this account in the context of the credential schema;
- the calculated permission-based repaid trust deposit amount of this account in the context of the credential schema;
- the calculated permission-based number of slashs of this account in the context of the credential schema;
- the number of issued credentials (if available (sessions)) in the context of the credential schema;
- the number of verified credentials (if available (sessions)) in the context of the credential schema;
- the number of validation processes run as a validator in the context of the credential schema;
- the number of validation processes run as an applicant in the context of the credential schema;

- the total number of ISSUER permissions in the context of the credential schema;
- the total number of VERIFIER permissions in the context of the credential schema;
- the total number of ISSUER_GRANTOR permissions in the context of the credential schema;
- the total number of VERIFIER_GRANTOR permissions in the context of the credential schema;
- the total number of ECOSYSTEM permissions in the context of the credential schema;

- the number of active (not revoked, not expired) ISSUER permissions in the context of the credential schema;
- the number of active (not revoked, not expired) VERIFIER permissions in the context of the credential schema;
- the number of active (not revoked, not expired) ISSUER_GRANTOR permissions in the context of the credential schema;
- the number of active (not revoked, not expired) VERIFIER_GRANTOR permissions in the context of the credential schema;
- the number of active (not revoked, not expired) ECOSYSTEM permissions in the context of the credential schema;

if `include_slash_details` is true:

- a list of permission_slash_details

**Permission Slash Details:**

- perm_id
- schema_id
- tr_id
- slashed_ts
- slashed_by
- repaid_ts
- repaid_by

### [IDX-PERM-PERMISSION-LIST] List Permissions

#### [IDX-PERM-PERMISSION-LIST-REQUEST] List Permissions - parameters

Added to [IDX-COMMONS-VERANA-REQUEST], this method MUST provide the following request parameters:

- `schema_id` (number) (*mandatory*): the schema id.
- `grantee` (account) (*optional*): the grantee account.
- `did` (string) (*optional*): the did the permission refers to.
- `perm_id` (number) (*optional*): limit to permissions where the `validator_perm_id` is `perm_id`.
- `type` (PermissionType) (*optional*): if we want to limit to a specific permission type.
- `only_valid` (boolean) (*optional*): if set to true, only return valid permissions.
- `query_at` (timestamp) (*optional*): if set, query *at* `query_at`, else query *at* now(). Used to query the ledger state at a previous datetime.
- `modified_after` (timestamp) (*optional*): limit to permissions modified after (or equal to) `modified_after`.
- `response_max_size` (small number) (*optional*): limit to `response_max_size` results. Must be min 1, max 1,024. Default to 32.
- `country` (string) (*optional*): limit to `country`.
- `vp_state` (ValidationState) (*optional*): limit to permissions with a `vp_state` not null and equal to `vp_state`.

#### [IDX-PERM-PERMISSION-LIST-RESPONSE] List Permissions - returned data

Added to [IDX-COMMONS-VERANA-RESPONSE], this method MUST provide the following response parameters:

A list of found permissions, if any.

Example (non-normative):

```json
{

   "metadata": {
      "index_ts": "2025-08-25T09:58:06+00:00Z",
      "height": 12345,
      "query_at": "2025-08-25T09:58:06+00:00Z",
      ...
   },
   "permissions": [
      {
         id: 1234,
         schema_id: ...
         type: ECOSYSTEM,
         ...
      },
      {
            id: 2345,
            schema_id: ...
            type: ECOSYSTEM,
            ...
      },
   ]
   
   ...
```

### [IDX-PERM-GET-PERMISSION] Get a Permission

#### Get a Permission - parameters

Added to [IDX-COMMONS-VERANA-REQUEST], this method MUST provide the following request parameters:

- `perm_id` (number) (*mandatory*)

#### Get a Permission - returned data

Added to [IDX-COMMONS-VERANA-RESPONSE], this method MUST provide the following response parameters:

Returns the permission.

### [IDX-PERM-PERMISSION-TREE] Get Permission Tree

#### Get Permission Tree - parameters

- `schema_id` (number) (*mandatory*): the schema id.
- `perm_id` (number) (*optional*): return sub-tree from this perm_id only
- `limit_type` (PermissionType) (*optional*): if we want to limit to until a specific permission type.
- `only_valid` (boolean) (*optional*): if set to true, only return valid permissions.

if `perm_id` is set, return only sub-tree starting from this perm.

If user specifies `limit_type`, we return only part of the tree.

- if `limit_type` is set to ECOSYSTEM: return only ECOSYSTEM permissions.
- if `limit_type` is set to ISSUER_GRANTOR: return at most ECOSYSTEM and ISSUER_GRANTOR permissions.
- if `limit_type` is set to VERIFIER_GRANTOR: return at most ECOSYSTEM and VERIFIER_GRANTOR permissions.
- if `limit_type` is set to ISSUER: return at most ECOSYSTEM, ISSUER_GRANTOR, and ISSUER permissions.
- if `limit_type` is set to VERIFIER: return at most ECOSYSTEM, VERIFIER_GRANTOR, and VERIFIER permissions.
- if `limit_type` is set to HOLDER: return at most ECOSYSTEM, ISSUER_GRANTOR, ISSUER, and HOLDER permissions.

If `only_valid` is specified and set to true, omit invalid permissions.

#### Get Permission Tree - returned data

Returns schema full info (all data) as well as a tree of permissions, with a format similar to this example:

```json
{
   "metadata": {
      "index_ts": "2025-08-25T09:58:06+00:00Z",
      "height": 12345,
      "query_at": "2025-08-25T09:58:06+00:00Z"
      ...
   }
   "schema_id": 1234,
   ... other schema info...
   "permission_tree": [
      {
         id: 1234,
         type: ECOSYSTEM,
         ...
         "issuer_grantors": [
            {
               id: 2345,
               type: ISSUER_GRANTOR,
               ...
               "issuers": [
                  {
                  id: 3456,
                  type: ISSUER,
                  ...
                  "issuers": [
                     ...
                  ]
                  },
                  {
                     ...
                  }
                  ...

               ]
            },
            {
               id: 5678,
               ...
            }
         ]
      }
   ]

}
```

## [IDX-SESS]

Permission Sessions MUST be indexed.

## [IDX-DD]

DID Directory MUST be indexed.

## [IDX-TD]

Trust Deposits MUST be indexed.

## [IDX-GLOBAL]

Global Variables MUST be indexed.

## [QUERY]

All query answers MUST be paginated.

## Implementation

As several cosmos-sdk based chain indexer implementations exist, it will be faster to fork and extend one instead of rebuilding everything from scratch. You should do your own research and we should discuss the choice before starting to build.

Example: [horoscope-v2](https://github.com/aura-nw/horoscope-v2/)
