---
name: cqrs-implementation
description: Implement Command Query Responsibility Segregation for scalable architectures. Use when separating read and write models, optimizing query performance, or building event-sourced systems.
---

# CQRS Implementation

Comprehensive guide to implementing CQRS (Command Query Responsibility Segregation) patterns.

## Use this skill when

- Separating read and write concerns
- Scaling reads independently from writes
- Building event-sourced systems
- Optimizing complex query scenarios
- Different read/write data models are needed
- High-performance reporting is required

## Do not use this skill when

- The domain is simple and CRUD is sufficient
- You cannot operate separate read/write models
- Strong immediate consistency is required everywhere

## Instructions

- Identify read/write workloads and consistency needs.
- Define command and query models with clear boundaries.
- Implement read model projections and synchronization.
- Validate performance, recovery, and failure modes.
- If detailed patterns are required, open `resources/implementation-playbook.md`.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [projection-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/projection-patterns/SKILL.md) - Build read models and projections from event streams. Use when implementing CQRS read sides, buildin...
- [event-store-design](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/event-store-design/SKILL.md) - Design and implement event stores for event-sourced systems. Use when building event sourcing infras...
- [microservices-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/microservices-patterns/SKILL.md) - Design microservices architectures with service boundaries, event-driven communication, and resilien...

