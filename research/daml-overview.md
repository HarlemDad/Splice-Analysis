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
How It All Fits Together

The system is a decentralized payment network. Super Validators govern the network through voting (dso-governance). The native token Amulet (splice-amulet) has built-in economics — it expires over time to encourage spending, and participants earn rewards through mining rounds. Users interact through wallets (splice-wallet) that can make payments and subscriptions (splice-wallet-payments). Apps like Splitwell and the name service build on top of this, and the token standard interfaces provide a clean abstraction for interoperability.
