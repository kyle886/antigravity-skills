---
name: supabase-edge-function-monitoring
description: Monitor Supabase Edge Function health, error rates, and invocation limits. Use when operating production Supabase backends, debugging edge function failures, or proactively monitoring usage against free/pro tier limits.
---

# Supabase Edge Function Monitoring

Health monitoring and operational observability for Supabase Edge Functions.

## Use this skill when

- Operating a production Supabase backend
- Debugging edge function errors or timeouts
- Monitoring invocation counts against tier limits
- Setting up alerts for edge function failures
- Auditing edge function security and performance

## Do not use this skill when

- Writing new edge functions (use Supabase docs instead)
- Monitoring database performance (use Supabase dashboard)
- You need general API monitoring (use `performance-engineer`)

## Instructions

### Phase 1: Inventory

1. List all deployed edge functions:

```bash
supabase functions list --project-ref <project_id>
```

Or via the Supabase MCP tool: `list_edge_functions`

2. For each function, document:
   - Name and slug
   - Purpose (auth, data processing, webhook, etc.)
   - JWT verification status (verify_jwt: true/false)
   - Expected invocation frequency
   - Dependencies and external API calls

### Phase 2: Health Checks

3. Check function logs for errors:

Use the Supabase MCP tool: `get_logs` with service `edge-function`

4. Look for these common issues:
   - **Boot errors**: Import failures, missing environment variables
   - **Runtime errors**: Unhandled exceptions, timeout errors
   - **Auth errors**: Missing or invalid JWT tokens
   - **Rate limit errors**: HTTP 429 responses
   - **Payload errors**: Invalid request body, missing fields

5. Monitor invocation limits:

| Tier | Invocations/Month | Execution Time | Memory |
| ---- | ----------------- | -------------- | ------ |
| Free | 500,000           | 150ms average  | 256MB  |
| Pro  | 2,000,000         | 150ms average  | 256MB  |
| Team | 2,000,000+        | Custom         | 256MB  |

### Phase 3: Security Audit

6. For each function, verify:
   - [ ] `verify_jwt: true` unless there's a documented reason
   - [ ] No secrets/keys logged or exposed in responses
   - [ ] Error responses don't include stack traces
   - [ ] Input validation (Zod or equivalent) on request body
   - [ ] CORS headers are restrictive (not `*`)
   - [ ] Rate limiting is enforced

### Phase 4: Performance Baseline

7. Measure cold start and warm response times:

```bash
# Cold start (first invocation after idle)
time curl -X POST https://<project>.supabase.co/functions/v1/<function> \
  -H "Authorization: Bearer <anon_key>" \
  -H "Content-Type: application/json"

# Warm response (subsequent invocations)
for i in {1..5}; do
  time curl -s -X POST https://<project>.supabase.co/functions/v1/<function> \
    -H "Authorization: Bearer <anon_key>" \
    -H "Content-Type: application/json" > /dev/null
done
```

8. Document baseline metrics for comparison:
   - Cold start time
   - Warm response time (p50, p95)
   - Error rate (errors / total invocations)

### Phase 5: Alerting (Recommendations)

9. Set up monitoring:
   - Check Supabase Dashboard → Edge Functions → Logs regularly
   - Set up Supabase Advisor checks for security issues
   - Consider external uptime monitoring (UptimeRobot, Checkly) for critical functions

## Safety

- Never expose real JWT tokens or API keys in logs or documentation
- Do not invoke functions with test data in production without a cleanup plan
- Be cautious with functions that trigger emails or external API calls
