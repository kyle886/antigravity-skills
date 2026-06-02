---
name: conductor-manage
description: "Manage track lifecycle: archive, restore, delete, rename, and cleanup"
metadata:
  argument-hint: "[--archive | --restore | --delete | --rename | --list | --cleanup]"
---

# Track Manager

Manage the complete track lifecycle including archiving, restoring, deleting, renaming, and cleaning up orphaned artifacts.

## Use this skill when

- Archiving, restoring, renaming, or deleting Conductor tracks
- Listing track status or cleaning orphaned artifacts
- Managing the track lifecycle across active, completed, and archived states

## Do not use this skill when

- Conductor is not initialized in the repository
- You lack permission to modify track metadata or files
- The task is unrelated to Conductor track management

## Instructions

- Verify `conductor/` structure and required files before proceeding.
- Determine the operation mode from arguments or interactive prompts.
- Confirm destructive actions (delete/cleanup) before applying.
- Update `tracks.md` and metadata consistently.
- If detailed steps are required, open `resources/implementation-playbook.md`.

## Safety

- Backup track data before delete operations.
- Avoid removing archived tracks without explicit approval.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [track-management](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/track-management/SKILL.md) - Use this skill when creating, managing, or working with Conductor tracks - the logical work units fo...
- [conductor-new-track](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/conductor-new-track/SKILL.md) - Create a new track with specification and phased implementation plan
- [conductor-status](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/conductor-status/SKILL.md) - Display project status, active tracks, and next actions
- [conductor-revert](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/conductor-revert/SKILL.md) - Git-aware undo by logical work unit (track, phase, or task)

