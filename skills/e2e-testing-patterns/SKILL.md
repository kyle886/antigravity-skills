---
name: e2e-testing-patterns
description: Master end-to-end testing with Playwright and Cypress to build reliable test suites that catch bugs, improve confidence, and enable fast deployment. Use when implementing E2E tests, debugging flaky tests, or establishing testing standards.
---

# E2E Testing Patterns

Build reliable, fast, and maintainable end-to-end test suites that provide confidence to ship code quickly and catch regressions before users do.

## Use this skill when

- Implementing end-to-end test automation
- Debugging flaky or unreliable tests
- Testing critical user workflows
- Setting up CI/CD test pipelines
- Testing across multiple browsers
- Validating accessibility requirements
- Testing responsive designs
- Establishing E2E testing standards

## Do not use this skill when

- You only need unit or integration tests
- The environment cannot support stable UI automation
- You cannot provision safe test accounts or data

## Instructions

1. Identify critical user journeys and success criteria.
2. Build stable selectors and test data strategies.
3. Implement tests with retries, tracing, and isolation.
4. Run in CI with parallelization and artifact capture.
- Load the nested playbook at `resources/playbook.md` on-demand for detailed implementation guides and checklists.

## Safety

- Avoid running destructive tests against production.
- Use dedicated test data and scrub sensitive output.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [api-design-principles](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/api-design-principles/SKILL.md) - Master REST and GraphQL API design principles to build intuitive, scalable, and maintainable APIs th...
- [debugging-toolkit-smart-debug](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/debugging-toolkit-smart-debug/SKILL.md) - Use when working with debugging toolkit smart debug
- [incident-response-incident-response](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/incident-response-incident-response/SKILL.md) - Use when working with incident response incident response
- [error-debugging-multi-agent-review](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/error-debugging-multi-agent-review/SKILL.md) - Use when working with error debugging multi agent review

