# Petty Wiki Wars — Plan

A pipeline that detects and ranks Wikipedia edit wars, built in weekend-sized stages. Each week ends with something working and pushed.

**Mental model:** ingestion = ingredients arriving; dbt = the recipe book; DAG = the prep chart (tasks + "this before that"); Airflow = the head chef running the chart on schedule; Docker = the kitchen in a shipping container; Terraform = building the kitchen from a blueprint file.

**Hand-written** = I write it myself. Claude Code may explain, hint and review, but not write it unless I explicitly say "just show me".

---

## Week 1 — Edit War Detector (ingestion)
Subscribe to Wikimedia's public live edit stream, keep English Wikipedia, detect reverts, and flag edit wars using Wikipedia's own rule (more than 3 reverts on one page in 24 hours). Show a live terminal leaderboard (`rich`) and land every raw event as hourly-partitioned Parquet files.

- Hand-written: stream consumer loop, revert detection, edit-war logic, Parquet landing.
- Claude Code: project scaffolding (uv, pyproject, folders), `rich` display formatting, test fixtures, review.
- Open question: confirm which field reliably marks a revert (edit summaries like "Undid revision…" are the fallback). Check the event schema first.
- Done: run one command, see the leaderboard, hourly files accumulate. Leave it running all week.

## Week 2 — Wikipedia Drama Index (dbt)
dbt models over the week's raw edits, running on DuckDB locally.
- Staging: parse timestamps, dedupe, drop bots.
- Intermediate: reverts per article per editor.
- Mart: "pettiness score" per article (revert count, revert speed, editors involved).
- Tests, e.g. reverts can't exceed edits. Publish dbt docs to GitHub Pages.

- Hand-written: all model SQL, the scoring logic, custom tests.
- Claude Code: `dbt init` setup, profiles config, explaining errors, GitHub Pages publishing.
- Done: `dbt build` passes with tests; docs site is live.

## Week 3 — Melodramatic DAG Runner (separate repo: `drama-dag`)
A ~300-line orchestrator: tasks as Python functions with declared dependencies; topological sort; parallel execution of independent tasks; retries; state so reruns skip succeeded tasks. Failing tasks write melodramatic diary entries.

- Hand-written: dependency resolution, scheduler loop, retry and state logic.
- Claude Code: packaging, CLI wrapper, tests (after I've written a few myself), review.
- Done: a sample DAG runs, fails dramatically, retries and resumes.

## Week 4 — Daily Pettiest Edit War (Airflow + Docker)
Airflow 3 on docker-compose. An hourly DAG: tidy the hour's raw files, then run dbt, then once a day post "Pettiest Edit War of the Day" to a Discord webhook. The week 1 detector keeps streaming alongside it (streaming + batch split).

- Hand-written: the DAG file, the task logic, making every task idempotent.
- Claude Code: docker-compose setup (start from the official file), debugging containers, the Discord webhook call.
- Likely spills into a second weekend; first-time setup is the hard part.
- Needs at least 4GB RAM for Docker, ideally 8.
- Done: the DAG runs on schedule in the UI, a backfill of a past day works, the Discord post arrives.

## Week 5 — Petty Wiki Wars in the Cloud (Terraform + GCP)
Terraform creates a GCS bucket, a BigQuery dataset, a least-privilege service account and a budget alert. Raw files go to GCS; dbt switches target to BigQuery.

- Hand-written: Terraform resource definitions, the dbt target switch.
- Claude Code: provider and backend boilerplate, explaining plan output, IAM debugging.
- Needs GCP billing enabled. The free tier should cover this volume, but verify before applying.
- Practise `terraform destroy` until it's a habit.
- Likely spills into a second weekend.
- Done: `terraform apply` builds everything from scratch, dbt runs on BigQuery, `terraform destroy` cleans up.

## Week 6 — Ship it (CI/CD)
GitHub Actions on every PR: SQL lint (sqlfluff), `dbt build` against a small DuckDB fixture, `terraform fmt`/`validate`. README with architecture diagram and demo GIF.

- Hand-written: README story and design decisions section.
- Claude Code: workflow YAML scaffold (which I review line by line), fixture data, diagram.
- Done: green CI badge; a stranger can understand the project in 30 seconds.

## After week 6
Back to one-off quirky projects: Query Roaster (sqlglot SQL linter), Recipe Arithmetic (embeddings), Fake Railway Stations (char-level transformer).
