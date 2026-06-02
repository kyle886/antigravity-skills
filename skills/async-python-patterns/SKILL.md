---
name: async-python-patterns
description: Master Python asyncio, concurrent programming, and async/await patterns for high-performance applications. Use when building async APIs, concurrent systems, or I/O-bound applications requiring non-blocking operations.
---

# Async Python Patterns

Comprehensive guidance for implementing asynchronous Python applications using asyncio, concurrent programming patterns, and async/await for building high-performance, non-blocking systems.

## Use this skill when

- Building async web APIs (FastAPI, aiohttp, Sanic)
- Implementing concurrent I/O operations (database, file, network)
- Creating web scrapers with concurrent requests
- Developing real-time applications (WebSocket servers, chat systems)
- Processing multiple independent tasks simultaneously
- Building microservices with async communication
- Optimizing I/O-bound workloads
- Implementing async background tasks and queues

## Do not use this skill when

- The workload is CPU-bound with minimal I/O.
- A simple synchronous script is sufficient.
- The runtime environment cannot support asyncio/event loop usage.

## Instructions

- Clarify workload characteristics (I/O vs CPU), targets, and runtime constraints.
- Pick concurrency patterns (tasks, gather, queues, pools) with cancellation rules.
- Add timeouts, backpressure, and structured error handling.
- Include testing and debugging guidance for async code paths.
- If detailed examples are required, open `resources/implementation-playbook.md`.

Refer to `resources/implementation-playbook.md` for detailed patterns and examples.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [rust-async-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/rust-async-patterns/SKILL.md) - Master Rust async programming with Tokio, async traits, error handling, and concurrent patterns. Use...
- [dotnet-backend-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/dotnet-backend-patterns/SKILL.md) - Master C#/.NET backend development patterns for building robust APIs, MCP servers, and enterprise ap...
- [fastapi-pro](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/fastapi-pro/SKILL.md) - Build high-performance async APIs with FastAPI, SQLAlchemy 2.0, and Pydantic V2. Master microservice...
- [bash-defensive-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/bash-defensive-patterns/SKILL.md) - Master defensive Bash programming techniques for production-grade scripts. Use when writing robust s...

