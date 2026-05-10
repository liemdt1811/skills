# Skills

A library of Claude Code skills that encode engineering discipline so Claude follows it by default.

## Why

Out of the box Claude writes code competently, but shipping well needs more than that: investigate before building, design the interface before writing, test-first, review at PR boundaries, and verify before merge.

These skills bake those habits into Claude's defaults. Each one fires on its own trigger phrases — `tdd` when a task starts with a feature, `prod-ready` before merge, `pr-review` on someone else's PR, and so on — instead of waiting for me to remember to ask.

The goal: turn engineering discipline from something I have to enforce into something Claude reaches for automatically.

## Where things live

- [`skills/`](./skills/) — the skill set. See [`skills/README.md`](./skills/README.md) for the index, the role/trigger taxonomy, and how skills compose into workflows.
