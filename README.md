# Rampart

## Overview

**Rampart** is a cloud-native, security-focused API gateway designed to simulate, detect, and mitigate real-world web attacks.

This project is a hands-on DevSecOps & cybersecurity lab demonstrating how to protect modern microservices architectures using a layered security approach.

---

## The Problem

Most developers learn security *after* being breached. This lab provides a safe environment to:

- Understand how attackers think and operate.
- Build defensive capabilities before production incidents.
- Practice incident response without real consequences.

---

## Architecture

┌─────────────────────────────────────────────┐
│     Attack Simulation Layer                  │
│  (OWASP ZAP, Custom Scripts)                │
└──────────────────┬──────────────────────────┘
                   │
┌──────────────────▼──────────────────────────┐
│         Rampart API Gateway                 │
│  ┌────────────────────────────────────────┐ │
│  │  WAF Layer (ModSecurity + OWASP CRS)   │ │
│  ├────────────────────────────────────────┤ │
│  │  Rate Limiter (Redis-backed)           │ │
│  ├────────────────────────────────────────┤ │
│  │  JWT Validator                         │ │
│  ├────────────────────────────────────────┤ │
│  │  Request Logger (Structured)           │ │
│  ├────────────────────────────────────────┤ │
│  │  Threat Analyzer (Pattern Matching)    │ │
│  └────────────────────────────────────────┘ │
└──────────────────┬──────────────────────────┘
                   │
      ┌────────────┼────────────┬────────────┐
      │            │            │            │
   ┌──▼───┐    ┌───▼────┐   ┌───▼────┐   ┌──▼─────┐
   │ User │    │ Payment│   │ Upload │   │ Admin  │
   │Svc   │    │Svc     │   │Svc     │   │Svc     │
   └──┬───┘    └───┬────┘   └───┬────┘   └───┬────┘
      │            │            │            │
      └────────────┴────────────┴────────────┘
                   │
┌──────────────────▼──────────────────────────┐
│ Data Layer                                  │
│ ├─ MongoDB (Application Data)               │
│ ├─ Redis (Sessions, Rate Limits)            │
│ └─ Loki (Security Events)                   │
└─────────────────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────┐
│ Security Operations Center                  │
│ ├─ Prometheus (Metrics)                    │
│ ├─ Grafana (Dashboards)                    │
│ └─ Alert Manager (Incidents)               │
└─────────────────────────────────────────────┘

### Data Flow

1. Attack traffic hits WAF → inspected, logged, scored  .
2. Legitimate requests proceed through rate limiter.
3. JWT validation ensures authentication.
4. Routed to appropriate microservice.
5. All events streamed to observability stack.

---

## Project Structure

```
rampart-api/
├── apps/
│   ├── gateway/          # Core API gateway (Node.js)
│   ├── dashboard/        # Security monitoring UI (React)
│   ├── user-service/     # Example microservice (vulnerable)
│   ├── payment-service/  # Example microservice (vulnerable)
│   └── attack-simulator/ # Automated attack scripts
├── infra/
│   ├── docker/           # Dockerfiles & compose files
│   ├── kubernetes/       # K8s manifests
│   └── helm/             # Helm charts
├── security/
│   ├── waf-rules/        # ModSecurity custom rules
│   ├── policies/         # OPA policies (future)
│   └── threat-intel/     # Attack signatures
├── observability/
│   ├── prometheus/
│   ├── grafana/
│   └── loki/
├── docs/
│   ├── architecture.md
│   ├── attack-playbook.md
│   └── deployment.md
├── scripts/
│   ├── setup.sh
│   ├── attack.sh
│   └── benchmark.sh
└── .github/
    └── workflows/
```

---

## Quick Start

### Clone and Setup

```bash
git clone https://github.com/alaeddinejebali/rampart-api.git
cd rampart-api
./scripts/setup.sh
```

### Deploy Locally

```bash
make dev
```

Access:

- Dashboard: http://localhost:3000  
- API Gateway: http://localhost:8080  
- Grafana: http://localhost:9000  

### Run Your First Attack

```bash
npm run attack:sqli -- --target=user-service --severity=high
```

---