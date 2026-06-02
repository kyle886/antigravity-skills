---
name: billing-automation
description: Build automated billing systems for recurring payments, invoicing, subscription lifecycle, and dunning management. Use when implementing subscription billing, automating invoicing, or managing recurring payment systems.
---

# Billing Automation

Master automated billing systems including recurring billing, invoice generation, dunning management, proration, and tax calculation.

## Use this skill when

- Implementing SaaS subscription billing
- Automating invoice generation and delivery
- Managing failed payment recovery (dunning)
- Calculating prorated charges for plan changes
- Handling sales tax, VAT, and GST
- Processing usage-based billing
- Managing billing cycles and renewals

## Do not use this skill when

- You only need a one-off invoice or manual billing
- The task is unrelated to billing or subscriptions
- You cannot change pricing, plans, or billing flows

## Instructions

- Define plans, pricing, billing intervals, and proration rules.
- Map subscription lifecycle states and renewal/cancellation behavior.
- Implement invoicing, payments, retries, and dunning workflows.
- Model taxes and compliance requirements per region.
- Validate with sandbox payments and reconcile ledger outputs.
- If detailed templates are required, open `resources/implementation-playbook.md`.

## Safety

- Do not charge real customers in testing environments.
- Verify tax handling and compliance obligations before production rollout.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [stripe-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/stripe-integration/SKILL.md) - Implement Stripe payment processing for robust, PCI-compliant payment flows including checkout, subs...
- [context-management-context-restore](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/context-management-context-restore/SKILL.md) - Use when working with context management context restore
- [context-management-context-save](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/context-management-context-save/SKILL.md) - Use when working with context management context save
- [payment-integration](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/payment-integration/SKILL.md) - Integrate Stripe, PayPal, and payment processors. Handles checkout flows, subscriptions, webhooks, a...

