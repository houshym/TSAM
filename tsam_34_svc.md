# Google Cloud TSAM V2 Guide + Night-Before Memorization Matrix

Revised from the uploaded 34-service guide. Focus: interview triggers, corrected feature positioning, missing must-know features, and executive TSAM pitch.

How to use: memorize the trigger matrix first, then use the service pages for deeper backup details.

# 1. High-Impact Corrections

| Area | Revision / Correction |
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
| Cloud CDN | Serve stale and invalidation are powerful, but mass invalidation can stampede the origin. Cloud CDN does not support stale-if-error as a directive. |
| Spanner Graph | Spanner Graph is available in Enterprise/Enterprise Plus and examples/docs target GoogleSQL; do not assume PostgreSQL-interface parity. |

# 2. Night-Before Memorization Matrix

Use this to answer quickly: Symptom -> GCP service -> business value. Do not memorize every bullet.

| Persona | Interviewer symptom | GCP solution | TSAM executive pitch |
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

# 3. Revised 34-Service Guide

## 1. Google Kubernetes Engine (GKE)

Trigger: Apps need Kubernetes with autoscaling, safe upgrades, and portable deployment patterns.

Corrected key highlights:

- Autopilot vs Standard: Autopilot reduces node operations and idle-node waste; Standard gives deeper node/network control.

- HPA/VPA/Cluster Autoscaler: separate pod replica scaling, request sizing, and node pool capacity so you avoid CPU/memory saturation at the right layer.

- Readiness/liveness/startup probes + PDBs: keep bad pods out of traffic and protect availability during maintenance.

- Workload Identity: bind Kubernetes service accounts to IAM identities; avoid long-lived service account keys.

- Dataplane V2, NetworkPolicy, GKE Sandbox, Binary Authorization, and Backup for GKE are common security/resilience discussion points.

Missing / add for interview:

- Add Multi-cluster Gateway as the newer Gateway API approach; keep MCI as Ingress-style multi-cluster L7 load balancing.

- Mention private clusters, release channels, node auto-provisioning, and cost allocation labels for TSAM operations.

Gotcha: Do not say GKE autoscaling is one thing. Separate pod, node, and request-size scaling.

## 2. Compute Engine (GCE)

Trigger: Legacy or custom VM workloads need predictable performance, DR, and cost control.

Corrected key highlights:

- Managed Instance Groups (MIGs) provide autoscaling, autohealing, rolling updates, and regional HA for VM fleets.

- Instance templates and health checks are the control plane for repeatable VM operations.

- Committed Use Discounts and reservations are the FinOps levers for predictable baseline load.

- Shielded VM, Confidential VM, OS Login, IAM, and VPC firewall rules are security baselines.

- PD async replication, snapshots, and machine images support DR patterns for VM-based apps.

Missing / add for interview:

- Add sole-tenant nodes only for compliance/licensing isolation, not as a default performance fix.

- Add resource policies for snapshots/schedules and placement policies for latency-sensitive workloads.

Gotcha: Spot VMs are cheap but preemptible; only pitch them for fault-tolerant batch/stateless workers.

## 3. Cloud Run

Trigger: Teams want serverless containers, fast releases, and lower operational overhead.

Corrected key highlights:

- Scale to zero cuts idle cost; min instances reduce cold-start latency for revenue paths.

- Concurrency and max instances protect latency and downstream databases from connection saturation.

- Revisions, tags, and traffic splits make canary/rollback simple.

- Direct VPC egress / Serverless VPC Access connects private backends like Cloud SQL or Memorystore.

- Cloud Run Jobs handle containerized batch work without maintaining workers.

Missing / add for interview:

- Add CPU allocation choices, request timeout, startup CPU boost, and service-to-service IAM authentication.

- For DB-heavy services, add connection pooling, Cloud SQL connector, and max-instances guardrails.

Gotcha: Cloud Run is not always cheaper if min instances and high CPU are always on; model it.

## 4. Cloud Functions (2nd gen / Cloud Run functions)

Trigger: Event-driven glue code is needed without managing servers.

Corrected key highlights:

- 2nd gen runs on Cloud Run infrastructure and uses Eventarc for many event sources.

- Concurrency, min instances, larger CPU/memory, and longer timeouts make it more capable than 1st gen.

- Retries help transient failures but require idempotent handlers.

- Ingress controls and IAM Invoker protect internal functions.

- Use Pub/Sub/Eventarc to decouple callers from slow or unreliable downstream systems.

Missing / add for interview:

- Add dead-letter handling at the event source when retries can loop forever.

- Use Cloud Run directly when you need container portability, custom runtime behavior, or richer traffic control.

Gotcha: Do not pitch functions for long-running, stateful, or high-connection workloads by default.

## 5. GKE Enterprise

Trigger: Enterprise platform teams need consistent governance across many clusters, including hybrid/multi-cloud.

Corrected key highlights:

- Fleet is the operating model: logically group clusters and apply shared capabilities.

- Attached clusters can include EKS, AKS, and other conformant Kubernetes clusters, but only a subset of GKE features applies.

- Config Sync and Policy Controller reduce drift and enforce guardrails.

- Cloud Service Mesh adds mTLS, traffic policy, telemetry, and resilience patterns across services.

- Connect Gateway centralizes access to registered clusters without exposing control planes broadly.

Missing / add for interview:

- Use current names: GKE Enterprise, Config Sync, Policy Controller, Cloud Service Mesh; Anthos terms are legacy in many contexts.

- Make clear that attached EKS/AKS lifecycle is still owned by AWS/Azure unless using GKE multi-cloud offerings.

Gotcha: GKE Enterprise governs non-GKE clusters; it does not magically turn EKS/AKS into native GKE.

## 6. Cloud Load Balancing

Trigger: Users need one reliable entry point with health-based routing and global scale.

Corrected key highlights:

- Global external Application Load Balancer uses Anycast VIPs, GFEs, health checks, and Google backbone routing.

- URL/path/header routing, backend services, NEGs, and connection draining protect user experience during changes.

- Cloud Armor, Cloud CDN, IAP, certificate management, SSL policies, and logging attach at the edge.

- Capacity and locality-based decisions reduce backend saturation and latency.

- Internal Application Load Balancer supports private service-to-service access patterns.

Missing / add for interview:

- Add Private Service Connect for private producer-consumer access and service publishing.

- Add backend mTLS / secure origins where required for regulated traffic.

Gotcha: DNS failover is slower than load-balancer health checks because DNS clients cache TTLs.

## 7. Google Cloud Armor

Trigger: Security or SRE sees bots, DDoS, OWASP attacks, or abusive traffic.

Corrected key highlights:

- DDoS protection and WAF rules run at Google edge before traffic hits backends.

- Preconfigured WAF rules map to OWASP risks; custom CEL rules allow precise Layer 7 filtering.

- Rate limiting and bot management/reCAPTCHA Enterprise reduce scraper and credential-stuffing load.

- Adaptive Protection learns baseline traffic and suggests mitigations for attacks.

- Preview mode lets teams test policies before blocking real users.

Missing / add for interview:

- Add address groups, threat intelligence lists, edge security policies, and logging-based tuning.

- Tie security changes to error-rate monitoring so a false positive does not become an outage.

Gotcha: A WAF is not a replacement for app fixes; it is a blast-radius and edge-control layer.

## 8. Cloud CDN

Trigger: Marketing spikes or global users create high latency and origin overload.

Corrected key highlights:

- Caches static and cacheable dynamic content at edge POPs to reduce latency and egress/origin load.

- Cache keys, cache modes, TTLs, negative caching, and signed URLs/cookies are the core controls.

- Serve-stale/stale-while-revalidate hides origin latency and can improve continuity when content expires.

- Invalidation by path/host/cache tags is useful for urgent content changes.

- External origin support helps multi-cloud or legacy origins.

Missing / add for interview:

- Add cache-hit ratio and origin latency as TSAM golden signals.

- Be careful with personalized content; use cache keys and headers correctly.

Gotcha: Do not invalidate everything during a flash sale; that can stampede the origin.

## 9. Cloud NAT

Trigger: Private workloads need outbound internet without public IPs.

Corrected key highlights:

- Cloud NAT provides managed egress for private VMs/GKE nodes without allowing unsolicited inbound traffic.

- Dynamic port allocation, min ports per VM, and multiple NAT IPs protect against port exhaustion.

- NAT logging gives translation visibility for troubleshooting and audit.

- Drain duration reduces disruption when changing NAT IP pools.

- Pair with Private Google Access for Google API access where appropriate.

Missing / add for interview:

- Add NAT utilization alerts: dropped_sent_packets_count, port allocation, connection errors.

- For static allowlists, reserve static external IPs for NAT.

Gotcha: NAT does not provide inbound access and is not a firewall replacement.

## 10. Cloud DNS

Trigger: Hybrid naming, DNS failover, or private service discovery is failing.

Corrected key highlights:

- Public and private managed zones separate internet-facing from internal names.

- Forwarding, peering, and inbound/outbound policies solve hybrid name resolution.

- DNSSEC protects public zones against spoofing/cache poisoning.

- Routing policies support weighted, geolocation, and failover patterns; health checks can integrate with eligible endpoints.

- Cloud DNS logs help diagnose resolution and routing-policy behavior.

Missing / add for interview:

- Add Service Directory integration for service discovery and split-horizon DNS patterns.

- Emphasize TTL caching: DNS changes are not instant for every client.

Gotcha: Do not promise exact traffic split with DNS; resolver caching makes it approximate.

## 11. Cloud SQL

Trigger: Teams need managed MySQL/PostgreSQL/SQL Server without running database VMs.

Corrected key highlights:

- Regional HA, automatic backups, PITR, maintenance windows, and replicas support business continuity.

- Query Insights, flags, and performance metrics help troubleshoot locks, slow queries, and CPU saturation.

- Auth Proxy/Connectors, IAM database authentication, private IP/PSC, and CMEK harden access.

- Read replicas offload reads; cross-region replicas support DR/read locality.

- Storage auto-increase prevents disk-full outages.

Missing / add for interview:

- Add connection pooling: serverless/GKE can exhaust DB connections before CPU is high.

- Add Database Migration Service for low-downtime migration.

Gotcha: Cloud SQL is regional relational DB, not globally active-active like Spanner.

## 12. Cloud Spanner

Trigger: Global relational transactions need scale, high availability, and strong consistency.

Corrected key highlights:

- TrueTime enables external consistency with horizontal scale.

- Regional and multi-region instances trade cost/latency for availability and locality.

- Schema changes are online; interleaved tables and key design reduce hotspot risk.

- Change Streams, Data Boost, PITR, Backup, and managed autoscaler are key ops features.

- Spanner Graph adds graph capabilities in Enterprise/Enterprise Plus GoogleSQL databases.

Missing / add for interview:

- Add PostgreSQL interface caveat: not every GoogleSQL feature has PostgreSQL-interface parity.

- Add key-design anti-pattern: monotonically increasing primary keys create hotspots.

Gotcha: Do not choose Spanner just because it is Google-scale; choose it when relational + global scale/HA justify complexity/cost.

## 13. Cloud Bigtable

Trigger: Massive low-latency key-value/wide-column workloads such as telemetry or time series.

Corrected key highlights:

- Design row keys carefully; hotspotting is the most common performance failure.

- Replication and app profiles let you route reads/writes for HA or locality.

- Key Visualizer exposes hot keys and access-pattern problems.

- Separates storage from serving nodes; scale nodes/clusters to meet throughput/latency.

- Backups, GC policies, authorized views, and IAM support operations and governance.

Missing / add for interview:

- Add autoscaling if enabled for clusters and alert on CPU, storage utilization, and p99 latency.

- Clarify consistency: single-row operations are atomic; multi-cluster replication has consistency tradeoffs by routing profile.

Gotcha: Bigtable is not SQL and not for ad hoc analytics; pair with BigQuery for analytics.

## 14. Firestore

Trigger: Mobile/web apps need serverless document database with realtime sync.

Corrected key highlights:

- Realtime listeners and offline persistence reduce client latency and improve mobile continuity.

- Security Rules protect direct client access; IAM governs server/admin access.

- Automatic scaling removes capacity planning for many app workloads.

- TTL, backups/PITR, import/export, and managed indexes support operations.

- Vector search and full-text-style patterns can simplify app search/RAG-lite use cases.

Missing / add for interview:

- Add composite-index planning and document hot-spot limits.

- Use Datastore mode only for legacy Datastore semantics, not as a toggle for the same app.

Gotcha: Firestore is not a relational join engine; model data around query patterns.

## 15. Memorystore

Trigger: App latency or database saturation needs a managed in-memory cache.

Corrected key highlights:

- Memorystore includes Redis, Redis Cluster, Memcached, and Valkey options.

- Use cache-aside/read-through/write-through patterns to shield primary databases.

- HA replicas and regional placement reduce cache outage impact.

- Persistence exists for Redis Cluster; RDB and AOF are alternatives and cannot both be enabled at once.

- Metrics: hit ratio, evictions, memory usage, connected clients, latency, and CPU.

Missing / add for interview:

- Add Valkey as a current option and vector search where relevant.

- Add cache-stampede protection: TTL jitter, request coalescing, and fallback rate limits.

Gotcha: A cache is not the source of truth; plan DB fallback and warm-up behavior.

## 16. Cloud Storage

Trigger: Durable object storage is needed for data lakes, backups, static assets, and artifacts.

Corrected key highlights:

- Storage classes, lifecycle rules, and Autoclass optimize cost by access pattern.

- Versioning, retention policies, object holds, and soft delete protect against deletion/ransomware.

- Dual-region/multi-region placement and Turbo Replication support availability and RPO objectives.

- Signed URLs and IAM/UBLA control object access.

- Pub/Sub notifications, Storage Transfer Service, and BigQuery external tables connect data pipelines.

Missing / add for interview:

- Add hierarchical namespace for managed folder-style analytics/data-lake workloads where relevant.

- Add bucket lock caution: retention policies can be irreversible after lock.

Gotcha: Buckets are globally named and object storage is not a filesystem unless using compatible tooling with tradeoffs.

## 17. BigQuery

Trigger: Analytics at scale, dashboards, logs, ML-in-SQL, and data sharing.

Corrected key highlights:

- Serverless columnar analytics separates storage from compute and scales slots transparently.

- Partitioning, clustering, materialized views, BI Engine, and result cache cut latency and cost.

- Row/column policies, policy tags, data masking, audit logs, and VPC SC support governance.

- BigQuery ML and remote functions reduce data movement for ML/AI workflows.

- BigQuery Omni queries data in S3/Azure Blob through BigLake tables without moving raw data into BigQuery.

Missing / add for interview:

- Add reservations/editions, slot autoscaling, and billing export for FinOps.

- Clarify Omni costs: raw data need not move, but query results/multi-cloud transfer choices still matter.

Gotcha: BigQuery is not an OLTP database; do not use it for high-QPS transactional writes.

## 18. Cloud Pub/Sub

Trigger: Microservices or data pipelines need asynchronous decoupling.

Corrected key highlights:

- At-least-once delivery is default; subscribers must be idempotent.

- Exactly-once delivery applies to pull/StreamingPull within a region, not every subscription mode.

- DLQs, retry policies, filtering, ordering keys, and schemas reduce poison-message blast radius.

- Retention, snapshots, and seek support recovery/replay.

- Push, pull, export subscriptions, and Pub/Sub Lite match different latency/cost/control needs.

Missing / add for interview:

- Add backpressure signals: oldest_unacked_message_age, backlog bytes/messages, ack deadline expirations.

- Topic retention can go up to 31 days; default subscription unacked retention is 7 days.

Gotcha: Exactly-once does not remove the need for idempotent business logic.

## 19. Cloud Dataflow

Trigger: Streaming/batch data transformation needs managed Apache Beam execution.

Corrected key highlights:

- Unified batch/streaming model standardizes pipeline code.

- Autoscaling, Streaming Engine, Dataflow Prime, and FlexRS balance latency and cost.

- DLQ side outputs isolate bad records instead of killing entire pipelines.

- Snapshots/drain/update patterns protect streaming state during deployment.

- Integrates with Pub/Sub, BigQuery, GCS, Bigtable, and VPC SC.

Missing / add for interview:

- Add watermark/late data/windowing concepts for interviews.

- Add custom templates for repeatable operator-driven launches.

Gotcha: Dataflow solves pipeline execution; it does not fix bad event-time semantics or unbounded cardinality.

## 20. Cloud Dataproc

Trigger: Spark/Hadoop ecosystem migration or managed open-source big data jobs.

Corrected key highlights:

- Managed clusters for Spark/Hadoop/Hive/Presto with fast provisioning.

- Serverless Spark removes cluster management for many jobs.

- Autoscaling, secondary workers, and graceful decommissioning reduce cost and job failures.

- Use GCS/BigLake as durable storage while treating clusters as ephemeral compute.

- Init actions and optional components standardize open-source tool stacks.

Missing / add for interview:

- Add Metastore/Dataplex integration for governance.

- Use Dataflow for Beam-native streaming; use Dataproc when Spark ecosystem compatibility matters.

Gotcha: Do not keep long-lived idle clusters; ephemeral/serverless is the FinOps default.

## 21. Vertex AI API & Model Garden

Trigger: Teams want Gemini/foundation models, tuning, evaluation, and governed AI deployment.

Corrected key highlights:

- Model Garden exposes Google, partner, and open models through managed endpoints/workflows.

- Provisioned Throughput reduces 429 risk for steady enterprise inference loads.

- Context caching, batching, and model selection lower latency and token cost.

- Evaluation, safety filters, prompt/version governance, and logging support responsible operations.

- IAM, VPC SC, CMEK, and audit logs are the security baseline.

Missing / add for interview:

- Add grounding/RAG, prompt management, function calling/tools, and model routing as interview topics.

- Add golden signals: p95 latency, tokens/sec, 4xx/5xx/429, safety block rate, groundedness/quality, cost/request.

Gotcha: Do not jump to fine-tuning first; try prompt, grounding, caching, and evaluation before tuning.

## 22. Vertex AI Vector Search

Trigger: RAG/search needs low-latency semantic retrieval across many embeddings.

Corrected key highlights:

- ANN indexes provide high-scale similarity search for embeddings.

- Metadata filters enforce tenant, permission, or category constraints during retrieval.

- Index update strategy matters: batch vs streaming, rebuild windows, and freshness requirements.

- Use with Vertex AI embeddings, BigQuery, GCS, and Agent Builder for RAG architectures.

- VPC/IAM controls protect retrieval APIs and enterprise context.

Missing / add for interview:

- Add hybrid search/reranking where exact keyword constraints matter.

- Add recall/latency/cost evaluation, not just index creation.

Gotcha: Vector Search retrieves candidates; it does not guarantee answer truth without grounding/evaluation.

## 23. Vertex AI Agent Builder

Trigger: Business wants chat/agent experiences grounded in enterprise data.

Corrected key highlights:

- Provides search, conversational agents, data connectors, grounding, and deployment tooling.

- Playbooks/flows structure agent behavior and API/tool calls.

- RAG ingestion handles chunking, embeddings, and retrieval paths for common document sources.

- Logging/evaluation helps identify failed intents, hallucination risk, and user drop-offs.

- Enterprise controls help isolate and govern data access.

Missing / add for interview:

- Add least-privilege tool calling and human escalation for risky actions.

- Add evaluation: groundedness, answer relevance, tool success rate, and abandonment rate.

Gotcha: Do not promise zero hallucination; promise grounding, guardrails, and measurement.

## 24. Document AI

Trigger: Unstructured documents need extraction, classification, and validation.

Corrected key highlights:

- Prebuilt processors extract invoices, forms, IDs, contracts, and tables.

- Custom processors/classifiers handle domain-specific layouts.

- Human-in-the-loop improves low-confidence extraction before database writes.

- Batch and online processing support archives and interactive uploads.

- Data redaction and confidence scores support compliance workflows.

Missing / add for interview:

- Add processor versioning and evaluation metrics: precision/recall, field confidence, manual review rate.

- Add Document AI Warehouse/BigQuery downstream patterns where relevant.

Gotcha: OCR accuracy is not enough; measure field-level business correctness.

## 25. Cloud Monitoring

Trigger: SRE needs to know what is slow, broken, overloaded, or burning error budget.

Corrected key highlights:

- Metrics scopes and dashboards centralize multi-project visibility.

- Alerting policies, incidents, notification channels, and uptime checks detect symptoms.

- SLOs, SLIs, and error budgets connect technical alerts to user/business impact.

- Managed Service for Prometheus brings Kubernetes/open-source metrics into Cloud Monitoring.

- Log-based and custom metrics convert app events into operational signals.

Missing / add for interview:

- Add golden signals: latency, traffic, errors, saturation.

- Add alert quality: burn-rate alerts beat noisy threshold-only alerts.

Gotcha: A dashboard is not observability unless it leads to a fast diagnosis path.

## 26. Cloud Logging

Trigger: Teams need audit, troubleshooting, SIEM export, or cost control for logs.

Corrected key highlights:

- Log Router routes logs to buckets, BigQuery, GCS, Pub/Sub, or SIEM destinations.

- Log buckets with retention, CMEK, analytics, and views support governance.

- Audit logs show admin activity, data access, and system events.

- Structured JSON logs make search and correlation much faster.

- Exclusions reduce ingestion cost but must not drop security/forensic data.

Missing / add for interview:

- Add log scopes and Log Analytics SQL for large-scale investigation.

- Add trace correlation fields so logs connect to slow requests.

Gotcha: Never exclude logs blindly; classify security, compliance, debug, and noise first.

## 27. Cloud Trace & Cloud Profiler

Trigger: Checkout/API is slow but infrastructure metrics look normal.

Corrected key highlights:

- Trace shows request path and latency across microservices.

- Profiler shows CPU, heap, wall time, and allocation hot spots in production.

- Trace/log correlation narrows investigation from user request to code path.

- Latency shift analysis helps detect regressions after releases.

- OpenTelemetry integration standardizes tracing across languages and platforms.

Missing / add for interview:

- Add Cloud Error Reporting for exception grouping and Error Reporting/Logging/Trace triage loop.

- Add sampling strategy so high traffic does not create excessive overhead/cost.

Gotcha: Tracing finds where time went; profiling finds why code consumed resources.

## 28. Cloud Billing & Recommender

Trigger: CFO/CTO says cloud bill is unpredictable or wasteful.

Corrected key highlights:

- Billing export to BigQuery enables unit economics and showback/chargeback.

- Budgets, alerts, and Pub/Sub notifications trigger governance workflows.

- Recommender identifies idle resources, rightsizing, CUD opportunities, and IAM improvements.

- Labels, folders, projects, and cost categories map spend to business owners.

- CUDs, reservations, autoscaling, and storage lifecycle policies are the main optimization levers.

Missing / add for interview:

- Add FinOps KPI: cost per transaction/user/order, not just total spend.

- Add anomaly detection and forecast alerts for sudden spikes.

Gotcha: Do not cut cost by breaking SLOs; tie savings to reliability guardrails.

## 29. Identity & Access Management (IAM)

Trigger: Security asks who can do what and how to reduce privilege risk.

Corrected key highlights:

- Least privilege with predefined/custom roles, groups, and service accounts is the baseline.

- IAM Conditions and Deny policies add contextual and hard-block controls.

- Workload Identity Federation removes long-lived keys for external workloads and CI/CD.

- Policy Analyzer/Troubleshooter explain access paths and blocked requests.

- Organization Policy sets guardrails such as disabling public IPs or key creation.

Missing / add for interview:

- Add Privileged Access Manager / break-glass patterns for temporary elevation.

- Add service account key inventory and disablement as a high-impact security action.

Gotcha: Allow policies grant; deny/org policies constrain. Know the difference.

## 30. Secret Manager

Trigger: Apps need passwords, API keys, certificates, and rotation without plaintext exposure.

Corrected key highlights:

- Central secret storage with IAM, audit logs, versions, and replication policies.

- Secret versions let apps roll forward/back during rotation.

- Cloud Run/GKE/Functions can mount or inject secrets at runtime.

- Rotation hooks can trigger workflows, but apps must reload/use new versions correctly.

- CMEK and VPC SC can strengthen governance for sensitive environments.

Missing / add for interview:

- Add separation of duties: secret admin is not necessarily secret accessor.

- Add alerting on unusual secret access and service account usage.

Gotcha: Secret Manager stores secrets; it does not automatically rotate every downstream credential unless you build the rotation flow.

## 31. Hybrid Connectivity: Cloud Interconnect & VPN

Trigger: On-prem or branch systems need reliable private connectivity to Google Cloud.

Corrected key highlights:

- Dedicated Interconnect provides high-throughput private connectivity; Partner Interconnect uses service providers.

- HA VPN provides encrypted internet-based tunnels for backup or lower-bandwidth needs.

- Cloud Router/BGP provides dynamic route exchange and failover.

- 99.99% designs require redundant connections and edge availability domains.

- Network Connectivity Center helps hub-and-spoke connectivity across VPC/on-prem/other clouds.

Missing / add for interview:

- Add MTU, route priority, asymmetric routing, and firewall rules to troubleshooting checklist.

- Add HA design diagrams when discussing production migrations.

Gotcha: A single Interconnect is not HA; redundancy is the architecture, not the product name.

## 32. Enterprise Security: VPC Service Controls & Security Command Center

Trigger: CISO fears data exfiltration, misconfiguration, or credential compromise.

Corrected key highlights:

- VPC Service Controls create service perimeters around supported Google APIs such as BigQuery/GCS.

- Ingress/egress rules allow controlled exceptions for users, projects, services, and identities.

- Security Command Center centralizes findings, vulnerabilities, misconfigurations, and threat signals.

- Event Threat Detection uses logs/signals to detect suspicious activity.

- Combine VPC SC with IAM, Org Policy, CMEK, logging, and DLP for defense-in-depth.

Missing / add for interview:

- Add dry-run mode and perimeter bridge patterns to avoid breaking production workflows.

- Add Assured Workloads for regulated-region/compliance control where relevant.

Gotcha: VPC SC is not a packet firewall; it controls API data exfiltration paths for supported services.

## 33. Migration Tooling: Migrate to VMs & Database Migration Service

Trigger: Customer must migrate with low downtime and low risk.

Corrected key highlights:

- Migrate to VMs replicates and tests VM workloads before cutover.

- Test clones reduce Day-1 surprise by validating boot, drivers, networking, and app dependencies.

- Database Migration Service uses continuous replication/CDC for low-downtime database moves.

- Migration waves should be grouped by dependency and business criticality.

- Observability and rollback plans matter as much as the migration tool.

Missing / add for interview:

- Add Migration Center for discovery/assessment/planning.

- Add cutover runbooks: DNS TTL, freeze window, validation, rollback.

Gotcha: Lift-and-shift is a step, not a modernization strategy; identify refactor candidates.

## 34. CI/CD: Cloud Build & Cloud Deploy

Trigger: Bad releases or manual deployment processes cause outages.

Corrected key highlights:

- Cloud Build provides managed CI workers, triggers, artifacts, and private pools.

- Cloud Deploy manages delivery pipelines to GKE and Cloud Run with promotion gates.

- Canary/blue-green strategies limit blast radius and accelerate rollback.

- Approvals and audit logs support release governance.

- Artifact Registry, Binary Authorization, and vulnerability scanning complete the supply-chain story.

Missing / add for interview:

- Add SLSA/provenance, SBOMs, and signed images for regulated customers.

- Add progressive delivery metrics: error rate, latency, saturation, business KPI before promotion.

Gotcha: A canary without automated metrics is just a smaller manual release.

# 4. Important Additions Missing from the 34-Service List

| Add this | Why it matters for TSAM |
| --- | --- |
| AlloyDB for PostgreSQL | Add as a standalone database service. Pitch: PostgreSQL compatibility with high performance, HA, read pools, cross-region replicas, AlloyDB AI/vector search, and better scale for enterprise Postgres workloads than self-managed Postgres. |
| Cloud KMS / Cloud HSM / EKM / Autokey | Add as standalone security/key-management service. Pitch: customer-controlled encryption, separation of duties, rotation, CMEK integrations, Autokey, and external key control for strict compliance. |
| Private Service Connect | Add to networking. Pitch: private producer/consumer access to managed services and service publishing without public IP exposure. |
| Artifact Registry | Add to CI/CD. Pitch: central container/package artifact storage with IAM, vulnerability scanning, provenance, and Binary Authorization integration. |
| Dataplex / Data Catalog / DLP | Add to data governance. Pitch: discover, classify, protect, and govern data across lakes/warehouses before analytics and AI use it. |

# 5. One-Sentence Memory Hooks

| Service | When you hear... | Say this warning |
| --- | --- | --- |
| Google Kubernetes Engine (GKE) | Apps need Kubernetes with autoscaling, safe upgrades, and portable deployment patterns | Do not say GKE autoscaling is one thing. Separate pod, node, and request-size scaling. |
| Compute Engine (GCE) | Legacy or custom VM workloads need predictable performance, DR, and cost control | Spot VMs are cheap but preemptible; only pitch them for fault-tolerant batch/stateless workers. |
| Cloud Run | Teams want serverless containers, fast releases, and lower operational overhead | Cloud Run is not always cheaper if min instances and high CPU are always on; model it. |
| Cloud Functions (2nd gen / Cloud Run functions) | Event-driven glue code is needed without managing servers | Do not pitch functions for long-running, stateful, or high-connection workloads by default. |
| GKE Enterprise | Enterprise platform teams need consistent governance across many clusters, including hybrid/multi-cloud | GKE Enterprise governs non-GKE clusters; it does not magically turn EKS/AKS into native GKE. |
| Cloud Load Balancing | Users need one reliable entry point with health-based routing and global scale | DNS failover is slower than load-balancer health checks because DNS clients cache TTLs. |
| Google Cloud Armor | Security or SRE sees bots, DDoS, OWASP attacks, or abusive traffic | A WAF is not a replacement for app fixes; it is a blast-radius and edge-control layer. |
| Cloud CDN | Marketing spikes or global users create high latency and origin overload | Do not invalidate everything during a flash sale; that can stampede the origin. |
| Cloud NAT | Private workloads need outbound internet without public IPs | NAT does not provide inbound access and is not a firewall replacement. |
| Cloud DNS | Hybrid naming, DNS failover, or private service discovery is failing | Do not promise exact traffic split with DNS; resolver caching makes it approximate. |
| Cloud SQL | Teams need managed MySQL/PostgreSQL/SQL Server without running database VMs | Cloud SQL is regional relational DB, not globally active-active like Spanner. |
| Cloud Spanner | Global relational transactions need scale, high availability, and strong consistency | Do not choose Spanner just because it is Google-scale; choose it when relational + global scale/HA justify complexity/cost. |
| Cloud Bigtable | Massive low-latency key-value/wide-column workloads such as telemetry or time series | Bigtable is not SQL and not for ad hoc analytics; pair with BigQuery for analytics. |
| Firestore | Mobile/web apps need serverless document database with realtime sync | Firestore is not a relational join engine; model data around query patterns. |
| Memorystore | App latency or database saturation needs a managed in-memory cache | A cache is not the source of truth; plan DB fallback and warm-up behavior. |
| Cloud Storage | Durable object storage is needed for data lakes, backups, static assets, and artifacts | Buckets are globally named and object storage is not a filesystem unless using compatible tooling with tradeoffs. |
| BigQuery | Analytics at scale, dashboards, logs, ML-in-SQL, and data sharing | BigQuery is not an OLTP database; do not use it for high-QPS transactional writes. |
| Cloud Pub/Sub | Microservices or data pipelines need asynchronous decoupling | Exactly-once does not remove the need for idempotent business logic. |
| Cloud Dataflow | Streaming/batch data transformation needs managed Apache Beam execution | Dataflow solves pipeline execution; it does not fix bad event-time semantics or unbounded cardinality. |
| Cloud Dataproc | Spark/Hadoop ecosystem migration or managed open-source big data jobs | Do not keep long-lived idle clusters; ephemeral/serverless is the FinOps default. |
| Vertex AI API & Model Garden | Teams want Gemini/foundation models, tuning, evaluation, and governed AI deployment | Do not jump to fine-tuning first; try prompt, grounding, caching, and evaluation before tuning. |
| Vertex AI Vector Search | RAG/search needs low-latency semantic retrieval across many embeddings | Vector Search retrieves candidates; it does not guarantee answer truth without grounding/evaluation. |
| Vertex AI Agent Builder | Business wants chat/agent experiences grounded in enterprise data | Do not promise zero hallucination; promise grounding, guardrails, and measurement. |
| Document AI | Unstructured documents need extraction, classification, and validation | OCR accuracy is not enough; measure field-level business correctness. |
| Cloud Monitoring | SRE needs to know what is slow, broken, overloaded, or burning error budget | A dashboard is not observability unless it leads to a fast diagnosis path. |
| Cloud Logging | Teams need audit, troubleshooting, SIEM export, or cost control for logs | Never exclude logs blindly; classify security, compliance, debug, and noise first. |
| Cloud Trace & Cloud Profiler | Checkout/API is slow but infrastructure metrics look normal | Tracing finds where time went; profiling finds why code consumed resources. |
| Cloud Billing & Recommender | CFO/CTO says cloud bill is unpredictable or wasteful | Do not cut cost by breaking SLOs; tie savings to reliability guardrails. |
| Identity & Access Management (IAM) | Security asks who can do what and how to reduce privilege risk | Allow policies grant; deny/org policies constrain. Know the difference. |
| Secret Manager | Apps need passwords, API keys, certificates, and rotation without plaintext exposure | Secret Manager stores secrets; it does not automatically rotate every downstream credential unless you build the rotation flow. |
| Hybrid Connectivity: Cloud Interconnect & VPN | On-prem or branch systems need reliable private connectivity to Google Cloud | A single Interconnect is not HA; redundancy is the architecture, not the product name. |
| Enterprise Security: VPC Service Controls & Security Command Center | CISO fears data exfiltration, misconfiguration, or credential compromise | VPC SC is not a packet firewall; it controls API data exfiltration paths for supported services. |
| Migration Tooling: Migrate to VMs & Database Migration Service | Customer must migrate with low downtime and low risk | Lift-and-shift is a step, not a modernization strategy; identify refactor candidates. |
| CI/CD: Cloud Build & Cloud Deploy | Bad releases or manual deployment processes cause outages | A canary without automated metrics is just a smaller manual release. |

# 6. Sources Checked

- GKE attached clusters overview: https://docs.cloud.google.com/kubernetes-engine/multi-cloud/docs/attached/eks/concepts/overview

- GKE deployment options: https://docs.cloud.google.com/kubernetes-engine/enterprise/docs/deployment-options

- Multi Cluster Ingress: https://docs.cloud.google.com/kubernetes-engine/docs/concepts/multi-cluster-ingress

- BigQuery Omni introduction: https://docs.cloud.google.com/bigquery/docs/omni-introduction

- Cloud CDN cache invalidation: https://docs.cloud.google.com/cdn/docs/cache-invalidation-overview

- Cloud CDN stale content: https://docs.cloud.google.com/cdn/docs/serving-stale-content

- Pub/Sub exactly-once delivery: https://docs.cloud.google.com/pubsub/docs/exactly-once-delivery

- Pub/Sub quotas and retention limits: https://docs.cloud.google.com/pubsub/quotas

- Memorystore RDB persistence: https://docs.cloud.google.com/memorystore/docs/cluster/about-rdb-persistence

- Memorystore AOF persistence: https://docs.cloud.google.com/memorystore/docs/cluster/about-aof-persistence

- Memorystore for Valkey: https://docs.cloud.google.com/memorystore/docs/valkey

- Spanner Graph overview: https://docs.cloud.google.com/spanner/docs/graph

- Cloud DNS routing policies: https://cloud.google.com/dns/docs/routing-policies-overview

- Cloud KMS Autokey: https://docs.cloud.google.com/kms/docs/kms-autokey

- Cloud KMS CMEK: https://docs.cloud.google.com/kms/docs/cmek

- AlloyDB AI: https://cloud.google.com/alloydb/ai
