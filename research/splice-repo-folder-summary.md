Folder-by-Folder Summary of the Splice Repository

---

apps/ — All application code, both backend (Scala) and frontend (TypeScript/React). Contains the main Splice node app, Super Validator app, Validator app, Wallet app, Scan app (transaction explorer), Amulet Name Service app, and the Splitwell demo app. Each app typically has a src/ directory for Scala backend code and a frontend/ directory for the React UI. A shared common/ directory holds libraries used across all apps.

build-tools/ — Scripts and tools for building, testing, and deploying Splice. Includes cncluster, a large Python CLI for managing Kubernetes clusters; Docker image build/push/scan scripts; Docker Compose orchestration scripts for local development; Auth0 token retrieval; Daml SDK installation; and Canton version bumping utilities.

canton/ — A vendored fork of the open-source Canton blockchain protocol. This is the largest component in the repo. Contains the Canton community edition source (participant nodes, synchronizer/domain nodes, ledger implementation, base libraries), plus Daml common staging code (TLS, JWT, gRPC utilities, error handling). CANTON_CODE_CHANGES.md documents the Splice-specific modifications from upstream Canton.

cluster/ — Infrastructure-as-code for deploying Splice to Kubernetes. Contains Pulumi projects (TypeScript) for GCP infrastructure, Canton network deployment, SV runbooks, and validator runbooks. Also contains Helm charts for each service type (SV node, validator, scan, domain, CometBFT, PostgreSQL), Docker image definitions, Docker Compose configs, and deployment scripts.

daml/ — All Daml smart contract source code. Contains the core packages: splice-amulet (token and economics), splice-dso-governance (DSO governance), splice-wallet (user wallet), splice-wallet-payments (payment primitives), splice-amulet-name-service (name service), splice-validator-lifecycle (validator onboarding), splitwell (demo app), and various utility packages. Also includes a dars/ directory with pre-built Daml archive files and corresponding test packages.

docs/ — Sphinx-based documentation. Contains reStructuredText source files, API documentation templates (OpenAPI), and scripts for generating Daml docs and previewing the documentation site.

images/ — A single architecture diagram (Splice-Canton-Decentralized-Synchronizer.jpg) showing how Splice applications interact with Canton infrastructure.

load-tester/ — A k6-based load testing framework written in TypeScript. Generates continuous load against Splice applications (repeated tap operations). Can be deployed to clusters as Kubernetes CronJobs for ongoing performance testing.

network-health/ — Network health monitoring documentation and tools. Contains a comprehensive troubleshooting guide for investigating CometBFT consensus issues, SV status report problems, and network-wide failures. Includes lnav log format configs and screenshots of Grafana dashboards.

nix/ — Nix package definitions for a reproducible development environment. Defines exact versions of all development tools (Canton, CometBFT driver, Daml compiler, Pulumi, etc.) via a Nix flake. Used with direnv so developers automatically get the correct tool versions when entering the project directory.

openapi-templates/ — Templates for customizing auto-generated TypeScript API client code from OpenAPI specifications. Contains a patch file applied to generated output.

party-allocator/ — A TypeScript tool for scale testing. Allocates multiple parties on a single node, taps coins for them, and creates transfer pre-approvals. Used in cluster deployments to simulate many participants.

project/ — SBT (Scala Build Tool) project configuration. Contains build plugins, common build settings, dependency definitions, a custom Daml build plugin, license header enforcement, and code quality rules (Wartremover, Scalafix). This is where the Scala build logic lives.

readme/ — Image assets referenced by README files in the repo.

scripts/ — A large collection of development and operations scripts (57+). Includes PostgreSQL and CometBFT management, Auth0 setup, code quality checks, key generation for governance, Git backporting, cost analysis tools, transaction log scanning tools, OAuth test server, and bootstrap configuration.

token-standard/ — The Canton Network Token Standard (CIP-0056) implementation. Contains the six Daml interface packages (MetadataV1, HoldingV1, TransferInstructionV1, AllocationV1, AllocationInstructionV1, AllocationRequestV1), a TypeScript CLI for interacting with tokens, example implementations, and tests. This is the interoperability layer that Amulet and other tokens implement.
