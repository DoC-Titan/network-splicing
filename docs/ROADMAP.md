# Roadmap

All milestones are planned. No implementation work is claimed complete by this document.

Estimates are rough engineering ranges for a small team with Windows networking experience. They are not release commitments; driver and transport discoveries may change scope substantially.

## M0 — Feasibility lab

**Indicative effort: 2–4 weeks.**

- [ ] Inventory available adapters and independently verify their upstream paths.
- [ ] Send test traffic through specifically selected interfaces.
- [ ] Attribute representative TCP and UDP flows to applications.
- [ ] Prototype an enforcement mechanism for excluding interfaces.
- [ ] Transport test traffic across two paths to one relay.
- [ ] Document driver, privileges, signing, and deployment requirements.
- [ ] Select project licensing before accepting implementation contributions.

**Exit criteria:** reproducible evidence of adapter selection and flow attribution; a documented enforcement approach; measured two-path transport results and a decision on whether to proceed.

## M1 — Discovery and policy foundation

**Indicative effort: 2–4 weeks.**

- [ ] Create a Windows application shell and background service.
- [ ] List physical adapters and display traffic counters.
- [ ] List running applications and support executable browsing.
- [ ] Create, validate, persist, and reload policies.
- [ ] Distinguish configured policies from actively enforced policies in the UI.
- [ ] Add an adapter-specific connectivity diagnostic.

**Exit criteria:** real adapter/application discovery and persistent policies; enforcement status is accurate. A settings screen alone does not qualify as routing functionality.

## M2 — Enforced application routing

**Indicative effort: 4–8 weeks.**

- [ ] Pin supported application flows to a selected adapter.
- [ ] Enforce explicit block-or-fallback behavior.
- [ ] Define policy precedence and unmatched-application defaults.
- [ ] Handle DNS and IPv6 consistently with supported policy guarantees.
- [ ] Show which policies require applications to reconnect.
- [ ] Recover cleanly after service restart and adapter reconnection.

**Exit criteria:** packet captures or equivalent independent observations demonstrate that test applications use only allowed adapters, including failure cases. Unsupported attribution cases are documented.

## M3 — Two-link true bonding

**Indicative effort: 6–10 weeks.**

- [ ] Deploy one authenticated relay with reproducible setup instructions.
- [ ] Implement the virtual-adapter tunnel path.
- [ ] Maintain encrypted transport paths over two independent adapters.
- [ ] Implement bounded reordering, scheduling, and loss handling.
- [ ] Support download and upload aggregation.
- [ ] Exercise TCP and UDP application traffic.
- [ ] Test continuity when one physical link disappears.

**Exit criteria:** a single ordinary application transfer exceeds the fastest individual-link baseline in a repeatable, suitable test setup; overhead and adverse cases are reported. Document interruption duration and session outcome during link failures.

## M4 — Per-application bonding groups

**Indicative effort: 4–8 weeks.**

- [ ] Support different allowed-network combinations simultaneously.
- [ ] Expand to three or more adapters.
- [ ] Add connection-level balancing and explicit backup priorities.
- [ ] Enforce metered-data budgets and application rate limits.
- [ ] Add per-application and per-adapter usage views.
- [ ] Validate both outgoing and return-path isolation.

**Exit criteria:** concurrent applications follow distinct network policies without cross-group leakage, including during budget exhaustion and path failure.

## M5 — Reliability beta

**Indicative effort: 6–10 weeks.**

- [ ] Improve scheduling for unequal links and cellular jitter.
- [ ] Test sleep/resume, address changes, captive portals, and tethering resets.
- [ ] Evaluate coexistence with VPNs, firewalls, and endpoint software.
- [ ] Complete installer, uninstall, update, and crash-recovery behavior.
- [ ] Establish signing and supported Windows-version requirements.
- [ ] Add actionable diagnostics and support documentation.
- [ ] Review local privilege boundaries and relay isolation.

**Exit criteria:** documented compatibility matrix, repeatable recovery behavior, bounded resource use, and installation/removal that preserves usable host networking.

## M6 — Production and service operations

Scope depends on whether the project remains self-hosted or adds a managed service.

- [ ] Evaluate regional relay placement and capacity needs.
- [ ] Measure bandwidth cost per active user.
- [ ] Add account provisioning and quotas where needed.
- [ ] Establish monitoring, incident response, and update operations.
- [ ] Define data retention and user-facing privacy documentation.
- [ ] Evaluate relay redundancy separately from client-link failover.

## Benchmark matrix

Test both download and upload across:

| Scenario | What to measure |
| --- | --- |
| One link at a time | Baseline application throughput, latency, CPU, and overhead |
| Similar independent links | Aggregation efficiency |
| Fast stable + slow high-latency link | Reordering cost and scheduler decisions |
| Lossy or fluctuating mobile path | Goodput, stalls, and recovery |
| Shared upstream links | Correct expectations when no extra WAN capacity exists |
| Adapter unplug/reconnect | Interruption duration and session survival |
| Distinct application groups | Outgoing and return-path policy isolation |
| Mobile budget exhausted | Accurate restriction/fallback behavior |
| Concurrent bulk traffic and latency-sensitive traffic | Fairness, latency, and queue growth |

Record topology, relay region, link baselines, test duration, repeated-run variability, and application goodput. Adapter byte counts alone are not proof of useful bandwidth aggregation.
