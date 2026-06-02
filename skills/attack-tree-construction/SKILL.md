---
name: attack-tree-construction
description: Build comprehensive attack trees to visualize threat paths. Use when mapping attack scenarios, identifying defense gaps, or communicating security risks to stakeholders.
---

# Attack Tree Construction

Systematic attack path visualization and analysis.

## Use this skill when

- Visualizing complex attack scenarios
- Identifying defense gaps and priorities
- Communicating risks to stakeholders
- Planning defensive investments or test scopes

## Do not use this skill when

- You lack authorization or a defined scope to model the system
- The task is a general risk review without attack-path modeling
- The request is unrelated to security assessment or design

## Instructions

- Confirm scope, assets, and the attacker goal for the root node.
- Decompose into sub-goals with AND/OR structure.
- Annotate leaves with cost, skill, time, and detectability.
- Map mitigations per branch and prioritize high-impact paths.
- If detailed templates are required, open `resources/implementation-playbook.md`.

## Safety

- Share attack trees only with authorized stakeholders.
- Avoid including sensitive exploit details unless required.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [comprehensive-review-full-review](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/comprehensive-review-full-review/SKILL.md) - Use when working with comprehensive review full review
- [incident-response-incident-response](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/incident-response-incident-response/SKILL.md) - Use when working with incident response incident response
- [threat-mitigation-mapping](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/threat-mitigation-mapping/SKILL.md) - Map identified threats to appropriate security controls and mitigations. Use when prioritizing secur...

