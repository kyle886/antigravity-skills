---
name: vector-index-tuning
description: Optimize vector index performance for latency, recall, and memory. Use when tuning HNSW parameters, selecting quantization strategies, or scaling vector search infrastructure.
---

# Vector Index Tuning

Guide to optimizing vector indexes for production performance.

## Use this skill when

- Tuning HNSW parameters
- Implementing quantization
- Optimizing memory usage
- Reducing search latency
- Balancing recall vs speed
- Scaling to billions of vectors

## Do not use this skill when

- You only need exact search on small datasets (use a flat index)
- You lack workload metrics or ground truth to validate recall
- You need end-to-end retrieval system design beyond index tuning

## Instructions

1. Gather workload targets (latency, recall, QPS), data size, and memory budget.
2. Choose an index type and establish a baseline with default parameters.
3. Benchmark parameter sweeps using real queries and track recall, latency, and memory.
4. Validate changes on a staging dataset before rolling out to production.

Refer to `resources/implementation-playbook.md` for detailed patterns, checklists, and templates.

## Safety

- Avoid reindexing in production without a rollback plan.
- Validate changes under realistic load before applying globally.
- Track recall regressions and revert if quality drops.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [core-web-vitals-audit](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/core-web-vitals-audit/SKILL.md) - Audit and optimize Core Web Vitals (LCP, CLS, INP) using Lighthouse, CrUX data, and PageSpeed Insigh...
- [pwa-optimization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/pwa-optimization/SKILL.md) - Optimize Progressive Web App service worker strategies, precache configuration, offline UX, and mani...
- [spark-optimization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/spark-optimization/SKILL.md) - Optimize Apache Spark jobs with partitioning, caching, shuffle optimization, and memory tuning. Use ...
- [embedding-strategies](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/embedding-strategies/SKILL.md) - Select and optimize embedding models for semantic search and RAG applications. Use when choosing emb...

