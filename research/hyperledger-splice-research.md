# Hyperledger Splice — Research Summary

## Overview

Splice is a Hyperledger Lab providing reference applications for creating decentralized
interoperability infrastructure for the Canton Network — a public-permissioned blockchain
network for regulated, real-world assets. The code was developed and contributed by
Digital Asset.

## Core Purpose

Splice enables entities to operate, fund, and govern publicly available decentralized
Canton synchronizers. These synchronizers provide connectivity and interoperability
between applications built on the Canton protocol using the Daml smart contract language.

The Canton Network was launched to bridge the gap between:
- **Public blockchains** — offer interoperability but sacrifice privacy and control
- **Private blockchains** — provide privacy but create walled gardens

## Key Components

### Super Validators
Each node in a decentralized synchronizer is operated by a "Super Validator."
A group of active Super Validators forms the "decentralized synchronizer operator" (DSO).

### Amulet
An open-source framework for creating native utility tokens. Amulet tokens:
- Pay for synchronizer traffic
- Incentivize application providers to use synchronizers
- Provide a native payment mechanism between applications and users

### Wallet Kernel
A wallet gateway enabling transparent interaction between dApps, Validator Nodes,
and Wallet Providers. Currently in active early development.

### Token Standard APIs
APIs for working with tokens on the Canton Network.

## Technical Architecture

The system consists of four major layers. On-ledger logic is implemented in Daml
and compiled into DAR (Daml Archive) files. The system includes built-in:
- Governance tooling (transparent and trackable operations)
- Operations monitoring
- Payment automation

## Governance & Licensing

- Licensed under Apache 2.0
- Maintained as open source within the Hyperledger Foundation
  (now part of Linux Foundation Decentralized Trust since September 2024)
- The Global Synchronizer Foundation (GSF) was formed, supported by the
  Linux Foundation, for transparent governance of the Global Synchronizer

## Scale & Production

- Over $3 trillion processed across Daml-based applications
- First production implementation by Super Validators operating the Global Synchronizer

## Sources

- [Splice GitHub Repository](https://github.com/hyperledger-labs/splice)
- [Introducing Splice — LF Decentralized Trust Blog](https://www.lfdecentralizedtrust.org/blog/introducing-splice-a-new-hyperledger-lab-that-supports-canton-network-interoperability)
- [Splice Wallet Kernel GitHub](https://github.com/hyperledger-labs/splice-wallet-kernel)
- [Splice Documentation](https://docs.sync.global/release_notes.html)
- [Global Synchronizer Foundation Announcement](https://www.linuxfoundation.org/press/leading-market-participants-partner-with-the-linux-foundation-to-form-the-global-synchronizer-foundation)
