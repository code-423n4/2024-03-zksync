# Overview

zkSync Era is a Layer 2 scaling solution designed to enhance Ethereum's scalability without compromises. This document provides an overview of zkSync Era's rollup mechanism, explaining the main idea and how it contributes to a more scalable, secure, and user-friendly blockchain ecosystem.

## What is a Rollup?

A rollup is a blockchain scalability solution that processes and stores transaction data off-chain while ensuring the data's integrity and availability on the main chain. By doing so, rollups significantly increase transaction throughput without compromising security. There are primarily two types of rollups: Optimistic Rollups and Zero-Knowledge (zk) Rollups. zkSync Era utilizes the latter, leveraging cryptographic proofs for security and efficiency.

## How and why it works?

Ethereum's decentralized network has limited transaction throughput because its capacity doesn't scale with the number of validators. In essence, each validator performs the same job of validating each processed transaction, creating a bottleneck. In contrast, in Web2 scalability improves with the addition of more servers, as the network's capacity to handle requests increases linearly with each new server. 

The concept of a rollup addresses the blockchain scalability issue by moving computation off-chain and only sending the result of these computations back to Ethereum. ZK rollups, in particular, submit a validity proof alongside the execution result, making the validation of a zk proof significantly cheaper than re-executing each transaction.

### The Data Availability (DA) Problem

A crucial aspect of ensuring the integrity and security of rollups is addressing the Data Availability (DA) problem. If the state of the rollup is unknown to observers of Ethereum, then in scenarios where the validators (centralized or decentralized) stop processing, it becomes impossible to make state transitions without relying on a trusted validator. However, if the data is always available to observers, it's feasible to restore the state and continue processing the network even if the trusted validator ceases its operation. This link provides further details on the Data Availability problem: [Ethereum Data Availability](https://ethereum.org/en/developers/docs/data-availability/).

### ZK proof

Zero-Knowledge Proofs (ZKPs) offer a method to execute verifiable programs, wherein it's cheap to verify a zk proof on-chain. In the context of zkSync, ZKPs allow for the confirmation of the correctness of transaction execution without re-executing them.

ZK circuits are out of the contest's scope. However, there is an ongoing live bug bounty program open to anyone interested in identifying vulnerabilities within this domain.
- [zkSync Era Bug Bounty on Immunefi](https://immunefi.com/bounty/zksyncera/)

## zkSync Era components

zkSync Era protocol consists of the following critical components:

* Node Implementation: This component is responsible for receiving transactions from users and processing them. It maintains the off-chain state and handles the aggregation of transactions into batches as well as sends sealed batches onchain.
* ZK Circuits: These circuits are intricate mathematical constructs that represent verifiable computation logic. They are responsible for determing what can be verified as a valid proof. Specifically for zkSync, these circuits define the computation rules for EraVM execution, thats also defines how transactions are executed.
* Prover: The prover constructs the cryptographic proofs that attest to the correctness of the transactions processed off-chain. These proofs can be verified later on Ethereum, ensuring that only valid transactions are accepted.
* Smart Contracts: These contracts are the on-chain component of the zkRollup. They are responsible for verifying the proofs submitted by the prover and updating the Ethereum blockchain's state accordingly. Additionally, they facilitate interactions between Ethereum and zkSync, such as deposits, withdrawals, and cross-layer messaging.

![zkSync components.png](zkSync%20components.png)

While this contest primarily focuses on the smart contracts side of zkSync Era, understanding the interaction between different components can be beneficial. For additional context or insights, consider exploring these resources:
* Node Implementation: https://github.com/matter-labs/zksync-era
* ZK Circuits: https://github.com/matter-labs/era-zkevm_circuits


## The Hyperchain

The Hyperchain is the continuation of Ethereum's rollup centric roadmap. Ethereum provides security via DA and verification of proofs, but the question of execution is left to the rollups. In order to have the best UX, it is necessary to solve interoperability and the free flow of assets between chains. We do this via the Shared Bridge Contract on L1 which stores Ether and EsRC20 tokens for all hyperchains (implementing custom bridges is still possible). This upgrade will allow hyperbridging between hyperchains in the next major upgrade. The chains will be able to trust each other due to the fact that they are managed by the same STM contract, using the same VM and proof system. In the future more upgrades will follow, as this is still a topic under heavy research. 

- For more details watch [video](https://www.youtube.com/watch?v=BxpKa-S2m34).
