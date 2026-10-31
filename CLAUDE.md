# CLAUDE.md

This is a learning project. I'm Parth, learning dbt, Airflow, Docker, Terraform and CI/CD by building a Wikipedia edit-war pipeline. The plan is in `docs/PLAN.md`; read it to know which week I'm on and what's in scope.

## The one rule that matters
Each week in `docs/PLAN.md` lists **hand-written** parts. Do not write those for me, even if asked casually. Instead, escalate hints:
1. Ask what I've tried and what I expect to happen.
2. Point me at the concept or the docs.
3. Describe the approach in plain words.
4. Pseudocode.

Only write real code for a hand-written part if I say **"just show me"**. When you do, keep it minimal and ask me to explain it back afterwards.

This rule applies to any file, including scratch, example and throwaway ones. Don't work around it by putting the logic somewhere that isn't the final file.

Everything else (scaffolding, config, boilerplate, test fixtures, CI YAML, formatting) you can write directly. Always explain what you changed and why in 2–3 sentences, because I'm learning the tooling too.

Test fixtures means sample data only. Don't write tests for hand-written code until I've written some myself, and never embed the logic in a test, helper or utility as a workaround.

## How to help
- Debugging: explain the error and its cause before proposing a fix. If the bug is in a hand-written file, tell me where to look instead of fixing it.
- Review: when I ask for review, check correctness, then data-engineering practice (idempotency, tests, naming, no hardcoded secrets), then style. Be direct.
- Don't add dependencies, files or features beyond the current week's scope without asking.
- Ask before running anything that touches the cloud (`terraform apply`, GCP commands) or deletes data.
- Suggest a commit message after each meaningful change (conventional commits: `feat:`, `fix:`, `docs:`, `chore:`). I'll commit myself.

## Stack
- Python 3.12, uv for environments and dependencies (`uv add`, `uv run`)
- DuckDB locally; dbt Core with dbt-duckdb (dbt-bigquery from week 5)
- Airflow 3 on docker-compose (week 4)
- Terraform on GCP: GCS + BigQuery (week 5)
- GitHub Actions (week 6)

## Layout
- `ingest/`: week 1 stream consumer and detector
- `data/raw/`: landed Parquet, gitignored
- `transform/`: dbt project (week 2)
- `orchestrate/`: Airflow DAGs and compose file (week 4)
- `infra/`: Terraform (week 5)
- `docs/`: plan and notes

## Never
- Commit secrets, `.env` files, service-account keys or Terraform state.
- Put credentials in code; use environment variables.