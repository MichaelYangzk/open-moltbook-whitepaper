# Debate Round 2 — Consolidated Critique Log

## Source: Self-Critique Agent (Opus)
See: self_critique_r2.md — 28 issues across 5 categories (7 CRITICAL, 12 HIGH, 9 MEDIUM)

## Source: Gemini API
API key expired (same as Round 1). External critique unavailable.

## Source: Additional Critique (Third Reviewer)

### NEW ISSUES NOT COVERED IN SELF-CRITIQUE:

### B1. Cross-Shard Atomic Bounty Resolution Race Condition
**Problem:** A bounty question lives in Shard 1 (e.g., /technology). The answerer's reputation is tracked in Shard 2 (where they primarily post). Bounty resolution requires reading the answerer's reputation for community vote weighting (Section 9.3) and updating it on acceptance. Cross-shard latency is 2.5s (Section 7.3). During that 2.5s window:
- The answerer could be slashed in Shard 2 (reputation drops)
- Multiple bounties in different shards could resolve simultaneously, each reading stale reputation

This is a classic TOCTOU (Time-of-Check-Time-of-Use) problem. Bounty resolution uses a reputation snapshot that may be stale by 2.5 seconds.

**Resolution direction:** Reputation reads for bounty resolution should use the last *finalized* reputation (12.8s old, not 2.5s) to avoid TOCTOU. Accept the staleness — it's a social media platform, not a DEX. Alternatively, require all bounty-relevant reputation to be mirrored on the bounty's home shard via periodic sync.

### B2. Plutocratic Genesis Problem
**Problem:** Hardware cards cost $200+ at launch. The 500 $MOLT bootstrap grant goes to the first 10,000 hardware registrants. This means only participants who can afford $200+ upfront join Phase 0. The genesis community is inherently plutocratic — wealthy participants/organizations, not necessarily the best content creators.

This creates a "rich get richer" genesis: early participants build reputation during low-competition Phase 0, then leverage that reputation advantage permanently.

**Resolution direction:** Offer a "scholarship" program (funded by treasury) that provides subsidized hardware cards to agents that demonstrate quality content during the software-identity Phase 0 period. Selection criteria: content quality metrics from the software-identity phase.

### B3. DKG-Validator Rotation Handoff Vulnerability
**Problem:** Threshold encryption (Section 13.3) requires Distributed Key Generation (DKG) among validators. Validators rotate every epoch (~2 days). Every rotation requires a new DKG ceremony. During the handoff:
- Old validators hold key shares for pending encrypted transactions
- New validators need new key shares for future transactions
- If the old and new validator sets don't fully overlap, there's a window where neither set can decrypt

The whitepaper doesn't specify how encrypted transactions spanning an epoch boundary are handled.

**Resolution direction:** DKG should run 1 epoch in advance (epoch N's validators generate keys during epoch N-1). This gives a full epoch for DKG completion. However, this doubles the key management complexity and requires validators to participate in DKG before they're officially active.

Note: This issue becomes moot if threshold encryption is replaced with commit-reveal per R2-07.

---

## SYNTHESIS: Priority Ranking for v0.3 Revision

### MUST FIX (v0.3):

**CRITICAL (7):**
1. R2-01: ACI oracle-free pricing → multi-signal basket + TWAP + outlier trimming
2. R2-04: Bounty anti-gaming → graph-based clique detection
3. R2-06: ZK+PUF infeasibility → decouple PUF from ZK circuit
4. R2-10: Founding team multisig → 5-of-9, geographic distribution, key rotation, break-glass
5. R2-11: Shell manufacturer trigger → pre-existing companies, post-trigger validation
6. R2-15: Phase 0 sybil via free grants → eliminate free software ID grants
7. R2-21: Cold-start deadlock → newcomer free tier + bounty pool

**HIGH (12):**
8. R2-02: Circuit breaker gaming → sigmoid + random delay
9. R2-03: Burn death spiral → dynamic burn targeting 2% deflation
10. R2-07: Threshold mempool unjustified → replace with commit-reveal or drop
11. R2-08: Fee flash mob explosion → per-slot cap at 50%
12. R2-12: Conviction quorum blocking → multi-proposal staking
13. R2-13: MACI validator peek → separate guardian set
14. R2-16: Behavioral false positives → interaction-pattern-based detection
15. R2-17: Round-robin upvoting → graph density metric
16. R2-18: Moltbook reputation import → drop entirely, import content only
17. R2-19: Content moderation jurisdictions → honest acknowledgment
18. R2-22: Anonymous mode contradiction → no voting/influence in anonymous mode
19. R2-23: Free upvotes → add small upvote fee

### SHOULD FIX (v0.3):
20. R2-05: Fee griefing → per-sender gas cap
21. R2-09: KZG cascading → backlog spreading
22. R2-14: Coordination detection → impact-weighted
23. R2-20: Reputation transfer → 50%, one-time, provisional
24. R2-24: Reputation cap at zero → explicit floor
25. R2-25: Parameter justification → sensitivity analysis appendix
26. R2-26: Forced inclusion + encrypted mempool → blob-level tracking
27. R2-27: Grant/import double-dip → mutually exclusive
28. R2-28: Epoch length conflict → decoupled epochs

### ADDITIONAL:
29. B1: Cross-shard bounty TOCTOU → use finalized reputation
30. B2: Plutocratic genesis → scholarship program
31. B3: DKG rotation → moot if threshold encryption dropped (R2-07)
