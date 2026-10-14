# Chain Designs

This directory contains technical designs for integrating blockchains into Rosen Bridge. It includes designs for currently supported chains and proposals for future integrations.

## Purpose

Each document describes the proposed or existing integration of a specific blockchain, including relevant architectural decisions, technical considerations, and unresolved questions.

These documents are intended to facilitate technical discussion and review of chain integrations.

## Requirements

[ RCS-003 ](https://github.com/rosen-bridge/rcs/tree/master/rcs-003) defines the requirements for integrating new chains into Rosen Bridge.

Chain designs should reference the relevant requirements and explain how the proposed integration addresses them. Any gaps, limitations, or unresolved requirements should be clearly identified.

## Directory Structure

Each chain has its own directory containing its design document and any supporting materials.

```text
chain-designs/
├── README.md
├── <chain-name>/
│   ├── design.md
│   └── ...
└── ...
```

## Contributing

To propose an integration for a new blockchain:

1. Create a directory named after the blockchain under `chain-designs/`.
2. Add a `design.md` describing the proposed integration.
3. Reference relevant RCS-003 requirements and document important design decisions, assumptions, and unresolved questions.
4. Submit a pull request for discussion and review.

The structure and level of detail may vary depending on the blockchain and the maturity of the proposal. Supporting materials, such as architecture diagrams, may be included when useful.

Submitting a design does not imply that the chain has been approved for integration or that implementation will proceed.
