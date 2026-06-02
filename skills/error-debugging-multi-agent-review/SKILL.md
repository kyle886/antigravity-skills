---
name: error-debugging-multi-agent-review
description: "Use when working with error debugging multi agent review"
---

# Multi-Agent Code Review Orchestration Tool

## Use this skill when

- Working on multi-agent code review orchestration tool tasks or workflows
- Needing guidance, best practices, or checklists for multi-agent code review orchestration tool

## Do not use this skill when

- The task is unrelated to multi-agent code review orchestration tool
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Role: Expert Multi-Agent Review Orchestration Specialist

A sophisticated AI-powered code review system designed to provide comprehensive, multi-perspective analysis of software artifacts through intelligent agent coordination and specialized domain expertise.

## Context and Purpose

The Multi-Agent Review Tool leverages a distributed, specialized agent network to perform holistic code assessments that transcend traditional single-perspective review approaches. By coordinating agents with distinct expertise, we generate a comprehensive evaluation that captures nuanced insights across multiple critical dimensions:

- **Depth**: Specialized agents dive deep into specific domains
- **Breadth**: Parallel processing enables comprehensive coverage
- **Intelligence**: Context-aware routing and intelligent synthesis
- **Adaptability**: Dynamic agent selection based on code characteristics

## Tool Arguments and Configuration

### Input Parameters
- `$ARGUMENTS`: Target code/project for review
  - Supports: File paths, Git repositories, code snippets
  - Handles multiple input formats
  - Enables context extraction and agent routing

### Agent Types
1. Code Quality Reviewers
2. Security Auditors
3. Architecture Specialists
4. Performance Analysts
5. Compliance Validators
6. Best Practices Experts

## Multi-Agent Coordination Strategy

### 1. Agent Selection and Routing Logic
- **Dynamic Agent Matching**:
  - Analyze input characteristics
  - Select most appropriate agent types
  - Configure specialized sub-agents dynamically
- **Expertise Routing**:
  ```python
  def route_agents(code_context):
      agents = []
      if is_web_application(code_context):
          agents.extend([
              "security-auditor",
              "web-architecture-reviewer"
          ])
      if is_performance_critical(code_context):
          agents.append("performance-analyst")
      return agents
  ```

### 2. Context Management and State Passing
- **Contextual Intelligence**:
  - Maintain shared context across agent interactions
  - Pass refined insights between agents
  - Support incremental review refinement
- **Context Propagation Model**:
  ```python
  class ReviewContext:
      def __init__(self, target, metadata):
          self.target = target
          self.metadata = metadata
          self.agent_insights = {}

      def update_insights(self, agent_type, insights):
          self.agent_insights[agent_type] = insights
  ```

### 3. Parallel vs Sequential Execution
- **Hybrid Execution Strategy**:
  - Parallel execution for independent reviews
  - Sequential processing for dependent insights
  - Intelligent timeout and fallback mechanisms
- **Execution Flow**:
  ```python
  def execute_review(review_context):
      # Parallel independent agents
      parallel_agents = [
          "code-quality-reviewer",
          "security-auditor"
      ]

      # Sequential dependent agents
      sequential_agents = [
          "architecture-reviewer",
          "performance-optimizer"
      ]
  ```

### 4. Result Aggregation and Synthesis
- **Intelligent Consolidation**:
  - Merge insights from multiple agents
  - Resolve conflicting recommendations
  - Generate unified, prioritized report
- **Synthesis Algorithm**:
  ```python
  def synthesize_review_insights(agent_results):
      consolidated_report = {
          "critical_issues": [],
          "important_issues": [],
          "improvement_suggestions": []
      }
      # Intelligent merging logic
      return consolidated_report
  ```

### 5. Conflict Resolution Mechanism
- **Smart Conflict Handling**:
  - Detect contradictory agent recommendations
  - Apply weighted scoring
  - Escalate complex conflicts
- **Resolution Strategy**:
  ```python
  def resolve_conflicts(agent_insights):
      conflict_resolver = ConflictResolutionEngine()
      return conflict_resolver.process(agent_insights)
  ```

### 6. Performance Optimization
- **Efficiency Techniques**:
  - Minimal redundant processing
  - Cached intermediate results
  - Adaptive agent resource allocation
- **Optimization Approach**:
  ```python
  def optimize_review_process(review_context):
      return ReviewOptimizer.allocate_resources(review_context)
  ```

### 7. Quality Validation Framework
- **Comprehensive Validation**:
  - Cross-agent result verification
  - Statistical confidence scoring
  - Continuous learning and improvement
- **Validation Process**:
  ```python
  def validate_review_quality(review_results):
      quality_score = QualityScoreCalculator.compute(review_results)
      return quality_score > QUALITY_THRESHOLD
  ```

## Example Implementations

### 1. Parallel Code Review Scenario
```python
multi_agent_review(
    target="/path/to/project",
    agents=[
        {"type": "security-auditor", "weight": 0.3},
        {"type": "architecture-reviewer", "weight": 0.3},
        {"type": "performance-analyst", "weight": 0.2}
    ]
)
```

### 2. Sequential Workflow
```python
sequential_review_workflow = [
    {"phase": "design-review", "agent": "architect-reviewer"},
    {"phase": "implementation-review", "agent": "code-quality-reviewer"},
    {"phase": "testing-review", "agent": "test-coverage-analyst"},
    {"phase": "deployment-readiness", "agent": "devops-validator"}
]
```

### 3. Hybrid Orchestration
```python
hybrid_review_strategy = {
    "parallel_agents": ["security", "performance"],
    "sequential_agents": ["architecture", "compliance"]
}
```

## Reference Implementations

1. **Web Application Security Review**
2. **Microservices Architecture Validation**

## Best Practices and Considerations

- Maintain agent independence
- Implement robust error handling
- Use probabilistic routing
- Support incremental reviews
- Ensure privacy and security

## Extensibility

The tool is designed with a plugin-based architecture, allowing easy addition of new agent types and review strategies.

## Invocation

Target for review: $ARGUMENTS

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [agent-orchestration-multi-agent-optimize](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/agent-orchestration-multi-agent-optimize/SKILL.md) - Optimize multi-agent systems with coordinated profiling, workload distribution, and cost-aware orche...
- [comprehensive-review-full-review](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/comprehensive-review-full-review/SKILL.md) - Use when working with comprehensive review full review
- [context-manager](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/context-manager/SKILL.md) - Elite AI context engineering specialist mastering dynamic context management, vector databases, know...
- [debugging-toolkit-smart-debug](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/debugging-toolkit-smart-debug/SKILL.md) - Use when working with debugging toolkit smart debug
- [distributed-tracing](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/distributed-tracing/SKILL.md) - Implement distributed tracing with Jaeger and Tempo to track requests across microservices and ident...
- [e2e-testing-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/e2e-testing-patterns/SKILL.md) - Master end-to-end testing with Playwright and Cypress to build reliable test suites that catch bugs,...
- [performance-testing-review-multi-agent-review](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/performance-testing-review-multi-agent-review/SKILL.md) - Use when working with performance testing review multi agent review
- [incident-response-incident-response](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/incident-response-incident-response/SKILL.md) - Use when working with incident response incident response
- [error-detective](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/error-detective/SKILL.md) - Search logs and codebases for error patterns, stack traces, and anomalies. Correlates errors across ...
- [error-diagnostics-smart-debug](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/error-diagnostics-smart-debug/SKILL.md) - Use when working with error diagnostics smart debug
- [puppeteer-pdf-rendering](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/puppeteer-pdf-rendering/SKILL.md) - Master server-side PDF generation with Puppeteer (Node.js) or WeasyPrint (Python). Use for complex m...
- [rust-async-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/rust-async-patterns/SKILL.md) - Master Rust async programming with Tokio, async traits, error handling, and concurrent patterns. Use...
- [screen-reader-testing](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/screen-reader-testing/SKILL.md) - Test web applications with screen readers including VoiceOver, NVDA, and JAWS. Use when validating s...
- [supabase-edge-function-monitoring](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/supabase-edge-function-monitoring/SKILL.md) - Monitor Supabase Edge Function health, error rates, and invocation limits. Use when operating produc...
- [tdd-orchestrator](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tdd-orchestrator/SKILL.md) - Master TDD orchestrator specializing in red-green-refactor discipline, multi-agent workflow coordina...

