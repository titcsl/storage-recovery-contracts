# Recovery Contracts for Post-Quantum Storage Migration

**Ishan Teredesai and Tushar Rahangdale**

New research artifact: a bounded state model and a synthetic SQLite/AES-GCM
process-crash experiment for key retirement and retained-backup recovery.

## Documents and full artifact

- [Manuscript draft](EnCyCriS_2027_Recovery_Contracts_Draft.pdf)
- [Supplementary reproduction guide](EnCyCriS_2027_Recovery_Contracts_Supplement.pdf)
- [Complete artifact: LaTeX sources, code, raw outcomes and hashes](Recovery_Contracts_Artifact.zip)

The standalone Python files below are identical to the experiment sources in
the archive. The archive also contains all recorded results and the full README.

## Run after cloning

Use Python 3.10+ on Linux (or Ubuntu in WSL):

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
bash run_experiments.sh
```

The model alone requires only the Python standard library:

```bash
python3 model.py --out results
```

The runner works in this flat repository and in the archive's folder layout.

## Executed results

18 bounded model configurations; 126 synthetic harness cases, including 108
worker-process terminations; six validation tests pass. Each policy has 42
harness cases.

| Retirement rule | Live failure cases | Backup failure cases | Both copies restored |
|---|---:|---:|---:|
| Intent-only | 12 | 30 | 12 |
| Live-only | 0 | 12 | 30 |
| Recovery-set | 0 | 0 | 42 |

The failure columns can overlap. These are deterministic case outcomes, not
failure probabilities or production reliability estimates.

## Limits and provenance

The harness uses AES-256-GCM and SQLite; it does not implement ML-KEM, a hybrid
KEM, a QRNG, or a production KMS. The model assumes complete dependency
information and serialized transitions. The experiment tests process termination,
not power loss. The two weaker policies are deliberately constructed controls,
not reported vulnerabilities in existing services.

OpenAI ChatGPT assisted with model design, code generation and drafting; the
paper discloses that assistance. Authors must review and approve all claims.

This is an author-review draft. A related four-author middleware manuscript has
an unresolved publication/withdrawal status. That relationship is documented in
a separate confidential note for the authors/chairs. This new title and artifact
do not certify submission eligibility or absence of overlap with future papers.
The repository was initially created private; reviewers can use the complete
supplementary archive without repository access.
