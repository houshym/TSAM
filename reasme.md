# Google Cloud TSAM Master Cheat Sheet (L6/L7 Level)

## The TSAM 4-Step Executive Reflex
Use this reflex for every RRK1 and RRK2 scenario to manage the CTO (FinOps/Efficiency), Head of SRE (Stability), and Ops Lead (Visibility).
1. **Accept Ambiguity:** Never solve immediately. Ask clarifying questions (e.g., "Are we scaling for a **Marketing** event or addressing daily load?").
2. **Diagnose via Golden Signals:** Translate SRE symptoms into business impact (e.g., "CPU **Saturation** will cause **Latency**, which destroys our **Revenue**").
3. **Tactical Fix:** The immediate architectural patch to restore **Business Continuity** today.
4. **Strategic Fix:** The long-term transformation (FinOps, CCoE, Modernization) to prevent recurrence.

---

## Pillar 1: Compute & Platform Modernization

### 1. Google Kubernetes Engine (GKE)
* **Cluster Autoscaler (CA):** Dynamically adjusts physical nodes. Prevents node-level **Saturation** during unpredicted **Traffic** spikes, protecting **Business Continuity**.
* **Horizontal Pod Autoscaler (HPA):** Scales pod replicas based on CPU/Memory. Lowers application **Latency** during viral surges.
* **Vertical Pod Autoscaler (VPA):** Optimizes container resource limits. Mitigates OOMKilled **Errors** while maximizing **FinOps** efficiency.
* **Liveness/Readiness Probes:** Isolates failing containers to prevent downstream routing **Errors**, safeguarding consumer-facing **Revenue**.
* **Pod Disruption Budgets (PDBs):** Enforces minimum availability during upgrades. Prevents self-inflicted application **Latency**.

### 2. Compute Engine (GCE)
* **Managed Instance Groups (MIGs):** Pools identical VMs to scale automatically, preventing compute **Saturation**.
* **Stateful MIGs:** Preserves unique VM identifiers and persistent disks upon recreation. Ensures legacy backend **Business Continuity**.
* **Committed Use Discounts (CUDs):** Contractual resource commitments (1/3 years) in exchange for deep cost reductions. The foundation of structured **FinOps**.
* **Live Migration:** Moves running VMs to new hosts during maintenance. Eliminates operational downtime.
* **Spot VMs:** Heavily discounted preemptible compute. Ideal for fault-tolerant batch architectures to minimize data-processing **FinOps** waste.

### 3. Cloud Run
* **Scale-to-Zero:** Instantly scales down when there is no **Traffic**, eliminating idle compute spend to maximize **FinOps** efficiency.
* **Min-Instances:** Keeps a baseline pool of containers continuously warm. Eliminates cold-start **Latency** to secure high-value **Revenue**.
* **Max-Instances:** Implements a hard scaling ceiling to protect downstream monolithic databases from connection pool **Saturation**.
* **Tag-Based Traffic Routing:** Enables safe Canary testing before routing production **Traffic**, limiting the blast radius of deployment **Errors**.
* **VPC Connector:** Connects serverless workloads to private resources, protecting internal data paths.

### 4. Cloud Functions (Gen 2)
* **Eventarc Triggers:** Direct routing of events from 90+ GCP sources. Creates decoupled architectures to preserve system **Business Continuity**.
* **Concurrency Support:** Handles up to 1,000 concurrent requests per instance. Lowers instance sprawl and controls memory **Saturation**.
* **Extended Request Timeout:** Supports 60-minute executions. Prevents timeout **Errors** on heavy backend payloads.
* **Granular Billing Model:** Measures cost to the nearest 100ms, aligning perfectly with lean **FinOps** goals.

### 5. GKE Enterprise (Anthos)
* **Multi-Cluster Management:** Centralized control plane for multi-cloud topologies. Ensures unified **Business Continuity** across AWS and GCP.
* **Anthos Config Management (ACM):** Enforces declarative GitOps across clusters. Prevents configuration drift and manual compliance **Errors**.
* **Anthos Service Mesh (ASM):** Managed Istio engine providing deep microservice **Visibility** and mTLS security.
* **Circuit Breaking (via ASM):** Automatically trips connections to a failing microservice. Prevents cascading failures and thread **Saturation**.
* **Cross-Cloud Consistency:** Standardizes runtime environments. Abstracts operational complexity to prevent cognitive **Saturation** for the SRE team.

---

## Pillar 2: Networking, Traffic Management & Edge

### 6. Cloud Load Balancing
* **Global Anycast IP:** Routes **Traffic** over Google’s private backbone to minimize public routing **Latency**.
* **Cross-Region Failover:** Automatically reroutes to healthy secondary regions if a datacenter fails, guaranteeing **Business Continuity**.
* **Capacity-Based Spilling:** Gracefully spills excess traffic to alternative regions to eliminate backend **Saturation**.
* **QUIC Protocol Support:** Enables HTTP/3. Slashes mobile web connection **Latency**, directly improving e-commerce **Revenue**.

### 7. Google Cloud Armor
* **Adaptive Protection:** Uses ML to establish baseline behavior and dynamically block anomalous spikes. Protects **Revenue** during **Marketing** flash sales.
* **DDoS Mitigation:** Absorbs high-volume distributed attacks at the edge, preventing malicious **Traffic** from causing infrastructure **Saturation**.
* **Preview Mode:** Logs rule triggers without blocking. Allows verification of security changes without risking accidental 403 **Errors** on real users.
* **Geo-Blocking & WAF:** Mitigates OWASP Top 10 vulnerabilities to prevent security-induced **Errors**.

### 8. Cloud CDN
* **Edge Caching:** Caches static assets at global Points of Presence, drastically lowering delivery **Latency**.
* **Cache Egress Cost Reduction:** Routes data out of edge caches rather than backends. Optimizes **FinOps** value.
* **Cache Invalidation:** Supports instant global cache clearing. Ensures rapid visibility for crucial **Marketing** asset updates.
* **Stale-While-Revalidate:** Serves expired assets to users while asynchronously fetching fresh content, hiding origin **Latency**.

### 9. Cloud NAT
* **Managed Serverless NAT:** Allows private VMs outbound internet access without public IPs. Scales automatically to prevent throughput **Saturation**.
* **Dynamic Port Allocation:** Adjusts allocated ports per instance based on workload usage. Prevents port exhaustion **Errors** under heavy load.
* **Endpoint Independent Mapping:** Guarantees connection consistency for multi-tier apps, preventing routing **Errors**.

### 10. Cloud DNS
* **100% SLA Anycast Network:** Leverages global PoPs. Guarantees public domain resolution **Business Continuity**.
* **Geographic Routing Policies:** Directs users to explicit target clusters based on location to lower handshake **Latency**.
* **Internal Health Checking:** Automatically alters routing based on load balancer states, mitigating localized regional **Errors**.

---

## Pillar 3: Databases & Caching

### 11. Cloud SQL
* **Automated HA Failover:** Uses synchronous storage replication across zones. Prevents downtime to protect **Business Continuity**.
* **Read Replicas:** Spreads heavy read queries across up to 30 instances. Offloads primary engine load to eliminate CPU **Saturation**.
* **Storage Auto-Resize:** Automatically expands underlying storage allocations. Prevents disk-full write **Errors**.
* **Query Insights:** Telemetry panel tracking slow queries. Diagnoses application-layer **Latency** for the Ops Lead.

### 12. Cloud Spanner
* **External Consistency & Global Scale:** Achieves relational consistency globally. Eliminates regional sharding **Errors**.
* **99.999% Availability SLA:** Provides ultimate uptime for multi-region configurations, protecting mission-critical **Revenue**.
* **Zero-Downtime Schema Updates:** Executes real-time changes without scheduled maintenance windows.
* **Interleaved Tables:** Co-locates child rows next to parent records, optimizing joins and dropping query **Latency**.

### 13. Cloud Bigtable
* **High-Throughput NoSQL:** Sub-10ms execution at massive scale. Ideal for absorbing massive clickstream **Traffic** from **Marketing** events.
* **Live Node Scalability:** Adjusts compute nodes dynamically with zero downtime. Prevents node-level **Saturation**.
* **Key Visualizer:** Diagnostic tool that visualizes access patterns to identify hot-spots causing localized **Latency**.

### 14. Firestore
* **Live Document Synchronization:** Pushes changes to listening clients via WebSockets, minimizing client-side sync **Latency**.
* **Offline Data Persistence:** Caches states locally on devices. Protects mobile app **Business Continuity** during poor connectivity.
* **Automatic Scale Engineering:** Absorbs sudden viral spikes without manual configuration, preventing API **Errors**.

### 15. Memorystore (Redis)
* **Sub-Millisecond Caching:** Caches frequent queries in memory. Dramatically drops application database **Latency**.
* **Downstream DB Shielding:** Absorbs transaction bursts to protect underlying core databases from connection **Saturation**.
* **Highly Available Architecture:** Active-replica node topologies with automated failover routing mitigate cache loss **Errors**.

---

## Pillar 4: Storage & Data Analytics

### 16. Cloud Storage (GCS)
* **Storage Class Lifecycle Automation:** Automatically migrates data to colder tiers based on age to optimize **FinOps** spend.
* **Object Versioning:** Retains history of overwritten data. Defends **Business Continuity** against ransomware or human **Errors**.
* **Signed URLs:** Grants time-bounded access directly to files, bypassing API services to prevent network **Saturation**.

### 17. BigQuery
* **Serverless Architecture Engine:** Scales parallel worker slots to prevent query processing **Saturation** without manual sizing.
* **BigQuery ML:** Executes ML models using standard SQL. Eliminates complex data movement, preventing operational **Latency**.
* **BigQuery Omni:** Queries data stored in AWS S3 or Azure directly. Avoids massive egress **FinOps** fees for multi-cloud setups.

### 18. Cloud Pub/Sub
* **Asynchronous Messaging:** Decouples microservices. Prevents upstream system crashes from causing downstream data **Errors**.
* **Dead Letter Topics (DLQs):** Quarantines unprocessable messages. Prevents processing loops from causing service **Saturation**.
* **Global Message Routing:** Ingests massive **Traffic** at the edge and routes securely, ensuring event-driven **Business Continuity**.

### 19. Cloud Dataflow
* **Horizontal Autoscaling:** Dynamically adjusts worker VMs mid-flight based on pipeline load, eliminating computational **Saturation**.
* **Streaming Engine:** Isolates execution state from worker VMs. Lowers data shuffling **Latency** and controls costs.
* **Snapshot State Saving:** Point-in-time backups of active pipelines. Allows code updates without state loss or execution **Errors**.

### 20. Cloud Dataproc
* **Serverless Dataproc Mode:** Executes Spark jobs without managing explicit VMs. Minimizes **FinOps** overhead.
* **Secondary Preemptive Worker Pools:** Uses cheap Spot VMs to handle heavy compute spikes, drastically slashing processing costs.
* **Graceful Decommissioning:** Drains running tasks safely before downscaling, preventing batch data-processing **Errors**.

---

## Pillar 5: GenAI & Advanced AI Systems

### 21. Vertex AI API & Model Garden
* **Provisioned Throughput:** Guarantees explicit requests-per-second capacities. Prevents 429 Rate Limit **Errors** during critical launches.
* **Context Caching:** Caches static document contexts at the gateway. Cuts prompt **Latency** and lowers inference **FinOps** costs.
* **Safety Filters:** Automatically blocks toxic content to protect brand reputation and **Revenue**.

### 22. Vertex AI Vector Search
* **Low-Latency ANN Indexing:** Sub-millisecond similarity lookups across billions of vectors. Avoids exhaustive database scan **Latency**.
* **Auto-scaling Indexing:** Automatically scales lookup infrastructure based on query **Traffic** to eliminate query **Saturation**.

### 23. Vertex AI Agent Builder
* **Enterprise Grounding:** Validates AI prompts against verified corporate data (BigQuery). Minimizes hallucination **Errors**.
* **Out-of-the-Box RAG:** Automates chunking and embedding generation, drastically reducing time-to-market for **Revenue** generation.

### 24. Document AI
* **Human-in-the-Loop (HITL):** Routes low-confidence extractions to human review. Prevents automated data **Errors** from corrupting databases.
* **High-Volume Batch Processing:** Processes thousands of documents concurrently without causing API **Saturation**.

---

## Pillar 6: Operations, Visibility & Governance

### 25. Cloud Monitoring
* **SLO/SLI Metrics:** Defines Error Budgets to align SRE teams with true business availability and **Revenue** targets.
* **Centralized Dashboards:** Consolidates health metrics across multi-project environments, providing complete operational **Visibility**.
* **Alert Fatigue Suppression:** Groups related alerts to prevent SRE cognitive **Saturation** during major incidents.

### 26. Cloud Logging
* **Log Router Sinks:** Filters and routes logs to GCS for compliance or BigQuery for analytics, optimizing storage **FinOps**.
* **Error Reporting:** Aggregates stack traces automatically. Surfaces critical bugs before they impact user retention and **Revenue**.
* **Live Log Tail:** Streams active production logs in real-time to accelerate debugging and reduce outage **Latency**.

### 27. Cloud Trace & Cloud Profiler
* **Distributed Tracking:** Records latency propagation across microservices. Identifies the exact service causing user **Latency**.
* **Continuous CPU Profiling:** Analyzes runtime threads with <1% overhead. Identifies code causing hidden **Saturation**.
* **Memory Leak Detection:** Visualizes heap allocation over time to find code triggering OOM **Errors**.

### 28. Google Cloud Billing & Recommender
* **Rightsizing Recommendations:** Uses ML to find over-provisioned databases and idle nodes, driving immediate **FinOps** savings.
* **Automated BQ Export:** Streams raw cost data to BigQuery for precise unit-economics modeling against **Revenue**.
* **Budget Alerts:** Triggers programmatic cost-capping scripts when spending trends project a breach, protecting the CTO's budget.

### 29. Identity & Access Management (IAM)
* **Workload Identity Federation:** Bridges external IdPs (AWS/Azure) with GCP. Eliminates static keys, preventing unauthorized **Traffic**.
* **Organizational Policies:** Enforces strict, top-down governance (e.g., blocking public IPs) to prevent human configuration **Errors**.
* **Conditional IAM:** Enforces context-based access rules, protecting **Business Continuity** during sensitive deployment windows.

### 30. Secret Manager
* **Automated Secret Rotation:** Hooks into Cloud Functions to change database credentials periodically with zero application **Latency**.
* **Direct GKE Mounting:** Injects secrets directly into pod volumes. Eliminates credential exposures in application source code.

---

## Pillar 7: Enterprise Perimeter & Migration (The Final Bridge)

### 31. Hybrid Connectivity (Interconnect & VPN)
* **Dedicated Interconnect:** Direct physical fiber connection. Provides consistent, low **Latency** to prevent public internet **Traffic** bottlenecks.
* **Dynamic Routing (BGP):** Automatically updates network routes via Cloud Router, preventing routing **Errors** when on-premise subnets change.

### 32. VPC Service Controls & Security Command Center
* **VPC SC Perimeters:** Creates an invisible security ring around managed services (BigQuery/GCS). Completely blocks data exfiltration **Traffic**.
* **SCC Premium:** Centralized threat reporting. Provides the CISO with complete **Visibility** into misconfigurations across all projects.

### 33. Migrate to Virtual Machines (M2VM) & DMS
* **M2VM Test-Clone:** Spins up isolated sandbox copies of live on-premise VMs in GCP before cutover. Eliminates "Day 1" deployment **Errors**.
* **Change Data Capture (CDC):** Trickle-feeds live database changes during migration. Ensures zero data loss, securing transactional **Business Continuity**.

### 34. CI/CD (Cloud Deploy & Cloud Build)
* **Automated Canary Deployments:** Natively shifts **Traffic** percentages (e.g., 5%). Limits the blast radius of bad code, protecting the user experience from massive **Errors**.
* **Cloud Build Private Pools:** Runs CI/CD workers inside secure VPCs. Prevents proprietary source code from traversing the public internet.

### 35. Apigee API Management
* **Advanced Rate Limiting & Quotas:** Throttles aggressive third-party **Traffic**. Prevents backend database **Saturation** and protects **Business Continuity**.
* **API Monetization:** Packages APIs into tiered products, directly driving net-new **Revenue**.
* **Centralized API Analytics:** Provides the Ops Lead with deep **Visibility** into third-party API **Errors** and geographic **Latency**.
