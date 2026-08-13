# FabricTrace

**An open specification for discrete tracing in AI compute fabrics.**

FabricTrace helps AI-SRE and platform teams investigate why a distributed GPU job or inference batch slowed down by reconstructing what happened across schedulers, runtimes, accelerators, NICs, switches, and physical topology.

It is designed for asynchronous, zero-copy AI infrastructure: GPU clusters, RDMA/RoCE or InfiniBand networks, NVLink-class fabrics, distributed training, and disaggregated inference.

## The problem

A production incident often leaves evidence in separate tools:

- application traces show that an inference request was slow;
- the scheduler shows where its workload ran;
- NCCL or runtime logs show a collective delay;
- GPU telemetry shows device and link counters; and
- NIC and switch telemetry show transport or congestion conditions.

An operator must manually combine these views to answer a critical question:

> Which infrastructure condition affected this workload, and what evidence supports that conclusion?

Traditional distributed tracing was designed around context propagation and spans created in an application's request path. That model does not fit every asynchronous accelerator operation or zero-copy fabric transfer. It can require instrumentation, context propagation, or synchronization at exactly the layer that must remain fast.

## What FabricTrace does

FabricTrace specifies **discrete tracing**:

> Independent systems emit immutable, timestamped execution facts. A collector reconstructs a topology-aware fabric execution graph, diagnostics, and optional OpenTelemetry spans afterward.

```text
Scheduler ──┐
Runtime ───┼── local facts ──> collector / reconstructor ──> execution graph
GPU ───────┤                         │
NIC ───────┤                         └── diagnostics, metrics, optional OTLP
Switch ────┤
Topology ──┘
```

The key principle: FabricTrace does not require trace headers in tensors, KV-cache blocks, RDMA payloads, or fabric packets. It does not construct spans in the workload data path.

## Example: diagnosing a slow collective

A multi-node training job reports a slow or failed collective.

FabricTrace can assemble the available evidence:

1. The scheduler records the job's ranks and placement.
2. The runtime reports collective lifecycle facts for each participating rank.
3. GPU and NIC sources report activity, completion, and counter facts.
4. The topology snapshot connects ranks to GPUs, PCIe roots, NICs, ports, and fabric links.
5. The collector reconstructs the execution graph and identifies measured conditions—for example, a link error increase, a lagging rank, or a shared-NIC contention window.

The resulting view distinguishes:

- **what was measured** — for example, a counter delta or link-state change;
- **what was deterministically joined** — for example, a collective and its participating ranks; and
- **what is a diagnostic hypothesis** — for example, that congestion likely contributed to the stall.

It never presents a hypothesis as a fact.

## What this enables

FabricTrace V1 focuses on three operational problems:

- **Collective stalls:** explain which ranks, accelerators, transport endpoints, and fabric conditions were involved in a slow or failed collective.
- **Inference tail latency:** connect a slow prefill or decode batch to placement, accelerator queueing, transport activity, and fabric health.
- **Placement and noisy neighbors:** test whether performance degradation aligns with shared GPUs, NICs, NUMA nodes, PCIe roots, or fabric paths.

## Design principles

- **Facts first; spans are derived.**
- **No workload data-path mutation.**
- **No observability-induced CPU-GPU synchronization or collective blocking.**
- **Topology is a versioned graph referenced by facts.**
- **Join keys are opaque and control-plane-only.**
- **Every relationship has provenance and an evidence class.**
- **Sampling, loss, and clock uncertainty are explicit.**
- **Vendor-neutral core with pluggable adapters.**
- **OpenTelemetry interoperability, not replacement.**

## What FabricTrace is not

FabricTrace is not a replacement for OpenTelemetry, Prometheus, DCGM, CUPTI, NCCL/RCCL, eBPF, or your scheduler. Those systems provide important source signals.

FabricTrace defines how to model and reconstruct the cross-layer execution evidence they expose. It also does not claim zero total overhead: collection consumes resources. The objective is **zero workload critical-path impact**—no payload mutation, synchronization, or telemetry-induced blocking.

## Adoption model

FabricTrace supports progressively stronger visibility:

| Mode | Data sources | Result |
|---|---|---|
| Passive | Existing scheduler, runtime, device, NIC, switch, and topology telemetry | Definitive local facts and topology-aware investigation |
| Correlated | Passive sources plus opaque keys at batch or collective boundaries | Deterministic joins across runtime, device, and transport where supported |
| Forensic | Opt-in, bounded high-fidelity capture | Greater incident detail with explicitly documented overhead |

## Project status

FabricTrace is in the RFC design phase. The first specification defines the V1 model, constraints, and reference-implementation scope:

- [RFC-001: Discrete Tracing for AI Compute Fabrics](./RFCs/RFC-001.md)

We are seeking feedback from AI-SREs, platform engineers, distributed-runtime maintainers, kernel and driver developers, scheduler maintainers, and network/fabric operators.

## Contributing

Please read [CONTRIBUTING.md](./CONTRIBUTING.md), then open an issue or pull request with architectural feedback, incident examples, adapter proposals, or schema changes grounded in real operating conditions.
