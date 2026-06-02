# TEST-AUTOMATOR Playbook

Implementation guidelines, checklists, and code patterns for **test-automator**.

## 1. Technical Implementation Checklist

- [ ] Verify environment prerequisites and dependencies are loaded
- [ ] Implement core logic conforming to test-automator architectural patterns
- [ ] Add rigorous error handling and validation pathways
- [ ] Run verification tests and code quality audits

## 2. Core Best Practices

- **Modularity:** Keep components focused, reusable, and single-purpose.
- **Safety & Robustness:** Never suppress errors; report failure contexts explicitly.
- **Observability:** Integrate semantic logging, error traces, and performance telemetry.

## 3. Code Scaffolds & Templates

```bash
# Implementation commands
# Verify environment setup and run project checks
npm test || pytest || cargo test
```
