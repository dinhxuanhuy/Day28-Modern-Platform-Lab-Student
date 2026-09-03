# Day 28 Track 2 — Reflection and Submission Notes

## Technical trade-offs

- The four student-owned integration functions remain small adapters around the
  existing contracts. Kafka headers are encoded as bytes, Delta input is
  deduplicated before MERGE, Feast feature names come from `FEATURE_REFS`, and
  readiness prioritizes mandatory failures.
- Idempotency is keyed by `idempotency_key`; when multiple events share a key,
  `(occurred_at, event_id)` provides deterministic newest-wins behavior.
- The platform deliberately reports `degraded` for optional dependencies and
  `not_ready` for mandatory dependency failures. This keeps liveness separate
  from readiness and avoids silently serving an unsafe path.

## Production gaps and operational notes

- Live evidence requires the Docker Compose stack and must be collected with
  real Kafka, Airflow, Delta, Feast, Qdrant, MLflow, gateway, Prometheus and
  OTEL services running.
- IP07 requires a real GPU-backed vLLM endpoint; it must not be replaced by a
  mock. IP10's LangSmith leg requires `LANGSMITH_API_KEY`; without it, report
  the gate as `UNVERIFIED` while retaining local OTLP evidence.
- Load-test percentiles, failure/recovery records, and rollback evidence must
  be captured from the live environment rather than invented in this file.

## Contribution

This submission was completed individually. The implementation covers the
four student-owned boundaries in `src/lab28_platform/integration_tasks.py`.
The fast validation suite and static repository checks pass.

## Validation performed

- `pytest starter-tests tests -q`: 87 passed
- `ruff check .`: passed
- `scripts/verify_matrix.py`: 245 checks passed
- `scripts/check_portability.py`: passed
- `scripts/validate_manifests.py`: passed

