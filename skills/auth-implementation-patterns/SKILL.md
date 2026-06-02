---
name: auth-implementation-patterns
description: Master authentication and authorization patterns including JWT, OAuth2, session management, and RBAC to build secure, scalable access control systems. Use when implementing auth systems, securing APIs, or debugging security issues.
---

# Authentication & Authorization Implementation Patterns

Build secure, scalable authentication and authorization systems using industry-standard patterns and modern best practices.

## Use this skill when

- Implementing user authentication systems
- Securing REST or GraphQL APIs
- Adding OAuth2/social login or SSO
- Designing session management or RBAC
- Debugging authentication or authorization issues

## Do not use this skill when

- You only need UI copy or login page styling
- The task is infrastructure-only without identity concerns
- You cannot change auth policies or credential storage

## Instructions

- Define users, tenants, flows, and threat model constraints.
- Choose auth strategy (session, JWT, OIDC) and token lifecycle.
- Design authorization model and policy enforcement points.
- Plan secrets storage, rotation, logging, and audit requirements.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Safety

- Never log secrets, tokens, or credentials.
- Enforce least privilege and secure storage for keys.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [context-management-context-restore](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/context-management-context-restore/SKILL.md) - Use when working with context management context restore
- [context-management-context-save](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/context-management-context-save/SKILL.md) - Use when working with context management context save
- [rust-async-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/rust-async-patterns/SKILL.md) - Master Rust async programming with Tokio, async traits, error handling, and concurrent patterns. Use...
- [error-handling-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/error-handling-patterns/SKILL.md) - Master error handling patterns across languages including exceptions, Result types, error propagatio...
- [k8s-security-policies](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/k8s-security-policies/SKILL.md) - Implement Kubernetes security policies including NetworkPolicy, PodSecurityPolicy, and RBAC for prod...
- [tailwind-design-system](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tailwind-design-system/SKILL.md) - Build scalable design systems with Tailwind CSS, design tokens, component libraries, and responsive ...

