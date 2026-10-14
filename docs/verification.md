# Local verification — October 13, 2026

Project 05 is locally implemented. Both full Windows and Linux suites passed **25 tests, zero failures/errors/skips**. Publication and remote CI require the separate delivery checkpoint.
Results are captured in [machine-readable evidence](evidence/verification-summary.json).

The full suite exercises ten real subprocess/HTTP scenarios with the Docker backend, Toxiproxy,
Prometheus, and Grafana. Controlled outcomes are eight recoveries, one safe dependency failure, and
one human-required permanent outage, with no duplicate side effects. The worker interruption is a
real process kill after the backend receipt commits, followed by a replacement worker using the
same durable task/checkpoint. Recovery consumes a second attempt and retains exactly one receipt.

Windows uses Python 3.14.6; Linux uses Python 3.12 with a clean installed wheel in Docker. The fixture
subset can run without Docker; its count excludes the stack test. Full verification requires
`LAB_STACK=1`, and a skipped stack test is not evidence of network-fault coverage. Exact parsed
JUnit counts and the captured scenario reports are preserved in the evidence file.

Executed commands include pinned installation, Ruff lint/format, `pip check`, installed CLI help,
`docker compose up -d --build`, full pytest, `recovery-lab run`, and the observability verifier.
Prometheus was queried for actual scraped scenario data. Grafana's API returned the provisioned
dashboard with all five panels. Windows and Linux scenario reports agree on outcomes and duplicate
counts; timings and task IDs naturally differ.

Initial checks exposed a misplaced test assertion, sandbox/shared-temp permission conflicts, and
Toxiproxy's canonical wildcard listener address. These were corrected before final verification.
Workspace-local fresh pytest temporary directories and disabled cache writes were used for the
Windows execution account. Failed preliminary commands are not counted as passing checks.

Retry effectiveness is the fraction of multi-attempt scenarios ending recovered, including intentional
exhaustion cases in the denominator. Recovery success rate uses the eight scenarios expected to
recover. Scenario duration includes controller and process startup; worker CPU measures only the
execution section of the final worker process. Database bytes exclude WAL and logs. These are local
fixture measurements, not production performance or reliability claims.

CI is configured for six OS/Python fixture jobs and one full-stack job. No remote success is claimed
until a published commit and its artifacts are independently checked.
