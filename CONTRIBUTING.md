# Contributing to quantum-cryptography-protocols

Thank you for contributing to the GALACTIC-UNION quantum-cryptography-protocols library!

---

## Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [Getting Started](#getting-started)
3. [Development Workflow](#development-workflow)
4. [Coding Standards](#coding-standards)
5. [Testing](#testing)
6. [Security Contributions](#security-contributions)
7. [Pull Request Process](#pull-request-process)

---

## Code of Conduct

This project follows the [Contributor Covenant v2.1](https://www.contributor-covenant.org/version/2/1/code_of_conduct/).

---

## Getting Started

```bash
git clone https://github.com/<your-fork>/quantum-cryptography-protocols.git
cd quantum-cryptography-protocols
python -m venv .venv && source .venv/bin/activate
pip install -e ".[dev]"
pre-commit install
```

---

## Development Workflow

1. **Branch naming**
   - `feature/<description>` · `fix/<description>` · `docs/<description>` · `security/<description>`

2. **Commit messages** — [Conventional Commits](https://www.conventionalcommits.org/):
   ```
   feat(bb84): add decoy-state extension for loss channel
   fix(kyber): correct endianness in NTT polynomial encoding
   docs(e91): clarify CHSH inequality measurement basis
   ```

3. **Reference the specification** — every protocol implementation must cite the academic paper in module docstring and link to the known-answer test (KAT) vectors used.

---

## Coding Standards

- **Python 3.11+** with full type annotations.
- **black** (line length 100) · **ruff** · **mypy --strict**.
- **NumPy-style docstrings** with `Parameters`, `Returns`, `References` sections.
- Constant-time implementations where required for side-channel resistance (use `hmac.compare_digest`, avoid branch-on-secret).

```bash
black src/ tests/
ruff check src/ tests/ --fix
mypy src/qcp
```

---

## Testing

```bash
# All tests
pytest tests/ -v

# Property-based tests (hypothesis)
pytest tests/property/ -v --hypothesis-seed=0

# Known-answer test vectors
pytest tests/vectors/ -v

# Coverage
pytest tests/ --cov=qcp --cov-report=term-missing
```

**Requirements:**
- New protocols → unit tests + KAT vectors + property-based tests.
- Bug fixes → regression test demonstrating the fix.
- Target: **≥ 95% coverage** for `src/qcp/`.

---

## Security Contributions

- **Do NOT** open public Issues for security vulnerabilities.
- Report privately via email or via the [GitHub Security Advisory](https://github.com/GALACTIC-UNION/quantum-cryptography-protocols/security/advisories/new) workflow.
- Include: affected module, reproduction steps, impact assessment, and proposed fix if available.
- We aim to acknowledge within 48 hours and patch within 14 days.

---

## Pull Request Process

1. All CI checks must pass.
2. At least one maintainer review required.
3. New protocols require two reviewers with domain expertise.
4. Update `CHANGELOG.md` under `[Unreleased]`.
5. Squash-merge to keep history clean.
