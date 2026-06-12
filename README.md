# quantum-cryptography-protocols

> **GALACTIC-UNION** · Quantum Cryptography Protocol Suite

[![CI](https://github.com/GALACTIC-UNION/quantum-cryptography-protocols/actions/workflows/ci.yml/badge.svg)](https://github.com/GALACTIC-UNION/quantum-cryptography-protocols/actions/workflows/ci.yml)
[![Python 3.11+](https://img.shields.io/badge/python-3.11%2B-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## Overview

`quantum-cryptography-protocols` (QCP) is a comprehensive library implementing quantum cryptographic protocols and post-quantum classical algorithms. It serves as the cryptographic backbone for the GALACTIC-UNION OCN, providing quantum key distribution, quantum digital signatures, quantum secret sharing, and NIST-standardized post-quantum primitives.

### Design Principles

- **Correctness first** — every protocol is traceable to its academic specification with exact parameter references.
- **Composability** — protocols are small, stateless units that compose into larger cryptographic systems.
- **Testability** — 100% of public APIs have corresponding property-based tests using `hypothesis`.
- **Pluggable backends** — swap between simulation (NumPy), hardware-emulation (Qiskit), or real QKD hardware via a provider interface.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Application Interface                     │
│          Key Manager · Certificate Authority · TLS Adapter  │
├────────────────────┬────────────────────────────────────────┤
│   QKD Protocols    │     Post-Quantum Primitives            │
│  BB84 · E91 · COW  │   CRYSTALS-Kyber · CRYSTALS-Dilithium │
│  MDI-QKD · TF-QKD  │   FALCON · SPHINCS+ · BIKE · HQC     │
├────────────────────┴────────────────────────────────────────┤
│                  Quantum Signature & Auth                    │
│         QDS · QSSS · Quantum Commitment Schemes             │
├─────────────────────────────────────────────────────────────┤
│                   Classical Primitives                       │
│      Information Reconciliation · Privacy Amplification     │
│      Toeplitz Hashing · Cascade Error Correction            │
├─────────────────────────────────────────────────────────────┤
│                    Provider Backends                         │
│        Simulation (NumPy) · Qiskit · Hardware QKD           │
└─────────────────────────────────────────────────────────────┘
```

---

## Directory Structure

```
quantum-cryptography-protocols/
├── src/
│   └── qcp/
│       ├── qkd/              # QKD protocol implementations
│       │   ├── bb84.py       # Bennett-Brassard 1984
│       │   ├── e91.py        # Ekert 1991 (entanglement-based)
│       │   ├── mdi_qkd.py    # Measurement-device-independent QKD
│       │   └── tf_qkd.py     # Twin-field QKD
│       ├── post_quantum/     # NIST PQC finalists & standards
│       │   ├── kyber.py      # CRYSTALS-Kyber (FIPS 203)
│       │   ├── dilithium.py  # CRYSTALS-Dilithium (FIPS 204)
│       │   ├── falcon.py     # FALCON (FIPS 206)
│       │   └── sphincs.py    # SPHINCS+ (FIPS 205)
│       ├── signatures/       # Quantum digital signatures
│       ├── secret_sharing/   # Quantum secret sharing schemes
│       ├── reconciliation/   # Cascade, Winnow error correction
│       ├── amplification/    # Privacy amplification (Toeplitz)
│       ├── providers/        # Backend provider interface & adapters
│       └── utils/            # Entropy estimation, RNG, helpers
├── docs/
│   ├── protocols/            # Per-protocol specification docs
│   ├── security-proofs/      # Security reduction summaries
│   ├── api/                  # Auto-generated API reference
│   └── tutorials/            # Integration guides
├── tests/
│   ├── unit/                 # Per-module unit tests
│   ├── property/             # Hypothesis property-based tests
│   ├── integration/          # End-to-end protocol runs
│   └── vectors/              # Known-answer test (KAT) vectors
├── config/
│   ├── default.yaml          # Default algorithm selections
│   ├── security_levels.yaml  # NIST security level mappings
│   └── logging.yaml          # Logging configuration
├── .github/
│   └── workflows/
│       └── ci.yml
├── CONTRIBUTING.md
├── LICENSE
└── pyproject.toml
```

---

## Modules & API Surface

### QKD — `qcp.qkd`

```python
from qcp.qkd import BB84, E91, MDIQKDSession

# BB84 — prepare-and-measure
alice = BB84.Alice(key_length=256)
bob   = BB84.Bob()
key   = BB84.run(alice, bob, channel=sim_channel)
print(f"QBER: {BB84.qber:.4f} | Key rate: {BB84.sifted_key_rate:.2f} bps")

# E91 — entanglement-based
session = E91.Session(source=entanglement_source)
key     = session.run(key_length=256)
bell    = session.bell_inequality_value  # Should violate CHSH ≥ 2√2 ≈ 2.828
```

### Post-Quantum — `qcp.post_quantum`

```python
from qcp.post_quantum import Kyber, Dilithium

# CRYSTALS-Kyber (FIPS 203) — KEM
pk, sk         = Kyber.keygen(level=3)   # Kyber-768
ciphertext, ss = Kyber.encapsulate(pk)
ss_recovered   = Kyber.decapsulate(sk, ciphertext)
assert ss == ss_recovered

# CRYSTALS-Dilithium (FIPS 204) — Digital signature
pk, sk  = Dilithium.keygen(level=3)     # Dilithium3
sig     = Dilithium.sign(sk, message=b"OCN authenticated message")
valid   = Dilithium.verify(pk, message=b"OCN authenticated message", sig=sig)
```

### Quantum Digital Signatures — `qcp.signatures`

```python
from qcp.signatures import QuantumDigitalSignature

qds = QuantumDigitalSignature(security_parameter=128)
pk, sk  = qds.keygen()
sig     = qds.sign(sk, message)
valid   = qds.verify(pk, message, sig)  # Unconditionally secure
```

### Provider Interface — `qcp.providers`

```python
from qcp.providers import QKDProvider, SimulatedProvider, QiskitProvider

# Swap backend without changing protocol code
provider = SimulatedProvider(noise_model="depolarizing", error_rate=0.01)
# or
provider = QiskitProvider(backend="ibm_kyiv")

BB84.set_provider(provider)
```

### REST API

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/v1/qkd/sessions` | Start a new QKD session |
| `GET` | `/v1/qkd/sessions/{id}` | Session status & derived key |
| `POST` | `/v1/pqc/keygen` | Generate post-quantum keypair |
| `POST` | `/v1/pqc/sign` | Sign a message |
| `POST` | `/v1/pqc/verify` | Verify a signature |
| `GET` | `/v1/entropy` | Quantum entropy source status |

---

## Installation

```bash
git clone https://github.com/GALACTIC-UNION/quantum-cryptography-protocols.git
cd quantum-cryptography-protocols
pip install -e ".[dev]"
```

**Optional extras:**
- `pip install -e ".[qiskit]"` — Qiskit simulator/hardware backend
- `pip install -e ".[hardware]"` — vendor QKD hardware adapters

---

## Security

- QKD protocols provide **information-theoretic security** conditional on device assumptions.
- Post-quantum primitives are **NIST-standardized** (FIPS 203/204/205/206) — resistant to Grover and Shor attacks.
- All randomness is sourced from `os.urandom()` or a configured QRNG provider.
- Report vulnerabilities via the [Security Policy](.github/SECURITY.md).

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT — see [LICENSE](LICENSE).
