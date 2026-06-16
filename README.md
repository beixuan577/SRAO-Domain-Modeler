# SRAO Domain Modeler

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![SRAO Framework](https://img.shields.io/badge/SRAO-Framework-blueviolet)](https://github.com/beixuan577?tab=repositories&q=srao)
[![Stage](https://img.shields.io/badge/Stage-0--1-green)]()

> **The first step to building multi-agent systems for any industry.**
> Transform domain knowledge and natural-language requirements into structured, machine-readable models.

Part of the **SRAO Framework** — a systematic methodology for building multi-agent workflow orchestration across industries. Start here, then chain with [SRAO-Agent-Graph](https://github.com/beixuan577/SRAO-Agent-Graph) and [SRAO-Workflow-Orchestrator](https://github.com/beixuan577/SRAO-Workflow-Orchestrator).

---

## Why This Matters

Most multi-agent projects fail not because agents are bad, but because **nobody modeled the domain first**. Teams jump to building agents before understanding:

- What entities exist and how they relate?
- What workflows actually happen in this industry?
- What does "success" look like in measurable terms?

SRAO Domain Modeler forces you to answer these questions *before* writing a single line of agent code.

## What It Does

**Stage 0 - Domain Modeling**
- Build concept dictionaries with entity types, attributes, and relationships
- Create entity-relationship diagrams (text-based, version-controllable)
- Extract parameterized workflow templates from SOPs/process descriptions

**Stage 1 - Requirement Structuring**
- Convert natural-language intent into Structured Requirement Models (SRM)
- Define KPI targets, constraints, and acceptance criteria
- Inventory available tools, data sources, and human roles

## Quick Start

1. Describe your industry scenario in natural language
2. The modeler generates a domain model (concept dictionary + ER diagram + workflow templates)
3. Requirements are structured into SRM documents with measurable goals
4. Pass SRM output to [SRAO-Agent-Graph](https://github.com/beixuan577/SRAO-Agent-Graph) for agent capability mapping

### Example: Manufacturing Domain

``
User: "We want to automate order scheduling in our factory"

Output:
  Concept Dictionary: Order, Work Order, Production Line, Equipment, Material, QC Record
  ER Diagram: [Order] --1:N--> [Work Order] --N:1--> [Production Line]
  Workflow Template: WF-MFG-001 Order Scheduling (4 steps, parallel+serial)
  SRM: REQ-001 with goals (scheduling <30min), constraints (3-month delivery), 
       acceptance (95% capacity utilization)
``

## Pre-built Industry Models

| Industry | Reference | Key Workflows |
|----------|-----------|---------------|
| Manufacturing | references/manufacturing.md | Order scheduling, Predictive maintenance, Quality tracing |
| Energy (Wind/Solar) | references/energy.md | Turbine health monitoring, Power forecasting, Drone inspection |
| Healthcare | references/healthcare.md | ER triage, Radiology AI, Surgical robotics |
| Agriculture | references/agriculture.md | Pest early warning, Precision irrigation, Yield prediction |
| Transport/Tunnel | references/transport.md | Rock stability monitoring, Fire robot patrol |

New industries? Start from scratch using the universal flow.

## Core Output: SRM Format

`yaml
structured_requirement:
  id: REQ-001
  name: "Order Scheduling Automation"
  domain: Manufacturing
  goals:
    - metric: "Scheduling response time"
      current: "4 hours"
      target: "<30 minutes"
  constraints:
    - "3-month delivery timeline"
  existing_tools: ["SAP ERP", "MES"]
  acceptance:
    - "95% capacity utilization"
`

## SRAO Framework Pipeline

`
+----------------------+     +----------------------+     +-----------------------------+
| SRAO-Domain-Modeler  |---->| SRAO-Agent-Graph     |---->| SRAO-Workflow-Orchestrator  |
| (Model & Structure)  | SRM | (Decompose & Map)    | DAG | (Orchestrate & Monitor)     |
+----------------------+     +----------------------+     +-----------------------------+
`

## Universal Formula

> **Industry Solution = Domain Knowledge Graph x Agent Capability Graph x Dynamic Orchestration Engine**

This repo handles the first term. The other two are handled by the companion repos.

## License

MIT