# Go Microservices Fabric — Cloud-Native Mesh & Autonomous AI Watchdog

[![Production Live](https://img.shields.io/badge/Production-Live%20at%20alloyce.duckdns.org%2Fmesh-06b6d4?style=for-the-badge&logo=caddy)](https://alloyce.duckdns.org/mesh)
[![Go Version](https://img.shields.io/badge/Go-1.22+-00ADD8?style=for-the-badge&logo=go)](https://golang.org)
[![gRPC Protocol](https://img.shields.io/badge/gRPC-v1.62-244c5a?style=for-the-badge&logo=grpc)](https://grpc.io)
[![Ambassador](https://img.shields.io/badge/API%20Gateway-Ambassador-black?style=for-the-badge)](https://www.getambassador.io)
[![AI Watchdog](https://img.shields.io/badge/Autonomous%20AI-Groq%20LLaMA%203.3-f55036?style=for-the-badge)](https://groq.com)
[![n8n Pipeline](https://img.shields.io/badge/Canary%20Engine-n8n%20Workflows-ea4b71?style=for-the-badge&logo=n8n)](https://n8n.io)

> High-performance distributed Go microservices fabric communicating over low-latency gRPC channels. Fronted by an Ambassador API gateway and hardened by an autonomous Groq AI Circuit-Breaker guardian that isolates failing pods and automates canary recovery.

---

## 1. Mesh Topology Architecture

```mermaid
flowchart TD
    Ingress["Inbound Traffic (HTTP/JSON)"] --> Gateway["Ambassador API Gateway (:8000)"]
    
    subgraph Mesh["Distributed Go Services (gRPC Channels)"]
        Gateway -->|gRPC :8001| Auth["Auth & JWT Service"]
        Gateway -->|gRPC :8002| Orders["Order Processing Engine"]
        Gateway -->|gRPC :8003| Catalog["Catalog & Inventory Service"]
        Gateway -->|gRPC :8004| Payment["Payment Gateway Adapter"]
        
        Orders -.->|gRPC| Catalog
        Orders -.->|gRPC| Payment
    end

    subgraph Monitoring["Observability & AI Guardian"]
        Prometheus["Prometheus Metrics Scraper"] -->|p99 Latency & 5xx Spikes| Watchdog["Mesh Sentinel Alpha (Groq AI)"]
        Watchdog --> CB["Circuit Breaker Guardian"]
        Watchdog --> Canary["Canary Traffic Router"]
    end

    subgraph Remediation["Autonomous Canary Pipeline (n8n)"]
        CB -->|Trip Threshold > 0.8| Drain["Drain Failing Pod & Reroute to Replicas"]
        Drain --> Gate["Human Operator Restart Signoff"]
        Gate -->|Approved| Warmup["Canary Warmup (5% -> 25% -> 100%)"]
    end
```

---

## 2. Microservice Inventory

| Service | Protocol | Default Port | Responsibility | Resiliency Guard |
|---|---|---|---|---|
| **Ambassador Gateway** | HTTP/2 / REST | `:8000` | Ingress TLS termination, JWT validation, rate limiting | Edge rate limiters |
| **Auth Service** | gRPC | `:8001` | Token issuance, cryptographic verification, user claims | Token cache |
| **Orders Service** | gRPC | `:8002` | Distributed order lifecycle & saga transaction coordination | **Circuit Breaker Protected** |
| **Catalog Service** | gRPC | `:8003` | Product schema, pricing index, stock availability | Read replica fallback |
| **Payment Service** | gRPC | `:8004` | External banking interface, credit clearance | Idempotent retries |

---

## 3. Circuit Breaker State Transition & Canary Healing

```mermaid
stateDiagram-v2
    [*] --> Closed: Nominal Latency (<20ms)
    Closed --> Open: p99 Latency > 200ms OR 5xx > 5%
    note right of Open
      Mesh Sentinel Alpha trips breaker.
      Traffic shifted to healthy replica pods.
      Operator prompted for container restart.
    end note
    Open --> HalfOpen: Pod Recycled & Human Approved
    HalfOpen --> Closed: Canary traffic warmup passes (0% errors)
    HalfOpen --> Open: Canary warmup detects regression
```

---

## 4. Live Production Walkthrough

Test live circuit-breaker tripping, fault injection, and canary healing in the interactive cockpit at:
👉 **[https://alloyce.duckdns.org/mesh](https://alloyce.duckdns.org/mesh)**

---

## 5. Repository Structure

```
.
├── services/
│   ├── gateway/     # Ambassador configuration & EnvoyFilters
│   ├── auth/        # Go Auth service (gRPC)
│   ├── orders/      # Go Orders service (gRPC)
│   ├── catalog/     # Go Catalog service (gRPC)
│   └── payment/     # Go Payment service (gRPC)
├── proto/           # Protocol buffer (.proto) definitions
├── workflows/       # n8n Canary warmup pipeline JSON
└── compose.yaml     # Mesh orchestration definition
```
