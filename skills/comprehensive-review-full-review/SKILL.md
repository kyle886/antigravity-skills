---
name: comprehensive-review-full-review
description: "Use when working with comprehensive review full review"
---

## Use this skill when

- Working on comprehensive review full review tasks or workflows
- Needing guidance, best practices, or checklists for comprehensive review full review

## Do not use this skill when

- The task is unrelated to comprehensive review full review
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

Orchestrate comprehensive multi-dimensional code review using specialized review agents

[Extended thinking: This workflow performs an exhaustive code review by orchestrating multiple specialized agents in sequential phases. Each phase builds upon previous findings to create a comprehensive review that covers code quality, security, performance, testing, documentation, and best practices. The workflow integrates modern AI-assisted review tools, static analysis, security scanning, and automated quality metrics. Results are consolidated into actionable feedback with clear prioritization and remediation guidance. The phased approach ensures thorough coverage while maintaining efficiency through parallel agent execution where appropriate.]

## Review Configuration Options

- **--security-focus**: Prioritize security vulnerabilities and OWASP compliance
- **--performance-critical**: Emphasize performance bottlenecks and scalability issues
- **--tdd-review**: Include TDD compliance and test-first verification
- **--ai-assisted**: Enable AI-powered review tools (Copilot, Codium, Bito)
- **--strict-mode**: Fail review on any critical issues found
- **--metrics-report**: Generate detailed quality metrics dashboard
- **--framework [name]**: Apply framework-specific best practices (React, Spring, Django, etc.)

## Phase 1: Code Quality & Architecture Review

Use Task tool to orchestrate quality and architecture agents in parallel:

### 1A. Code Quality Analysis
- Use Task tool with subagent_type="code-reviewer"
- Prompt: "Perform comprehensive code quality review for: $ARGUMENTS. Analyze code complexity, maintainability index, technical debt, code duplication, naming conventions, and adherence to Clean Code principles. Integrate with SonarQube, CodeQL, and Semgrep for static analysis. Check for code smells, anti-patterns, and violations of SOLID principles. Generate cyclomatic complexity metrics and identify refactoring opportunities."
- Expected output: Quality metrics, code smell inventory, refactoring recommendations
- Context: Initial codebase analysis, no dependencies on other phases

### 1B. Architecture & Design Review
- Use Task tool with subagent_type="architect-review"
- Prompt: "Review architectural design patterns and structural integrity in: $ARGUMENTS. Evaluate microservices boundaries, API design, database schema, dependency management, and adherence to Domain-Driven Design principles. Check for circular dependencies, inappropriate coupling, missing abstractions, and architectural drift. Verify compliance with enterprise architecture standards and cloud-native patterns."
- Expected output: Architecture assessment, design pattern analysis, structural recommendations
- Context: Runs parallel with code quality analysis

## Phase 2: Security & Performance Review

Use Task tool with security and performance agents, incorporating Phase 1 findings:

### 2A. Security Vulnerability Assessment
- Use Task tool with subagent_type="security-auditor"
- Prompt: "Execute comprehensive security audit on: $ARGUMENTS. Perform OWASP Top 10 analysis, dependency vulnerability scanning with Snyk/Trivy, secrets detection with GitLeaks, input validation review, authentication/authorization assessment, and cryptographic implementation review. Include findings from Phase 1 architecture review: {phase1_architecture_context}. Check for SQL injection, XSS, CSRF, insecure deserialization, and configuration security issues."
- Expected output: Vulnerability report, CVE list, security risk matrix, remediation steps
- Context: Incorporates architectural vulnerabilities identified in Phase 1B

### 2B. Performance & Scalability Analysis
- Use Task tool with subagent_type="application-performance::performance-engineer"
- Prompt: "Conduct performance analysis and scalability assessment for: $ARGUMENTS. Profile code for CPU/memory hotspots, analyze database query performance, review caching strategies, identify N+1 problems, assess connection pooling, and evaluate asynchronous processing patterns. Consider architectural findings from Phase 1: {phase1_architecture_context}. Check for memory leaks, resource contention, and bottlenecks under load."
- Expected output: Performance metrics, bottleneck analysis, optimization recommendations
- Context: Uses architecture insights to identify systemic performance issues

## Phase 3: Testing & Documentation Review

Use Task tool for test and documentation quality assessment:

### 3A. Test Coverage & Quality Analysis
- Use Task tool with subagent_type="unit-testing::test-automator"
- Prompt: "Evaluate testing strategy and implementation for: $ARGUMENTS. Analyze unit test coverage, integration test completeness, end-to-end test scenarios, test pyramid adherence, and test maintainability. Review test quality metrics including assertion density, test isolation, mock usage, and flakiness. Consider security and performance test requirements from Phase 2: {phase2_security_context}, {phase2_performance_context}. Verify TDD practices if --tdd-review flag is set."
- Expected output: Coverage report, test quality metrics, testing gap analysis
- Context: Incorporates security and performance testing requirements from Phase 2

### 3B. Documentation & API Specification Review
- Use Task tool with subagent_type="code-documentation::docs-architect"
- Prompt: "Review documentation completeness and quality for: $ARGUMENTS. Assess inline code documentation, API documentation (OpenAPI/Swagger), architecture decision records (ADRs), README completeness, deployment guides, and runbooks. Verify documentation reflects actual implementation based on all previous phase findings: {phase1_context}, {phase2_context}. Check for outdated documentation, missing examples, and unclear explanations."
- Expected output: Documentation coverage report, inconsistency list, improvement recommendations
- Context: Cross-references all previous findings to ensure documentation accuracy

## Phase 4: Best Practices & Standards Compliance

Use Task tool to verify framework-specific and industry best practices:

### 4A. Framework & Language Best Practices
- Use Task tool with subagent_type="framework-migration::legacy-modernizer"
- Prompt: "Verify adherence to framework and language best practices for: $ARGUMENTS. Check modern JavaScript/TypeScript patterns, React hooks best practices, Python PEP compliance, Java enterprise patterns, Go idiomatic code, or framework-specific conventions (based on --framework flag). Review package management, build configuration, environment handling, and deployment practices. Include all quality issues from previous phases: {all_previous_contexts}."
- Expected output: Best practices compliance report, modernization recommendations
- Context: Synthesizes all previous findings for framework-specific guidance

### 4B. CI/CD & DevOps Practices Review
- Use Task tool with subagent_type="cicd-automation::deployment-engineer"
- Prompt: "Review CI/CD pipeline and DevOps practices for: $ARGUMENTS. Evaluate build automation, test automation integration, deployment strategies (blue-green, canary), infrastructure as code, monitoring/observability setup, and incident response procedures. Assess pipeline security, artifact management, and rollback capabilities. Consider all issues identified in previous phases that impact deployment: {all_critical_issues}."
- Expected output: Pipeline assessment, DevOps maturity evaluation, automation recommendations
- Context: Focuses on operationalizing fixes for all identified issues

## Consolidated Report Generation

Compile all phase outputs into comprehensive review report:

### Critical Issues (P0 - Must Fix Immediately)
- Security vulnerabilities with CVSS > 7.0
- Data loss or corruption risks
- Authentication/authorization bypasses
- Production stability threats
- Compliance violations (GDPR, PCI DSS, SOC2)

### High Priority (P1 - Fix Before Next Release)
- Performance bottlenecks impacting user experience
- Missing critical test coverage
- Architectural anti-patterns causing technical debt
- Outdated dependencies with known vulnerabilities
- Code quality issues affecting maintainability

### Medium Priority (P2 - Plan for Next Sprint)
- Non-critical performance optimizations
- Documentation gaps and inconsistencies
- Code refactoring opportunities
- Test quality improvements
- DevOps automation enhancements

### Low Priority (P3 - Track in Backlog)
- Style guide violations
- Minor code smell issues
- Nice-to-have documentation updates
- Cosmetic improvements

## Success Criteria

Review is considered successful when:
- All critical security vulnerabilities are identified and documented
- Performance bottlenecks are profiled with remediation paths
- Test coverage gaps are mapped with priority recommendations
- Architecture risks are assessed with mitigation strategies
- Documentation reflects actual implementation state
- Framework best practices compliance is verified
- CI/CD pipeline supports safe deployment of reviewed code
- Clear, actionable feedback is provided for all findings
- Metrics dashboard shows improvement trends
- Team has clear prioritized action plan for remediation

Target: $ARGUMENTS

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [api-documenter](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/api-documenter/SKILL.md) - Master API documentation with OpenAPI 3.1, AI-powered tools, and modern developer experience practic...
- [attack-tree-construction](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/attack-tree-construction/SKILL.md) - Build comprehensive attack trees to visualize threat paths. Use when mapping attack scenarios, ident...
- [backtesting-frameworks](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/backtesting-frameworks/SKILL.md) - Build robust backtesting systems for trading strategies with proper handling of look-ahead bias, sur...
- [business-analyst](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/business-analyst/SKILL.md) - Master modern business analysis with AI-powered analytics, real-time dashboards, and data-driven ins...
- [c4-code](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/c4-code/SKILL.md) - Expert C4 Code-level documentation specialist. Analyzes code directories to create comprehensive C4 ...
- [c4-context](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/c4-context/SKILL.md) - Expert C4 Context-level documentation specialist. Creates high-level system context diagrams, docume...
- [chart-js-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/chart-js-integration/SKILL.md) - Master Chart.js for data visualization with responsive, animated charts. Use when creating dashboard...
- [cloudflare-workers](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/cloudflare-workers/SKILL.md) - Build and deploy edge applications with Cloudflare Workers, Pages Functions, KV, D1, and Durable Obj...
- [code-review-excellence](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/code-review-excellence/SKILL.md) - Master effective code review practices to provide constructive feedback, catch bugs early, and foste...
- [error-debugging-multi-agent-review](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/error-debugging-multi-agent-review/SKILL.md) - Use when working with error debugging multi agent review
- [full-stack-orchestration-full-stack-feature](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/full-stack-orchestration-full-stack-feature/SKILL.md) - Use when working with full stack orchestration full stack feature
- [performance-testing-review-multi-agent-review](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/performance-testing-review-multi-agent-review/SKILL.md) - Use when working with performance testing review multi agent review
- [customer-support](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/customer-support/SKILL.md) - Elite AI-powered customer support specialist mastering conversational AI, automated ticketing, senti...
- [dependency-upgrade](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/dependency-upgrade/SKILL.md) - Manage major dependency version upgrades with compatibility analysis, staged rollout, and comprehens...
- [fastapi-templates](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/fastapi-templates/SKILL.md) - Create production-ready FastAPI projects with async patterns, dependency injection, and comprehensiv...
- [git-pr-workflows-git-workflow](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/git-pr-workflows-git-workflow/SKILL.md) - Orchestrate a comprehensive git workflow from code review through PR creation, leveraging specialize...
- [globe-gl-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/globe-gl-integration/SKILL.md) - Master Globe.gl for interactive 3D globe visualization with WebGL. Use when building geographic data...
- [helm-chart-scaffolding](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/helm-chart-scaffolding/SKILL.md) - Design, organize, and manage Helm charts for templating and packaging Kubernetes applications with r...
- [incident-runbook-templates](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/incident-runbook-templates/SKILL.md) - Create structured incident response runbooks with step-by-step procedures, escalation paths, and rec...
- [jspdf-generation](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/jspdf-generation/SKILL.md) - Master client-side PDF generation with jsPDF and html2canvas. Use when creating downloadable reports...
- [kpi-dashboard-design](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/kpi-dashboard-design/SKILL.md) - Design effective KPI dashboards with metrics selection, visualization best practices, and real-time ...
- [linkerd-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/linkerd-patterns/SKILL.md) - Implement Linkerd service mesh patterns for lightweight, security-focused service mesh deployments. ...
- [llm-evaluation](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/llm-evaluation/SKILL.md) - Implement comprehensive evaluation strategies for LLM applications using automated metrics, human fe...
- [nft-standards](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/nft-standards/SKILL.md) - Implement NFT standards (ERC-721, ERC-1155) with proper metadata handling, minting strategies, and m...
- [postmortem-writing](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/postmortem-writing/SKILL.md) - Write effective blameless postmortems with root cause analysis, timelines, and action items. Use whe...
- [prometheus-configuration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/prometheus-configuration/SKILL.md) - Set up Prometheus for comprehensive metric collection, storage, and monitoring of infrastructure and...
- [python-packaging](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/python-packaging/SKILL.md) - Create distributable Python packages with proper project structure, setup.py/pyproject.toml, and pub...
- [python-testing-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/python-testing-patterns/SKILL.md) - Implement comprehensive testing strategies with pytest, fixtures, mocking, and test-driven developme...
- [react-native-architecture](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/react-native-architecture/SKILL.md) - Build production React Native apps with Expo, navigation, native modules, offline sync, and cross-pl...
- [service-mesh-observability](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/service-mesh-observability/SKILL.md) - Implement comprehensive observability for service meshes including distributed tracing, metrics, and...
- [supabase-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/supabase-integration/SKILL.md) - Master direct Supabase integration with TypeScript, including client setup, authentication, RLS poli...
- [test-automator](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/test-automator/SKILL.md) - Master AI-powered test automation with modern frameworks, self-healing tests, and comprehensive qual...
- [unity-ecs-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/unity-ecs-patterns/SKILL.md) - Master Unity ECS (Entity Component System) with DOTS, Jobs, and Burst for high-performance game deve...
- [vanilla-js-components](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/vanilla-js-components/SKILL.md) - Create framework-agnostic web components using Vanilla JavaScript. Use when building portable, CMS-e...
- [wcag-audit-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/wcag-audit-patterns/SKILL.md) - Conduct WCAG 2.2 accessibility audits with automated testing, manual verification, and remediation g...

