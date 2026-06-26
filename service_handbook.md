# GCP TSAM — Decision Trees & Crisis Playbooks

A recall-first study doc. **Part 1** = selection trees (which service?). **Part 2** = crisis playbooks (symptom → diagnosis → fix). **Part 3** = the repeating meta-rules that let you reconstruct everything else.

> How to use it: memorize the **fork questions** in Part 1 (≈6 questions per tree, not 35 facts) and the **IF→THEN shape** of Part 2. The forks are the same questions you'd ask the customer out loud, so drilling this is also rehearsing your consultative voice.

---

# PART 1 — SELECTION DECISION TREES

## Tree 1 — Database / data store

```
Need to store data?
├─ Relational? (rows, joins, ACID transactions)
│   ├─ Global + strong consistency? (multi-region, no double-spend, financial ledger)
│   │     └─ YES → CLOUD SPANNER        (external consistency via TrueTime)
│   ├─ Need high performance / HTAP / vector for RAG?
│   │     └─ YES → ALLOYDB              (columnar engine + read pools + pgvector)
│   └─ Standard relational app / lift-and-shift?
│         └─ → CLOUD SQL                (Enterprise Plus edition for more perf)
└─ Non-relational (NoSQL)?
    ├─ Huge throughput, single-digit-ms, time-series / IoT, wide-column?
    │     └─ → BIGTABLE
    ├─ App / document data, mobile sync, serverless?
    │     └─ → FIRESTORE
    └─ Cache / session store?
          └─ → MEMORYSTORE (Redis)
```
**Forks to memorize:** relational? → global+consistent? → high-perf/vector? (For NoSQL: throughput/time-series? → document? → cache?)

## Tree 2 — Compute (where to run code)

```
Need to run a workload?
├─ Containers?
│   ├─ Full Kubernetes control / stateful / service mesh / complex orchestration?
│   │     └─ → GKE                       (Autopilot for hands-off nodes)
│   └─ Stateless container, want zero ops + scale-to-zero?
│         └─ → CLOUD RUN
├─ Event-driven single function? (file lands, message arrives, row changes)
│     └─ → CLOUD FUNCTIONS              (+ Eventarc, idempotent handler)
├─ Need full OS control / GPUs / legacy / specific kernel / VM lift-and-shift?
│     └─ → COMPUTE ENGINE               (Spot VMs for batch; MIGs for HA)
└─ Existing web app wanting managed PaaS?
      └─ → APP ENGINE
```
**Forks:** containers? → need full k8s control vs just run-a-container → event glue? → need the OS/GPU?

## Tree 3 — Storage & data movement

```
What kind of storage or movement?
├─ Object / blob? (files, images, backups, data lake)
│     └─ → CLOUD STORAGE               (Bucket Lock = immutable; lifecycle = cost)
├─ Shared POSIX / NFS file system? (legacy app, read-write-many in GKE)
│     └─ → FILESTORE                    (Regional / Enterprise tier for HA)
├─ Block storage attached to a VM?
│     └─ → PERSISTENT DISK / HYPERDISK
├─ Centralized backup & DR across VMs / DBs / GKE?
│     └─ → BACKUP AND DR SERVICE        (immutable, isolated vaults)
└─ Move / replicate data between systems?
    ├─ CDC from a DB / near-zero-downtime migration / live feed to BigQuery?
    │     └─ → DATASTREAM
    ├─ Bulk one-time transfer (on-prem / other cloud → GCS)?
    │     └─ → STORAGE TRANSFER SERVICE
    └─ Stream + transform events in flight?
          └─ → PUB/SUB + DATAFLOW
```
**Forks:** object vs file vs block? → backup/DR? → moving data (CDC vs bulk vs streaming)?

## Tree 4 — Data processing & analytics

```
What do you need to do with data?
├─ Ad-hoc SQL analytics over huge data / warehouse / in-warehouse ML?
│     └─ → BIGQUERY                     (partition+cluster+reservations for cost)
├─ Transform / move data (ETL / ELT)?
│   ├─ Code-based (Beam), complex, streaming?
│   │     └─ → DATAFLOW                 (Streaming Engine + dead-letter)
│   ├─ SQL transforms inside BigQuery (ELT)?
│   │     └─ → DATAFORM
│   └─ Existing Spark / Hadoop?
│         └─ → DATAPROC
├─ Async messaging / event backbone / decouple services?
│     └─ → PUB/SUB
├─ Govern / catalog / lineage / data-quality across the estate?
│     └─ → DATAPLEX
└─ Orchestrate pipelines (DAGs)?
      └─ → CLOUD COMPOSER (Airflow)
```
**Forks:** analyze (BigQuery) vs move/transform (Dataflow/Dataform/Dataproc) vs message (Pub/Sub) vs govern (Dataplex)?

## Tree 5 — AI / ML

```
What's the AI / ML need?
├─ Use a foundation model? (chat, summarize, generate)
│   ├─ Want model choice / third-party / open models?
│   │     └─ → MODEL GARDEN  (on Vertex AI)
│   └─ Predictable high-volume capacity (no 429s under burst)?
│         └─ → PROVISIONED THROUGHPUT
├─ RAG / enterprise search that MUST respect permissions?
│     └─ → VERTEX AI SEARCH            (ACL-aware retrieval)
├─ Autonomous agent that takes actions?
│     └─ → AGENT BUILDER / AGENT ENGINE (bounded + IAM-scoped tools + Model Armor)
├─ Extract structured data from documents? (PDFs, invoices, forms)
│     └─ → DOCUMENT AI                  (+ human-in-the-loop)
├─ Train / serve a custom model with MLOps?
│   ├─ No-code on tabular / image data?
│   │     └─ → AUTOML (Vertex AI)
│   └─ Custom code + pipelines?
│         └─ → VERTEX AI (Pipelines, Registry, Model Monitoring)
└─ Guardrail an AI app (injection, sensitive data, jailbreak)?
      └─ → MODEL ARMOR
```
**Forks:** consume a model? → RAG? → agent (actions)? → read documents? → train custom? → guardrail?

## Tree 6 — Networking / connectivity

```
What networking need?
├─ Isolate workloads / segment a flat network / block exfiltration?
│     └─ → VPC + firewall policies + VPC SERVICE CONTROLS (dry-run first)
├─ Distribute traffic / global low latency / failover / secure entry point?
│     └─ → GLOBAL EXTERNAL APPLICATION LB  (+ Cloud Armor)
├─ Speed up static content / offload origin?
│     └─ → CLOUD CDN
├─ Connect on-prem / other cloud privately?
│   ├─ Dedicated high bandwidth + SLA?
│   │     └─ → CLOUD INTERCONNECT
│   └─ Quick / encrypted over the internet?
│         └─ → HA VPN
├─ Private access to a managed service / SaaS (no internet path)?
│     └─ → PRIVATE SERVICE CONNECT
└─ Multi-site hub-and-spoke topology?
      └─ → NETWORK CONNECTIVITY CENTER
```
**Forks:** isolate? → distribute/balance? → cache? → hybrid link (dedicated vs VPN)? → private service access?

## Tree 7 — Security control

```
What's the security need?
├─ Who can do what? (access)                 → IAM (least privilege, Workload Identity, Recommender)
├─ Protect a public app? (DDoS / WAF / bots)  → CLOUD ARMOR (+ reCAPTCHA Enterprise)
├─ See risk / misconfig / threats org-wide?   → SECURITY COMMAND CENTER
├─ Detect & respond to active threats (SOC)?  → GOOGLE SECOPS (SIEM / SOAR / UEBA)
├─ Protect data at rest / keys / secrets?     → CLOUD KMS (CMEK/EKM) + SECRET MANAGER
├─ Data residency / sovereignty / compliance? → ASSURED WORKLOADS + Org Policy (resource-locations)
├─ Find / classify sensitive data (PII)?      → SENSITIVE DATA PROTECTION (DLP)
└─ Secure the software supply chain?          → ARTIFACT REGISTRY scanning + BINARY AUTHORIZATION (Cloud Build SLSA)
```
**Forks:** access vs perimeter vs detection vs data-protection vs compliance vs supply-chain?

---

# PART 2 — CRISIS PLAYBOOKS (IF → THEN)

Each follows the same shape: **IF** (symptoms) → **DIAGNOSE** (root cause + golden signals) → **STOP THE BLEEDING** (reversible, 0–3 wks) → **DURABLE FIX** (3–12 mo) → **persona wins**. Reversible-first always.

---

## Playbook 1 — AI / agent runaway cost + outage + compliance bypass
*(the AetherPay pattern)*

**IF:** an AI/agent feature causes token cost to spike, threads/pods block waiting on it, latency explodes (e.g. 200ms → 30s), 504s spread, and the app "fails open" past a compliance gate.

**DIAGNOSE:** a non-deterministic, *unbounded* workload was placed *synchronously in the critical path* of a deterministic system, sharing its compute, with no cost cap, no output observability, and a dangerous fail-open default. Golden signals: **Saturation** (thread-pool exhaustion) → **Latency** → **Errors**; Traffic is normal (it's an *input class*, not load).

**STOP THE BLEEDING:**
- Token/quota caps + **Model Armor** prompt-size limit (kills the cost spike).
- Max-iteration / max-token **circuit breaker** in the agent loop (and at **Apigee**).
- **Decouple from the critical path:** **Pub/Sub** between the app and the agent (async), **dead-letter** for poison inputs → thread exhaustion ends, latency recovers.
- **Flip fail-open → fail-closed:** hold/queue pending a verdict; never settle unchecked.
- **Isolate:** move the agent to a **separate GKE node pool**.
- **See it:** **Cloud Monitoring** on token rate, loop count, compliance-completion %, queue depth (not just HTTP 200).

**DURABLE FIX:** re-platform on **Agent Engine** (bounded execution + IAM-scoped tools + agent identity/audit); **Gen AI Evaluation** + adversarial tests in **Cloud Build** CI; canary rollout (never 100% at once); per-transaction AI unit economics + anomaly alerts; route simple docs to **Gemini Flash**, complex to **Pro**.

**Persona wins:** CTO → caps + Flash routing cut spend without killing the EU launch. SRE → node-pool isolation + async means a PDF can't take down the ledger. Ops → token/compliance dashboards page you before customers do.

---

## Playbook 2 — Unbounded autoscaling blew the budget (+ outage)

**IF:** an autoscaling event scaled without limit, costs spiked, and/or the scale-up still failed to absorb load.

**DIAGNOSE:** no upper bound + scaling on the wrong signal, often into a downstream bottleneck (DB) so more instances just pile onto a blocked dependency. Golden signals: **Saturation** at the bottleneck, not the autoscaled tier.

**STOP THE BLEEDING:** set **max-instances / HPA + cluster-autoscaler ceilings**; **budget alerts + anomaly detection**; cache the hot read path (**Memorystore**); add **Cloud SQL read replicas** to relieve the DB.

**DURABLE FIX:** scale on the *right* signal (queue depth / concurrency), decouple the bottleneck (**Pub/Sub** + async), CUDs for the steady base + Spot for spillover, capacity planning tied to the demand calendar.

**Persona wins:** CTO → guardrails cap spend without blocking legit spikes. SRE → scaling validated by load test, not hope. Ops → anomaly alert on day one, not at the invoice.

---

## Playbook 3 — Single-region outage / failover that didn't trigger

**IF:** a regional disruption took the app fully down; the "DR plan" had never been executed; a DB failover didn't fire; RTO unknown.

**DIAGNOSE:** they had a DR *document*, not a DR *capability* — untested. A read replica is not a DR strategy.

**STOP THE BLEEDING:** define **RTO/RPO per tier** with the business; enable **Cloud SQL regional HA** and *test the failover* in a controlled game day; validate **backup restores**; map dependencies to find other single-homed assumptions.

**DURABLE FIX:** tiered **multi-region** (warm standby) for tier-1 paths only; data-layer HA + cross-region replicas per RPO; **production-readiness reviews** with a DR gate; scheduled game days so recovery stays proven.

**Persona wins:** CTO → only tier-1 goes multi-region; cost matches criticality. SRE → the plan is the *proven* game day, not a doc. Ops → health-based failover triggers + a rehearsed runbook.

---

## Playbook 4 — DDoS / bots / scalpers on a public app

**IF:** spikes of legit + bot traffic, credential stuffing, scalpers buying inventory in ms, occasional DDoS downing checkout, no WAF/bot management.

**DIAGNOSE:** the highest-value moments are least protected — everything hits origin with no edge defense layer.

**STOP THE BLEEDING:** **Cloud Armor** (WAF + always-on DDoS + **per-identity rate limiting**) on the global ALB; **reCAPTCHA Enterprise** (risk-*scoring*, not hard-block) for bots; **Cloud CDN** to absorb static volume; rate-limit the **auth** path against credential stuffing.

**DURABLE FIX:** **Adaptive Protection** (ML DDoS) tuned per endpoint; standard edge front door for all public endpoints; inventory fairness (waiting room + **server-side purchase limits**) so speed stops being the bot's advantage; pre-event attack/load simulation.

**Persona wins:** CTO → one bad on-sale > a year of edge cost; fairness protects conversion. SRE → managed global edge *adds* availability; dry-run rules first. Ops → Cloud Armor dashboards distinguish attack vs spike in real time.

---

## Playbook 5 — Cost up AND reliability down *(the classic)*

**IF:** cloud spend rose sharply (e.g. +40% YoY) while reliability got *worse*.

**DIAGNOSE:** not two problems — one. The same missing control plane that let spend run wild let fragility (untested failover, single-homed deps) reach production. **Re-anchor on control** — this unifies CTO and SRE.

**STOP THE BLEEDING:** unified observability (**Cloud Ops** + Managed Prometheus) + golden-signal dashboards; define **SLOs** with a baseline; **Cloud SQL HA** tested; autoscaling **ceilings** + budget anomaly alerts; incident process + runbooks.

**DURABLE FIX:** tiered multi-region for tier-1; **error-budget policy** (reliability work outranks features when budget burns); FinOps maturity (labels, showback, CUDs, rightsizing) run *alongside* reliability reviews; PRRs as a launch gate.

**Persona wins:** CTO → six-week board story + cost discipline. SRE → a systematic program, not band-aids. Ops → week-one visibility.

---

## Playbook 6 — RAG assistant leaking confidential data

**IF:** an internal GenAI assistant answers everyone from the whole corpus — surfaces salaries, privileged docs to users who shouldn't see them.

**DIAGNOSE:** not model quality — an *authorization* problem. Retrieval ignores who's asking. (Missing layer: **control**.)

**STOP THE BLEEDING:** move to **Vertex AI Search** with **document-level ACL-aware retrieval** (model never sees unauthorized docs); **Sensitive Data Protection (DLP)** to classify/tag the corpus; **Model Armor** output backstop; **Cloud Logging** of query + retrieved docs + identity.

**DURABLE FIX:** **Dataplex** classification driving access policy; propagate the end-user identity end-to-end into the retrieval filter; **VPC-SC** around the corpus; a data-onboarding gate requiring classification + ACLs.

**Persona wins:** CTO → ACLs prevent the malpractice exposure; disclaimers don't. SRE → DLP-driven ACLs + Model Armor = defense in depth. Ops → per-query logs reconstruct who saw what.

---

## Playbook 7 — Insider data exfiltration

**IF:** a privileged/departing user may be exfiltrating PII; broad **BigQuery** access, exports to personal/external buckets, no monitoring, found out anecdotally.

**DIAGNOSE:** trusted access with no guardrails or visibility — open egress + standing privilege + no behavioral baseline.

**STOP THE BLEEDING:** **SCC** + **Event Threat Detection** + **BigQuery Data Access logs** to spot abnormal pulls; **DLP** to locate PII; **VPC-SC** perimeter so data can't leave to external buckets; **IAM Recommender** to right-size standing access; time-bound (JIT) grants.

**DURABLE FIX:** **Google SecOps** UEBA (deviation-from-self alerts); **BigQuery column-level security + dynamic masking**; **DLP de-identification** for analytics that don't need raw PII; access reviews + Access Transparency/Approval.

**Persona wins:** CTO → masking keeps analysts productive; VPC-SC closes the exit. SRE → dry-run perimeters; UEBA baselines cut noise. Ops → near-real-time alert on abnormal export volume.

---

## Playbook 8 — Ransomware exposure (backups encryptable)

**IF:** backups share project/credentials with prod (encryptable), restores untested, flat network, unknown RTO; a peer just got hit.

**DIAGNOSE:** backups share fate with prod — an attacker who reaches prod encrypts the backups too. Prevent spread *and* guarantee recovery.

**STOP THE BLEEDING:** **immutable, isolated backups** — **Cloud Storage Bucket Lock + Object Retention Lock** in a *separate project* (and/or **Backup and DR Service**); **test the restore** for a real RTO; **hierarchical firewall policies** to limit lateral spread; **SCC Event Threat Detection** on precursor behavior (mass changes).

**DURABLE FIX:** 3-2-1 + immutable, continuously restore-tested; zero-trust segmentation + phishing-resistant MFA; tiered RTO/RPO; isolated clean-room recovery; ransomware game days + exec tabletops.

**Persona wins:** CTO → immutability turns "we have backups" into "we can recover." SRE → restore-test co-first; clean-room avoids re-infection. Ops → paged on precursor behavior, not the ransom note.

---

## Playbook 9 — Observability bill exploded / can't find the signal

**IF:** logging+monitoring is a top-3 line item rivaling compute; everything at DEBUG, duplicate logs, long retention, high-cardinality metrics; incidents are *harder* to debug despite all the data.

**DIAGNOSE:** more telemetry ≠ more observability — they're paying premium prices to *bury* the signal in noise. Cut cost and improve signal together.

**STOP THE BLEEDING:** **Cloud Logging exclusion filters** (drop health-checks + prod DEBUG pre-billing); tier retention via **log buckets**; route bulk logs to **BigQuery** / Log Analytics (far cheaper than premium retention); trim cost-driving high-cardinality metrics.

**DURABLE FIX:** signal-driven telemetry mapped to SLOs/alerts; per-team observability cost attribution (labels) + budget anomaly alerts; **Managed Service for Prometheus** with sane retention; structured logging.

**Persona wins:** CTO → mostly config, not new tools; immediate bill cut. SRE → tier (route to BigQuery), don't delete; keep what you might need. Ops → less noise = faster incident search; creep is attributable.

---

## Playbook 10 — Creeping latency, "infra green but users unhappy"

**IF:** p99 latency steadily degrades, intermittent slowness, dashboards green, no SLOs, siloed per-service metrics, many-service request paths, engineers guessing.

**DIAGNOSE:** they monitor *resources*, not *user experience*, in a distributed system where latency hides in *interactions* between services.

**STOP THE BLEEDING:** **Cloud Trace** via **OpenTelemetry** (exposes *where* in the chain latency accrues — usually finds it); define **Cloud Monitoring SLOs** on the key journeys; golden signals correlated; fix the revealed offender (slow dependency, N+1, contention).

**DURABLE FIX:** traces+metrics+logs correlated, SLO dashboards, **burn-rate alerting**; latency budgets per service + perf tests in CI; PRRs requiring tracing + SLOs on new services.

**Persona wins:** CTO → tracing is the missing dimension, not duplicate tooling. SRE → user-centric SLOs are the right signal. Ops → dashboards fire before tickets; point at the responsible service immediately.

---

## Playbook 11 — Software supply-chain compromise

**IF:** a compromised dependency reached prod images via CI/CD, active for an unknown period; no scanning, no SBOM, unsigned images, broad CI service-account access.

**DIAGNOSE:** the path from code to prod had no integrity control at any step. (Contain, then secure end-to-end.)

**STOP THE BLEEDING:** scope affected images via logs, rotate creds, rebuild from known-good; lock down CI blast radius with **Workload Identity Federation** + least-privilege IAM; turn on **Artifact Analysis** scanning, block deploys on critical CVEs; pin to curated base images in a private **Artifact Registry**.

**DURABLE FIX:** **Software Delivery Shield** — **SLSA** provenance + SBOM via **Cloud Build**, image signing, **Binary Authorization** (only signed/attested deploy to GKE); proxy/scan public deps via remote repos; **Container Threat Detection** in SCC; supply-chain IR runbooks + tabletops.

**Persona wins:** CTO → one-time pipeline setup; SBOM proves what's running. SRE → tiered scan-gating + dry-run Binary Authorization. Ops → scan at build + queryable SBOM = no more "unknown duration."

---

## Playbook 12 — Streaming pipeline silently stale / data loss

**IF:** a real-time pipeline falls behind under spikes (growing lag), occasional data loss, a poison message stalls a stream, dashboards silently go stale; the business decides on this data.

**DIAGNOSE:** no backpressure handling, no poison-message isolation, no freshness monitoring — silent staleness is worse than visible downtime.

**STOP THE BLEEDING:** ensure **Pub/Sub** buffers between producers and processing (absorb spikes); **dead-letter topics** so a bad message doesn't stall the stream; **Dataflow** Streaming Engine + autoscaling to keep up; **Cloud Monitoring** on backlog / oldest-unacked-age / freshness — alert *before* dashboards go stale.

**DURABLE FIX:** **freshness/completeness SLOs** with burn-rate alerting (treat "data is current" as the SLI); idempotent **BigQuery** writes + replay from Pub/Sub; autoscaling tuned to backlog; pipeline runbooks + PRRs.

**Persona wins:** CTO → dead-letter is cheap; Dataflow scales for spikes only. SRE → SLO on freshness, not just "running." Ops → staleness becomes a page, not a surprise.

---

# PART 3 — THE META-RULES (reconstruct everything else)

You can't store 700 facts, but these ~10 patterns regenerate most of the best practices and tactical fixes on demand:

1. **Diagnose before prescribe.** Open every scenario by naming the *root cause* beneath the symptoms. Biggest trust signal.
2. **Two problems are usually one.** Cost-up + reliability-down, or cost + safety + outage — re-anchor on the shared root cause (usually *control* or an *untested assumption*). Unifies the personas.
3. **Reversible-first under crisis.** Rollback / config change / failover before any forward fix. Gives the CTO speed without sacrificing the SRE's safety.
4. **Decouple the critical path.** Anything slow or non-deterministic (AI, a heavy job) comes *out* of the synchronous path → **Pub/Sub** + async + **dead-letter**.
5. **Isolate the blast radius.** Separate **GKE node pool**, separate project, **VPC-SC** perimeter — so one failure can't take the core down.
6. **Dry-run before enforce.** Every security/network control (**VPC-SC**, **Org Policy**, **Binary Authorization**, firewall) supports it. Universal answer to "that'll break things."
7. **Tier to criticality.** Multi-region, the biggest model, the heaviest governance — only for tier-1. Satisfies CTO (cost) and SRE (rigor) at once.
8. **Right-size + commit the baseline.** Every cost answer: **Recommender** rightsizing + **CUDs** for steady load + Spot for spillover + lifecycle/autoscaling.
9. **Measure user experience, not resources.** **SLOs** on journeys + **burn-rate alerting**, not CPU thresholds. Fixes "green but unhappy" and alert fatigue.
10. **Fail-closed on anything that protects.** Compliance gates, auth, safety checks must hold (queue) on failure — never fail-open.
11. **Visibility ships first.** Observability/detection is almost always phase-one — it's the Ops Lead's win and the foundation everything else stands on.
12. **It's not a backup until you've restored it.** Immutable + isolated + *tested*. Same logic: untested DR is a document, not a capability.

**The four "missing layers" to name in any AI/security crisis:** no **measurement** (evals/SLOs), no **control** (caps/guardrails/perimeters), no **governance** (audit/ownership), or **fate-sharing** (backups/credentials/blast radius). Naming which one is missing *is* the diagnosis.

**Persona one-liner pattern (every scenario):**
- **CTO** — cheapest path that's still safe; tie cost to the business outcome.
- **Head of SRE** — systematic + reversible + tested; no band-aids, no untested forward-fixes.
- **Ops Lead** — give them visibility/detection *first*; runbooks for the 3am action.
