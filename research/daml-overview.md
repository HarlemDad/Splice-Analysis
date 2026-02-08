Here's a plain-English overview of all the Daml packages inside daml/:

---
Core Foundation

splice-util — The base utility library. Provides helper functions used everywhere else: safe contract fetching, assertions, and a patching mechanism for upgrading contract data.

---
The Token Layer

splice-amulet — The heart of the system. Defines Amulet, the native token of the network. Key concepts:

- Amulet & LockedAmulet — The token itself. Amulets have an expiring amount — they slowly lose value over time through inactivity/holding fees, which encourages circulation.
- Mining Rounds — The economy runs in rounds. Each round goes through a lifecycle: Open → Summarizing → Issuing → Closed. During open rounds, activity is recorded. During summarizing, rewards are tallied. During issuing, new Amulets are minted as rewards.
- Reward Coupons — When participants do useful work, they earn coupons (AppRewardCoupon, ValidatorRewardCoupon, SvRewardCoupon). These coupons get converted into actual Amulets during the issuing phase.
- AmuletRules — The master governance contract that controls how transfers, minting, tapping (faucets), and fee calculations work.
- Transfer & Fees — Transfer logic including fee schedules (fixed fees, per-round rates), with support for two-step transfers and external party integration.
- Validator Licenses — Tracks which validators are onboarded and their faucet usage state.
- Decentralized Synchronizer — Manages traffic/bandwidth allocation on the network.

---
Governance

splice-dso-governance — Governs the Decentralized Synchronizer Operator (DSO), which is the collective that runs the network. Key concepts:

- DsoRules — The main governance contract. Super Validators (SVs) collectively make decisions through a confirmation/voting mechanism requiring 2/3 agreement.
- SV Onboarding — Workflow for adding new Super Validators to the network.
- SV State — Tracks each SV's status, reward weight, and participation.
- Amulet Price Voting — SVs vote on the USD price of Amulet.
- CometBFT Integration — Ties into the CometBFT consensus layer for governance node coordination.
- DsoBootstrap — Handles initial network bootstrapping.

---
Payments & Wallet

splice-wallet-payments — Shared payment primitives:

- Payment Requests — An app can request payment from a user (in USD or Amulet). The request goes through states: requested → accepted → terminated.
- Subscriptions — Recurring payment model. A subscription request creates an idle state, which periodically triggers payments.

splice-wallet — The end-user wallet contracts:

- WalletInstall — The main wallet contract. Supports batched execution of multiple operations atomically (e.g., collect rewards, merge Amulets, make payments all in one transaction).
- Transfer Offers — One user offers to send Amulet to another.
- Transfer Preapproval — Pre-authorized transfers (receiver approves in advance).
- Buy Traffic Request — Users can purchase network traffic/bandwidth.
- Top-Up State — Tracks automatic traffic top-up configuration.
- Minting Delegation — Allows delegated minting rights.

---
Name Service

splice-amulet-name-service — An on-ledger domain name system (like DNS for the Amulet network):

- AnsRules — Governs how names are registered and managed.
- AnsEntry — A registered name entry, paid for via subscriptions.
- Conversion Rate Feed — Oracle for Amulet/USD conversion rates used in name pricing.

---
Validator Lifecycle

splice-validator-lifecycle — Simple onboarding workflow for validators joining the network. Uses a secret-based approach: a validator receives a secret, presents it to join, and the secret is marked as used.

---
Reference Application

splitwell — A sample expense-splitting app built on the platform. Groups of users can track shared expenses and settle up using Amulet. Demonstrates how third-party apps integrate with the wallet and payment system.

---
Utility / Integration Packages

- splice-api-featured-app-v1 — Interface for apps to register as "featured" and earn reward coupons for activity.
- splice-api-token-burn-mint-v1 — Burn/mint interface for token lifecycle operations.
- splice-util-featured-app-proxies — Proxy contracts that let featured apps and wallet users delegate activity tracking.
- splice-util-batched-markers — Batched creation of activity markers for featured apps.
- splice-util-token-standard-wallet — Token merge delegation utilities.

---
Token Standard (token-standard/)

The token standard is a set of Daml interfaces that define a generic, registry-agnostic protocol for managing tokenized assets. Think of it like an abstract API — any token registry (including Amulet) can implement these interfaces, and any wallet or app can interact with tokens through them without needing to know the specifics of the underlying registry. The standard lives in the token-standard/ directory and is organized into six interface packages that build on each other:

splice-api-token-metadata-v1 — The foundation layer. Defines generic types used across all the other interfaces:

- AnyValue — A universal value type that can represent text, numbers, dates, parties, contract references, lists, or maps. Used to pass arbitrary data across interface boundaries.
- ChoiceContext — A bag of key-value pairs that an app backend can pass into interface choices. Allows app-specific data to flow through without changing the interface.
- Metadata — Key-value string pairs attached to contracts and choice results for extensibility. Follows Kubernetes-style naming conventions (DNS-prefixed keys).
- ExtraArgs — Combines ChoiceContext and Metadata into a single type that most choices accept, keeping the interfaces flexible for future expansion.

splice-api-token-holding-v1 — Defines what it means to own tokens:

- InstrumentId — A globally unique identifier for a type of token/instrument, consisting of an admin party (the registry) and a text id.
- Lock — A mechanism to freeze holdings. Lock holders (one or more parties) must approve before locked holdings can move. Locks can have expiry times (absolute or relative) and a human-readable context explaining why the lock exists.
- Holding (interface) — The core ownership contract. Its view contains: the owner (who holds it), the instrumentId (what they hold), the amount (how much), an optional lock, and metadata. Any token registry implements this interface for its token contracts.

splice-api-token-transfer-instruction-v1 — Defines how tokens move between parties:

- Transfer — A specification for moving tokens: sender, receiver, amount, instrument, timing constraints (requestedAt, executeBefore), and optional input holdings to fund the transfer.
- TransferInstruction (interface) — Tracks the state of an in-progress transfer. A transfer can be in one of two states: pending receiver acceptance, or pending internal registry workflows. The interface provides four choices: Accept (receiver approves), Reject (receiver declines), Withdraw (sender cancels), and Update (registry advances internal workflow steps).
- TransferInstructionResult — The outcome of any transfer operation. Can be Pending (still in progress, returns the instruction contract), Completed (success, returns new receiver holdings), or Failed (returned to sender). Also returns any "change" holdings to the sender.
- TransferFactory (interface) — A factory contract that wallets use to initiate transfers. The sender exercises TransferFactory_Transfer with the transfer details, and the registry either completes it immediately or creates a TransferInstruction for multi-step processing.

splice-api-token-allocation-v1 — Enables atomic multi-leg settlements (e.g., delivery-versus-payment):

- SettlementInfo — Describes a settlement: who the executor is, a reference to the settlement, and timing deadlines (allocateBefore for senders to reserve assets, settleBefore for the executor to finalize).
- TransferLeg — A single leg of a multi-leg settlement: sender, receiver, amount, and instrument. A settlement can have many legs (e.g., one leg sends tokens, another sends payment).
- AllocationSpecification — Links a transfer leg to its settlement, with a unique leg identifier.
- Allocation (interface) — Represents reserved assets for one leg of a settlement. Once all legs are allocated, the executor can atomically execute all transfers. Choices: ExecuteTransfer (finalize this leg), Cancel (executor releases the reservation), Withdraw (sender takes back their assets before the deadline).

splice-api-token-allocation-instruction-v1 — The workflow for creating allocations:

- AllocationInstruction (interface) — Tracks the progress of creating an allocation. Like TransferInstruction, it can go through multiple steps. Senders can Withdraw, and the registry can Update to advance internal workflows.
- AllocationFactory (interface) — A factory that wallets use to request allocations. The sender exercises AllocationFactory_Allocate with the allocation details and input holdings. The registry either creates the allocation directly or returns a pending instruction.
- AllocationInstructionResult — The outcome: Pending (instruction created, awaiting more steps), Completed (allocation created successfully), or Failed (holdings released back).

splice-api-token-allocation-request-v1 — How apps request allocations from users:

- AllocationRequest (interface) — An app creates this to ask users to allocate assets for a settlement. It contains the settlement info and the list of transfer legs that need funding. Senders can Reject the request (signaling they won't participate), and the executor can Withdraw it (e.g., if a trade is cancelled). Wallets see these requests and prompt users to fund the required legs.

How the Token Standard layers work together: A wallet discovers what tokens a user holds via Holding contracts. To send tokens, it uses a TransferFactory to create a transfer — simple transfers may complete immediately, while complex ones produce a TransferInstruction that goes through multiple steps. For atomic settlements (like trading token A for token B), an app creates an AllocationRequest asking both parties to reserve assets. Each party's wallet uses an AllocationFactory to create Allocations (possibly going through AllocationInstructions). Once all legs are allocated, the executor atomically executes all transfers. Throughout, Metadata and ExtraArgs provide extensibility without changing the interfaces.

---
How It All Fits Together

The system is a decentralized payment network. Super Validators govern the network through voting (dso-governance). The native token Amulet (splice-amulet) has built-in economics — it expires over time to encourage spending, and participants earn rewards through mining rounds. Users interact through wallets (splice-wallet) that can make payments and subscriptions (splice-wallet-payments). Apps like Splitwell and the name service build on top of this, and the token standard interfaces provide a clean abstraction for interoperability.
