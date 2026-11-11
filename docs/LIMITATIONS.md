# Limitations and open questions

## Bandwidth is bounded by the whole path

An approximate ceiling is the minimum of combined usable access capacity, relay capacity, destination capacity, and client processing capacity, reduced by encapsulation overhead and scheduling/reordering losses.

A 100 Mbps link plus a 50 Mbps link does not guarantee a 150 Mbps application transfer. Shared congestion, destination rate limits, retransmissions, and CPU load all matter.

## Multiple adapters do not necessarily mean independent internet

Ethernet and Wi-Fi attached to the same router often share one broadband connection. Two mobile links may also share congested radio infrastructure. Separate adapter names do not prove separate upstream capacity.

## Local load balancing is not single-flow bonding

Distributing separate connections can improve total throughput when there are many transfers. It normally does not accelerate one conventional TCP connection. Specialized multi-connection downloaders can sometimes combine paths using range requests, but that depends on application/server support and is not a general solution for arbitrary applications.

## A remote aggregation endpoint is usually required

The proposed relay reconstructs traffic and provides a stable exit identity. It adds hosting cost, transit overhead, geographic latency, and an operational dependency.

## Latency and throughput can conflict

Using a high-jitter path can delay delivery of packets arriving on a faster path. Reordering buffers consume memory and add delay. The scheduler must be allowed to avoid a path when its contribution is harmful.

Bonding is not a promise of lower gaming ping. Path selection or explicitly budgeted redundancy may be more appropriate for latency-sensitive traffic.

## Failover has boundaries

- Direct sessions typically break when their source public IP changes.
- Relay-backed sessions can survive access-link changes, but outages and timeouts can still interrupt them.
- Losing every allowed path cannot be hidden indefinitely.
- A relay outage is not solved by having multiple client connections.

## Application identity is imperfect

Helper processes, shared services, app updates, containers, and app-controlled proxies complicate attribution. A path-based executable selector may need additional rules. Some shared-service traffic may not be reliably attributable to its original requesting application.

Policies need explicit scope rather than a blanket promise that selecting one executable covers every related process.

## DNS and IPv6 are part of routing correctness

Shared system DNS resolution and unhandled IPv6 traffic can undermine strict network restrictions. Supported guarantees must include these paths or explicitly block unsupported traffic.

## Mobile-data accounting is approximate relative to carrier billing

Tunnel headers, retransmissions, redundancy, and unrelated system traffic consume physical-link data. Local counters and carrier accounting may differ. Define whether a displayed number is application goodput, tunnel usage, or total adapter traffic.

## Windows networking integration is difficult

Robust enforcement may need a signed driver and administrator installation. Compatibility with other VPNs, firewalls, antivirus products, and Windows updates requires ongoing testing. Kernel components should be kept as small as practical.

## Relay IPs can change service behavior

Services see the relay's location and public IP for tunneled traffic. Some may block datacenter addresses, require additional authentication, or display different regional content. Direct and tunneled traffic can expose different public identities.

## Encryption does not eliminate relay trust

Tunnel encryption protects traffic between client and relay. The relay can still observe connection metadata and application traffic that is not independently encrypted. HTTPS remains important for end-to-end protection.

## Hardware and transport limits remain

A PC may need additional Wi-Fi hardware to connect to multiple wireless networks simultaneously. USB, CPU, Wi-Fi airtime, MTU, NAT behavior, and networks that restrict tunnel traffic can all become bottlenecks.

## Policy changes may require reconnection

Initial routing-policy updates should take effect on new flows. Moving existing direct connections between networks is generally disruptive. The interface must make this visible.

## Decisions still open

1. Which Windows interception approach gives reliable enforcement with the smallest driver surface?
2. Which established transport foundation behaves best under path asymmetry and loss?
3. How should unknown applications and shared-service traffic be treated by default?
4. What is the precise DNS policy for each operating mode?
5. Which component languages and desktop framework best fit the implementation team?
6. Which software license and dependency licenses fit the project's goals?
7. Should the first release be self-hosted only, or include managed relays?
8. What performance thresholds justify enabling an additional path automatically?
