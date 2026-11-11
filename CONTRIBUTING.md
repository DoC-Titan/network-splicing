# Contributing

Network Splicing is currently a design-stage project. There is no runnable implementation or build/test command yet.

## Useful contributions now

- Review the architecture and identify concrete Windows networking constraints.
- Suggest user workflows for per-application policies.
- Design reproducible adapter-routing and multipath experiments.
- Improve the roadmap, terminology, and documented limitations.
- Share benchmark methodology with enough topology detail to reproduce results.

Before a substantial implementation, open a discussion or issue describing the problem, proposed approach, dependencies, and how correctness will be verified. Licensing must be selected before implementation contributions are accepted.

## Engineering expectations

- Distinguish planned behavior from implemented and verified behavior.
- Never present a saved UI rule as enforced without a working networking path.
- Validate both directions of application network restrictions.
- Compare bonding to the fastest individual-link baseline.
- Use established cryptographic components.
- Keep credentials, access tokens, private keys, and identifying packet captures out of commits.
- Document privileges, installation requirements, and cleanup for experiments.

## Reporting future bugs

Include the Windows version, application version, adapter types, policy, expected behavior, observed behavior, and reproducible steps. Redact credentials and personal network information from logs before sharing them.
