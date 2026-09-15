# Day 13 — Understanding AI Supply Chains

<p align="center">
  <img src="../Pictures/Day13.png" alt="AI Security Learning Journal — Day 13: Understanding AI Supply Chains" width="100%">
</p>

> AI supply-chain security begins by identifying every external component and relationship that must be trusted before a system can operate.

## At a Glance

**Reading time:** about 24 minutes

This entry maps the models, datasets, libraries, repositories, infrastructure, and providers that make up an AI supply chain.

**Key takeaways:**

- A model is an artifact inside a larger dependency and trust graph.
- Provenance must describe origin, ownership, transformations, and custody.
- Trust should be evidence-based, scoped, and continuously reassessed.

**Suggested path:** Follow the dependency map first, then use the provenance and continuous-trust sections to evaluate a real system.

**Quick navigation:** [Four core components](#the-four-core-components) · [Trust is transitive](#trust-is-transitive) · [Provenance](#provenance-becomes-a-security-property) · [Pre-deployment questions](#my-pre-deployment-security-questions)

## AI Security Learning Journal

Today I started **Module 4 — AI Supply Chain Security**.

Until now, much of my learning journey has focused on what can happen after an AI system exists: insecure architectures, Prompt Injection, Jailbreaking, excessive capabilities, unsafe outputs, and failures across trust boundaries.

This topic moved the investigation further upstream.

Instead of asking only:

> **How can this AI system be attacked?**

I started asking:

> **What had to be trusted before this AI system could even exist?**

A production AI system rarely begins with components created entirely by the organization deploying it.

Models may come from public repositories.

Datasets may come from third parties.

Frameworks may contain hundreds of thousands of lines of code.

Packages may introduce dependencies I never selected directly.

A base model may have been trained by one organization, adapted by another, quantized by someone else, distributed through another platform, and finally downloaded into my infrastructure.

Every step creates a trust relationship.

And every trust relationship creates part of the attack surface.

My first major takeaway from this topic is:

> **A working model proves functionality. It does not prove provenance, integrity, or trustworthiness.**

---

## From Software Supply Chains to AI Supply Chains

The basic supply-chain problem already exists in traditional software.

A modern application rarely contains only code written by its own development team.

Instead, it may depend on:

```text
Application
    │
    ├── Framework
    │      │
    │      ├── Dependency A
    │      │      └── Transitive Dependency
    │      │
    │      └── Dependency B
    │
    └── Runtime
```

When I install one package, I may implicitly trust many others.

This means that my actual trust decision is much larger than:

```text
I trust Package A
```

It may really mean:

```text
I trust Package A
        ↓
I trust what Package A depends on
        ↓
I trust what those dependencies depend on
        ↓
I trust the repositories distributing them
        ↓
I trust the identities allowed to publish them
        ↓
I trust the infrastructure used to build them
```

An attacker does not necessarily need to compromise my application directly.

If they can compromise something my application already trusts, the existing supply chain may deliver the attack for them.

This is one reason supply-chain attacks can scale so effectively.

Instead of attacking every downstream victim individually:

```text
Attacker
   ├── Victim A
   ├── Victim B
   ├── Victim C
   └── Victim D
```

the attacker may target something upstream:

```text
              Attacker
                  │
                  ▼
          Trusted Component
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
     Victim A  Victim B  Victim C
```

The trust relationship becomes part of the delivery mechanism.

---

## AI Expands the Traditional Supply Chain

AI does not replace the traditional software supply chain.

It extends it.

An AI application may still depend on operating systems, containers, package managers, libraries, APIs, CI/CD pipelines and cloud infrastructure.

But now additional artifacts enter the picture:

```text
Traditional Software Supply Chain
            +
        AI Models
            +
        Training Data
            +
      Model Adaptations
            +
   Model Transformations
            =
     AI Supply Chain
```

This makes AI supply-chain security especially interesting to me because a model is not simply another software library.

Its behaviour may reflect decisions and artifacts originating much earlier in its lifecycle.

A model I download today may represent the result of:

```text
Architecture Selection
        ↓
Training Dataset
        ↓
Training Process
        ↓
Base Model
        ↓
Fine-Tuning
        ↓
Adapters
        ↓
Optimization
        ↓
Quantization
        ↓
Packaging
        ↓
Distribution
        ↓
Deployment
```

I may control only the final step.

Yet my application inherits trust assumptions from everything above it.

---

## The Four Core Components

A useful way to reason about an AI supply chain is through four major components:

```text
AI Supply Chain
│
├── Models
├── Datasets
├── Frameworks
└── Dependencies
```

Each creates different security questions.

---

## Models

Pre-trained models allow organizations to build AI systems without training everything from scratch.

That creates enormous practical value.

It also creates inherited trust.

When I consume a model built elsewhere, I may be trusting:

```text
Model Architecture
        +
Training Data
        +
Training Process
        +
Learned Weights
        +
Fine-Tuning
        +
Adapters
        +
Transformations
        +
Packaging
```

The fact that inference works correctly does not prove those stages were trustworthy.

This distinction matters:

```text
Functional
    ≠
Secure
    ≠
Trusted
```

A malicious or compromised model does not necessarily need to behave maliciously all the time.

Normal behaviour can coexist with hidden behaviour.

For example:

```text
Normal Input
     ↓
Expected Behaviour

Specific Trigger
     ↓
Unexpected / Malicious Behaviour
```

That means traditional functional testing may not reveal the security problem.

---

## Datasets

Models learn from data.

Therefore the provenance and integrity of training data become security concerns.

A dataset can influence:

```text
What the model learns

What patterns it associates

What biases it develops

What behaviours it reproduces

How it responds to specific inputs
```

This creates an important distinction between a runtime attack and a training-time attack.

A malicious model artifact may attack the system when loaded or executed.

A poisoned dataset may instead influence the model while it is being created.

```text
Poisoned Dataset
      ↓
Training Pipeline
      ↓
Learned Parameters
      ↓
Model
      ↓
Unexpected Behaviour
```

Both belong to supply-chain security because the organization is consuming an upstream artifact whose integrity affects the downstream system.

---

## Frameworks

AI development relies heavily on large frameworks.

These frameworks provide capabilities such as:

```text
Tensor Operations
Model Construction
Training
Optimization
Inference
Hardware Acceleration
Serialization
Data Processing
```

But a framework is itself software.

It therefore inherits the familiar risks of the traditional software supply chain:

```text
Framework
    ↓
Packages
    ↓
Dependencies
    ↓
Transitive Dependencies
```

The AI-specific layer does not remove traditional application-security concerns.

It sits on top of them.

---

## Dependencies

One of the most important reminders from this topic was that my attack surface includes software I did not explicitly choose.

Consider:

```text
My Project
    ↓
Package A
    ↓
Package B
```

I selected Package A.

I never selected Package B.

But if Package A requires B and my package manager installs it, B becomes part of my runtime and therefore part of my security boundary.

Package B is a **transitive dependency**.

The trust relationship is implicit:

```text
I trust A
    ↓
A trusts B
    ↓
I indirectly trust B
```

This is why dependency analysis cannot stop at the packages explicitly listed by the developer.

---

## Trust Is Transitive

This led me to a broader security principle.

In complex systems, trust frequently propagates.

```text
Organization
     ↓ trusts
Repository
     ↓ trusts
Publisher
     ↓ publishes
Framework
     ↓ requires
Dependency
     ↓ requires
Another Dependency
```

The organization at the top may know almost nothing about the component at the bottom.

Yet that component may still execute inside its environment.

This is a dangerous property of modern software ecosystems:

> **Operational trust can travel farther than human visibility.**

The less visibility I have into that chain, the more important provenance, verification and containment become.

---

## Transitive Dependencies Expand the Attack Surface

This also changed how I think about a simple command such as installing a package.

What appears conceptually as:

```text
Install Framework
```

may really represent:

```text
Install Framework
      │
      ├── Dependency A
      │      ├── Dependency C
      │      └── Dependency D
      │
      ├── Dependency B
      │
      └── ...
```

Every additional component potentially introduces:

```text
Another maintainer
Another repository
Another release process
Another credential
Another build pipeline
Another vulnerability
Another opportunity for compromise
```

The dependency graph is therefore also a trust graph.

---

## Dependency Confusion

One attack pattern illustrates this particularly well.

Imagine an organization uses an internal package:

```text
company-internal-ml-helper
```

The organization expects it to come from an internal repository.

An attacker publishes a package with the same name to a public registry.

Now the security question becomes:

> **Which source will the package manager resolve?**

If configuration or version resolution causes the public package to be selected, malicious code may enter the environment through a normal installation process.

The developer did not intentionally install malware.

The attacker exploited assumptions about where a trusted dependency should come from.

This is **dependency confusion**.

---

## Dependency Confusion Is Not Typosquatting

It is important for me to keep these concepts separate.

### Dependency Confusion

The attacker may use the **same package name** as an internal dependency:

```text
Internal:
company-internal-helper

Public:
company-internal-helper
```

The attack exploits package-resolution behaviour and repository trust.

### Typosquatting

The attacker creates a **similar-looking name**:

```text
legitimate-package
legitmate-package
```

The attack depends more directly on a developer selecting or typing the wrong package.

Both attack the dependency layer.

But the trust failure is different.

---

## The Four Attack Layers

Another useful mental model is to separate AI supply-chain attacks into four layers:

```text
AI Supply Chain Attack Surface
│
├── Model Layer
├── Dependency Layer
├── Data Layer
└── Infrastructure Layer
```

This separation helps identify what is actually being compromised.

But these layers should not be treated as isolated silos.

An attacker may combine them.

---

## Layer 1 — Model

The model layer is particularly distinctive because the model artifact itself can introduce security risk.

I found it useful to divide model-level attacks into three conceptual levels:

```text
Model Layer
│
├── Serialization
├── Architecture
└── Weights
```

These are not equivalent.

And defending against one does not automatically defend against the others.

---

## Serialization-Level Risk

Serialization allows program state or objects to be stored and later reconstructed.

Some Python serialization mechanisms can reconstruct objects in ways that invoke executable behaviour.

This creates a dangerous trust boundary:

```text
Downloaded Artifact
       ↓
Deserializer
       ↓
Object Reconstruction
       ↓
Unexpected Code Execution
```

The security problem can occur **while loading the artifact**, before I even evaluate whether the model performs its advertised ML task correctly.

This is why I should not mentally treat every model file as passive data.

Depending on the format:

> **Loading can itself be execution.**

---

## Architecture-Level Risk

Removing unsafe serialization does not prove the model architecture itself is trustworthy.

Malicious behaviour could exist in the model's computational structure.

Conceptually:

```text
Input
  ↓
Model Architecture
  ↓
Embedded Malicious Logic
  ↓
Output / Side Effect
```

This attack is different from hiding executable behaviour in the serialization format.

The dangerous behaviour belongs to the architecture being executed.

---

## Weights-Level Risk

The third level is more subtle.

Instead of adding conventional executable logic, an attacker may manipulate the learned parameters themselves.

Conceptually:

```text
Normal Input
     ↓
Normal Behaviour

Trigger Input
     ↓
Manipulated Learned Behaviour
```

This means a model can appear completely functional during ordinary testing while retaining hidden behaviour associated with particular patterns.

This is one reason model accuracy alone cannot establish integrity.

---

## Performance Is Not Integrity

This connects directly to something I learned earlier in this journey:

> **Performance is not trust.**

Imagine:

```text
Accuracy = 98%
```

That tells me something about performance on the evaluated dataset.

It does not automatically tell me:

```text
Who created the model

Whether the training data was poisoned

Whether specific triggers exist

Whether the artifact was modified

Whether dependencies are safe

Whether the distribution account was compromised
```

A targeted backdoor may affect only a very specific condition while leaving normal evaluation performance almost unchanged.

Therefore:

```text
High Accuracy
      ≠
Supply Chain Integrity
```

Evaluating only the final result is like evaluating a cake by its appearance without knowing the ingredients, their origin, or what happened during preparation.

---

## Safer Serialization Does Not Mean Safe Model

A particularly important distinction for me was understanding what safer model formats actually solve.

Moving away from executable serialization formats can remove an important attack vector.

But:

```text
Safer Serialization
       ↓
Reduces Serialization Risk
```

does not imply:

```text
Trusted Architecture
Trusted Weights
Trusted Training
Trusted Dataset
Trusted Provenance
```

This gives me another principle:

> **A safer file format removes an attack vector, not the entire supply-chain risk.**

---

## SafeTensors and the Security Boundary

SafeTensors is designed to store tensor data without relying on arbitrary Python object deserialization.

That makes it fundamentally different from formats that may invoke executable object reconstruction.

From a security perspective:

```text
Pickle-based Artifact
        ↓
Potential Serialization Execution

SafeTensors Artifact
        ↓
Raw Tensor-Oriented Representation
```

This is a meaningful security improvement.

But the correct conclusion is:

```text
Serialization Attack Surface Reduced
```

not:

```text
Model Proven Safe
```

The weights themselves can still encode unwanted behaviour.

The training process can still have been compromised.

The model can still inherit backdoors.

The provenance can still be unknown.

Security controls need to match the specific layer of risk.

---

## GGUF and Local Models

Another useful example is GGUF, commonly encountered when running quantized LLMs locally.

The absence of the same pickle-style arbitrary deserialization behaviour does not make a GGUF artifact automatically trustworthy.

There are still questions such as:

```text
Who trained the original model?

Who produced this artifact?

Was the model modified?

Who performed the quantization?

Can I verify the lineage?

Can I verify the artifact?
```

This reinforces:

> **Non-executable format does not mean trusted model.**

---

## Quantization Is Also a Supply-Chain Step

Before this topic, it would have been easy to think of quantization purely as optimization.

Now I see another dimension.

Suppose:

```text
Original Model
     ↓
Third-Party Quantization
     ↓
Quantized Model
     ↓
My Infrastructure
```

Even if I trust the original model, the third party introduced another transformation.

That creates another trust dependency.

The security question is no longer only:

> Do I trust the original model?

It also becomes:

> Do I trust the artifact produced after the transformation?

This connects strongly with something I learned earlier:

> **Model optimization changes the artifact I am trusting. Validate the actual production version.**

---

## Fine-Tuning Does Not Reset Trust

Transfer learning allows teams to start from existing models rather than training everything from zero.

That provides enormous efficiency.

But it also means downstream systems inherit properties from upstream models.

Conceptually:

```text
Base Model
     ↓
Fine-Tuning
     ↓
Application-Specific Model
```

Fine-tuning changes behaviour.

It should not automatically be treated as a security reset.

If the base artifact contains unwanted behaviour, the fact that I adapted it for my own domain does not prove the original problem disappeared.

The lineage still matters.

---

## LoRA Adds Another Trust Dependency

LoRA and similar parameter-efficient adaptation techniques make it possible to modify model behaviour using relatively small learned adapters instead of retraining all model parameters.

A useful conceptual representation is:

```text
Base Model
     +
LoRA Adapter
     ↓
Adapted Model Behaviour
```

From a supply-chain perspective, this means I now have at least two artifacts to trust:

```text
Base Model Provenance
        +
Adapter Provenance
```

A trusted base model combined with an untrusted adapter does not produce a trusted system.

This was an important correction to my initial intuition.

The adapter is not merely "some extra domain data."

It is itself a learned artifact produced through another process and therefore another link in the supply chain.

---

## The Data Layer

Supply-chain attacks do not require malicious executable files.

An attacker can instead compromise information entering the training process.

```text
External Dataset
       ↓
Training Pipeline
       ↓
Model
       ↓
Production
```

If the dataset is untrusted, manipulated or poisoned, the resulting model may inherit unwanted behaviour.

This means dataset provenance matters just as much as software provenance.

Questions include:

```text
Where did the data come from?

Who collected it?

Who modified it?

How was it validated?

What transformations were applied?

Can its integrity be verified?

Were unexpected sources introduced?
```

The dataset itself is part of the supply chain.

---

## The Infrastructure Layer

Supply-chain security is not only about inspecting files.

Sometimes the file is malicious because the infrastructure distributing it was compromised.

Examples of infrastructure-level trust include:

```text
Repository Accounts
Maintainer Credentials
Build Systems
CI/CD Pipelines
Artifact Registries
Release Workflows
Distribution Infrastructure
```

Suppose an attacker steals a maintainer's credentials.

They may then publish through:

```text
Correct Repository
Correct Account
Correct Package Name
Expected Distribution Channel
```

From the consumer's perspective, many reputation signals still look legitimate.

That is what makes infrastructure compromise especially dangerous.

The attacker did not merely create a suspicious imitation.

They compromised the mechanism that creates legitimacy.

---

## Reputation Is Evidence, Not Proof

Repository reputation is useful.

Signals such as:

```text
Verified Organization
Established History
Large User Base
Detailed Documentation
Security Scanning
Active Community
```

can increase confidence.

But they should not be interpreted as proof.

A trusted maintainer can be compromised.

A repository can be compromised.

A build pipeline can be compromised.

A security scanner can have blind spots.

A popular artifact can contain inherited risk.

Therefore:

> **Trust signals should contribute to a security decision, not replace one.**

---

## Automated Scanning Is a Control, Not a Guarantee

Security scanners are valuable.

But a scanner is still software interpreting another artifact.

Conceptually:

```text
Artifact
   ↓
Scanner
   ↓
PASS
```

does not mathematically establish:

```text
Artifact
   ↓
Runtime
   ↓
SAFE
```

A scanner may:

```text
Miss an unknown technique
Misinterpret malformed data
Lack visibility into model behaviour
Inspect serialization but not weights
Contain parser limitations
```

This brings Prompt Defence's defence-in-depth lesson directly into supply-chain security.

> **No single scanner should become the entire trust boundary.**

---

## Downloading a Model vs Calling an API

Not every organization downloads model weights.

There are two very different consumption models.

### Local / Downloaded Model

```text
Model Repository
       ↓
Model Artifact
       ↓
Your Infrastructure
       ↓
Inference
```

The artifact crosses my trust boundary.

I become responsible for evaluating and operating it.

I may gain more visibility and control, but I also directly accept risks associated with the artifact and its runtime.

### Hosted API

```text
My Application
       ↓
Provider API
       ↓
Provider Infrastructure
       ↓
Provider Model
```

I do not receive the model artifact.

That removes some local artifact risks.

But it does not eliminate the supply chain.

Instead, much of it becomes invisible to me.

I am trusting the provider's:

```text
Model Selection
Training
Data Curation
Fine-Tuning
Security Controls
Hosting
Version Management
Update Process
```

This led to another major takeaway:

> **The supply chain does not disappear behind an API. It becomes less visible to the consumer.**

---

## Control vs Visibility

The download/API comparison can also be viewed as a trade-off.

### Download

```text
More Artifact Visibility
More Operational Control
More Local Responsibility
```

### API

```text
Less Artifact Visibility
Less Infrastructure Responsibility
Greater Provider Dependency
```

Neither model automatically eliminates supply-chain risk.

The trust boundaries simply move.

---

## Artifact Pinning and API Versioning

Local artifacts can sometimes be pinned cryptographically.

For example:

```text
Expected Artifact
      ↓
Cryptographic Hash
      ↓
Downloaded Artifact
      ↓
Hash Comparison
```

If the hash changes, I know I no longer have the exact same artifact.

With a hosted service, the situation can be different.

```text
Application
    ↓
model-name
    ↓
Provider
```

Depending on the provider's versioning guarantees, the same logical model identifier may not necessarily provide the same level of artifact immutability as a locally pinned file.

This creates a different supply-chain concern:

> **A stable API name does not inherently prove a stable underlying model artifact.**

Versioning guarantees therefore become part of the provider trust assessment.

---

## Provenance Becomes a Security Property

The word that kept appearing throughout this topic was **provenance**.

For me, provenance means being able to answer:

```text
What is this?

Where did it come from?

Who produced it?

What happened to it?

Who transformed it?

How did it reach me?

Can I verify that history?
```

Without provenance, I have uncertainty.

And an important correction from earlier in my journey still applies:

> **Missing provenance does not prove compromise. It creates uncertainty that must be handled as risk.**

That distinction matters.

Security analysis should not turn absence of evidence into evidence of attack.

But it should not silently turn uncertainty into trust either.

---

## The Supply Chain Is a Graph, Not a Line

The phrase "supply chain" can make the architecture sound linear:

```text
A → B → C → D
```

Real AI systems look more like graphs.

```text
                 Dataset A
                    │
Dataset B ──────────┤
                    ▼
                Training
                    │
Base Model ─────────┤
                    ▼
                 Adapter
                    │
Framework ──────────┤
                    ▼
               Application
                    │
Dependencies ───────┤
                    ▼
               Production
```

Every branch introduces its own provenance and trust questions.

This is why understanding the actual architecture matters before trying to secure it.

---

## Compound Supply-Chain Attacks

The four attack layers are useful analytical categories.

But real attackers do not need to respect those categories.

Consider:

```text
Compromised Repository Account
          │
          ▼
Infrastructure Layer
          │
          ▼
Malicious Model
          │
          ▼
Model Layer
          │
          ▼
Malicious Dependency
          │
          ▼
Dependency Layer
          │
          ▼
Poisoned Dataset
          │
          ▼
Data Layer
          │
          ▼
Victim Environment
```

Looking at each layer independently could underestimate the total attack path.

This connects directly with threat modelling.

The question is not only:

> What threats exist at each component?

It is also:

> **How can compromise propagate between components through existing trust relationships?**

---

## Supply Chain Meets Threat Modelling

My earlier threat-modelling workflow can now be extended.

Previously I thought in terms of:

```text
Architecture
    ↓
Components
    ↓
Assets
    ↓
Trust Boundaries
    ↓
Threats
```

Now I need another upstream dimension:

```text
Architecture
    ↓
Components
    ↓
Component Provenance
    ↓
Supply Chain
    ↓
Trust Relationships
    ↓
Transformations
    ↓
Assets
    ↓
Threats
```

The deployed architecture tells me **what exists now**.

Supply-chain analysis helps explain **how those components got there**.

Both views are necessary.

---

## Supply Chain Meets Reconnaissance

This also connects strongly to AI System Reconnaissance.

Reconnaissance asks:

```text
What AI components actually exist?
```

Supply-chain analysis adds:

```text
Where did those components come from?
```

Together:

```text
Discover Component
      ↓
Identify Artifact
      ↓
Determine Provenance
      ↓
Map Dependencies
      ↓
Identify Transformations
      ↓
Evaluate Trust
      ↓
Assess Risk
```

This makes software inventories, model inventories and dependency inventories security tools rather than administrative documentation.

---

## My Pre-Deployment Security Questions

If I received an open-source model for production evaluation, I would not start by loading it.

I would first build context.

My reasoning would look approximately like this:

```text
1. Provenance
      ↓
2. Model Artifact
      ↓
3. Dependencies
      ↓
4. Dataset Lineage
      ↓
5. Training / Adaptation History
      ↓
6. Repository & Infrastructure Signals
      ↓
7. Runtime Capabilities
      ↓
8. Isolated Validation
      ↓
9. Production Decision
```

### 1 — Provenance

```text
Who published it?
Is the source authoritative?
How old is the account/project?
Is the organization verifiable?
What is the release history?
```

### 2 — Artifact

```text
What format is it?
What happens when it is loaded?
Can I verify its hash?
Are security findings available?
Was it transformed?
```

### 3 — Dependencies

```text
What direct dependencies exist?
What transitive dependencies exist?
Where are they resolved from?
Are package names suspicious?
Are versions pinned?
```

### 4 — Dataset Lineage

```text
What data was used?
Where did it originate?
How was it validated?
What transformations occurred?
```

### 5 — Training and Adaptation

```text
What is the base model?
Was it fine-tuned?
Are adapters involved?
Who produced them?
Was the model quantized?
Who performed that transformation?
```

### 6 — Repository and Infrastructure

```text
Who can publish?
Has ownership changed?
Are there unexpected releases?
What security signals exist?
```

### 7 — Runtime

Even if the model is malicious, I want to know what it could reach.

```text
Network?
Filesystem?
Credentials?
Cloud Metadata?
Databases?
Secrets?
Other Internal Systems?
```

### 8 — Isolated Validation

Unknown artifacts should not receive production trust as their first test environment.

### 9 — Production Decision

Only after understanding both:

```text
Artifact Risk
       +
Runtime Exposure
```

would I decide whether the component belongs in production.

---

## The Model Page Is Part of the Investigation

When evaluating a public model repository, I now see the page differently.

Previously I might focus primarily on:

```text
Does this model solve my problem?
```

Now I would also inspect signals such as:

```text
Publisher identity
Organization verification
Account history
Artifact format
Security findings
Model documentation
Training information
Limitations
Repository activity
Community signals
File inventory
```

A professional-looking model card is useful.

It is not a security boundary.

A large download count is useful context.

It is not an integrity proof.

A verified organization is a strong trust signal.

It does not make compromise impossible.

The security decision comes from combining evidence.

---

## Trust Signals vs Security Guarantees

This distinction became one of the most useful mental models from Day 13.

```text
Trust Signals
│
├── Reputation
├── Verification
├── Documentation
├── Downloads
├── Community
├── Security Scans
└── Provenance Information
```

These signals help answer:

> **How much confidence should I have?**

They do not answer with certainty:

> **Is this artifact impossible to compromise?**

Security engineering rarely gives that kind of certainty.

Instead, I accumulate evidence, reduce uncertainty and control the consequences if my trust decision is wrong.

---

## Assume Upstream Trust Can Fail

This connects beautifully with the principle I took from Prompt Defence:

> **Assume the model can fail. Design the system so the security boundaries do not fail with it.**

For supply-chain security, I can extend it:

> **Assume an upstream trust relationship can fail. Design the environment so one compromised component does not automatically compromise everything downstream.**

That means provenance and verification are only part of the solution.

Runtime containment still matters.

Even a carefully reviewed artifact should not automatically receive:

```text
Unlimited Network Access
+
Production Credentials
+
Sensitive Filesystem Access
+
Administrative Permissions
```

Supply-chain security and least privilege reinforce each other.

---

## A New Way I Think About Trust

Before studying AI supply chains, it would have been easy to think:

```text
Known Organization
        +
Popular Model
        +
Good Documentation
        +
Works Correctly
        =
Trusted
```

Now I see something different:

```text
Reputation
        +
Provenance
        +
Artifact Inspection
        +
Dependency Analysis
        +
Data Lineage
        +
Transformation History
        +
Security Validation
        +
Runtime Containment
        =
Evidence for a Trust Decision
```

The difference is subtle but important.

Trust should be the **result of evaluation**, not the starting assumption.

---

## My Biggest Takeaway

The biggest lesson from Day 13 is not that public models, packages or datasets are inherently unsafe.

It is that every external component represents a trust decision.

And sometimes that decision is being made implicitly.

When I run an AI system, I may be trusting people and infrastructure I have never directly interacted with:

```text
Model Authors
Dataset Maintainers
Framework Developers
Package Maintainers
Repository Operators
Build Infrastructure
Adapter Authors
Model Quantizers
API Providers
```

The final model is only the visible artifact at the end of that chain.

That gives me the principle I want to carry into the rest of this module:

> **Do not evaluate only the artifact in front of you. Evaluate the chain of trust that produced it.**

And one question now comes before `model.load()`:

> **What exactly am I trusting when I load this model?**

---

## Key Takeaways

- AI supply-chain security extends traditional software supply-chain security rather than replacing it.
- Models, datasets, frameworks and dependencies all create upstream trust relationships.
- Transitive dependencies expand the attack surface beyond packages explicitly selected by developers.
- A model can be functional while still being malicious or compromised.
- Model-level attacks can exist at serialization, architecture and weights levels.
- Safer serialization removes specific attack vectors but does not prove model integrity.
- SafeTensors reduces serialization risk but does not establish trusted weights, training or provenance.
- Non-pickle formats should not automatically be interpreted as trusted models.
- Fine-tuning does not automatically remove inherited model risk.
- LoRA adapters introduce additional artifacts and therefore additional trust dependencies.
- Quantization is a transformation step and can introduce another party into the supply chain.
- Dataset integrity and provenance are security concerns because compromised data can affect learned behaviour.
- Dependency confusion and typosquatting attack different trust assumptions.
- Repository accounts, maintainer identities and build pipelines belong to the infrastructure attack surface.
- Reputation and popularity are trust signals, not security guarantees.
- Automated security scanning is a defensive control, not proof of safety.
- Downloading models and consuming hosted APIs create different supply-chain risk profiles.
- The supply chain does not disappear behind an API; much of it becomes less visible to the consumer.
- Cryptographic artifact pinning and provider-side model versioning represent different trust models.
- Supply-chain attacks can combine model, dependency, data and infrastructure layers.
- Provenance reduces uncertainty but should be combined with technical validation and runtime containment.
- The actual production artifact matters, including adaptations and transformations performed after the original model was released.
- A dependency graph is also a trust graph.
- Supply-chain analysis complements AI reconnaissance and threat modelling.
- Trust should emerge from evidence rather than be assumed from reputation.

---

## Final Reflection

Day 13 changed where I place the beginning of an AI security assessment.

The system does not begin when the model starts inference.

It begins much earlier.

It begins with the datasets someone selected, the model someone trained, the framework someone maintained, the dependency someone published, the adapter someone created, the transformation someone performed, and the infrastructure someone trusted to distribute all of it.

By the time an AI artifact reaches production, it may already contain years of inherited technical and organizational trust.

So my security question is no longer only:

> **Can I secure this AI system?**

It is also:

> **Can I explain why I trust the components from which this AI system was built?**

That is where AI supply-chain security begins.

---

## Next

Day 13 established the trust map.

The next step is to go deeper into **AI Supply Chain Attack Vectors** and examine how attackers exploit these relationships across models, dependencies, data and infrastructure.

---

## References

- [OWASP — LLM03:2025 Supply Chain](https://genai.owasp.org/llmrisk/llm032025-supply-chain/)
- [MITRE — ATLAS](https://atlas.mitre.org/)
- [PyTorch — Security Policy](https://github.com/pytorch/pytorch/security/policy)
- [Hugging Face — Hub security](https://huggingface.co/docs/hub/security)
- [OpenSSF — Supply-chain Levels for Software Artifacts](https://slsa.dev/)

---

*This repository documents my personal learning journey in AI Security. It contains my own explanations, reflections, security reasoning, and independently structured notes based on concepts studied through multiple educational and industry sources, including the TryHackMe AI Security learning path. It does not reproduce challenge solutions, flags, credentials, proprietary lab content, or step-by-step walkthroughs.*

**Learn → Question → Understand → Apply → Share**
