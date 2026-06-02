---
name: incident-response-incident-response
description: "Use when working with incident response incident response"
---

## Use this skill when

- Working on incident response incident response tasks or workflows
- Needing guidance, best practices, or checklists for incident response incident response

## Do not use this skill when

- The task is unrelated to incident response incident response
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

Orchestrate multi-agent incident response with modern SRE practices for rapid resolution and learning:

[Extended thinking: This workflow implements a comprehensive incident command system (ICS) following modern SRE principles. Multiple specialized agents collaborate through defined phases: detection/triage, investigation/mitigation, communication/coordination, and resolution/postmortem. The workflow emphasizes speed without sacrificing accuracy, maintains clear communication channels, and ensures every incident becomes a learning opportunity through blameless postmortems and systematic improvements.]

## Configuration

### Severity Levels
- **P0/SEV-1**: Complete outage, security breach, data loss - immediate all-hands response
- **P1/SEV-2**: Major degradation, significant user impact - rapid response required
- **P2/SEV-3**: Minor degradation, limited impact - standard response
- **P3/SEV-4**: Cosmetic issues, no user impact - scheduled resolution

### Incident Types
- Performance degradation
- Service outage
- Security incident
- Data integrity issue
- Infrastructure failure
- Third-party service disruption

## Phase 1: Detection & Triage

### 1. Incident Detection and Classification
- Use Task tool with subagent_type="incident-responder"
- Prompt: "URGENT: Detect and classify incident: $ARGUMENTS. Analyze alerts from PagerDuty/Opsgenie/monitoring. Determine: 1) Incident severity (P0-P3), 2) Affected services and dependencies, 3) User impact and business risk, 4) Initial incident command structure needed. Check error budgets and SLO violations."
- Output: Severity classification, impact assessment, incident command assignments, SLO status
- Context: Initial alerts, monitoring dashboards, recent changes

### 2. Observability Analysis
- Use Task tool with subagent_type="observability-monitoring::observability-engineer"
- Prompt: "Perform rapid observability sweep for incident: $ARGUMENTS. Query: 1) Distributed tracing (OpenTelemetry/Jaeger), 2) Metrics correlation (Prometheus/Grafana/DataDog), 3) Log aggregation (ELK/Splunk), 4) APM data, 5) Real User Monitoring. Identify anomalies, error patterns, and service degradation points."
- Output: Observability findings, anomaly detection, service health matrix, trace analysis
- Context: Severity level from step 1, affected services

### 3. Initial Mitigation
- Use Task tool with subagent_type="incident-responder"
- Prompt: "Implement immediate mitigation for P$SEVERITY incident: $ARGUMENTS. Actions: 1) Traffic throttling/rerouting if needed, 2) Feature flag disabling for affected features, 3) Circuit breaker activation, 4) Rollback assessment for recent deployments, 5) Scale resources if capacity-related. Prioritize user experience restoration."
- Output: Mitigation actions taken, temporary fixes applied, rollback decisions
- Context: Observability findings, severity classification

## Phase 2: Investigation & Root Cause Analysis

### 4. Deep System Debugging
- Use Task tool with subagent_type="error-debugging::debugger"
- Prompt: "Conduct deep debugging for incident: $ARGUMENTS using observability data. Investigate: 1) Stack traces and error logs, 2) Database query performance and locks, 3) Network latency and timeouts, 4) Memory leaks and CPU spikes, 5) Dependency failures and cascading errors. Apply Five Whys analysis."
- Output: Root cause identification, contributing factors, dependency impact map
- Context: Observability analysis, mitigation status

### 5. Security Assessment
- Use Task tool with subagent_type="security-scanning::security-auditor"
- Prompt: "Assess security implications of incident: $ARGUMENTS. Check: 1) DDoS attack indicators, 2) Authentication/authorization failures, 3) Data exposure risks, 4) Certificate issues, 5) Suspicious access patterns. Review WAF logs, security groups, and audit trails."
- Output: Security assessment, breach analysis, vulnerability identification
- Context: Root cause findings, system logs

### 6. Performance Engineering Analysis
- Use Task tool with subagent_type="application-performance::performance-engineer"
- Prompt: "Analyze performance aspects of incident: $ARGUMENTS. Examine: 1) Resource utilization patterns, 2) Query optimization opportunities, 3) Caching effectiveness, 4) Load balancer health, 5) CDN performance, 6) Autoscaling triggers. Identify bottlenecks and capacity issues."
- Output: Performance bottlenecks, resource recommendations, optimization opportunities
- Context: Debug findings, current mitigation state

## Phase 3: Resolution & Recovery

### 7. Fix Implementation
- Use Task tool with subagent_type="backend-development::backend-architect"
- Prompt: "Design and implement production fix for incident: $ARGUMENTS based on root cause. Requirements: 1) Minimal viable fix for rapid deployment, 2) Risk assessment and rollback capability, 3) Staged rollout plan with monitoring, 4) Validation criteria and health checks. Consider both immediate fix and long-term solution."
- Output: Fix implementation, deployment strategy, validation plan, rollback procedures
- Context: Root cause analysis, performance findings, security assessment

### 8. Deployment and Validation
- Use Task tool with subagent_type="deployment-strategies::deployment-engineer"
- Prompt: "Execute emergency deployment for incident fix: $ARGUMENTS. Process: 1) Blue-green or canary deployment, 2) Progressive rollout with monitoring, 3) Health check validation at each stage, 4) Rollback triggers configured, 5) Real-time monitoring during deployment. Coordinate with incident command."
- Output: Deployment status, validation results, monitoring dashboard, rollback readiness
- Context: Fix implementation, current system state

## Phase 4: Communication & Coordination

### 9. Stakeholder Communication
- Use Task tool with subagent_type="content-marketing::content-marketer"
- Prompt: "Manage incident communication for: $ARGUMENTS. Create: 1) Status page updates (public-facing), 2) Internal engineering updates (technical details), 3) Executive summary (business impact/ETA), 4) Customer support briefing (talking points), 5) Timeline documentation with key decisions. Update every 15-30 minutes based on severity."
- Output: Communication artifacts, status updates, stakeholder briefings, timeline log
- Context: All previous phases, current resolution status

### 10. Customer Impact Assessment
- Use Task tool with subagent_type="incident-responder"
- Prompt: "Assess and document customer impact for incident: $ARGUMENTS. Analyze: 1) Affected user segments and geography, 2) Failed transactions or data loss, 3) SLA violations and contractual implications, 4) Customer support ticket volume, 5) Revenue impact estimation. Prepare proactive customer outreach list."
- Output: Customer impact report, SLA analysis, outreach recommendations
- Context: Resolution progress, communication status

## Phase 5: Postmortem & Prevention

### 11. Blameless Postmortem
- Use Task tool with subagent_type="documentation-generation::docs-architect"
- Prompt: "Conduct blameless postmortem for incident: $ARGUMENTS. Document: 1) Complete incident timeline with decisions, 2) Root cause and contributing factors (systems focus), 3) What went well in response, 4) What could improve, 5) Action items with owners and deadlines, 6) Lessons learned for team education. Follow SRE postmortem best practices."
- Output: Postmortem document, action items list, process improvements, training needs
- Context: Complete incident history, all agent outputs

### 12. Monitoring and Alert Enhancement
- Use Task tool with subagent_type="observability-monitoring::observability-engineer"
- Prompt: "Enhance monitoring to prevent recurrence of: $ARGUMENTS. Implement: 1) New alerts for early detection, 2) SLI/SLO adjustments if needed, 3) Dashboard improvements for visibility, 4) Runbook automation opportunities, 5) Chaos engineering scenarios for testing. Ensure alerts are actionable and reduce noise."
- Output: New monitoring configuration, alert rules, dashboard updates, runbook automation
- Context: Postmortem findings, root cause analysis

### 13. System Hardening
- Use Task tool with subagent_type="backend-development::backend-architect"
- Prompt: "Design system improvements to prevent incident: $ARGUMENTS. Propose: 1) Architecture changes for resilience (circuit breakers, bulkheads), 2) Graceful degradation strategies, 3) Capacity planning adjustments, 4) Technical debt prioritization, 5) Dependency reduction opportunities. Create implementation roadmap."
- Output: Architecture improvements, resilience patterns, technical debt items, roadmap
- Context: Postmortem action items, performance analysis

## Success Criteria

### Immediate Success (During Incident)
- Service restoration within SLA targets
- Accurate severity classification within 5 minutes
- Stakeholder communication every 15-30 minutes
- No cascading failures or incident escalation
- Clear incident command structure maintained

### Long-term Success (Post-Incident)
- Comprehensive postmortem within 48 hours
- All action items assigned with deadlines
- Monitoring improvements deployed within 1 week
- Runbook updates completed
- Team training conducted on lessons learned
- Error budget impact assessed and communicated

## Coordination Protocols

### Incident Command Structure
- **Incident Commander**: Decision authority, coordination
- **Technical Lead**: Technical investigation and resolution
- **Communications Lead**: Stakeholder updates
- **Subject Matter Experts**: Specific system expertise

### Communication Channels
- War room (Slack/Teams channel or Zoom)
- Status page updates (StatusPage, Statusly)
- PagerDuty/Opsgenie for alerting
- Confluence/Notion for documentation

### Handoff Requirements
- Each phase provides clear context to the next
- All findings documented in shared incident doc
- Decision rationale recorded for postmortem
- Timestamp all significant events

Production incident requiring immediate response: $ARGUMENTS

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [airflow-dag-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/airflow-dag-patterns/SKILL.md) - Build production Apache Airflow DAGs with best practices for operators, sensors, testing, and deploy...
- [analytics-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/analytics-integration/SKILL.md) - Integrate Google Analytics 4 (GA4) and custom event tracking. Use when adding analytics, conversion ...
- [application-performance-performance-optimization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/application-performance-performance-optimization/SKILL.md) - Optimize end-to-end application performance with profiling, observability, and backend/frontend tuni...
- [architecture-decision-records](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/architecture-decision-records/SKILL.md) - Write and maintain Architecture Decision Records (ADRs) following best practices for technical decis...
- [attack-tree-construction](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/attack-tree-construction/SKILL.md) - Build comprehensive attack trees to visualize threat paths. Use when mapping attack scenarios, ident...
- [backtesting-frameworks](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/backtesting-frameworks/SKILL.md) - Build robust backtesting systems for trading strategies with proper handling of look-ahead bias, sur...
- [changelog-automation](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/changelog-automation/SKILL.md) - Automate changelog generation from commits, PRs, and releases following Keep a Changelog format. Use...
- [chart-js-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/chart-js-integration/SKILL.md) - Master Chart.js for data visualization with responsive, animated charts. Use when creating dashboard...
- [cloudflare-workers](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/cloudflare-workers/SKILL.md) - Build and deploy edge applications with Cloudflare Workers, Pages Functions, KV, D1, and Durable Obj...
- [code-refactoring-context-restore](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/code-refactoring-context-restore/SKILL.md) - Use when working with code refactoring context restore
- [context-driven-development](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/context-driven-development/SKILL.md) - Use this skill when working with Conductor's context-driven development methodology, managing projec...
- [cost-optimization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/cost-optimization/SKILL.md) - Optimize cloud costs through resource rightsizing, tagging strategies, reserved instances, and spend...
- [cre-brand-compliance](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/cre-brand-compliance/SKILL.md) - Audit and enforce brand consistency for commercial real estate companies. Covers visual identity (co...
- [data-quality-frameworks](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/data-quality-frameworks/SKILL.md) - Implement data quality validation with Great Expectations, dbt tests, and data contracts. Use when b...
- [database-migration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/database-migration/SKILL.md) - Execute database migrations across ORMs and platforms with zero-downtime strategies, data transforma...
- [dbt-transformation-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/dbt-transformation-patterns/SKILL.md) - Master dbt (data build tool) for analytics engineering with model organization, testing, documentati...
- [debugging-toolkit-smart-debug](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/debugging-toolkit-smart-debug/SKILL.md) - Use when working with debugging toolkit smart debug
- [dependency-upgrade](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/dependency-upgrade/SKILL.md) - Manage major dependency version upgrades with compatibility analysis, staged rollout, and comprehens...
- [devops-troubleshooter](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/devops-troubleshooter/SKILL.md) - Expert DevOps troubleshooter specializing in rapid incident response, advanced debugging, and modern...
- [distributed-tracing](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/distributed-tracing/SKILL.md) - Implement distributed tracing with Jaeger and Tempo to track requests across microservices and ident...
- [e2e-testing-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/e2e-testing-patterns/SKILL.md) - Master end-to-end testing with Playwright and Cypress to build reliable test suites that catch bugs,...
- [error-debugging-multi-agent-review](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/error-debugging-multi-agent-review/SKILL.md) - Use when working with error debugging multi agent review
- [error-diagnostics-smart-debug](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/error-diagnostics-smart-debug/SKILL.md) - Use when working with error diagnostics smart debug
- [full-stack-orchestration-full-stack-feature](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/full-stack-orchestration-full-stack-feature/SKILL.md) - Use when working with full stack orchestration full stack feature
- [gdpr-data-handling](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/gdpr-data-handling/SKILL.md) - Implement GDPR-compliant data handling with consent management, data subject rights, and privacy by ...
- [globe-gl-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/globe-gl-integration/SKILL.md) - Master Globe.gl for interactive 3D globe visualization with WebGL. Use when building geographic data...
- [helm-chart-scaffolding](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/helm-chart-scaffolding/SKILL.md) - Design, organize, and manage Helm charts for templating and packaging Kubernetes applications with r...
- [incident-runbook-templates](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/incident-runbook-templates/SKILL.md) - Create structured incident response runbooks with step-by-step procedures, escalation paths, and rec...
- [postmortem-writing](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/postmortem-writing/SKILL.md) - Write effective blameless postmortems with root cause analysis, timelines, and action items. Use whe...
- [incident-response-smart-fix](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/incident-response-smart-fix/SKILL.md) - [Extended thinking: This workflow implements a sophisticated debugging and resolution pipeline that ...
- [jspdf-generation](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/jspdf-generation/SKILL.md) - Master client-side PDF generation with jsPDF and html2canvas. Use when creating downloadable reports...
- [kpi-dashboard-design](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/kpi-dashboard-design/SKILL.md) - Design effective KPI dashboards with metrics selection, visualization best practices, and real-time ...
- [langchain-architecture](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/langchain-architecture/SKILL.md) - Design LLM applications using the LangChain framework with agents, memory, and tool integration patt...
- [linkerd-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/linkerd-patterns/SKILL.md) - Implement Linkerd service mesh patterns for lightweight, security-focused service mesh deployments. ...
- [malware-analyst](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/malware-analyst/SKILL.md) - Expert malware analyst specializing in defensive malware research, threat intelligence, and incident...
- [microservices-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/microservices-patterns/SKILL.md) - Design microservices architectures with service boundaries, event-driven communication, and resilien...
- [nextjs-app-router-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/nextjs-app-router-patterns/SKILL.md) - Master Next.js 14+ App Router with Server Components, streaming, parallel routes, and advanced data ...
- [nft-standards](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/nft-standards/SKILL.md) - Implement NFT standards (ERC-721, ERC-1155) with proper metadata handling, minting strategies, and m...
- [nodejs-backend-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/nodejs-backend-patterns/SKILL.md) - Build production-ready Node.js backend services with Express/Fastify, implementing middleware patter...
- [observability-engineer](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/observability-engineer/SKILL.md) - Build production-ready monitoring, logging, and tracing systems. Implements comprehensive observabil...
- [openapi-spec-generation](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/openapi-spec-generation/SKILL.md) - Generate and maintain OpenAPI 3.1 specifications from code, design-first specs, and validation patte...
- [premium-web-animations](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/premium-web-animations/SKILL.md) - Copy-pasteable Framer Motion and CSS animation patterns for React. Scroll-triggered reveals, stagger...
- [python-packaging](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/python-packaging/SKILL.md) - Create distributable Python packages with proper project structure, setup.py/pyproject.toml, and pub...
- [rag-implementation](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/rag-implementation/SKILL.md) - Build Retrieval-Augmented Generation (RAG) systems for LLM applications with vector databases and se...
- [react-modernization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/react-modernization/SKILL.md) - Upgrade React applications to latest versions, migrate from class components to hooks, and adopt con...
- [react-native-architecture](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/react-native-architecture/SKILL.md) - Build production React Native apps with Expo, navigation, native modules, offline sync, and cross-pl...
- [responsive-polish-checklist](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/responsive-polish-checklist/SKILL.md) - The "last 5%" cross-browser, cross-device quality checklist for production websites. Touch targets, ...
- [screen-reader-testing](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/screen-reader-testing/SKILL.md) - Test web applications with screen readers including VoiceOver, NVDA, and JAWS. Use when validating s...
- [security-auditor](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/security-auditor/SKILL.md) - Expert security auditor specializing in DevSecOps, comprehensive cybersecurity, and compliance frame...
- [shadcn-ui-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/shadcn-ui-patterns/SKILL.md) - Master shadcn/ui component patterns, customization, theming, and composition. Use when building acce...
- [similarity-search-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/similarity-search-patterns/SKILL.md) - Implement efficient similarity search with vector databases. Use when building semantic search, impl...
- [sior-brand-guidelines](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/sior-brand-guidelines/SKILL.md) - Apply SIOR brand guidelines including colors, typography, logos, and visual identity. Use when desig...
- [slo-implementation](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/slo-implementation/SKILL.md) - Define and implement Service Level Indicators (SLIs) and Service Level Objectives (SLOs) with error ...
- [spark-optimization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/spark-optimization/SKILL.md) - Optimize Apache Spark jobs with partitioning, caching, shuffle optimization, and memory tuning. Use ...
- [stride-analysis-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/stride-analysis-patterns/SKILL.md) - Apply STRIDE methodology to systematically identify threats. Use when analyzing system security, con...
- [supabase-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/supabase-integration/SKILL.md) - Master direct Supabase integration with TypeScript, including client setup, authentication, RLS poli...
- [tailwind-design-system](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tailwind-design-system/SKILL.md) - Build scalable design systems with Tailwind CSS, design tokens, component libraries, and responsive ...
- [tdd-workflows-tdd-cycle](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tdd-workflows-tdd-cycle/SKILL.md) - Use when working with tdd workflows tdd cycle
- [tdd-workflows-tdd-refactor](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tdd-workflows-tdd-refactor/SKILL.md) - Use when working with tdd workflows tdd refactor
- [threat-mitigation-mapping](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/threat-mitigation-mapping/SKILL.md) - Map identified threats to appropriate security controls and mitigations. Use when prioritizing secur...
- [track-management](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/track-management/SKILL.md) - Use this skill when creating, managing, or working with Conductor tracks - the logical work units fo...
- [typography-and-font-optimization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/typography-and-font-optimization/SKILL.md) - Font loading strategies, responsive type scales, text rendering optimization, and typographic polish...
- [unity-ecs-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/unity-ecs-patterns/SKILL.md) - Master Unity ECS (Entity Component System) with DOTS, Jobs, and Burst for high-performance game deve...
- [vanilla-js-components](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/vanilla-js-components/SKILL.md) - Create framework-agnostic web components using Vanilla JavaScript. Use when building portable, CMS-e...
- [wcag-audit-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/wcag-audit-patterns/SKILL.md) - Conduct WCAG 2.2 accessibility audits with automated testing, manual verification, and remediation g...
- [web3-testing](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/web3-testing/SKILL.md) - Test smart contracts comprehensively using Hardhat and Foundry with unit tests, integration tests, a...
- [website-conversion-optimization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/website-conversion-optimization/SKILL.md) - B2B marketing website conversion patterns — trust signals, CTA hierarchy, social proof, lead capture...

