# FabricTrace Governance

## Purpose

FabricTrace is an open specification project. Its purpose is to develop interoperable, evidence-first observability standards for AI compute fabrics.

The project values technical rigor, operational usefulness, vendor neutrality, privacy, and low workload critical-path impact.

## Roles

### Contributors

Anyone may propose issues, RFCs, examples, test fixtures, adapters, or documentation improvements. Contributions are governed by the [Code of Conduct](./CODE_OF_CONDUCT.md).

### Maintainers

Maintainers steward the repository, review contributions, manage releases, and make final decisions when consensus cannot be reached. The initial maintainers are the repository owners; additional maintainers may be appointed through a documented maintainer decision.

## Decision-making

We prefer evidence-backed consensus reached in public issues, pull requests, and Discussions.

For material specification changes, contributors SHOULD use an RFC issue or pull request that states:

* the problem and target operators;
* proposed semantics and alternatives considered;
* performance, privacy, compatibility, and vendor-neutrality implications; and
* migration or conformance impact.

Maintainers make the final decision after allowing reasonable opportunity for review. When consensus is not possible, maintainers will record the decision and rationale in the relevant issue or pull request.

## Specification lifecycle

RFCs use these states:

* **Draft:** open for design feedback; semantics may change.
* **Accepted:** approved direction; implementation and conformance work may begin.
* **Implemented:** validated by at least one reference implementation or conformance suite.
* **Superseded:** replaced by a later RFC.

Changes that affect a stable or accepted schema require an explicit compatibility assessment.

## Discussions

GitHub Discussions is the preferred place for exploratory design before an RFC is ready. Enable Discussions in repository settings and create these categories:

* **Architecture** — discrete tracing, facts, topology, reconstruction, and evidence semantics.
* **Use cases** — incident narratives and operator requirements.
* **Adapters and integrations** — runtime, scheduler, accelerator, NIC, and switch source proposals.
* **Announcements** — maintainer-only project updates.

See [the repository discussion setup notes](./.github/DISCUSSIONS.md) for the recommended configuration.

## Releases

Specification releases use semantic versioning. The project publishes release notes that identify accepted RFCs, schema changes, compatibility implications, and deprecations.
