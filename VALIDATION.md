# Manuscript and artifact validation — 8 November 2026

## Workshop fit and format

Target: [EnCyCriS 2027 at ICSE](https://conf.researchr.org/home/icse-2027/encycris-2027),
a short position paper with executable evidence. The official call permits
short papers up to five pages including references and requires IEEE conference
format. The revised main paper is four pages and uses unmodified IEEEtran
`[10pt,conference]`, without margin, body-font, or line-spacing overrides.

The work addresses security-engineering requirements, recovery, and validation
of migration tooling. Its critical-systems acceptance case is explicitly
illustrative; it supplies no hospital deployment or industrial evaluation.
This is a defensible topical fit, not a guarantee of acceptance or eligibility.
The public call checked for this revision does not specify an anonymous-paper
requirement or a particular artifact upload mechanism. The identified paper
preserves both authors and their exact email spellings. The repository remains
private; its link is not a substitute for permitted reviewer artifact access.

## Review-response mapping

| Review request | Implemented change and evidence |
|---|---|
| Challenge serialization assumption | Added two real workers, four IPC-controlled schedules, complete scans with/without a shared local lock, actual restores, 48 raw cases, and complete traces. Main Section III-C / Table IV; supplement Sections 5–6. |
| Narrow novelty; compare verified sources | Replaced contribution paragraph with executable predicates and focused evidence. Table I compares guidance, rotation documentation, and crash-consistency research without inferring missing product features. |
| Align post-quantum framing | New title: *Recovery-Set Contracts for Cryptographic Storage Migration*. Abstract starts with the algorithm-independent lifecycle scope; no token KEM implementation. |
| Concrete critical-systems acceptance case | Table V separates required deployment evidence from what the artifact demonstrates. The hospital example remains explicitly illustrative. |
| Pending work, release, and safety | Section II distinguishes pending targets from retained references, authorized release from migration, and preservation from eventual completion. |
| Presentation and interpretation | Figure 1 shows both retirement checks and the persistent snapshot key. Tables II–IV visibly qualify counts, overlapping columns where applicable, and absence of probability estimates. |
| Reviewer-ready artifact | Separate commands; raw-file/table map; exact manifests; nine tests; result verifier; expected totals and failure conditions; explicit plaintext-key fixture and access limits. |

## Executed verification

The complete run used:

```bash
bash run_experiments.sh
```

The recorded console output is `results/execution_log.txt`. After adding the
recorded-results verifier to the runner, that verifier was executed separately:

```bash
python3 experiment/verify_results.py --results results
```

Its successful output is `results/verification_log.txt`. No experiment source
was changed after the definitive run.

- All nine regression tests passed: six existing model tests and three new
  concurrency tests covering stale scans, registered pins, and protected schedules.
- Eighteen bounded model configurations reached a fixed point. Across all six
  guarded configurations, 1,890 states and 7,232 transitions contain no missing key.
- All 126 interruption cases completed; 108 terminated the child with exit 93.
  The guarded policy restored both copies in 42/42 cases; weak controls produced
  live/backup failure counts 12/30 and 0/12. Columns overlap.
- All 48 concurrent cases completed with both workers exiting normally and no
  dangling pins. Unlocked cases failed live restoration in 12/24; all protected
  cases restored both copies (24/24). All 48 snapshots restored successfully.
- Protected writers were rejected in 18 cases and committed in six. Writer lock
  contention occurred in 12 cases; retirement lock contention occurred in six.
- The seven pre-existing result/manifest files reproduced byte-for-byte against
  the earlier independent output directory in the same runtime environment.
- Concurrency CSV, summary JSON, and full trace JSON reproduced byte-for-byte
  between the initial and definitive run. The run-completion timestamp changes.
- Recorded source SHA-256 hashes match source files. CSV/JSON summaries and
  traces agree, and the published totals pass the recorded-results verifier.

The nine tests include actual process interactions; no new test merely checks
paper text. Ciphertext/key bytes are randomized and not archived. Stored traces
record commands, inventories, lock ownership, cached checks, and actual restores.
Same-environment repetition is not cross-platform or independent validation.

### What the new experiment establishes

A complete dependency scan can become stale if admission/publication overlaps
retirement. Both implementations inspect the same live, snapshot, and registered
pending references. The unlocked counterexample does not hide a known pin.
The shared lock covers the full scan/delete interval and the writer's availability
check, pin registration, preparation, SQL publication, and pin removal. When
retirement wins, the writer retries after deletion and safely rejects the
unavailable target. When the writer wins, retirement observes its committed
reference and retains the key. This is an observed local mechanism for the
selected schedules, not a proof of all concurrent executions.

The concurrency harness extends the original forward-only model with admission
of a still-available inactive generation, representing delayed work or import.
The original model chooses only the active generation. A current-only admission
policy could reject stale choices instead. This distinction is stated in the
main paper, supplement, README, source, and runtime manifest. It avoids claiming
an implementation/refinement proof of the closed model.

Concurrent pins are durable SQLite rows; abstract pending work is volatile.
Worker crashes with abandoned pins, distributed coordination, uncooperative
writers, concurrent activation, and snapshot changes are not tested. Snapshot
and active-generation state are fixed in these trials. Enabling changes would
require extending the synchronization protocol. The lock uses one stable inode.

### Claim boundaries retained

- Key-reference availability is not complete recovery correctness or profile
  completion. Decoder support, authorization, retention policy, and restore
  implementation remain deployment obligations.
- Snapshot release is authorized retention expiry, not migration or secure erasure.
- The guard supplies safety and can block retirement indefinitely.
- Local JSON keys are plaintext test fixtures, not a secure KMS. Inventory entry
  removal does not demonstrate physical erasure.
- No ML-KEM/hybrid KEM, QRNG, hospital deployment, remote KMS, power-loss testing,
  performance/scalability result, cryptographic proof, or failure probability.
- AI assistance is acknowledged with affected text, tables/diagram, model, and
  code, following [IEEE guidance](https://conferences.ieeeauthorcenter.ieee.org/author-ethics/guidelines-and-policies/submission-policies/).

## Reference audit

All ten items are cited in the manuscript. Metadata and the corresponding
claims were checked against publisher, standards-body, author-institution,
or official product documentation. No invented DOI or secondary summary was
used as a bibliographic authority.

| No. | Verified reference and primary source | Supported use / correction |
|---|---|---|
| 1 | Chandramouli and Pinhas, *Security Guidelines for Storage Infrastructure*, NIST SP 800-209, October 2020, [DOI](https://doi.org/10.6028/NIST.SP.800-209) | Added. Section 4.8, RA-SS-R1/R2, addresses recovery copies, encryption keys, and dependent key-management systems. |
| 2 | NIST, *Module-Lattice-Based Key-Encapsulation Mechanism Standard*, FIPS 203, August 2024, [official record](https://csrc.nist.gov/pubs/fips/203/final) | ML-KEM provides a post-quantum key-establishment primitive. Not cited as evidence that this artifact implements it. |
| 3 | Barker et al., *Considerations for Achieving Crypto Agility: Strategies and Practices*, NIST CSWP 39upd1, [official record](https://csrc.nist.gov/pubs/cswp/39/upd1/considerations-for-achieving-crypto-agility/final) | Operational migration motivation. Corrected metadata to December 2025, updated June 2026, matching the publisher's publication-date wording. DOI `10.6028/NIST.CSWP.39-upd1`. |
| 4 | Barker, *Recommendation for Key Management: Part 1—General*, NIST SP 800-57 Part 1 Rev. 5, May 2020, [official record](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final) | Backup/recovery, destruction, and inventories: Sections 8.2.2, 8.3.1, 8.3.4, and 9.2. DOI `10.6028/NIST.SP.800-57pt1r5`. |
| 5 | AWS, [*Rotate AWS KMS keys*](https://docs.aws.amazon.com/kms/latest/developerguide/rotate-keys.html), accessed 7 November 2026 | Retaining earlier AWS-generated key material for decryption. Imported material has different deletion/expiry behavior; the paper's example is now qualified. |
| 6 | MongoDB, [*Rotate and Rewrap Encryption Keys*](https://www.mongodb.com/docs/manual/core/queryable-encryption/fundamentals/manage-keys/), accessed 7 November 2026 | DEK rewrapping and the warning against deleting still-needed keys. Not presented as an evaluated baseline. |
| 7 | Pillai, Chidambaram, Alagappan, Al-Kiswany, A. C. Arpaci-Dusseau, and R. H. Arpaci-Dusseau, [*All File Systems Are Not Created Equal: On the Complexity of Crafting Crash-Consistent Applications*](https://www.usenix.org/conference/osdi14/technical-sessions/presentation/pillai), OSDI 2014, pp. 433–448 | Persistence-ordering and filesystem assumptions. Added all six authors and the page range from the USENIX BibTeX record. No DOI was invented. |
| 8 | Reardon, Basin, and Čapkun, *SoK: Secure Data Deletion*, IEEE S&P 2013, pp. 301–315, [ETH record](https://www.research-collection.ethz.ch/items/de378883-51d3-4076-bd02-8c232ec896c6) | Added to distinguish removing an availability/retention obligation from secure erasure. Verified DOI `10.1109/SP.2013.28` and pages against the institutional record. |
| 9 | SQLite, [*Atomic Commit in SQLite*](https://sqlite.org/atomiccommit.html), accessed 7 November 2026 | SQL transaction atomicity and rollback. The separate JSON key file being outside that transaction is an observation about this harness's code. |
| 10 | Alagic et al., *Recommendations for Key-Encapsulation Mechanisms*, NIST SP 800-227, September 2025, [official record](https://csrc.nist.gov/pubs/sp/800/227/final) | Standards-based KEM implementation guidance for future integration. DOI `10.6028/NIST.SP.800-227`. |

## Document and package checks

The main PDF has four pages; the supplement has six. Every page was rendered
and visually inspected for clipped text, overlaps, diagram/table readability,
and references. All fonts are embedded Type 1 fonts. All ten bibliography
entries are cited, and citation/cross-reference labels resolve. Both PDFs have
a clickable private-repository URL. Author names and requested email spellings
are preserved. The previous project title and publication-status administration
are absent from the manuscript and supplement.

The ZIP includes current PDFs, both LaTeX sources, unchanged IEEEtran class,
code, tests, all raw observations, manifests, console logs, and reproduction
instructions. `SHA256SUMS` covers packaged files except itself. Administrative
notes, earlier drafts/results, temporary databases, secrets, caches, and TeX
build intermediates are excluded. A checksum verification precedes packaging.

The manuscript's four-page layout is within the short-paper page limit; the
separate six-page supplement is additional artifact material and must use a
mechanism the venue permits. No conference submission or reviewer access grant
is made by this revision. This report records checks, not a certification of
acceptance, cryptographic security, or publication eligibility.
