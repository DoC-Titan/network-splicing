# Proposed architecture

This document describes a design direction, not an implemented system. Frameworks, transport protocols, and driver choices remain subject to feasibility work.

## Components

### 1. Desktop interface

Network dashboard, application picker, policy editor, usage views, and diagnostics. Communicates with a privileged Windows service through authenticated, access-controlled local IPC. Closing the interface should not terminate active networking.

### 2. Windows service

- Discover adapters and track link/address changes.
- Manage policy lifecycle and application-flow mappings.
- Maintain relay paths through specific physical interfaces.
- Reconnect after sleep, address changes, and tethering resets.
- Collect health metrics and usage counters.
- Coordinate startup, recovery, and clean teardown.

Stable adapter identity should be distinguished from interface indexes and IP addresses, which can change.

### 3. Application classification and enforcement

Investigate Windows Filtering Platform for application-aware classification and enforcement. Evaluate which features can be implemented with existing components and which require a signed kernel driver.

A virtual adapter alone does not reliably retain the originating application identity. Associate application identity with a flow before traffic enters the shared tunnel, then retain the selected policy throughout the flow's lifetime.

Executable paths are a starting point, not a complete identity solution. Define handling for helper processes, service-hosted traffic, updates, path changes, and shared networking services. Do not promise arbitrary attribution of traffic generated on behalf of other applications.

### 4. Policy engine

Each policy should specify:

- Application selector and priority.
- Allowed adapters.
- Mode: pinning, balancing, bonding, failover, or future redundancy.
- Preferred and backup paths.
- Behavior when no eligible path is available.
- Metered-data eligibility and usage limits.
- Upload/download rate limits.
- Child-process handling where attribution supports it.

Illustrative policy, not a finalized configuration schema:

```json
{
  "application": "C:\\Apps\\Downloader\\downloader.exe",
  "mode": "bond",
  "allowedAdapters": ["ethernet-stable-id", "usb-stable-id"],
  "whenUnavailable": "block",
  "meteredBudget": {
    "adapter": "usb-stable-id",
    "dailyBytes": 5000000000
  }
}
```

Policy precedence must be deterministic. The initial implementation should apply routing changes to new flows and make reconnect requirements visible for existing flows.

### 5. Direct routing path

Pin or distribute flows through eligible interfaces without a relay where technically supported. Route-table edits alone are insufficient for robust per-application control. Prototype enforcement before committing to a mechanism.

Direct flows usually cannot preserve their sessions when switching to a different public source IP.

### 6. Virtual adapter and bonding transport

Capture traffic selected for relay-backed operation. Carry it through authenticated, encrypted paths bound to the intended physical adapters.

The design needs:

- Tunnel session and path identifiers.
- Replay protection and authenticated path membership.
- Packet numbering and bounded reordering.
- MTU discovery or conservative sizing and fragmentation handling.
- Congestion-aware scheduling and queue limits.
- Loss handling without uncontrolled retransmission amplification.
- Explicit treatment of TCP and UDP application traffic.

Use established cryptographic libraries and protocols. Benchmark transport choices; do not assume wrapping all traffic in one reliable stream will perform well, especially for TCP application traffic.

### 7. Bonding groups

Applications with different allowed-network sets need distinct policy contexts. Those contexts may share transport infrastructure only if the scheduler and return path preserve each flow's restrictions.

Example:

- Group A: Ethernet + Wi-Fi.
- Group B: Ethernet + USB tethering.
- Group C: Wi-Fi only.

Group A must never use USB merely because Group B has an active USB path. Return traffic must follow the same eligibility constraints.

### 8. Scheduler

Start with a measurable baseline and evolve toward adaptive scheduling based on bandwidth estimates, round-trip time, loss, queue depth, and jitter.

Balance application fairness with per-path congestion control. A poor path should be deprioritized when its reordering cost outweighs its contribution. Redundancy, if implemented, should be explicit because it consumes extra capacity.

### 9. Relay

A Linux-hosted relay terminates tunnel sessions, reconstructs traffic, forwards it to destinations, and schedules return packets across eligible client paths.

It needs authentication, session isolation, forwarding/NAT behavior, bounded buffers, resource quotas, cleanup, and operational metrics. It must not operate as an unauthenticated open proxy.

One relay provides a stable exit identity during access-link changes. Relay-server failure is a separate problem; access-path failover does not imply relay failover.

### 10. DNS and address families

Define DNS behavior explicitly, including Windows shared resolution and application-controlled DNS. Route or block IPv6 according to policy rather than allowing an unhandled address family to bypass restrictions.

Prototype strict-policy behavior for both IPv4 and IPv6, including failure cases.

## Stack decisions to validate

| Area | Candidate direction | Decision criterion |
| --- | --- | --- |
| Desktop UI | Native Windows UI or lightweight web-backed desktop shell | Packaging, accessibility, and team expertise |
| Background service / client core | Rust or C# with suitable native integration | Networking access, maintainability, and performance |
| Kernel integration, if required | Supported Windows driver toolchain | Minimum necessary kernel surface and signing path |
| Relay | Rust or Go on Linux | Transport support, throughput, and operability |
| Transport | Established encrypted protocol with multipath-aware framing | Loss, reordering, fairness, and interoperability benchmarks |
| Policy storage | Versioned local configuration | Atomic writes, migration, and recovery |

No technology in this table is a dependency or final commitment yet.

## Diagnostics and privacy

Record policy decisions, link transitions, counters, and errors. Avoid payload logging by default. Diagnostic export should make its contents visible to the user. Tunnel encryption protects the access path; destination HTTPS provides separate end-to-end application encryption.
