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

| Type                           |Variable                               | Description                    | Default Value (if unspecified)                    |
|--------------------------------|---------------------------------------|----------------------------------|----------------------------------|
| General                        | APP_NAME                              |                                  | Veranito                         |
|                                | APP_LOGO                              |                                  | logo.svg                        |
|                                | ADDRESS_EXPLORER                      |                                  | https://www.mintscan.io/verana/address/VERANA_ADDRESS   |
| Network Configuration          | MAINNET_API_ENDPOINT                  |                                  | https://api.verana.network       |
|                                | MAINNET_RPC_ENDPOINT                  |                                  | https://rpc.verana.network       |
|                                | MAINNET_IDX_ENDPOINT                  |                                  | https://idx.verana.network       |
|                                | MAINNET_CHAIN_ID                      |                                  | vna-mainnet-1       |
|                                | MAINNET_NAME                          |                                  | Mainnet       |
|                                | MAINNET_TOPUP_VS                      |   List of VSs for top-up       | did:example:123, did:example:456       |
|                                | TESTNET_API_ENDPOINT                  |                                  | https://api.testnet.verana.network       |
|                                | TESTNET_RPC_ENDPOINT                  |                                  | https://rpc.testnet.verana.network       |
|                                | TESTNET_IDX_ENDPOINT                  |                                  | https://idx.testnet.verana.network       |
|                                | TESTNET_CHAIN_ID                      |                                  | vna-testnet-1       |
|                                | TESTNET_NAME                          |                                  | Testnet       |
|                                | TESTNET_TOPUP_VS                      |   List of VSs for top-up       | did:example:123, did:example:456       |
|                                | DEVNETS__VNA-DEVNET_MAIN__API_ENDPOINT|                                  | https://api.vna-devnet-main.devnet.verana.network       |
|                                | DEVNETS__VNA-DEVNET_MAIN__RPC_ENDPOINT|                                  | https://rpc.vna-devnet-main.devnet.verana.network       |
|                                | DEVNETS__VNA-DEVNET_MAIN__IDX_ENDPOINT|                                  | https://idx.vna-devnet-main.devnet.verana.network       |
|                                | DEVNETS__VNA-DEVNET_MAIN__NAME        |                                  | Vna Devnet Main       |
|                                | DEVNETS__VNA-DEVNET_MAIN__TOPUP_VS    |   List of VSs for top-up       | did:example:123, did:example:456       |
|                                | DEFAULT_NETWORK                       |   Default selected network in App    | vna-mainnet-1       |
| Internationalization           | DEFAULT_LOCALE                        |   Failover locale | en_US       |
|                                | SUPPORTED_LOCALES                     |                                  | en_US, fr_FR, en:en_US, fr:fr_FR       |
