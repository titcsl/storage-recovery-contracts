# Recovery-Set Contracts for Cryptographic Storage Migration

Artifact for an EnCyCriS 2027 short position paper with executable evidence.
**Authors:** Ishan Teredesai and Tushar Rahangdale. The email spelling
`tusharrahangadle@gmail.com` is intentional.

Private repository: https://github.com/titcsl/storage-recovery-contracts

Its URL alone does not grant reviewer access. The complete ZIP contains the
current paper, supplement, LaTeX package, code, results, traces, and environment
manifests for transfer through the venue's permitted artifact mechanism. No
public access or reviewer invitation is claimed. The main paper is self-contained.

## Current deliverables

- [Current paper PDF](EnCyCriS_2027_Recovery_Contracts.pdf)
- [Supplementary protocol and reproduction guide](EnCyCriS_2027_Recovery_Contracts_Supplement.pdf)
- [Complete ZIP: LaTeX package, current PDFs, source, tests, raw results and traces](Recovery_Contracts_Artifact.zip)

Updated 8 November 2026 with the executed two-worker serialization experiment.
The older `Draft.pdf` filename is retained as an identical compatibility copy;
use the current-paper link above. Extract the ZIP for the complete documented
file layout, checksums, and evidence. Root-level Python files also support the
one-command runner. Document sources are additionally available at the root.

## Implemented scope

- Finite model distinguishing committed live references, retained snapshots,
  and volatile prepared work; separate reference-availability and profile predicates.
- Exhaustive exploration to a fixed point within 18 finite configurations.
- SQLite/AES-256-GCM interruption harness: six termination cuts plus completion,
  six datasets, and three policies, giving 126 cases and 108 process terminations.
- Two-worker concurrency harness: complete recovery-set checks, with or without
  a cooperative shared lock, over four controlled schedules and six datasets.
  Actual inventories and decrypted plaintext determine its 48 outcomes; no
  expected-result function generates or filters the observations.
- Nine regression tests and a separate recorded-result consistency verifier.

Post-quantum migration motivates this algorithm-independent lifecycle study.
The artifact implements neither ML-KEM nor a hybrid KEM. It demonstrates no
QRNG, remote KMS, hospital deployment, cryptographic bound, or performance gain.

## Observed results

| Rule | Model states (3 records, 3 generations) | States missing live / backup keys | Interruption live / backup failure cases |
|---|---:|---:|---:|
| Intent-only | 3,682 | 1,814 / 1,766 | 12 / 30 |
| Live-only | 1,996 | 0 / 403 | 0 / 12 |
| Recovery-set | 1,495 | 0 / 0 | 0 / 0 |

There are 42 interruption cases per policy. Failure columns overlap; counts
are deterministic coverage outcomes, not failure probabilities. All 42 guarded
cases restore both copies. Its six bounded model configurations total 1,890
states and 7,232 transitions, with zero missing-key states.

| Requested concurrent schedule | Unlocked live / backup failures | Locked live / backup failures | Locked writer outcome |
|---|---:|---:|---|
| Commit in scan/delete gap | 6 / 0 | 0 / 0 | 6 rejected |
| Prepare in scan/delete gap | 6 / 0 | 0 / 0 | 6 rejected |
| Writer prepares first | 0 / 0 | 0 / 0 | 6 committed |
| Retirement finishes first | 0 / 0 | 0 / 0 | 6 rejected |

Each cell covers six datasets. All 24 protected cases restore both copies;
12 of 24 unlocked cases fail live restoration. A blocked action retries after
the lock holder finishes, preventing the unsafe requested order. This is
selected-schedule evidence, not exhaustive concurrency verification or reliability.

## Reproduction commands

Use Python 3.10+ on Linux (Windows: WSL/Ubuntu). Linux is the reproduction target
for `fcntl.flock` and directory `fsync`. Unzip and run from `recovery-contracts/`:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

One command for model exploration (standard library only):

```bash
python3 experiment/model.py --out results
```

One command for the interruption/crash harness:

```bash
python3 experiment/crash_harness.py --out results
```

One command for controlled concurrent schedules:

```bash
python3 experiment/concurrency_harness.py --out results
```

Regression tests and verification of recorded results:

```bash
python3 -m unittest discover -s experiment -p 'test_*.py' -v
python3 experiment/verify_results.py --results results
```

Or run that entire sequence with one command:

```bash
bash run_experiments.sh
```

The runner also supports the repository's flat Python-file layout. For individual
commands there, omit `experiment/` and use `-s .` for unittest. The complete ZIP
uses the directory layout above and includes all recorded evidence.

Commands replace files in `results/`. Preserve the delivered directory or run
in a separate copy. A failed command does not make leftover files a successful
run. Temporary databases, lock files, and synthetic encryption keys are discarded
after each case. Ciphertext/key bytes are not archived; outcomes and traces are.

### Totals and explicit failure conditions

The executed reference run produced nine passing tests, 18 model configurations,
126 interruption cases (108 terminations), and 48 concurrent cases. Expected
negative-control failures are those observed above. The verifier checks these
published totals after results are generated, as well as source hashes and
agreement among raw outcomes and summaries.

A timeout, failed worker, unexpected exit, authentication error, wrong record
count, plaintext mismatch, dangling pin, or inventory/restore disagreement aborts
a run. A protected missing-key state, source-hash mismatch, changed published
totals, or inconsistent result files fails validation. Negative-control failures
must not be silently converted into successful restores.

### Exact captured environment

| Component | Recorded value |
|---|---|
| Python | `3.12.14 (main, Aug 25 2026, 14:00:49) [Clang 22.1.3 ]` |
| Platform | `Linux-6.18.57-x86_64-with-glibc2.39` |
| SQLite / cryptography | `3.53.1` / `50.0.2` |
| Concurrent run completed | `2026-11-08T06:49:15.591912+00:00` |
| Scheduling | Parent IPC gates, two spawned workers, nonblocking local `flock` |

`results/*manifest.json` preserves exact runtime strings, source hashes, bounds,
and assumptions. SQLite is supplied by Python; its version may differ elsewhere.
No timing claim is made.

## Exact synchronization scope

Both implementations inspect live references, the snapshot, and registered
pending targets. The unlocked implementation caches eligibility before deletion.
Both protected workers share one stable local lock-file inode:

- Retirement holds the lock from the full scan through deletion or retention.
- The writer holds it through target-key availability checking, durable SQLite
  pin registration, preparation, publication, and pin removal. Publication and
  pin removal share one SQL transaction.

Locking deletion alone is insufficient. Active generation and snapshot contents
are fixed during these trials; enabling changes requires including them in the
synchronization protocol. The parent schedules requests and observes/restores;
it does not hold the lifecycle lock.

The concurrent experiment **extends** the finite model by admitting a request
with an earlier generation choice while that key is available. The finite
model's prepare operation selects only the active generation. A current-only
system could reject stale choices instead. Concurrent pins are durable SQLite
rows; model pending work is volatile. No concurrent worker crash is injected,
so abandoned-pin recovery is not demonstrated. This is not a refinement proof
of the closed model, a distributed protocol, or exhaustive interleaving coverage.

## File-to-paper map

All abbreviated result filenames below are inside `results/`.

| File | Evidence / purpose |
|---|---|
| `paper/paper.tex`, `paper/paper.pdf` | Current four-page paper with repository URL |
| `paper/supplement.tex`, `paper/supplement.pdf` | Six-page protocol, tables, traces, and reproduction guide |
| `experiment/model.py`, `test_model.py` | Model and six original tests (both under `experiment/`) |
| `experiment/crash_harness.py` | Process termination and actual restoration |
| `experiment/concurrency_harness.py`, `test_concurrency.py` | Two workers and three new tests (both under `experiment/`) |
| `experiment/verify_results.py` | Checks recorded files, hashes, and published totals |
| `model_summary.csv`, `model_results.json` | Table II and all 18 model configurations |
| `model_traces.json` | Shortest missing-key and profile-gap witnesses |
| `crash_trials.csv`, `crash_summary.json` | Table III; 126 cases and policy aggregates |
| `concurrency_trials.csv`, `concurrency_summary.json` | Table IV; 48 cases and schedule aggregates |
| `concurrency_traces.json` | Commands, observations, inventories, lock status, and restores |
| `*manifest.json`, `execution_log.txt`, `verification_log.txt` | Runtime/source provenance and successful-run console output |
| `VALIDATION.md` | Review-response mapping, validation, and ten-reference audit |
| `SHA256SUMS` | Integrity checks for packaged sources and outputs |

Tables I and V compare verified sources and state illustrative acceptance
obligations; they are not measured outcomes. Figure 1 explains the interruption
sequence and persistent snapshot dependency.

## Build and integrity checks

```bash
sha256sum --check SHA256SUMS
cd paper
latexmk -pdf -interaction=nonstopmode -halt-on-error paper.tex
latexmk -pdf -interaction=nonstopmode -halt-on-error supplement.tex
```

Use TeX Live with TikZ, Latin Modern, and `latexmk`. `paper/IEEEtran.cls` is
unchanged upstream; see `THIRD_PARTY_NOTICES.md`. Check integrity before rerunning
or rebuilding: new manifest timestamps and PDF metadata can change hashes.

## Limits and provenance

Reference availability is only one recovery condition; decoder compatibility,
authorization, and restore capability need separate deployment evidence. Pending
work is not a retained recovery reference, but the guard protects its target
until commit. Snapshot release is an authorized retention decision, not migration.
Safety can preserve a legacy copy while blocking retirement indefinitely.

The local JSON file contains **unprotected plaintext test keys**. It is a test
fixture, not a secure KMS, and removing entries does not establish physical
erasure. Locks require local cooperation. The study excludes distributed
transactions, malicious writers, concurrent snapshot changes, production scale,
power loss, and cryptographic security proofs. Terminating a process does not
stop the kernel. Weak policies are constructed controls, not product defects.

OpenAI ChatGPT assisted with the model, code, writing, diagram, and reference
checking, as acknowledged in the paper. Results came from executed programs.
Authors must review and adopt the work; these checks do not certify acceptance
or production security.
