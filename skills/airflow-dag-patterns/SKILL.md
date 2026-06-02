---
name: airflow-dag-patterns
description: Build production Apache Airflow DAGs with best practices for operators, sensors, testing, and deployment. Use when creating data pipelines, orchestrating workflows, or scheduling batch jobs.
---

# Apache Airflow DAG Patterns

Production-ready patterns for Apache Airflow including DAG design, operators, sensors, testing, and deployment strategies.

## Use this skill when

- Creating data pipeline orchestration with Airflow
- Designing DAG structures and dependencies
- Implementing custom operators and sensors
- Testing Airflow DAGs locally
- Setting up Airflow in production
- Debugging failed DAG runs

## Do not use this skill when

- You only need a simple cron job or shell script
- Airflow is not part of the tooling stack
- The task is unrelated to workflow orchestration

## Instructions

1. Identify data sources, schedules, and dependencies.
2. Design idempotent tasks with clear ownership and retries.
3. Implement DAGs with observability and alerting hooks.
4. Validate in staging and document operational runbooks.

Refer to `resources/implementation-playbook.md` for detailed patterns, checklists, and templates.

## Safety

- Avoid changing production DAG schedules without approval.
- Test backfills and retries carefully to prevent data duplication.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [tdd-workflows-tdd-cycle](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tdd-workflows-tdd-cycle/SKILL.md) - Use when working with tdd workflows tdd cycle
- [tdd-workflows-tdd-refactor](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tdd-workflows-tdd-refactor/SKILL.md) - Use when working with tdd workflows tdd refactor
- [incident-response-incident-response](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/incident-response-incident-response/SKILL.md) - Use when working with incident response incident response
- [dbt-transformation-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/dbt-transformation-patterns/SKILL.md) - Master dbt (data build tool) for analytics engineering with model organization, testing, documentati...
- [ml-pipeline-workflow](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/ml-pipeline-workflow/SKILL.md) - Build end-to-end MLOps pipelines from data preparation through model training, validation, and produ...

