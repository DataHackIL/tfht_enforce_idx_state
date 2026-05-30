# tfht_enforce_idx_state

Persistent operational state for the
[`tfht_enforce_idx`](https://github.com/DataHackIL/tfht_enforce_idx) pipeline.
This repository is committed to by automation after scheduled discovery, ingest,
backfill, and publication runs.

Created by [Shay Palachy Affek](http://www.shaypalachy.com/).

> **Operational-state repository:** this is not a standalone application,
> package, dataset release, or public-facing product. Treat it as the
> git-backed state store for the Enforcement Index pipeline.

## What This Repository Stores

`tfht_enforce_idx_state` keeps small, reviewable JSON/JSONL state files that let
pipeline runs resume safely across GitHub Actions jobs.

| Area | Purpose |
| --- | --- |
| Discovery candidates | Deduplicated candidate URLs, provenance rows, retry queues, and backfill queues. |
| Discovery metrics | Latest diagnostics, source suggestions, and search-engine overlap reports. |
| Discovery runs | One JSON record per scheduled or manual discovery run. |
| Backfill batches | Search-query execution state used to avoid repeated Brave/Exa API calls. |
| Ingest state | URL-level seen state, ingest run records, logs, and publication bundles. |
| Legacy state | `seen.json` preserves the older ingest seen-state shape. |

The parent codebase, workflows, and business logic live in
[`DataHackIL/tfht_enforce_idx`](https://github.com/DataHackIL/tfht_enforce_idx).

## Directory Layout

```text
news_items/
  discover/
    candidates/
      latest_candidates.jsonl       # PersistentCandidate records, deduped by URL
      candidate_provenance.jsonl    # CandidateProvenance records
      backfill_queue.jsonl          # Candidates awaiting backfill scraping
      retry_queue.jsonl             # Candidates queued for scrape retry
    metrics/
      discovery_diagnostics_latest.json
      engine_overlap_latest.json
      source_suggestions_latest.json
    runs/
      <timestamp>.json              # DiscoveryRun records
    backfill_batches/
      latest_backfill_batches.jsonl # BackfillBatch records
      executed_queries.jsonl        # Search-query dedup keys
  ingest/
    seen.json                       # URL-keyed ingest dedup state
    runs/                           # IngestRun records
    logs/                           # Ingest log JSON files
    publication/                    # Publication bundles
seen.json                           # Legacy top-level ingest seen state
```

## Safety and Maintenance Notes

- Human edits should be rare. Prefer changing the pipeline in
  `DataHackIL/tfht_enforce_idx`, then letting the workflow write the next state
  update.
- Do not delete deduplication state casually. Files such as
  `news_items/discover/backfill_batches/executed_queries.jsonl` prevent repeated
  paid search API calls and repeated downstream processing.
- Treat timestamped run and log files as audit evidence. If state must be
  compacted, do it in a focused PR that explains the retention rule and the
  affected date range.
- Do not add credentials, API responses containing secrets, local cache paths,
  or private operator notes. This is a public repository.

## Backfill Query Deduplication

`news_items/discover/backfill_batches/executed_queries.jsonl` is the primary
guard against double-charging search API quotas when repeated or incremental
backfill jobs run. Before issuing a search query, the pipeline loads this file
and skips any previously executed combination of:

- engine
- query kind
- keyword
- source hint
- date window

The current seeded coverage records the January-May 2026 source-targeted
backfill across the configured source domains, keywords, weekly windows, and
search engines.

## License and Reuse

Repository contents are distributed under the [MIT License](./LICENSE).

The files here are operational state emitted by a public-interest data pipeline.
The license makes the repository reusable, but it does not turn this state store
into a stable public dataset API. Downstream users should depend on formal
published outputs from the parent project rather than on internal run-state file
layouts.

## Credits

Created by [Shay Palachy Affek](http://www.shaypalachy.com/) [GitHub](https://github.com/shaypal5)
