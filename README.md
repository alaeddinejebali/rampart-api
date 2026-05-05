# Rampart API

> ## 🚧 Project status
> This is my homelab project and it's still a work in progress (**not finished**).  
> I'm building it step by step, so things might be broken, missing, or change frequently.

## Overview
 
**Rampart API** is a cloud-native, security-focused API gateway designed to simulate, detect, and mitigate real-world web attacks.
 
This project is a hands-on DevSecOps & cybersecurity lab demonstrating how to protect modern microservices architectures using a layered security approach.
 
---
 
## The Problem
 
Most developers learn security *after* being breached. This lab provides a safe environment to:
 
- Understand how attackers think and operate.
- Build defensive capabilities before production incidents.
- Practice incident response without real consequences.
---
 
## Tech Stack
 
### Frontend
- React (Vite + TypeScript)
- TailwindCSS
- Recharts (data visualization)
### Backend
- Node.js (NestJS framework)
- MongoDB (application data)
- Redis (sessions, rate limiting)
- TypeScript
### Infrastructure
- Docker & Docker Compose
- Kubernetes (k3s/kind/minikube)
- Helm (package management)
### Security Tools
- ModSecurity + OWASP CRS (WAF)
- OWASP ZAP (attack simulation)
- Trivy (container scanning)
- Falco (runtime security)
### Observability
- Prometheus (metrics)
- Grafana (dashboards)
- Loki (log aggregation)
---
 
## Architecture
 
```
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
```
 
### Data Flow
 
1. Attack traffic hits WAF → inspected, logged, scored.
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
 
- **Dashboard**: http://localhost:3000  
- **API Gateway**: http://localhost:8080  
- **Grafana**: http://localhost:9000  
### Run Your First Attack
 
```bash
npm run attack:sqli -- --target=user-service --severity=high
```
 
Watch the dashboard light up with detected threats!
 
---
 
## Attack Simulation Matrix
 
| Attack Type | Tool | Detection | Mitigation |
|------------|------|----------|------------|
| SQL Injection | Custom | WAF rules | Parameterized queries |
| NoSQL Injection | Custom | Query analysis | Input sanitization |
| XSS | ZAP | CSP validation | Output encoding |
| Brute Force | Hydra | Rate limiting | Account lockout |
| IDOR | Custom | Auth checks | UUID resources |
| Path Traversal | DirBuster | Path validation | Whitelisting |
| XXE | Custom | Parser hardening | Disable entities |
| SSRF | Custom | Outbound monitoring | URL whitelist |
 
---
 
## Skills Demonstrated
 
### Security Engineering
- ✅ WAF rule development (ModSecurity DSL)
- ✅ Threat modeling (STRIDE framework)
- ✅ Attack pattern recognition
- ✅ Secure JWT implementation (RS256)
### DevSecOps
- ✅ Container security (Trivy scanning)
- ✅ Runtime security (Falco rules)
- ✅ Security-as-code (OPA policies)
- ✅ SIEM integration (structured logging)
### Cloud-Native Architecture
- ✅ Kubernetes security (RBAC, Network Policies)
- ✅ Observability stack (Prometheus, Grafana)
- ✅ Microservices architecture
### Offensive Security
- ✅ OWASP Top 10 exploitation
- ✅ Attack automation tooling
- ✅ Red team methodology
---
 
## Screenshots
 
> Screenshots will be added once the dashboard is deployed.
 
### Security Dashboard
*Coming soon: Real-time threat detection and mitigation analytics*
 
### Attack Timeline
*Coming soon: Forensic reconstruction of attack chains*
 
### Kubernetes Security View
*Coming soon: Pod security posture and network policies*
 
---
 
## Roadmap
 
### Phase 1: Core Gateway (Current)
- [x] WAF integration
- [x] JWT authentication
- [x] Rate limiting
- [x] Basic observability
### Phase 2: Advanced Security (Next)
- [ ] Service mesh (Istio + mTLS)
- [ ] OPA policy engine
- [ ] Behavioral anomaly detection
- [ ] Automated threat response
### Phase 3: Enterprise Features (Future)
- [ ] SIEM integration (Splunk/Elastic)
- [ ] Chaos engineering (attack resilience)
- [ ] Multi-tenant isolation
- [ ] Compliance reporting (SOC 2, PCI DSS)
---
 
## License
 
MIT License