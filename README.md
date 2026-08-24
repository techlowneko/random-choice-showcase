# Random Choice

**Pay-per-use random and weighted selection for autonomous agents.**

Random Choice is a production Preflight Stack service designed to give AI agents and automated workflows a small, reusable decision primitive when they need one item selected from a bounded set of options.

A caller submits between 2 and 100 JSON-compatible choices and receives one selected result. Optional relative weights can be supplied when some choices should be more likely than others.

The service is delivered as an x402 pay-per-use API and currently costs **$0.001 USDC per paid request**.

> This repository is a public engineering case study. The production source code, randomness implementation, payment configuration, infrastructure, operational controls, analytics, and internal hardening remain private.

## The Problem

Autonomous software often reaches small decision points that do not justify a dedicated subsystem:

- Break a tie between acceptable actions
- Choose one candidate from a bounded set
- Apply a soft preference through weighted selection
- Sample one option as part of a larger workflow
- Introduce server-side randomness without maintaining custom selection logic

Those decisions are simple conceptually, but an agent still needs a reliable, machine-readable way to make them.

Random Choice turns that need into one focused API call.

## What It Does

Random Choice supports two selection modes.

### Uniform selection

Submit only `choices` and each option participates in an equal-probability selection.

```json
{
  "choices": ["alpha", "beta", "gamma"]
}
```

### Weighted selection

Provide one positive relative weight for each choice.

```json
{
  "choices": ["alpha", "beta", "gamma"],
  "weights": [7, 2, 1]
}
```

The weights are relative. They do not need to total 1 or 100.

In the example above, `alpha` receives 7 parts of the total weight, `beta` receives 2, and `gamma` receives 1.

## Designed for Agent Workflows

Random Choice is intentionally narrow.

```text
Agent / Application
        │
        ▼
   Choice Set
        │
        ├── Uniform
        │      or
        └── Weighted
        │
        ▼
 Random Choice API
        │
        ▼
 Structured Result
        │
        ▼
  Agent continues
```

The service is meant to behave like a small composable primitive rather than a general-purpose decision platform.

A caller can use it when randomness is useful, receive a structured result, and continue the surrounding workflow without creating a persistent customer account or subscription relationship.

## API

### Endpoint

```text
POST https://random.preflightstack.com/random-choice
```

### Example Request

```json
{
  "choices": [
    {"task": "research"},
    {"task": "summarize"},
    {"task": "review"}
  ],
  "weights": [2, 1, 3]
}
```

### Simplified Response

```json
{
  "selected": {"task": "review"},
  "index": 2,
  "weighted": true,
  "choice_count": 3,
  "selected_weight": 3,
  "total_weight": 6,
  "request_id": "rc_example"
}
```

The authoritative production contract is available through the public API documentation and OpenAPI specification.

## Public API Contract

| Property | Current behavior |
| --- | --- |
| Method | `POST` |
| Choices | 2–100 per request |
| Input | JSON-compatible values |
| Selection | Uniform or weighted |
| Weights | Optional positive relative weights |
| Randomness | Cryptographically secure server-side randomness |
| Payment | x402 |
| Price | `$0.001` USDC per paid request |

## Example Uses

### Tie breaking

An agent has several acceptable next actions and wants to select one without introducing deterministic ordering bias.

### Weighted preference

Several outcomes are acceptable, but some should occur more frequently than others.

### Sampling

A workflow needs one item selected from a bounded candidate set.

### Lightweight task variation

An autonomous process can vary among approved actions without maintaining its own random-selection component.

## Engineering Goals

Random Choice was built around a few deliberately small goals.

### Predictable contract

The request and response shapes are constrained so calling software can integrate the service without a large client-side abstraction layer.

### Server-side randomness

Selection is performed using cryptographically secure server-side randomness rather than client-supplied randomness or a simple browser-style pseudo-random call.

### Weighted selection without hidden normalization requirements

Callers provide positive relative weights. They do not need to pre-normalize values into percentages or probabilities.

### Bounded inputs

The service intentionally limits request size and choice count so the primitive remains small and operationally predictable.

### Pay-per-use access

x402 allows the service to be purchased at the moment an agent needs it rather than requiring a conventional subscription or long-lived API account.

### Privacy-conscious operation

The production service is designed to collect only limited operational and commercial telemetry needed to understand and maintain the service. Submitted choices and weights are not intended to become a stored analytics dataset.

## Technology

The production system uses technologies including:

`TypeScript` `Node.js` `REST APIs` `Docker` `x402` `OpenAPI` `USDC` `GitHub Actions`

The public interface is intentionally much smaller than the production implementation behind it.

## What Is Not Public

This showcase intentionally does not publish:

- Production source code
- Exact random-selection implementation
- Internal numeric normalization or ticketing logic
- Bias-avoidance and edge-case handling
- Payment and facilitator configuration
- Wallet or credential configuration
- Concurrency and runtime protections
- Abuse controls and request-hardening details
- Internal telemetry implementation
- Deployment configuration
- Production secrets or environment variables
- Internal test suites that expose implementation details

These details remain part of the private production service.

## Scope

Random Choice is designed for bounded, lightweight random selection inside software workflows.

It is not presented as:

- A gambling or wagering service
- A source of security tokens, cryptographic keys, or authentication secrets
- A stable experiment-assignment or routing system
- A substitute for domain-specific decision logic in consequential medical, legal, or financial decisions
- A publicly verifiable or provably-fair randomness beacon

The service provides server-side random selection as a small agent-facing utility.

## Production Status

Random Choice is a deployed Preflight Stack service.

**Preflight Stack**  
https://preflightstack.com

**Product page**  
https://preflightstack.com/random-choice/

**API Documentation**  
https://random.preflightstack.com/docs

**OpenAPI**  
https://random.preflightstack.com/openapi.json

## About This Repository

This repository documents the public behavior, purpose, and engineering approach behind Random Choice.

The production implementation remains private.

The goal of this case study is to demonstrate the design and deployment of a real x402 agent-facing API while keeping the implementation details required to reproduce or operate the production service out of the public repository.
