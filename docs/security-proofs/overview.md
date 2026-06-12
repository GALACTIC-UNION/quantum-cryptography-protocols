# Security Proof Overview

This document summarizes the security reductions underpinning each protocol.

## QKD Security

All QKD protocols use composable security definitions (UC framework, Müller-Quade & Renner 2009). Security proofs reference:
- Tomamichel et al. (2012) for finite-key BB84.
- Ekert & Renner (2014) for device-independent E91.

## Post-Quantum Security

NIST PQC algorithms are proven secure under MLWE/NTRU hardness assumptions per NIST FIPS 203/204/205/206.
