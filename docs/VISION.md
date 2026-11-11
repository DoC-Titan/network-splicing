# Vision

## Mission

Make every available internet connection useful, while letting users decide exactly which applications can use it.

Network Splicing should answer three questions clearly:

1. Which connections are available and healthy?
2. Which networks is each application allowed to use?
3. Is the chosen policy actually improving performance or reliability?

## Who it is for

- Users combining home broadband with mobile tethering for large transfers.
- Creators uploading large files over multiple independent connections.
- Users protecting a limited mobile-data allowance from background traffic.
- People who want predictable application routing instead of changing system-wide routes.
- Developers and enthusiasts experimenting with self-hosted bonding infrastructure.

## Product principles

### Control must be real

A saved preference is not an enforced policy. The interface must distinguish configured, active, degraded, unsupported, and blocked states. Excluded networks must remain excluded even when an allowed link fails.

### Performance must be measured

Show useful application throughput separately from adapter traffic and tunnel overhead. Compare bonding against the fastest individual connection under comparable conditions.

### Different applications need different policies

A game, a browser, and a bulk upload have different needs. Support explicit modes rather than presenting one universal optimization switch.

### Metered connections deserve explicit controls

Make mobile-data eligibility, usage, limits, and fallback behavior visible. Account for tunnel overhead when enforcing physical-link budgets; local counters may differ from carrier billing.

### Explain degraded behavior

If a path fails, a data budget is exhausted, or bonding offers no benefit, explain what happened and which policy is now active.

### Keep infrastructure choices open

Begin with a self-hostable relay prototype. Evaluate a managed relay service only after understanding operating costs and reliability needs.

## Initial product boundary

- Windows 11 client.
- Ethernet, Wi-Fi, and USB tethering as IP-capable adapters.
- Application selection through running processes and executable paths.
- Direct pinning and relay-backed bonding as distinct experiences.
- Two-link bonding before generalizing to larger combinations.
- One relay location for initial experiments.

Cross-platform clients, global relay operations, and automatic application optimization are later possibilities, not initial commitments.

## Example user journey

1. Open the dashboard and see Ethernet, Wi-Fi, and USB tethering.
2. Select a download application.
3. Allow Ethernet and USB tethering and choose bonding.
4. Set a tethering budget and the behavior when that budget runs out.
5. Start a new download and see application throughput plus each path's contribution.
6. Disconnect Ethernet and observe the active policy, recovery, and any interruption.

## What success looks like

- Policies demonstrably prevent application traffic from escaping onto excluded interfaces.
- A single conventional transfer benefits from two independent links in a documented test environment.
- Link failures produce predictable recovery or blocking behavior.
- Users can tell whether bonding is helping and what mobile data it costs.
- Installation, shutdown, and removal leave Windows networking in a usable state.
