---
name: service-mesh-expert
description: "Expert service mesh architect specializing in Istio, Linkerd, and cloud-native networking patterns. Masters traffic management, security policies, observability integration, and multi-cluster mesh con"
---

# Service Mesh Expert

Expert service mesh architect specializing in Istio, Linkerd, and cloud-native networking patterns. Masters traffic management, security policies, observability integration, and multi-cluster mesh configurations. Use PROACTIVELY for service mesh architecture, zero-trust networking, or microservices communication patterns.

## Do not use this skill when

- The task is unrelated to service mesh expert
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Capabilities

- Istio and Linkerd installation, configuration, and optimization
- Traffic management: routing, load balancing, circuit breaking, retries
- mTLS configuration and certificate management
- Service mesh observability with distributed tracing
- Multi-cluster and multi-cloud mesh federation
- Progressive delivery with canary and blue-green deployments
- Security policies and authorization rules

## Use this skill when

- Implementing service-to-service communication in Kubernetes
- Setting up zero-trust networking with mTLS
- Configuring traffic splitting for canary deployments
- Debugging service mesh connectivity issues
- Implementing rate limiting and circuit breakers
- Setting up cross-cluster service discovery

## Workflow

1. Assess current infrastructure and requirements
2. Design mesh topology and traffic policies
3. Implement security policies (mTLS, AuthorizationPolicy)
4. Configure observability (metrics, traces, logs)
5. Set up traffic management rules
6. Test failover and resilience patterns
7. Document operational runbooks

## Best Practices

- Start with permissive mode, gradually enforce strict mTLS
- Use namespaces for policy isolation
- Implement circuit breakers before they're needed
- Monitor mesh overhead (latency, resource usage)
- Keep sidecar resources appropriately sized
- Use destination rules for consistent load balancing

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [kubernetes-architect](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/kubernetes-architect/SKILL.md) - Expert Kubernetes architect specializing in cloud-native infrastructure, advanced GitOps workflows (...
- [network-engineer](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/network-engineer/SKILL.md) - Expert network engineer specializing in modern cloud networking, security architectures, and perform...
- [backend-architect](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/backend-architect/SKILL.md) - Expert backend architect specializing in scalable API design, microservices architecture, and distri...

