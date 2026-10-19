# "Verifiable OS for Agents": an alternative framing

The site doesn't use this framing. It's kept here so it can be picked up later: OutLayer as an
operating system for agents, with each platform primitive mapped onto its Linux equivalent. The
main page sells a narrower story instead: a secure transaction layer for AI agents.

## Fix before reuse

The copy below is as written. Some claims are wrong and need fixing before any of it goes live:

- **Bitcoin.** Wallets cover NEAR, every EVM chain, and Solana. There is no Bitcoin.
- **"On-chain attestation records" / "on-chain receipt".** Each run's TDX attestation is stored by
  the coordinator and served at `attestation_url`. It is not written on chain.
- **WIT package names.** The real names are `near:rpc`, `near:storage`, `outlayer:wallet`,
  `near:vrf` and `near:payment` (see `worker/wit/` in out-layer/outlayer). Names such as
  `outlayer:near:rpc`, `outlayer:storage`, `outlayer:vrf` and `outlayer:payment` don't exist.
- **"Every operation produces an attestation".** A TDX quote is produced for each completed run,
  not for every host call inside the run.

## Headline

**Verifiable OS for Agents. Not Another Wrapper.**

Every primitive an OS needs, except that every operation runs inside Intel TDX enclaves and
produces a cryptographic attestation certificate that anyone can verify on-chain.

## At a glance (tiles)

| Tile | Copy |
| --- | --- |
| TEE Kernel | 5 syscall interfaces exposed through WIT. The agent calls the kernel; the kernel enforces trust. |
| Encrypted Filesystem | Persistent storage with atomic CAS and increment. Private by default, public when you choose. |
| Hardware Secrets | Client encrypts, TEE decrypts. Operator never sees plaintext. API keys protected by hardware isolation. |
| Agent Custody | Multi-chain wallets. MPC-derived keys with spending limits, whitelists, and emergency freeze. |
| Access Conditions | 9 composable rules: token balance, NFT ownership, DAO role, regex, boolean logic. Kernel-level access control. |
| Attestation Chain | TDX 5-measurement verification. Sigstore binary certification. Attestation records signed with post-quantum keys (NEAR ML-DSA-65). Verify the hardware, the binary, the chain. |
| Scheduler | Interval, storage-diff, webhook triggers. The scheduler is untrusted: it decides *when*, the TEE decides *what*. |
| Verifiable Randomness | Ed25519 VRF with on-chain proof. Anti-grinding via blockchain-assigned request IDs. Provably fair. |
| Stateless Processes | Erlang model: start, work, write state, terminate. No crashes, no leaks. Immune to cascading failures. |
| Blockchain RPC | Full NEAR access from inside the enclave. View calls, send transactions. Agents interact with DeFi without exposing keys. |
| Built-in Monetization | Native stablecoin payments per execution. Refund API. Developers earn per call: no Stripe, no invoicing. |

## Why an OS

**Linux for Applications. OutLayer for Agents.**

The same fundamental abstractions (processes, filesystem, permissions, syscalls), designed for
autonomous agents and enforced by hardware attestation.

| Linux, for applications | OutLayer, for agents |
| --- | --- |
| Kernel: syscall interface to hardware | TEE Kernel: WIT syscall interfaces, hardware-attested |
| Filesystem: read, write, fsync | Encrypted Storage: per-agent encryption, atomic ops, cross-agent reads |
| Network: socket, connect, send | Blockchain RPC: full NEAR access from inside the enclave |
| /dev/urandom: random number generator | Verifiable RNG: Ed25519 VRF with on-chain cryptographic proof |
| Users & Groups: permission model | Access Conditions: 9 composable rules (token balance, NFT, DAO role…) |
| cron: scheduled execution | Scheduler: interval, storage-diff, webhook triggers |

## The kernel

**One Interface. Complete Agent Infrastructure.**

Storage, blockchain, wallets, randomness, payments: every primitive exposed as a kernel syscall.
Your agent runs as a sandboxed WASI process.

| Interface | Linux equivalent | Copy |
| --- | --- | --- |
| `near:rpc` | Network stack | Full NEAR RPC from inside the enclave. View calls, send signed transactions, read any contract state. |
| `near:storage` | Filesystem | Persistent key-value storage with atomic CAS and increment. Private by default; public when you choose, enabling cross-agent composability. |
| `outlayer:wallet` | Hardware security module | MPC-derived keys that never leave the enclave. Transfer, swap, withdraw, all governed by a policy engine with spending limits and multisig. |
| `near:vrf` | /dev/urandom (verifiable) | Ed25519 VRF with on-chain proof. Anti-grinding via blockchain-assigned request IDs. |
| `near:payment` | Native monetization | Stablecoin payments per execution. Refund API for partial returns. Developers earn per call. |

## Process model

**Stateless by Design. Composable by Default.**

Agents follow the Erlang model: start, work, write state, terminate. No crashes, no leaks, no
zombies. State is explicit, not in RAM.

- **Cross-agent composability.** Any agent can publish to public storage, and any other agent can
  read it. An oracle publishes prices; a trading bot reads them; a risk agent monitors both. Like
  Unix pipes, but for agents.
