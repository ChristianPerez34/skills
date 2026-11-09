---
name: find-implicit-state-machines
description: Map implicit state machines in application code, especially workflows spread across flags, statuses, async effects, lifecycle logic, or conditional UI. Use when the user asks which implicit state machines or hidden transitions exist in an app.
---

# Find implicit state machines

Keep the investigation read-only. An implicit state machine is behavior where the next state or effects depend on the current state plus an event or condition, but the transitions are spread across the code rather than represented by one authoritative model. A state field by itself is not a machine; establish temporal behavior.

## Process

1. **Bound the scope.** Read repository instructions and app docs/config; map app-owned production roots and entry points. Exclude generated, vendored, and dependency code by default; use tests to corroborate production behavior. **Done when** every production root is in scope and every exclusion is recorded.

2. **Sweep state carriers.** Search by code patterns, not just names: stores/reducers/component state; booleans, enums, nullable fields and their writes; domain or persisted `status`/`phase` fields; event handlers and routes; requests, retries, cancellation, optimistic updates; subscriptions, timers, lifecycle callbacks, caches, and job queues. Trace each candidate's reads and writes through its consumers and across relevant app layers. **Done when** every discovered carrier is classified as a machine candidate, an explicit machine, a non-machine with a reason, or an unresolved candidate with missing evidence recorded.

3. **Reconstruct transitions.** For each candidate, identify initial/restored states, events, guards, next states, side effects, and observable outputs. Trace async races, errors/retries, cancellation, persistence, and invalid combinations where present. Split independent lifecycles; combine fields only when the code couples them. An explicit machine centralizes its states and transitions; a partial machine centralizes the main state but scatters some transitions or effects; an implicit machine lacks an authoritative transition model. Mark unproven reachability and external behavior as inferred or unknown. **Done when** every implicit or partial candidate has evidence-backed states and known transitions, with unresolved edges called out.

4. **Verify coverage.** Re-sweep every production root for unaccounted state writes, status/phase changes, and event-driven conditional behavior; trace each new candidate through steps 2–3. **Done when** every root is accounted for and every candidate has a disposition.

5. **Report the map.** Include:
   - Scope, coverage, and exclusions.
   - Every implicit or partial machine, with its carriers and `path:line` evidence.
   - States and transitions (`from | event/guard | to | effect/output`), plus why the machine is implicit or partial.
   - Invariants, risky or invalid combinations, confidence, and unresolved boundaries where relevant.
   - Explicit machines, non-machine candidates, and unresolved candidates, with brief reasons, so no lead disappears from the inventory.

Keep the report diagnostic; reserve refactoring recommendations for when the user asks. Never claim runtime reachability from static code alone; distinguish observed transitions from inferred ones. The map is complete only when every app-owned production root and discovered state carrier has a disposition, and every unresolved transition or external boundary is listed.
