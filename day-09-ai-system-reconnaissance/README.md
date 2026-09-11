# Day 09 — AI System Reconnaissance

<p align="center">
  <img src="../Pictures/Day9.png" alt="AI Security Learning Journal — Day 09: AI System Reconnaissance" width="100%">
</p>

> You cannot effectively threat-model an AI system you cannot see, and you cannot secure an AI system by protecting the model alone.

## At a Glance

**Reading time:** about 27 minutes

This entry connects AI reconnaissance to threat modelling and blue-team detection by mapping deployed components, metadata, relationships, and potential attack paths.

**Key takeaways:**

- Discovery, fingerprinting, enumeration, exposure, and exploitation are different findings.
- Relationships between individually secure components can still create unsafe attack paths.
- Reconnaissance should continuously update both the threat model and defensive monitoring.

**Suggested path:** Read the discovery–fingerprinting–enumeration sequence, then jump to the structured methodology and final module model.

**Quick navigation:** [Infrastructure stack](#understanding-the-ai-infrastructure-stack) · [Reconnaissance](#ai-reconnaissance-is-more-than-port-scanning) · [Methodology](#a-structured-ai-reconnaissance-methodology) · [Final model](#module-2-my-final-security-model)

## From Threat Modelling to Reality

Day 08 changed how I approach AI Security assessments.

Instead of starting with vulnerabilities, I started with:

**Architecture → Assets → Data Flows → Trust Boundaries → Threats**

Day 09 introduced another question that needs to come even earlier:

> **Does the architecture I am threat-modelling actually represent what is deployed?**

An organisation may describe its AI application as:

```text
User
  ↓
Application
  ↓
LLM
```

But the infrastructure actually supporting that application may include:

```text
User
  ↓
Application / API
  ↓
LLM
  ↓
Retrieval
  ↓
Vector Database


Data / ML Engineering
        │
        ├── Notebooks
        │
        ├── Experiment Tracking
        │
        ├── Model Registry
        │
        └── Artifact Storage
                   ↓
             Model Serving
```

This difference matters.

Threat modelling tells me what could go wrong with the system I **believe** exists.

Reconnaissance helps me understand what **actually exists**.

That became the central lesson of Day 09.

---

## You Cannot Protect What You Cannot See

Asset visibility is already fundamental in traditional cybersecurity.

AI makes that problem larger.

Deploying AI capabilities can introduce infrastructure such as:

* model-serving endpoints;
* experiment-tracking platforms;
* model registries;
* vector databases;
* notebook environments;
* artifact repositories;
* object storage;
* orchestration platforms;
* monitoring interfaces;
* internal APIs;
* external model dependencies.

These components may be introduced by different teams.

Some may begin as experimental infrastructure.

Some may later become production dependencies.

Some may be deployed with developer-friendly defaults that were never revisited.

The security question therefore becomes larger than:

> **Is our chatbot secure?**

I also need to ask:

> **Do we know every component participating in the AI lifecycle, what those components expose, how they communicate, and which trust relationships connect them?**

AI Security therefore becomes an **asset visibility problem** as much as an application-security problem.

---

## AI Adds Infrastructure — It Does Not Replace Traditional Infrastructure

Traditional environments already contain:

```text
Operating Systems
Networks
Web Applications
APIs
Databases
Cloud Infrastructure
Identity Systems
Monitoring
Storage
```

AI adds another layer:

```text
Traditional Infrastructure
          +
AI Infrastructure
          │
          ├── Model Serving
          ├── Experiment Tracking
          ├── Model Registries
          ├── Vector Databases
          ├── Notebooks
          ├── Artifact Storage
          ├── Training Pipelines
          ├── Metrics
          ├── AI APIs
          └── External Models
```

The important word is:

> **Adds.**

A modern AI application inherits traditional cybersecurity risks while introducing additional risks across the AI/ML lifecycle.

---

## The Model Is Only One Asset

Before this module, it would have been easy to focus on:

```text
User
 ↓
LLM
 ↓
Response
```

But reconnaissance makes it obvious that the LLM may be only one visible part of a much larger system.

For example:

```text
                        ┌──────────────┐
                        │     User     │
                        └──────┬───────┘
                               ↓
                        ┌──────────────┐
                        │ Application  │
                        └──────┬───────┘
                               ↓
                        ┌──────────────┐
                        │     LLM      │
                        └──────┬───────┘
                               ↓
                        ┌──────────────┐
                        │     RAG      │
                        └──────┬───────┘
                               ↓
                        ┌──────────────┐
                        │ Vector Store │
                        └──────────────┘


┌──────────────┐
│   Notebook   │
└──────┬───────┘
       ↓
┌──────────────┐
│ Experiment / │
│   Tracking   │
└──────┬───────┘
       ↓
┌──────────────┐
│    Model     │
│   Registry   │
└──────┬───────┘
       ↓
┌──────────────┐
│   Artifact   │
│   Storage    │
└──────┬───────┘
       ↓
┌──────────────┐
│Model Serving │
└──────────────┘
```

Every component creates another possible security boundary.

Every relationship creates another trust relationship.

Every new service may produce telemetry, metadata, credentials, APIs, permissions, or paths toward another system.

---

## Understanding the AI Infrastructure Stack

One of the most useful parts of this Day was learning what an AI environment can actually look like on a network.

The goal was not memorising products.

The goal was understanding the **roles** those products perform.

---

## Model Serving

Once a model is trained, something needs to load it and respond to inference requests.

That creates a model-serving layer.

A serving platform may expose:

```text
Inference APIs
Model Metadata
Health Endpoints
Management Interfaces
Metrics
gRPC Services
Configuration
```

This means finding a model server can reveal much more than:

> **There is an AI model here.**

It can help reveal:

> **How the organisation deploys and operates its AI models.**

From a defensive perspective, this means model serving is both:

**an application interface**

and

**an infrastructure asset**.

---

## Experiment Tracking

Machine learning involves experimentation.

Teams repeatedly adjust:

* parameters;
* datasets;
* model architecture;
* training configuration;
* evaluation criteria.

Experiment-tracking platforms preserve that history.

They may contain information about:

```text
Experiments
   ↓
Runs
   ↓
Parameters
   ↓
Metrics
   ↓
Tags
   ↓
Artifacts
```

This information can be operationally valuable.

But from a reconnaissance perspective, it can also reveal how the organisation builds its AI systems.

---

## Model Registries

Model registries are especially interesting because they provide visibility into the model lifecycle.

They may contain:

```text
Model Names
Versions
Lifecycle Stages
Creation Timestamps
Contributors
Run IDs
Artifact Locations
Model Metadata
```

A registry can therefore become:

> **A map of the organisation's ML product portfolio.**

This is why a registry can be extremely valuable during an authorised security assessment even if no software vulnerability is discovered.

---

## Vector Databases

RAG architectures depend heavily on retrieval.

A simplified flow is:

```text
Question
   ↓
Embedding
   ↓
Vector Search
   ↓
Relevant Context
   ↓
LLM
   ↓
Response
```

The vector database may therefore reveal information about:

* indexed collections;
* document categories;
* vector dimensions;
* embedding configuration;
* metadata;
* data organisation.

The most important conceptual lesson for me is:

> **The LLM is not necessarily the system's knowledge boundary.**

The retrieval layer may provide information the underlying model never contained.

---

## Notebooks

Notebook environments are incredibly useful for:

* experimentation;
* debugging;
* data analysis;
* model development;
* pipeline testing.

That flexibility also means they often communicate with multiple AI components.

Conceptually:

```text
Notebook
  │
  ├── Dataset
  ├── Model Registry
  ├── Vector Database
  ├── Experiment Tracker
  ├── Object Storage
  └── External Model Source
```

A notebook can therefore become a bridge between otherwise separate parts of an AI environment.

One of the most important architectural lessons from the training was:

> **Developer convenience can unintentionally become infrastructure connectivity.**

This is particularly important when development environments contain:

* internal paths;
* service relationships;
* credentials;
* tokens;
* configuration.

---

## Model and Artifact Storage

Models ultimately need to be stored somewhere.

The model itself may exist as serialized artifacts inside:

```text
Object Storage
Cloud Buckets
Internal Repositories
Model Registries
Artifact Stores
```

This gives us a familiar CIA problem.

### Confidentiality

Can someone obtain the model?

### Integrity

Can someone replace or modify the model?

### Availability

Can someone prevent the organisation from retrieving or deploying the model?

Traditional security principles still apply.

The asset has changed.

---

## Metrics and Observability

Metrics exist to help defenders and operators understand a system.

But exposed telemetry can also become reconnaissance intelligence.

Depending on the environment, metrics may reveal things such as:

* model identifiers;
* model versions;
* inference activity;
* resource utilisation;
* GPU usage;
* batch size;
* deployment behaviour;
* latency;
* topology.

This gave me another useful principle:

> **Operational visibility for defenders can become reconnaissance visibility for attackers when exposed across the wrong trust boundary.**

---

## AI Reconnaissance Is More Than Port Scanning

One of the concepts I initially needed to refine was the difference between:

```text
Discovery
Fingerprinting
Enumeration
```

These stages are related.

But they answer different questions.

---

## Discovery — What Exists?

Discovery asks:

> **What is running?**

At this stage, I am identifying systems and exposed services.

Conceptually:

```text
Network
   ↓
Host
   ↓
Open Service
```

This tells me something exists.

But it may not tell me exactly what it is.

That is why discovery alone is not enough.

---

## Fingerprinting — What Exactly Is It?

Fingerprinting asks:

> **What technology or framework is behind this service?**

Imagine the initial discovery only identifies:

```text
HTTP
```

That tells me very little.

The next stage asks:

```text
What kind of HTTP service is this?
```

The training showed several categories of evidence that can help answer that question.

---

## HTTP Headers

Response headers can reveal:

* runtime;
* framework;
* server implementation;
* product-specific markers.

A generic HTTP service can therefore begin to reveal its identity through seemingly ordinary metadata.

---

## JSON Response Structure

Different services often return characteristic response structures.

For reconnaissance, I care about things like:

```text
Field names
Object structure
Model identifiers
Version metadata
Platform information
```

The structure itself can become a fingerprint.

---

## Endpoint Naming

Traditional applications often use resource names:

```text
/users
/accounts
/products
```

AI services frequently expose concepts related to:

```text
/models
/inference
/generate
/embeddings
/health
/metrics
/collections
/experiments
```

Endpoint naming can therefore reveal that an application is doing something AI-specific even before the product is fully identified.

---

## Error Behaviour

This was another interesting lesson.

An error is not always useless.

Verbose error messages can reveal:

* expected data structures;
* framework-specific terminology;
* internal library names;
* tensor shapes;
* expected types;
* paths;
* configuration assumptions.

That means:

> **Failure behaviour can become fingerprinting data.**

This is why production systems should avoid unnecessarily verbose diagnostic responses across untrusted boundaries.

---

## Protocol Behaviour

AI infrastructure does not always use ordinary REST APIs.

Some AI services use protocols such as gRPC.

That means an assessment needs to consider that:

```text
HTTP Scanner
      ≠
Complete AI Service Discovery
```

A service may be perfectly visible to the application while remaining poorly characterised by traditional HTTP-only reconnaissance.

---

## Fingerprinting Is Identification, Not Exploitation

This distinction became important during the knowledge test.

If I identify:

> **This service appears to be a specific AI framework**

I have not necessarily exploited anything.

I have identified a component.

So:

```text
Discovery
≠
Exploitation

Fingerprinting
≠
Exploitation
```

The precision of the terminology matters.

---

## Enumeration — What Does the Service Reveal?

Enumeration goes one stage deeper.

It asks:

> **What information is this component willing to give me?**

For example:

```text
Discovery:
"There is a model-management service."

Fingerprinting:
"This appears to be a specific model-management platform."

Enumeration:
"These are the models, versions, experiments, artifacts and relationships it exposes."
```

That is a major change.

---

## Metadata Is Intelligence

One concept I want to keep from this Day is:

> **Metadata can become security intelligence.**

A single API response may reveal information about:

* model names;
* experiment names;
* versions;
* contributors;
* artifact locations;
* deployment environments;
* training history;
* source relationships.

None of those pieces alone necessarily represents compromise.

But together they can reveal the shape of the AI environment.

---

## Enumeration Can Reveal Relationships

This is where reconnaissance becomes architecture reconstruction.

Suppose enumeration reveals:

```text
Experiment
   ↓
Model
   ↓
Version
   ↓
Artifact Location
```

Now one service is pointing toward another system.

That transforms reconnaissance from:

```text
Find individual service
```

into:

```text
Understand relationships between services
```

And relationships are where attack paths begin to emerge.

---

## From Findings to an Attack-Surface Map

A list of services is useful.

A connected map is far more valuable.

Imagine discovering:

```text
Notebook

Experiment Tracker

Model Registry

Artifact Storage
```

Individually, these are four findings.

But suppose reconnaissance shows:

```text
Notebook
   ↓
Experiment Tracker
   ↓
Model Registry
   ↓
Artifact Storage
```

Now the security question changes.

I am no longer asking:

> **Is each component vulnerable?**

I also need to ask:

> **What trust exists between these components?**

---

## Trust Relationships Matter More Than Individual Products

This became one of the strongest lessons from Day 09.

Imagine all four components are:

* updated;
* patched;
* free of known critical CVEs.

The system can still contain a dangerous attack path.

Why?

Because relationships introduce risk through:

```text
Credentials
Permissions
Authentication Boundaries
Network Exposure
Configuration
Metadata
Shared Storage
Implicit Trust
Overprivileged Identities
```

Each relationship might be legitimate.

Together, they can form a path across the environment.

---

## A Secure Component Does Not Guarantee a Secure System

This is probably one of my favourite conclusions from the Day:

> **A secure component does not necessarily create a secure system. Trust relationships between individually secure components can form a critical attack path.**

This connects directly with Day 06:

> **A secure model connected to an insecure architecture still results in an insecure system.**

But Day 09 makes the idea even more operational:

> **The individual products may be secure while the relationships between them create the risk.**

---

## Attack-Path Chaining

My mental model became:

```text
Initial Discovery
       ↓
Service Identification
       ↓
Metadata
       ↓
Relationship Discovery
       ↓
Credentials / Permissions
       ↓
Authenticated Pivot
       ↓
Another AI Component
       ↓
Potential Access to Higher-Value Asset
```

This does not mean every reconnaissance finding produces a compromise.

It means the findings help identify:

> **possible progression paths that need validation.**

That distinction is important.

---

## Potential Attack Path ≠ Confirmed Attack Path

One of the terminology lessons I want to retain is:

```text
Potential Path
      ≠
Confirmed Capability
```

Finding an artifact location does not prove I can write to it.

Finding a credential does not automatically prove it has useful permissions.

Finding a management interface does not automatically prove it can be abused.

The next question always needs to be:

> **What do the permissions actually allow?**

This matters enormously in both threat modelling and DFIR.

---

## Exposure Is Not Vulnerability

I also became more careful with these distinctions:

```text
Exposure
    ≠
Vulnerability

Vulnerability
    ≠
Exploitation

Exploitation
    ≠
Compromise

Reconnaissance
    ≠
Compromise
```

A service can be exposed intentionally and secured correctly.

A vulnerability can exist without being exploited.

Reconnaissance can occur without successful exploitation.

And enumeration does not automatically mean compromise.

These terms describe different states.

---

## Why Terminology Matters

In a security report, saying:

> **The attacker compromised the ML platform**

means something very different from:

> **The attacker enumerated the ML platform.**

The evidence required to support those conclusions is different.

As I move further toward security and DFIR work, this precision becomes increasingly important.

---

## Following the AI Lifecycle

One useful way to map reconnaissance findings is to follow the AI lifecycle itself.

```text
DATA
 ↓
INGESTION
 ↓
TRAINING / EXPERIMENTATION
 ↓
MODEL
 ↓
REGISTRY
 ↓
ARTIFACT STORAGE
 ↓
DEPLOYMENT
 ↓
INFERENCE
 ↓
RETRIEVAL
 ↓
OUTPUT
```

Every transition creates another question.

---

## Data and Ingestion

I want to know:

* Where do the data come from?
* Who can modify them?
* Is provenance known?
* What validation happens before ingestion?
* Can external sources contribute content?

This connects directly with poisoning threats.

---

## Training and Experimentation

Questions include:

* Who can initiate training?
* Which datasets are used?
* Where are results stored?
* Which identities control the pipeline?
* Which dependencies are involved?

Training infrastructure is therefore part of the attack surface.

---

## Model Registry

Questions include:

* Which model is authoritative?
* Who can register a version?
* Who can promote it?
* Where is the artifact?
* How is integrity validated?
* Is provenance documented?

The registry becomes a critical trust boundary between:

```text
Training
   ↓
Approved Model
   ↓
Production
```

---

## Deployment and Inference

I want to understand:

* Which interface exposes the model?
* Who can invoke it?
* Which metadata are exposed?
* Is management functionality reachable?
* Which other services communicate with the inference layer?

Inference is both a product function and an infrastructure function.

---

## Retrieval

For RAG systems:

```text
Who controls the data?

Who can insert content?

Who can retrieve which content?

Are permissions enforced before retrieval?

Can attacker-controlled content become model context?
```

The retrieval layer creates a bridge between storage and model behaviour.

---

## Output

Finally:

* What can leave the system?
* Can sensitive context reach the response?
* Is output validated?
* Can downstream systems execute generated content?
* Is the requesting identity authorised for the underlying data?

Input filtering alone is not enough.

---

## AI Reconnaissance From the Blue Team Perspective

One of the most useful parts of this Day was reversing the perspective.

Everything an authorised assessor does during reconnaissance generates telemetry.

That means the same knowledge can be used defensively.

Conceptually:

```text
Red Team / Assessor
       ↓
Discovery
       ↓
Fingerprinting
       ↓
Enumeration
       ↓
Attack-Surface Mapping
```

becomes:

```text
Blue Team
       ↓
Detect Discovery
       ↓
Detect Fingerprinting
       ↓
Detect Enumeration
       ↓
Correlate Behaviour
```

This was a particularly useful connection for me because it joins offensive understanding with Blue Team detection.

---

## Detecting AI-Aware Scanning

Not every scan has the same meaning.

Imagine a single source systematically probing a collection of services commonly associated with AI/ML infrastructure.

That may indicate:

> **AI-aware reconnaissance**

rather than generic scanning.

The sequence matters.

```text
Source
  ↓
AI Service
  ↓
Another AI Service
  ↓
Another AI Service
```

A pattern can reveal intent more clearly than one event.

---

## Detecting Fingerprinting

Suppose a source first performs broad discovery.

Immediately afterward, it begins making framework-specific requests.

Conceptually:

```text
Generic Scan
    ↓
Service Found
    ↓
Specific Endpoint Probe
    ↓
Malformed / Identification Requests
```

Now the behaviour has changed from:

> **What exists?**

to:

> **What exactly did I find?**

That transition itself can be valuable detection context.

---

## Detecting Enumeration

Enumeration may appear as bursts of requests for:

```text
Models
Versions
Experiments
Configurations
Metrics
Collections
Artifacts
Notebooks
```

A legitimate application might access some of these resources.

So one request alone may not be enough.

The broader sequence matters.

---

## Context Separates Legitimate Automation From Reconnaissance

AI environments naturally contain lots of automation.

For example:

```text
Notebook → Experiment Tracker

Monitoring → Metrics Endpoint

Application → Inference Server
```

These are normal.

So a defender needs context.

Questions include:

* Is this the expected source?
* Is the identity expected?
* Does the request have the expected session?
* Is the frequency normal?
* Did scanning occur first?
* Is this endpoint normally accessed by this system?
* Is the sequence consistent with normal ML operations?

This is behavioural detection.

---

## Not Every Log Is Malicious

This was reinforced strongly in the practical Blue Team activity.

A request that looks technical or automated is not automatically hostile.

For example:

```text
Known ML Pipeline
      ↓
Expected ML API
      ↓
Expected User-Agent
      ↓
Expected Timing
```

may simply be legitimate machine-learning activity.

That reinforces an important SOC principle:

> **Detection requires context, not only pattern matching.**

---

## Reconnaissance Is Not Compromise — But It Matters

Imagine observing:

```text
Scanning
   ↓
Fingerprinting
   ↓
Enumeration
   ↓
Relationship Discovery
   ↓
Attempted Pivot
```

There may still be no:

* malware;
* RCE;
* data exfiltration;
* destructive action.

But the behaviour still matters.

It demonstrates:

* intent;
* knowledge acquisition;
* target selection;
* progression.

So:

> **Reconnaissance is not compromise, but targeted reconnaissance can represent preparation for compromise.**

This is why I would investigate a coherent sequence before waiting for exploitation.

---

## Turning Red-Team Knowledge Into Blue-Team Detection

Another important lesson from Day 09 is that understanding attacker methodology helps build better detection.

If I know that reconnaissance usually progresses through:

```text
Discovery
   ↓
Fingerprinting
   ↓
Enumeration
```

I can look for corresponding sequences in telemetry.

For example:

```text
Broad AI Port Scan
        ↓
Framework-Specific Probe
        ↓
Metadata Requests
        ↓
Access to Another Related Service
```

Each event individually may appear low-severity.

Together, they tell a story.

---

## From Reconnaissance Back to Threat Modelling

Day 08 and Day 09 form a loop.

Day 08:

> **Model the threats against the architecture.**

Day 09:

> **Verify the architecture actually exists as documented.**

Then:

```text
Reconnaissance
      ↓
Asset Discovery
      ↓
Architecture Update
      ↓
Threat Model Update
      ↓
Risk Assessment
      ↓
Controls
      ↓
Detection
      ↓
Reassessment
```

This is much stronger than running reconnaissance and threat modelling as unrelated activities.

---

## The Documentation Gap Is a Security Gap

Imagine:

### Team A

Has an excellent threat model.

It contains:

* assets;
* trust boundaries;
* STRIDE;
* ATLAS;
* OWASP;
* mitigations.

### Team B

Performs authorised reconnaissance and discovers multiple AI services missing from that documentation.

Which view represents the real risk?

The answer is:

> **The architecture needs to be reconciled with reality.**

A perfect threat model of an incomplete environment is still incomplete.

---

## Threat Model What Actually Exists

The correct process becomes:

```text
Real-World Discovery
        ↓
Asset Inventory
        ↓
Architecture Update
        ↓
Data Flow Update
        ↓
Trust Boundary Update
        ↓
Threat Model Update
        ↓
Risk Reassessment
```

I do not discard the existing threat model.

I make it more accurate.

---

## Supply Chain Is Also Part of Reconnaissance

AI systems often depend heavily on external resources.

Examples include:

* public model repositories;
* third-party base models;
* external datasets;
* packages;
* container images;
* cloud services;
* model hubs.

That means reconnaissance should also ask:

> **What does this AI environment trust that the organisation does not directly control?**

This connects Day 09 directly with supply-chain security.

---

## External Models Are Dependencies

If a system downloads a model from an external source, that source becomes part of the trust chain.

Conceptually:

```text
External Repository
      ↓
Base Model
      ↓
Internal Pipeline
      ↓
Fine-Tuning
      ↓
Internal Model
      ↓
Production
```

A compromise upstream may eventually affect production.

So understanding provenance matters even during reconnaissance.

---

## Credentials Connect Supply Chains

Access tokens and credentials can connect internal systems with external AI platforms.

From a security perspective, those credentials need:

* minimal scope;
* rotation;
* appropriate storage;
* monitoring;
* lifecycle management.

Again, least privilege remains a traditional cybersecurity principle applied to a new kind of dependency.

---

## Three Threat Paths Reinforced by the Assessment

The final Threat Modelling Assessment helped reinforce something very important:

> **Different threats travel through different architectural paths.**

Therefore, the most effective control points differ by threat.

I am intentionally not documenting the laboratory solution.

The useful lesson is the reasoning behind the scenarios.

---

## Prompt Injection

Conceptually:

```text
Untrusted Input
      ↓
Prompt / Context
      ↓
LLM
      ↓
Response / Action
```

The question is not merely:

> **Can someone write a malicious prompt?**

I also need to ask:

* What does the model receive?
* Which instructions are trusted?
* What can the model do?
* What tools are available?
* Which privileges sit behind those tools?
* What happens if the instruction succeeds?

The impact of Prompt Injection depends heavily on the architecture around the model.

---

## Sensitive Data Leakage

Sensitive information follows another path:

```text
Sensitive Data
      ↓
Storage
      ↓
Retrieval
      ↓
Model Context
      ↓
Response
      ↓
User
```

If the user was never authorised to see the information, relying on the LLM to avoid mentioning it is already too late.

A stronger architecture applies controls earlier.

For example:

```text
Authorisation
Data Classification
Retrieval Filtering
Least Privilege
Context Minimisation
Output Controls
```

The principle becomes:

> **Do not give the model information that the requesting identity was never authorised to access.**

---

## Data Poisoning

Poisoning travels through another path:

```text
Malicious Data
      ↓
Source
      ↓
Ingestion
      ↓
Storage / Index
      ↓
Retrieval
      ↓
LLM
      ↓
Influenced Response
```

This reinforces another distinction:

> **Poisoned retrieval data is not necessarily a poisoned model.**

The model weights may remain perfectly intact.

The context delivered to the model is what has been corrupted.

---

## Protect Upstream When the Threat Starts Upstream

One of my strongest conclusions from the assessment was:

> **If the threat begins before the LLM, the primary defence should not exist only at the LLM.**

For Data Poisoning, for example, strong controls may belong at:

```text
Data Source
     ↓
Ingestion
     ↓
Validation
     ↓
Provenance
     ↓
Storage
```

Waiting until corrupted information reaches the model means the defence has already moved much further downstream.

---

## Controls Must Follow the Threat Path

Defence in depth remains important.

But threat modelling needs greater precision than:

> **Protect everything.**

I want to ask:

```text
Where does the threat originate?
        ↓
Where does it enter?
        ↓
Which components propagate it?
        ↓
Which trust boundaries does it cross?
        ↓
Which asset is affected?
        ↓
Where can I break the path most efficiently?
```

That leads to another major takeaway:

> **Effective AI threat modelling is not about protecting every component equally. It is about understanding how a specific threat propagates through the architecture and placing controls where they can break that path.**

---

## A Structured AI Reconnaissance Methodology

The training presented reconnaissance as a repeatable process.

The details of tools can change.

The methodology matters more.

I currently think about it in five broad phases.

---

## Phase 1 — Passive Reconnaissance

Before touching the target environment, determine what is already publicly visible.

Potential sources may reveal:

* technologies;
* public services;
* job requirements;
* public documentation;
* repositories;
* container images;
* published research;
* model dependencies.

The goal is:

> **Build hypotheses before generating active traffic.**

---

## Phase 2 — Active Discovery

Within authorised scope:

> **What AI-related services actually exist?**

The goal is inventory and exposure mapping.

Not exploitation.

---

## Phase 3 — Fingerprinting

For each discovered service:

> **What exactly is it?**

Use observable behaviour such as:

* headers;
* response formats;
* protocols;
* endpoint conventions;
* error behaviour.

---

## Phase 4 — Metadata Enumeration

Once identified:

> **What does this service reveal about the AI environment?**

Examples include:

* model inventory;
* deployment metadata;
* experiment history;
* configurations;
* relationships;
* artifact references;
* collection information.

The goal is understanding the architecture.

---

## Phase 5 — Supply Chain Review

Finally:

> **What external systems, models, packages, repositories or storage locations does the environment trust?**

This expands reconnaissance beyond the internal network.

---

## My AI Security Assessment Lifecycle

After Day 08 and Day 09, my own process has become broader than the five reconnaissance phases.

If I were asked today to evaluate a new AI environment, I would think like this:

```text
1. Discover
      ↓
2. Inventory
      ↓
3. Understand Architecture
      ↓
4. Map Data Flows
      ↓
5. Identify Assets
      ↓
6. Identify Trust Boundaries
      ↓
7. Reconnoitre
      ↓
8. Fingerprint
      ↓
9. Enumerate
      ↓
10. Build Attack Paths
      ↓
11. Threat Model
      ↓
12. Prioritise
      ↓
13. Mitigate
      ↓
14. Detect
      ↓
15. Reassess
      ↺
```

This is the security lifecycle that now makes the most sense to me.

---

## Framework Mapping

Frameworks help convert technical observations into a common language.

I do not see them as the assessment itself.

I see them as ways to structure and communicate what I observe.

---

## MITRE ATLAS

ATLAS provides AI-specific adversarial behaviours.

For Day 09, the important conceptual mappings include:

* active reconnaissance against AI systems;
* discovering ML assets;
* discovering model/system information;
* identifying supply-chain dependencies.

The important thing for me is not memorising IDs.

It is understanding:

> **Reconnaissance against AI infrastructure has observable adversary behaviours that can be described consistently.**

Because framework identifiers can evolve over time, I would verify current ATLAS IDs before using them in a formal assessment.

---

## MITRE ATT&CK

Many AI reconnaissance techniques are still traditional cybersecurity techniques underneath.

Examples include:

```text
Network Scanning
Service Discovery
Infrastructure Discovery
Credential Discovery
Information Gathering
```

This reinforces something important:

> **AI Security is still cybersecurity.**

AI introduces new assets.

Attackers still need to interact with networks, identities, systems, APIs, storage and applications.

---

## NIST AI RMF

The NIST AI RMF provides another perspective.

Reconnaissance supports the need to:

* identify system components;
* understand interactions;
* map dependencies;
* understand risk.

This strongly connects with the **MAP** function.

You cannot manage AI risk if you do not know which AI systems exist.

---

## NIST CSF

The same logic maps directly to traditional cybersecurity functions such as:

**Asset Management**

and

**Risk Assessment**.

AI assets still need to be:

* inventoried;
* classified;
* monitored;
* protected.

The asset categories have expanded.

The responsibility has not disappeared.

---

## AI Reconnaissance as a Blue-Team Capability

One of my biggest changes in perspective is that reconnaissance should not only be thought about offensively.

Security teams can use the same knowledge to ask:

> **Would we know if somebody were performing this against us?**

For example:

```text
Can we detect AI-aware scans?

Can we detect sessionless model enumeration?

Can we detect unexpected access to metrics?

Can we detect notebook enumeration?

Can we detect unexpected access between ML components?

Can we detect changes in normal AI service relationships?
```

That turns AI reconnaissance knowledge into detection engineering knowledge.

---

## Monitoring Relationships, Not Only Components

Traditional monitoring may ask:

```text
Is the service running?
```

Security monitoring should also ask:

```text
Who is talking to the service?

Why?

From where?

Using which identity?

In which sequence?

Is this normal?
```

For AI environments, relationship monitoring becomes particularly valuable because the infrastructure is highly interconnected.

---

## Behavioural Detection

A single event:

```text
GET model metadata
```

may be legitimate.

But:

```text
AI Service Scan
      ↓
Framework Fingerprinting
      ↓
Model Enumeration
      ↓
Notebook Access
      ↓
Storage Request
```

is much more interesting.

The chain matters more than the isolated event.

This is directly related to the attack-path reasoning I use in DFIR.

---

## Reconnaissance as an Early Detection Opportunity

If the Blue Team can identify reconnaissance before exploitation, the organisation gains time.

That means security does not always need to begin at:

```text
Exploit Detected
```

It can potentially begin at:

```text
Suspicious Discovery Behaviour
```

This does not mean every scan is an incident.

It means targeted, correlated reconnaissance should be evaluated in context.

---

## What Changed in My Security Thinking

Before Day 09, reconnaissance could easily be reduced in my mind to:

> **Find open ports and identify services.**

Now I see a much richer process:

```text
Discover
   ↓
Identify
   ↓
Enumerate
   ↓
Connect
   ↓
Understand Trust
   ↓
Build Attack Paths
   ↓
Feed Threat Model
   ↓
Build Detection
```

That is a very different security activity.

---

## From Products to Relationships

The biggest change is that I no longer want to stop at:

```text
Jupyter found
ML platform found
Registry found
Storage found
```

I want to understand:

```text
Jupyter
   ↓
What can it reach?

ML Platform
   ↓
What does it reveal?

Registry
   ↓
Where are the artifacts?

Storage
   ↓
Who can read or modify them?
```

The relationships become more important than the product list.

---

## From Vulnerabilities to Attack Paths

Another change is moving beyond:

> **Does this service have a CVE?**

toward:

> **Could the legitimate relationships between these services create an attack path?**

A CVE can certainly create risk.

But so can:

* missing authentication;
* weak segmentation;
* overprivileged credentials;
* exposed metadata;
* shared secrets;
* excessive trust.

---

## From Reconnaissance to Detection

Finally, I now think about reconnaissance from both sides.

As an assessor:

> **What can I discover?**

As a defender:

> **What would someone else's discovery activity look like in my telemetry?**

This dual perspective makes the exercise much more valuable.

---

## Closing Module 2 — Secure AI Systems

Day 09 also closes **Module 2 — Secure AI Systems**.

Looking back, the module created a clear progression.

---

## Day 06 — Securing AI Systems

I learned that:

> **A secure model connected to an insecure architecture still results in an insecure system.**

Security needs to cover:

* architecture;
* trust boundaries;
* tools;
* data;
* permissions;
* outputs;
* monitoring.

---

## Day 07 — LLM Security

The attack surface expanded into:

```text
DATA
MODEL
SYSTEM
USER
```

And I learned:

> **Sometimes I need to protect the AI from the attacker. Other times I need to protect the human from what the attacker can do with AI.**

---

## Day 08 — AI Threat Modelling

I learned to begin before the vulnerability:

```text
Architecture
   ↓
Assets
   ↓
Data Flows
   ↓
Trust Boundaries
   ↓
Threats
   ↓
Techniques
   ↓
Risks
   ↓
Mitigations
```

And I developed a repeatable assessment methodology.

---

## Day 09 — AI System Reconnaissance

Now I have added:

> **Verify what actually exists.**

Threat modelling tells me how the documented architecture can fail.

Reconnaissance tells me whether that architecture is complete.

---

## What the Final Assessment Reinforced

The final assessment did not need to become another Day.

Instead, it reinforced the relationships between everything learned in Module 2.

The most important lesson was not a specific answer.

It was understanding that different threats propagate differently.

For example:

```text
Prompt Injection
      ↓
Instruction / Context Path
```

```text
Sensitive Data Leakage
      ↓
Data Access / Retrieval / Output Path
```

```text
Data Poisoning
      ↓
Data Source / Ingestion / Retrieval Path
```

Each requires different defensive priorities.

That makes threat modelling architectural rather than generic.

---

## Module 2 — My Final Security Model

After completing the module, my mental model is:

```text
Discover what actually exists
           ↓
Understand how it works
           ↓
Identify what has value
           ↓
Map how components trust each other
           ↓
Identify how threats enter
           ↓
Understand how they propagate
           ↓
Break the important attack paths
           ↓
Detect attempts to traverse them
           ↓
Continuously reassess
```

That is significantly different from:

```text
Find vulnerability
      ↓
Patch vulnerability
```

AI systems are too interconnected for that to be sufficient.

---

## Key Takeaways

The main lessons I am taking from Day 09 are:

### AI infrastructure can exist outside the documented application architecture.

Security teams need visibility into what is actually deployed.

### Discovery, fingerprinting, and enumeration are different activities.

They answer:

```text
What exists?
What exactly is it?
What does it reveal?
```

### Metadata can reveal architecture.

A service does not need to be exploited to provide meaningful intelligence.

### A secure component does not guarantee a secure system.

Trust relationships can create attack paths between otherwise well-secured components.

### Reconnaissance is not exploitation.

Precise terminology matters.

### Potential attack paths need validation.

Finding a relationship does not prove the permissions required to abuse it.

### AI reconnaissance also belongs to the Blue Team.

The same methodology creates identifiable patterns in logs.

### Threat modelling and reconnaissance should continuously update each other.

A threat model is only useful if it reflects reality.

### Different threats require controls at different locations.

Security controls should follow the path of the threat.

### AI Security remains cybersecurity.

Traditional concepts such as CIA, least privilege, identity, segmentation, monitoring and supply-chain security still apply.

---

## My Biggest Takeaway

If I had to summarise Day 09 in one idea:

> **Reconnaissance closes the gap between the AI architecture an organisation believes it has and the AI infrastructure that is actually deployed.**

And the principle I want to carry forward is:

> **You cannot effectively threat-model an AI system you cannot see, and you cannot secure an AI system by protecting the model alone.**

The model is only one component.

Real AI Security requires visibility across:

```text
Data
Models
Infrastructure
Retrieval
Storage
Identity
Pipelines
Dependencies
APIs
People
Trust Relationships
```

That is what turns isolated AI components into a system.

And it is the system that needs to be secured.

---

## Next

Module 2 is now complete.

The next stage of the journey moves into **Prompt Security**, where the focus will shift more deeply toward attacks and defences involving the natural-language instruction layer of LLM applications.

The process remains:

**Learn → Question → Understand → Apply → Share**

---

## References

- [MITRE — ATLAS](https://atlas.mitre.org/)
- [MITRE — ATT&CK](https://attack.mitre.org/)
- [NIST — Cybersecurity Framework 2.0](https://www.nist.gov/cyberframework)
- [OWASP — Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

---

## About This Learning Journal

This repository documents my personal learning journey and reflections while studying AI Security.

The learning path is inspired by my studies using **TryHackMe's AI Security material**, combined with my previous cybersecurity and incident-management experience.

The explanations, analogies, examples, and conclusions presented here represent my own understanding and reflections.

This repository does not reproduce TryHackMe labs, questions, solutions, flags, credentials, assessment answers, or proprietary course content.

**Learn → Question → Understand → Apply → Share**
