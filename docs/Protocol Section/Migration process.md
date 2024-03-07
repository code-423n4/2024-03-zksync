# Migration process

This document describes the process of migrating the currently deployed single chain zkSync Era into the Hyperchain, including the process of moving funds from Diamond Proxy and L1 ERC20 Bridge into the brand new Shared bridge while ensuring security guarantees. Issues related to the migration process are considered in the scope of the contest.

## Introduction

We will migrate the old system of Era's `Diamond Proxy`, `L1ERC20Bridge` to a system that is capable of hosting multiple interoperable hyperchains with the `L1SharedBridge`, `Bridgehub`, `StateTransitionManager` and other contracts. 

This upgrade process has the following steps: 

I. Upgrade the system

1. Deploy the new contracts
    
    We first deploy the new contracts. These include the `Bridgehub`, `StateTransitionManager`, `L1SharedBridge`, the new facets and the `ValidatorTimelock`. At this point, if we wanted to we could register new hyperchains. 

2. Integrate Era into new contracts, and upgrade L2 system contracts
    
    We upgrade Era (we do this via a standard diamond cut upgrade, set the L2 system contract upgrade tx hash, and a call to the new DiamondInit function to initialize the `DiamondProxy` with the new protocol version and variables).
    We integrate Era into the the contracts via `registerAlreadyDeployedStateTransition` on STM and `createNewChain` on Bridgehub.  
    We transfer all Ether to the `L1SharedBridge`. We do this via the the `transferFundsFromLegacy` function on the `L1SharedBridge`.

    At this point deposits to the `L1SharedBridge` and to the legacy functions on `Mailbox` facet of the `DiamondProxy` should work. The legacy `L1ERC20Bridge` will also work, as it will communicate with the backwards compatible `Mailbox`.

II. Upgrade L1ERC20Bridge contract

1. Upgrade L2 bridge

    The new `L2ERC20Bridge` will upgraded to become the `L2SharedBridge`, and it will be backwards compatible with all messages from the old `L1ERC20Bridge`, so we upgrade that first as L1->L2 messages are much faster, and in the meantime we can upgrade the `L1ERC20Bridge`. The new `L2SharedBridge` can receive deposits from both the old `L1ERC20Bridge` and the new `L1SharedBridge`.

2. Upgrade L1ERC20Bridge

    We upgrade the `L1ERC20Bridge`, and move all ERC20 tokens to the `L1SharedBridge`. 
    
Note migrating Era and its `L1ERC20Bridge` are possible to do for other separately deployed hyperchains as well, in case we need to do it multiple times.