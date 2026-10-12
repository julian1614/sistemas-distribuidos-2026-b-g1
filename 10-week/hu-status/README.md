<!-- HU-STATUS TEMPLATE - do NOT remove the <!-- ... --> markers or the table headers.
     Your weekly grade is read AUTOMATICALLY from this file:
       10-week/hu-status/README.md  (inside YOUR fork). English. -->

# Weekly Status - Week 10

<!-- CONFIG-START - must match your profile repo (username/username) CONFIG -->
- FULL_NAME: Julian Alberto Trujillo Bonilla
- GITHUB_USER: julian1614
- TEAM: medicine-inventory-system (MediStock)
- SPRINT_GOAL: Freeze a verifiable integrated C2 candidate, document evidence and prepare QA/release promotion.
<!-- CONFIG-END -->

## 1. User stories worked this week
| HU ID | Title | Status (todo/doing/done) | Evidence (PR or commit URL) |
|---|---|---|---|
| HU-WEB-001 | Integrated role-scoped frontend | doing | https://github.com/code-corhuila/mis-front/commit/ed59a56fcb862c33912c61b70a060260f1d2de9a |
| HU-CAT-001 | Catalog publication and migration verification | doing | https://github.com/code-corhuila/mis-catalog-db/commit/2495e6480e93ea9dbcf7607389e6e9a5f34a5f0f |
| HU-WRK-001 | Persisted expiry alerts and signed pipeline | doing | https://github.com/code-corhuila/mis-inventory-api/commit/a816bbe543033eaea615e4f176fd20eac8fef6db |
| HU-REL-001 | C2 QA and release promotion | doing | https://github.com/code-corhuila/mis-catalog-db/pull/2 |

Statuses are deliberately not done: source publication and scoped tests do not establish full HU acceptance or a tagged main release. This report was prepared retrospectively on 2026-10-12; the student confirmed that the recovery work was concentrated in this week.

## 2. My individual contribution
Published the existing implementation in official repositories and integrated login, domain views, reception/exit scopes, overview and the expiry-alert reader in the frontend.
- Investigated the resolved-alert reevaluation failure and published the timestamp-preservation fix with a real PostgreSQL regression.
- Prepared committed source review archives with version/hash manifests. These are preliminary develop snapshots, not final v2.0.0 main artifacts.
- Re-ran Catalog migration acceptance on 2026-10-12: repeated update, empty rollback/rebuild, v1 upgrade preservation and audit rollback rejection all passed in disposable project mis-cat-check-265d97d3. Its volume and network were removed.
- Confirmed source commit dates for frontend work on 2026-10-12. Work from earlier weeks remains unverified.
- Planning for this recovery was performed this week. No dated minutes, daily/review/retro attendance or team participation record is available; those ceremonies are not claimed as completed.

## 3. Blockers and risks
Catalog PR #2 was merged into develop, not qa; origin/qa remains at the scaffold commit. A separate PR targeting qa is still required.
- Active dev login works, but the alert route returned 404; source/pipeline verification is not equivalent to an aligned live deployment.
- Security database migrations still have no confirmed official repository destination.
- Full QA promotion, main approval, updated release metadata and v2.0.0 tags are pending.
- Historical weekly goals, ceremony evidence and additional member contributions are not confirmed. A retrospective report cannot replace missing weekly evidence.

## 4. Plan for next week
Prioritize a reproducible end-to-end reception/inventory demonstration including injected failure and recovery.
- Correct the QA target, validate each promoted component and request main release approval through child branches.
- Update CHANGELOG/ADRs and export final archives from approved main v2.0.0 tags only.
- Keep unresolved scope visible; do not add new features or declare unfinished HUs complete.

## 5. Compliance self-check
- [ ] Conventional Commits - `type(scope): summary`
- [ ] Per-environment HU branch + PR to that environment (hu-xxx-dev -> develop, ...)
- [ ] Testable acceptance criteria
- [ ] Tests added/updated (unit / integration)
- [ ] DDD / hexagonal boundaries respected (domain has no I/O)
- [ ] No secrets; config via environment variables

## 6. Evidence links
https://github.com/code-corhuila/mis-front/commit/ed59a56fcb862c33912c61b70a060260f1d2de9a
- https://github.com/code-corhuila/mis-front/commit/1c94ba2d40e5c0245f945937d9a9ba5eb39ede88
- https://github.com/code-corhuila/mis-catalog-db/commit/2495e6480e93ea9dbcf7607389e6e9a5f34a5f0f
- https://github.com/code-corhuila/mis-catalog-db/blob/d80494c/tests/Test-Migrations.ps1
- https://github.com/code-corhuila/mis-catalog-db/pull/2 (merged into develop; not QA evidence)
- https://github.com/code-corhuila/mis-inventory-api/blob/a816bbe543033eaea615e4f176fd20eac8fef6db/docs/WRK-001-signed-pipeline-acceptance.md
- Local Catalog test rerun passed with exit code 0 on 2026-10-12; the log has not been published to the fork. Scoped test execution is not formal QA approval.
