# Google Cloud TSAM: 34-Service V2 Cheat Sheet

Night-before memorization matrix: symptom → GCP service → executive value.

**Memorize triggers, not bullets.** Use this markdown for first-pass interview answers; use the full DOCX guide for deeper backup.

## Slide 2 — V2 corrections that matter

High-risk mistakes corrected from the uploaded 34-service draft.

| Area | Corrected TSAM wording |
| --- | --- |
| Title/count | The pasted guide says top 30 but contains 34 services; revised version uses 34 and adds high-priority missing services appendix. |
| GKE Enterprise naming | Use GKE Enterprise, fleets, Config Sync, Policy Controller, and Cloud Service Mesh. Anthos/ASM/ACM names are legacy in many conversations. |
| Attached clusters | EKS/AKS/conformant clusters can be attached to fleets, but Google provides a management/governance layer and a subset of features, not full native GKE lifecycle control. |
| MCI vs Gateway | Multi Cluster Ingress is Ingress-style external ALB for GKE clusters; Multi-cluster Gateway is the newer Gateway API model. |
| BigQuery Omni | Avoid saying simply 'no egress fees.' Correct pitch: query data in S3/Azure Blob where it resides; raw data need not move, but result movement and transfer choices still matter. |
| Pub/Sub exactly-once | Exactly-once applies to pull/StreamingPull and within a region. Keep idempotency in the design. |
| Memorystore persistence | Redis Cluster persistence supports RDB or AOF, not both at the same time. Also add Valkey as current Memorystore option. |
| Bigtable consistency | Single-row operations are atomic; do not imply global multi-row transaction consistency across replicated clusters. |
| Cloud DNS | Routing policies are useful, but DNS TTL/resolver caching means failover and traffic splits are not exact or instant. |
| VPC Service Controls | VPC SC protects supported Google API access paths; it is not a network firewall and needs careful ingress/egress exceptions. |

## Slide 3 — The TSAM answer pattern

A strong answer is business-first, then technical, then risk-aware.

1. **Symptom** — What business pain is described?

2. **Service** — Pick the control plane or data plane fix.

3. **Value** — Tie it to revenue, risk, continuity, or cost.

- Always mention the failure mode: latency, traffic, errors, or saturation.

- Then name the mitigation: canary, cache, DLQ, VPC SC, autoscaling, PITR, failover.

- Close with executive value: protects revenue, reduces risk, controls cost, or preserves continuity.

## Slide 4 — Compute & Platform

Memorize the trigger and gotcha. Feature details live in the DOCX.

| Service | When interviewer says... | Gotcha / TSAM warning |
| --- | --- | --- |
| Kubernetes Engine (GKE) | Apps need Kubernetes with autoscaling, safe upgrades, and portable deployment patterns | Do not say GKE autoscaling is one thing. Separate pod, node, and request-size scaling. |
| Compute Engine (GCE) | Legacy or custom VM workloads need predictable performance, DR, and cost control | Spot VMs are cheap but preemptible; only pitch them for fault-tolerant batch/stateless workers. |
| Run | Teams want serverless containers, fast releases, and lower operational overhead | Cloud Run is not always cheaper if min instances and high CPU are always on; model it. |
| Functions (2nd gen / Run functions) | Event-driven glue code is needed without managing servers | Do not pitch functions for long-running, stateful, or high-connection workloads by default. |
| GKE Enterprise | Enterprise platform teams need consistent governance across many clusters, including hybrid/multi-cloud | GKE Enterprise governs non-GKE clusters; it does not magically turn EKS/AKS into native GKE. |

## Slide 5 — Networking & Edge

Memorize the trigger and gotcha. Feature details live in the DOCX.

| Service | When interviewer says... | Gotcha / TSAM warning |
| --- | --- | --- |
| Load Balancing | Users need one reliable entry point with health-based routing and global scale | DNS failover is slower than load-balancer health checks because DNS clients cache TTLs. |
| Armor | Security or SRE sees bots, DDoS, OWASP attacks, or abusive traffic | A WAF is not a replacement for app fixes; it is a blast-radius and edge-control layer. |
| CDN | Marketing spikes or global users create high latency and origin overload | Do not invalidate everything during a flash sale; that can stampede the origin. |
| NAT | Private workloads need outbound internet without public IPs | NAT does not provide inbound access and is not a firewall replacement. |
| DNS | Hybrid naming, DNS failover, or private service discovery is failing | Do not promise exact traffic split with DNS; resolver caching makes it approximate. |

## Slide 6 — Databases & Caching

Memorize the trigger and gotcha. Feature details live in the DOCX.

| Service | When interviewer says... | Gotcha / TSAM warning |
| --- | --- | --- |
| SQL | Teams need managed MySQL/PostgreSQL/SQL Server without running database VMs | Cloud SQL is regional relational DB, not globally active-active like Spanner. |
| Spanner | Global relational transactions need scale, high availability, and strong consistency | Do not choose Spanner just because it is Google-scale; choose it when relational + global scale/HA justify complexity/cost. |
| Bigtable | Massive low-latency key-value/wide-column workloads such as telemetry or time series | Bigtable is not SQL and not for ad hoc analytics; pair with BigQuery for analytics. |
| Firestore | Mobile/web apps need serverless document database with realtime sync | Firestore is not a relational join engine; model data around query patterns. |
| Memorystore | App latency or database saturation needs a managed in-memory cache | A cache is not the source of truth; plan DB fallback and warm-up behavior. |

## Slide 7 — Storage, Messaging & Data Processing

Memorize the trigger and gotcha. Feature details live in the DOCX.

| Service | When interviewer says... | Gotcha / TSAM warning |
| --- | --- | --- |
| Storage | Durable object storage is needed for data lakes, backups, static assets, and artifacts | Buckets are globally named and object storage is not a filesystem unless using compatible tooling with tradeoffs. |
| BigQuery | Analytics at scale, dashboards, logs, ML-in-SQL, and data sharing | BigQuery is not an OLTP database; do not use it for high-QPS transactional writes. |
| Pub/Sub | Microservices or data pipelines need asynchronous decoupling | Exactly-once does not remove the need for idempotent business logic. |
| Dataflow | Streaming/batch data transformation needs managed Apache Beam execution | Dataflow solves pipeline execution; it does not fix bad event-time semantics or unbounded cardinality. |
| Dataproc | Spark/Hadoop ecosystem migration or managed open-source big data jobs | Do not keep long-lived idle clusters; ephemeral/serverless is the FinOps default. |

## Slide 8 — AI / GenAI

Memorize the trigger and gotcha. Feature details live in the DOCX.

| Service | When interviewer says... | Gotcha / TSAM warning |
| --- | --- | --- |
| Vertex AI API & Model Garden | Teams want Gemini/foundation models, tuning, evaluation, and governed AI deployment | Do not jump to fine-tuning first; try prompt, grounding, caching, and evaluation before tuning. |
| Vertex AI Vector Search | RAG/search needs low-latency semantic retrieval across many embeddings | Vector Search retrieves candidates; it does not guarantee answer truth without grounding/evaluation. |
| Vertex AI Agent Builder | Business wants chat/agent experiences grounded in enterprise data | Do not promise zero hallucination; promise grounding, guardrails, and measurement. |
| Document AI | Unstructured documents need extraction, classification, and validation | OCR accuracy is not enough; measure field-level business correctness. |

## Slide 9 — Ops, Identity & FinOps

Memorize the trigger and gotcha. Feature details live in the DOCX.

| Service | When interviewer says... | Gotcha / TSAM warning |
| --- | --- | --- |
| Monitoring | SRE needs to know what is slow, broken, overloaded, or burning error budget | A dashboard is not observability unless it leads to a fast diagnosis path. |
| Logging | Teams need audit, troubleshooting, SIEM export, or cost control for logs | Never exclude logs blindly; classify security, compliance, debug, and noise first. |
| Trace & Profiler | Checkout/API is slow but infrastructure metrics look normal | Tracing finds where time went; profiling finds why code consumed resources. |
| Billing & Recommender | CFO/CTO says cloud bill is unpredictable or wasteful | Do not cut cost by breaking SLOs; tie savings to reliability guardrails. |
| Identity & Access Management (IAM) | Security asks who can do what and how to reduce privilege risk | Allow policies grant; deny/org policies constrain. Know the difference. |
| Secret Manager | Apps need passwords, API keys, certificates, and rotation without plaintext exposure | Secret Manager stores secrets; it does not automatically rotate every downstream credential unless you build the rotation flow. |

## Slide 10 — Security, Hybrid, Migration & Delivery

Memorize the trigger and gotcha. Feature details live in the DOCX.

| Service | When interviewer says... | Gotcha / TSAM warning |
| --- | --- | --- |
| Hybrid Connectivity: Interconnect & VPN | On-prem or branch systems need reliable private connectivity to Google Cloud | A single Interconnect is not HA; redundancy is the architecture, not the product name. |
| Enterprise Security: VPC Service Controls & Security Command Center | CISO fears data exfiltration, misconfiguration, or credential compromise | VPC SC is not a packet firewall; it controls API data exfiltration paths for supported services. |
| Migration Tooling: Migrate to VMs & Database Migration Service | Customer must migrate with low downtime and low risk | Lift-and-shift is a step, not a modernization strategy; identify refactor candidates. |
| CI/CD: Build & Deploy | Bad releases or manual deployment processes cause outages | A canary without automated metrics is just a smaller manual release. |

## Slide 11 — Night-before Matrix: Reliability & Security

Fast trigger answers for SRE, CTO, CISO, and network personas.

| Persona | Symptom | GCP service solution | Executive pitch |
| --- | --- | --- | --- |
| CTO | Cloud bill is unpredictable | Billing Export + Recommender + CUDs + Autopilot | Create unit-cost visibility, commit to stable baseline, and let elastic/serverless absorb spikes without overbuying. |
| SRE | Bad deploy took down the site | Cloud Deploy + GKE/Cloud Run canary + Cloud Monitoring SLOs | Limit blast radius, watch error-budget burn, and auto/fast rollback before revenue impact grows. |
| CISO | We fear data exfiltration | VPC Service Controls + IAM/WIF + CMEK/KMS + SCC | Build API perimeters, eliminate static keys, control encryption keys, and surface threats centrally. |
| VP Ops | Checkout is slow | Cloud Trace + Profiler + Logging correlation | Trace the exact slow dependency and profile the code path causing CPU/memory saturation. |
| Data VP | ML will crash the database | BigQuery ML + Dataflow + BigQuery/Spanner Data Boost | Run analytics/ML off the OLTP path and isolate heavy jobs from production serving systems. |
| CTO | We need multi-cloud governance | GKE Enterprise fleets + Config Sync + Policy Controller | Apply common policy/config/access across GKE, EKS, AKS, and on-prem with fewer human errors. |
| Marketing | Flash sale tomorrow | Cloud CDN + Cloud Armor + Load Balancing + Cloud Run/GKE autoscaling | Cache at edge, block bots, scale app tiers, and protect origin/database saturation. |
| SRE | Cache crashed and DB is melting | Memorystore + rate limits + TTL jitter + Pub/Sub queueing | Treat cache as optional, prevent stampede, and let queues absorb load while cache warms. |
| DBA | Single-region DB outage is unacceptable | Spanner multi-region or Cloud SQL HA/cross-region replica | Choose global relational HA when needed; otherwise design Cloud SQL regional HA plus DR. |
| Security | Developers keep storing keys | Workload Identity Federation + Secret Manager | Remove long-lived keys, centralize secrets, and audit every access. |
| Network | Private workloads need internet updates | Cloud NAT + Private Google Access | Enable outbound access without public IP exposure and monitor NAT port exhaustion. |
| Hybrid | On-prem app must reach GCP privately | Interconnect + Cloud Router/BGP + HA VPN backup | Private, redundant connectivity with dynamic routing and tested failover. |

## Slide 12 — Night-before Matrix: Data, AI, FinOps & Delivery

Memorize these as compact interview responses.

| Persona | Symptom | GCP service solution | Executive pitch |
| --- | --- | --- | --- |
| Platform | Every cluster is configured differently | GKE Enterprise + Config Sync + Policy Controller | Stop drift by making Git the source of truth and enforcing fleet-wide guardrails. |
| App Team | Cold starts hurt revenue | Cloud Run min instances + concurrency tuning | Buy warmth only where latency matters and cap scaling to protect downstream systems. |
| Analytics | Dashboards are slow and expensive | BigQuery partitioning/clustering + BI Engine + materialized views | Scan less data, cache repeated computations, and accelerate executive dashboards. |
| AI Lead | RAG answers are hallucinating | Vertex AI Agent Builder + Vector Search + evaluation | Ground responses in approved data, measure quality, and gate risky tool calls. |
| Compliance | Need immutable records | Cloud Storage retention lock + Object Versioning + CMEK | Make records tamper-resistant and recoverable with customer-controlled encryption. |
| SecOps | We don't know our risky assets | Security Command Center + Event Threat Detection | Centralize findings, prioritize exploitable risk, and detect suspicious activity from logs. |
| Migration | Cutover must be near-zero downtime | DMS CDC + Migrate to VMs test clones | Replicate continuously, validate clones, lower DNS TTL, and cut over with rollback. |
| Data Eng | Poison messages stop pipelines | Pub/Sub DLQ + Dataflow side outputs | Quarantine bad records and keep the healthy stream moving. |
| SRE | Users see 404 storms from broken links | Cloud CDN negative caching + Logging | Cache repeated errors briefly and identify the broken source before it overloads origin. |
| Architect | Global relational app has hotspots | Spanner key design + Key Visualizer-like metrics + autoscaler | Avoid monotonic keys and scale compute before latency becomes revenue loss. |
| DevEx | Manual releases are slow | Cloud Build + Cloud Deploy + Artifact Registry | Create auditable, repeatable pipelines from build to progressive delivery. |
| CFO | Cloud savings may hurt reliability | FinOps with SLO guardrails | Optimize idle/overprovisioned resources while protecting latency, error rate, and availability. |

## Slide 13 — Critical gotchas interviewers love

These stop you from sounding like a brochure.

- GKE autoscaling has layers: HPA/VPA/Cluster Autoscaler solve different problems.

- GKE Enterprise can govern EKS/AKS attached clusters, but AWS/Azure still own their native lifecycle.

- MCI is Ingress-style multi-cluster L7; Multi-cluster Gateway is the newer Gateway API pattern.

- BigQuery is analytics, not OLTP; Spanner is relational global transactions, not a cheap default DB.

- Pub/Sub exactly-once is pull/StreamingPull and regional; still write idempotent consumers.

- VPC Service Controls is not a firewall; it controls supported Google API data exfiltration paths.

- DNS routing is approximate because resolvers cache TTLs; load balancers fail over faster.

- Do not invalidate all CDN cache before a flash sale; that can overload the origin.

## Slide 14 — Important additions missing from the 34-service list

If you have time, add these to your TSAM mental map.

| Add this service/topic | Why it matters for TSAM |
| --- | --- |
| AlloyDB for PostgreSQL | Add as a standalone database service. Pitch: PostgreSQL compatibility with high performance, HA, read pools, cross-region replicas, AlloyDB AI/vector search, and better scale for enterprise Postgres workloads than self-managed Postgres. |
| Cloud KMS / Cloud HSM / EKM / Autokey | Add as standalone security/key-management service. Pitch: customer-controlled encryption, separation of duties, rotation, CMEK integrations, Autokey, and external key control for strict compliance. |
| Private Service Connect | Add to networking. Pitch: private producer/consumer access to managed services and service publishing without public IP exposure. |
| Artifact Registry | Add to CI/CD. Pitch: central container/package artifact storage with IAM, vulnerability scanning, provenance, and Binary Authorization integration. |
| Dataplex / Data Catalog / DLP | Add to data governance. Pitch: discover, classify, protect, and govern data across lakes/warehouses before analytics and AI use it. |

**Highest priority:** AlloyDB and Cloud KMS/CMEK/EKM deserve standalone coverage because they appear constantly in database modernization, security, and compliance discussions.

## Slide 15 — 30-second answer template

> “The symptom sounds like **[latency / traffic / errors / saturation]**. I would first protect the user path with **[service/control]**, then add visibility with **[monitoring/logging/trace]**, and finally make the executive tradeoff explicit: **[continuity / revenue / security / FinOps]**. The main risk is **[gotcha]**, so I would validate using **[metric/SLO]**.”

- **For SRE:** say SLO, error budget, canary, rollback, p95/p99 latency.

- **For Security:** say least privilege, no static keys, VPC SC, KMS/CMEK, audit logs.

- **For Data:** say isolate OLTP from analytics, partition/cluster, DLQ, replay, governance.

- **For FinOps:** say unit economics, baseline CUDs, autoscaling, labels, Recommender.
