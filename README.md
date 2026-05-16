# tfht_enforce_idx_state

Persistent operational state for the [tfht_enforce_idx](https://github.com/DataHackIL/tfht_enforce_idx) pipeline. Committed by GitHub Actions workflows after each run.

## Directory layout

```
news_items/
  discover/
    candidates/
      latest_candidates.jsonl       # PersistentCandidate records (append-only, deduped by URL)
      candidate_provenance.jsonl    # CandidateProvenance records (one per discovery event)
      backfill_queue.jsonl          # Candidates awaiting backfill scraping
      retry_queue.jsonl             # Candidates queued for scrape retry
    metrics/
      discovery_diagnostics_latest.json
      engine_overlap_latest.json
      source_suggestions_latest.json
    runs/
      <timestamp>.json              # DiscoveryRun records (one per run)
    backfill_batches/
      latest_backfill_batches.jsonl # BackfillBatch records (one per batch, append-only)
      executed_queries.jsonl        # ExecutedBackfillQuery records — cross-run dedup keys
                                    # (engine, query_kind, keyword, source_hint, date window)
  ingest/
    seen.json                       # URL-keyed seen-state for ingest dedup
    runs/                           # IngestRun records
    logs/                           # Ingest log JSON files
    publication/                    # Publication bundles
seen.json                           # Legacy top-level seen state (ingest)
```

## backfill_batches/executed_queries.jsonl

This file is the primary guard against double-charging search API quotas (Brave, Exa) when running repeated or incremental backfill jobs. Before issuing any search query, the pipeline loads this file and skips any `(engine, query_kind, keyword, source_hint, date_from, date_to)` combination that was already executed.

**Current coverage:** 5,760 records seeded from the Jan–May 2026 backfill:
- 18 keywords × 8 source domains × 20 weekly windows (2026-01-01 → 2026-05-15) × 2 engines (brave + exa)
- All `source_targeted` query kind
- batch_id: `seed-2026-full-source-targeted-domain-run`

New executions are appended after each successful engine call by the `news-items-backfill-discover` workflow.
