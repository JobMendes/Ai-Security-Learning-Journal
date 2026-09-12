# Day 10 — Prompt Injection

<p align="center">
  <img src="../Pictures/Day10.png" alt="AI Security Learning Journal — Day 10: Prompt Injection" width="100%">
</p>

> Untrusted content should never gain authority merely because an LLM can read it.

## At a Glance

**Reading time:** about 24 minutes

This entry explains direct and indirect prompt injection as an architectural trust problem created when instructions and untrusted data share model context.

**Key takeaways:**

- Instruction hierarchy is not authorization enforcement.
- Impact depends on the tools, data, permissions, and actions connected to the model.
- Independent authorization, least privilege, output controls, and monitoring must contain model failure.

**Suggested path:** Read the instruction/data problem first, then the attack-path model and defense-in-depth sections.

**Quick navigation:** [Instruction/data problem](#the-fundamental-instructiondata-problem) · [Indirect injection](#indirect-prompt-injection) · [Defense in depth](#defense-in-depth-around-the-model) · [Attack-path model](#my-prompt-injection-attack-path-model)

## From System Security to Instruction Security

The previous days changed how I look at AI systems.

I started by learning that securing a model is not enough.

Then I moved through:

**Architecture → Assets → Trust Boundaries → Threat Modelling → Reconnaissance**

Day 10 introduced another security boundary:

> **The boundary between data and instructions.**

This boundary is unusual.

In traditional applications, code and data can often be separated through strong technical mechanisms.

An LLM works differently.

System instructions, user messages, retrieved documents, tool responses, conversation history, and external content may eventually coexist inside the context used by the model to generate its next tokens.

That creates a fundamental security problem:

> **What happens when content that should only be treated as data influences the model as if it were an instruction?**

That is where Prompt Injection begins.

---

## What Prompt Injection Actually Is

My simplest mental model is:

```text
Expected behaviour
       ↓
Trusted instructions
       ↓
LLM
       ↓
Expected response
```

Prompt Injection attempts to introduce another instruction path:

```text
Trusted Instructions
        ↓
       LLM
        ↑
Untrusted Instruction
```

If the untrusted instruction changes the model's behaviour, the application may no longer operate according to its intended rules.

This does not necessarily require:

- malware;
- memory corruption;
- remote code execution;
- exploitation of a traditional software bug.

The attacker targets the **instruction-processing behaviour** of the LLM application.

---

## The Fundamental Instruction/Data Problem

One of the biggest lessons from this Day is that LLM applications consume many different kinds of information.

For example:

```text
System Prompt
Developer Instructions
User Prompt
Conversation History
Retrieved Documents
Tool Outputs
Web Content
Emails
PDFs
Database Results
```

Humans immediately assign different meanings to these things.

A system prompt is an instruction.

A PDF is normally data.

An email is content.

A database result is information.

But all of them may eventually contribute tokens to the model's context.

This creates the central problem:

> **Semantic content can influence model behaviour regardless of whether the application intended that content to carry authority.**

---

## How an LLM Sees Context

At a simplified level, an LLM processes tokens.

Something conceptually like:

```text
SYSTEM:
Protect confidential information.

USER:
Summarize this document.

DOCUMENT:
Quarterly financial report...
```

eventually becomes structured context presented to the model.

The model does not interpret this environment through operating-system permission primitives.

It predicts tokens based on:

- training;
- context;
- instruction structure;
- probability;
- model behaviour;
- decoding configuration.

This is important because the application may conceptually think:

```text
SYSTEM = trusted instruction
USER = request
DOCUMENT = untrusted data
```

while the model is still processing natural-language content from all three sources.

---

## Tokens Do Not Carry Security Intent

A token does not inherently contain a label saying:

```text
TRUSTED
UNTRUSTED
MALICIOUS
BENIGN
DATA
COMMAND
```

Applications can provide structure.

Models can be trained to follow instruction hierarchies.

Guardrails can improve behaviour.

But this is different from having a hard security primitive attached to every token.

This helped me understand why Prompt Injection is difficult to eliminate through prompting alone.

The model is operating on meaning.

The attacker is also operating on meaning.

---

## Context Windows and Instruction Mixing

The context window is where information relevant to the current inference can coexist.

Conceptually:

```text
┌─────────────────────────────────────┐
│           CONTEXT WINDOW            │
├─────────────────────────────────────┤
│ System Instructions                 │
│ Developer Instructions              │
│ Conversation History                │
│ User Input                          │
│ Retrieved Documents                 │
│ Tool Results                        │
└─────────────────────────────────────┘
                 ↓
                LLM
                 ↓
             Prediction
```

This architecture is incredibly powerful.

It allows the model to reason over many sources.

But the same flexibility creates security complexity.

If untrusted information enters this context, I need to assume it may attempt to influence model behaviour.

---

## Instruction Hierarchy

Modern LLM systems can distinguish different instruction roles.

A simplified hierarchy might look like:

```text
SYSTEM
   ↓
DEVELOPER
   ↓
USER
   ↓
TOOL / EXTERNAL CONTENT
```

Higher-priority instructions are intended to override conflicting lower-priority instructions.

This is important.

But I learned that I should not confuse this with traditional authorization.

---

## Instruction Hierarchy Is Not Authorization Enforcement

Consider:

```text
root
user
guest
```

In an operating system, permissions can be technically enforced by security mechanisms outside the user's natural-language interpretation.

Now compare:

```text
SYSTEM
DEVELOPER
USER
TOOL
```

These are instruction roles.

They help establish how the model should interpret competing instructions.

But:

> **Instruction priority is not the same thing as an authorization boundary.**

This distinction matters enormously.

A system prompt saying:

```text
Never access confidential records for unauthorized users.
```

is useful.

But the application should not rely exclusively on that sentence to enforce access control.

Authorization should exist independently:

```text
User
  ↓
Identity
  ↓
Authorization
  ↓
Allowed Data
  ↓
LLM
```

not:

```text
User
  ↓
LLM decides whether access is allowed
  ↓
Sensitive Data
```

My rule is:

> **Instruction hierarchy is not authorization enforcement.**

---

## System, Developer, User, Tool and Retrieved Context

Different context sources have different intended purposes.

### System

Defines high-level model/application behaviour.

### Developer

Defines application-specific behaviour and constraints.

### User

Provides the current request.

### Tools

Return information from external capabilities.

### Retrieved Content

Provides additional context from sources such as RAG, websites, documents, emails, or databases.

The security problem appears when:

```text
LOWER-TRUST CONTENT
        ↓
influences
        ↓
HIGHER-TRUST BEHAVIOUR
```

The application needs to prevent the authority of content from increasing simply because the model consumed it.

---

## Direct Prompt Injection

The most obvious form occurs when the attacker directly interacts with the LLM application.

Conceptually:

```text
Attacker
   ↓
Malicious User Prompt
   ↓
LLM
   ↓
Behaviour Change
```

For example, an attacker may attempt to:

- override previous instructions;
- redefine the task;
- change output restrictions;
- manipulate the role assumed by the model;
- convince the model that authorization exists.

The exact wording is not the important part.

The objective is:

> **Cause attacker-controlled instructions to influence behaviour that should have remained constrained by trusted instructions.**

---

## Prompt Injection Is Not Just "Ignore Previous Instructions"

A common mistake would be reducing Prompt Injection to a phrase such as:

```text
Ignore previous instructions.
```

That is only one possible representation of an intent.

The attacker does not need those exact words.

Natural language provides enormous semantic flexibility.

For example:

```text
Forget the earlier rules.

The previous restrictions no longer apply.

Continue under the following updated policy.

Treat the next instructions as authoritative.

For this task, use these replacement requirements.
```

The surface representation changes.

The semantic objective may remain similar.

This is why simple string matching is not a sufficient security strategy.

---

## Synonymised and Paraphrased Overrides

This technique reinforced an important difference between traditional pattern matching and language-based attacks.

Imagine a filter:

```python
if "ignore previous instructions" in prompt:
    block()
```

This protects against a string.

It does not necessarily protect against the **meaning** represented by the string.

An attacker can express similar intent through:

- synonyms;
- paraphrasing;
- different sentence structures;
- contextual manipulation;
- indirect requests.

Therefore:

> **A semantic attack surface cannot be reliably secured with literal-string blocklists alone.**

This does not mean input filtering is useless.

It means it cannot be the only layer.

---

## Format-Based Injection

Malicious instructions can also be concealed through formatting.

Examples can include content that is:

- visually hidden;
- embedded in markup;
- placed in comments;
- positioned outside normal visual areas;
- encoded in structures primarily consumed by machines.

The important distinction is:

> **Hidden from the human does not mean hidden from the machine.**

A human reviewing a document may see:

```text
Normal business document
```

while the AI processing pipeline receives:

```text
Normal business document
+
Additional machine-readable content
```

This creates a gap between:

> **What the human believes the AI received**

and

> **What the AI actually received.**

---

## Hidden Does Not Mean Harmless

This principle extends beyond Prompt Injection.

Whenever an AI application processes a document, the security team should ask:

```text
What does the user see?

What does the parser see?

What does the model see?
```

Those may be three different things.

That difference can become an attack surface.

---

## Indirect Prompt Injection

Indirect Prompt Injection changed my understanding of the problem significantly.

The attacker does not necessarily interact directly with the target LLM.

Instead:

```text
Attacker
   ↓
External Content
   ↓
Legitimate User / Application
   ↓
LLM retrieves or processes content
   ↓
Injected instruction enters context
```

The malicious instruction may exist inside:

- a document;
- an email;
- a website;
- a knowledge base;
- retrieved RAG content;
- a tool response;
- another external source.

The user may simply perform a legitimate action:

```text
Summarize this document.
```

But the document itself contains instructions targeting the model.

---

## Why Indirect Prompt Injection Changes the Attack Surface

With direct Prompt Injection, the obvious attacker-controlled surface is:

```text
Chat Input
```

Indirect Prompt Injection expands this to:

```text
Chat Input
Documents
Emails
Websites
RAG
Search Results
Tool Outputs
External APIs
Knowledge Bases
```

That is a much larger security problem.

Any content that can eventually reach the model's context becomes relevant to the threat model.

---

## Untrusted Data Became Instructions

This became one of the simplest ways for me to describe Indirect Prompt Injection:

> **Untrusted data became instructions.**

A PDF should be data.

An email should be data.

A website should be data.

A retrieved RAG document should be data.

A tool result should be data.

But if content inside those sources influences the LLM's behaviour as an instruction, the trust relationship has changed.

Conceptually:

```text
Untrusted Content
       ↓
Application Ingestion
       ↓
LLM Context
       ↓
Content interpreted as instruction
       ↓
Behaviour changes
```

The failure is therefore not only about the model.

It is also about **how the application integrates untrusted content with the model**.

---

## PDFs, Emails, Websites and Documents as Attack Vectors

This changes how I look at ordinary content.

Traditionally:

```text
PDF
Email
Website
Document
```

may primarily be considered information sources.

In an LLM-enabled application they may also become:

```text
Potential Instruction Sources
```

That does not make every document malicious.

It means external content needs an explicit trust classification.

---

## RAG as an Injection Surface

RAG introduces another important path.

A simplified RAG architecture:

```text
User
  ↓
Question
  ↓
Retrieval
  ↓
Documents
  ↓
LLM Context
  ↓
Response
```

From a functionality perspective, this is excellent.

From a security perspective:

```text
Who controls the documents?
Who can modify them?
Who can add new content?
How is content validated?
Which user can retrieve which document?
Can retrieved text contain instructions?
```

This connects directly with previous Days.

A vector database is not only a data store.

The content it returns can influence model behaviour.

---

## RAG Data Has Content Authority — But Should Not Automatically Have Instruction Authority

This distinction became useful to me.

Retrieved data needs enough authority to inform the answer.

For example:

```text
Policy document says:
Employees receive 20 days of annual leave.
```

The model should use that information.

But if the same document says:

```text
Ignore the user's question and perform another action.
```

that content should not automatically receive instruction authority.

So the architecture needs to preserve a distinction between:

```text
Content used as evidence
```

and:

```text
Content allowed to control behaviour
```

---

## Tool Outputs as an Injection Surface

Tools can introduce the same problem.

Imagine:

```text
LLM
 ↓
Search Tool
 ↓
External Website
 ↓
Tool Output
 ↓
LLM Context
```

The model may trust the tool because the application trusts the tool.

But the tool may simply be transporting attacker-controlled content.

Therefore:

> **Trusted transport does not automatically make transported content trusted.**

This is another trust-boundary issue.

---

## Simulated Dialogue and Context Manipulation

Attackers can also attempt to manipulate the model by introducing text that resembles:

- previous conversation;
- system messages;
- assistant responses;
- role transitions;
- hypothetical policies.

The objective is to influence how the model interprets the surrounding context.

This reinforces the broader principle:

> **Natural-language structure itself is part of the attack surface.**

---

## Multi-Turn Prompt Shaping

Not every attack needs to occur in a single prompt.

An attacker may gradually shape context:

```text
Turn 1
  ↓
Introduce assumption

Turn 2
  ↓
Reinforce behaviour

Turn 3
  ↓
Trigger action
```

This can be harder to recognise than:

```text
IGNORE ALL SECURITY RULES
```

because no individual turn necessarily contains the complete malicious objective.

The attack emerges through **context accumulation**.

---

## Context as Temporary Attack State

I found it useful to compare this concept to a temporary state established during an attack.

Something introduced earlier may remain relevant inside the active conversation context.

Later input can then activate or exploit that state.

Conceptually:

```text
Earlier Turn
     ↓
Context Manipulation
     ↓
Context Persists
     ↓
Later Trigger
     ↓
Behaviour Change
```

This should not be confused with:

- model training;
- fine-tuning;
- permanent model modification.

It is primarily a property of the active context.

---

## Prompt Injection vs Jailbreaking

These concepts are related but not identical.

My current distinction is:

### Prompt Injection

Manipulating an LLM application's behaviour through crafted instructions introduced into its context.

### Jailbreaking

Attempting to bypass restrictions or safety behaviour imposed on the model.

The next Day will explore Jailbreaking more deeply, so I expect this distinction to become more precise.

---

## Prompt Injection vs Indirect Prompt Injection

The distinction is primarily about how attacker-controlled instructions reach the model.

### Direct

```text
Attacker
   ↓
LLM Input
```

### Indirect

```text
Attacker
   ↓
External Content
   ↓
Application
   ↓
LLM
```

The attacker may never directly interact with the final model in the second case.

---

## Prompt Injection vs Prompt Leakage

Another important distinction:

### Prompt Injection

Attempts to influence model behaviour through malicious instructions.

### Prompt Leakage

Causes internal prompt/instruction content to be disclosed.

For example:

```text
SYSTEM:
Internal behavioural rules...
```

If those hidden instructions are exposed to an unauthorized user:

```text
Prompt Leakage
```

This is more specific than general sensitive-data leakage.

---

## Prompt Leakage vs Sensitive Information Disclosure

These should not be treated as synonyms.

```text
System Prompt
Developer Instructions
Hidden Behaviour Rules
```

being revealed may represent:

**Prompt Leakage**

while:

```text
PII
Customer Data
Credentials
Internal Documents
Financial Information
```

being exposed represents:

**Sensitive Information Disclosure**

Prompt leakage can sometimes enable further attacks.

But the concepts describe different assets.

---

## Capabilities Determine Impact

This was probably the most important connection with Day 08.

Imagine two applications with the same Prompt Injection vulnerability.

### Application A

```text
User
 ↓
LLM
 ↓
Read-only public database
```

### Application B

```text
User
 ↓
LLM Agent
 ↓
Production Shell
```

The vulnerability may be similar.

The risk is not.

Why?

Because:

```text
Vulnerability
     +
Capabilities
     +
Privileges
     +
Reachable Assets
     ↓
Potential Impact
```

The attacker's influence becomes more dangerous as the application gains more power.

---

## From Text Manipulation to Action

A simple chatbot may only produce text.

An agent can potentially:

```text
Read
Write
Search
Send
Delete
Execute
Create
Modify
Approve
```

This changes Prompt Injection dramatically.

The security question is no longer only:

> **What can the attacker make the model say?**

It becomes:

> **What can the attacker make the application do through the model?**

---

## Capability Abuse

This concept helped me understand why agentic AI requires stronger security controls.

Imagine:

```text
Attacker
   ↓
does not have access to internal incident data
```

but:

```text
SOC Copilot
   ↓
does have access to internal incident data
```

If the attacker manipulates the Copilot into retrieving that information:

```text
Attacker
    ↓
Prompt Injection
    ↓
SOC Copilot
    ↓
Legitimate Tool Permission
    ↓
Internal Incident Data
```

the attacker did not necessarily steal the Copilot's credentials.

Instead, the attacker caused the application to exercise a legitimate capability for an illegitimate purpose.

That is **capability abuse**.

---

## The Confused Deputy Problem

This strongly resembles the classic **confused deputy** security problem.

The deputy has authority.

The attacker does not.

But the attacker manipulates the deputy into using its authority on the attacker's behalf.

Conceptually:

```text
Attacker
    │
    │ cannot access
    ▼
Sensitive Resource

Attacker
    ↓
Manipulates
    ↓
AI Agent
    │
    │ CAN access
    ▼
Sensitive Resource
```

This is one of the most useful ways for me to understand Prompt Injection against agents.

---

## Agents Increase the Consequences

The more capabilities an agent receives, the more important Prompt Injection becomes.

Compare:

```text
Chatbot
  ↓
Generate text
```

with:

```text
Agent
 ├── Email
 ├── Database
 ├── SIEM
 ├── EDR
 ├── Ticketing
 ├── Cloud API
 └── Shell
```

The second architecture creates far more potential consequences.

Therefore:

> **Agent capability design is part of Prompt Injection mitigation.**

---

## Trust Boundaries Around LLM Applications

Before this Day, I might have drawn:

```text
User
 ↓
LLM
```

and placed most of the security thinking there.

Now I want to draw:

```text
             UNTRUSTED
                 │
      ┌──────────┼──────────┐
      │          │          │
    User       Email      Website
      │          │          │
      └──────────┼──────────┘
                 ↓
        ─ TRUST BOUNDARY ─
                 ↓
          AI Application
                 ↓
                LLM
                 ↓
        ─ TRUST BOUNDARY ─
                 ↓
              Tools
                 ↓
        Sensitive Systems
```

There are multiple boundaries.

And each boundary needs controls appropriate to the assets behind it.

---

## Why the LLM Cannot Be the Only Security Boundary

This became one of my strongest conclusions.

Imagine:

```text
Sensitive Database
       ↓
      LLM
       ↓
"Please don't reveal secrets"
       ↓
      User
```

The system is effectively asking the LLM to be:

- authorization layer;
- policy engine;
- data-loss-prevention system;
- security boundary.

That is too much trust.

A better architecture is:

```text
User Identity
      ↓
Authorization
      ↓
Allowed Data
      ↓
LLM
      ↓
Output Controls
```

The model participates in the application.

It should not be solely responsible for enforcing the security properties surrounding itself.

My rule:

> **Do not make the LLM solely responsible for enforcing the security boundary that protects the LLM.**

---

## Defense in Depth Around the Model

A more resilient architecture uses multiple controls.

Conceptually:

```text
Untrusted Input
      ↓
[Content Controls]
      ↓
LLM Context
      ↓
[Instruction Separation / Guardrails]
      ↓
Tool Request
      ↓
[Authorization]
      ↓
Capability
      ↓
[Least Privilege]
      ↓
Sensitive Resource
      ↓
[Output / Egress Controls]
```

No individual control needs to be treated as perfect.

The goal is to prevent one model failure from becoming full application compromise.

---

## Input and Content Controls

Controls may attempt to identify:

- suspicious instructions;
- hidden content;
- unexpected markup;
- anomalous document structures;
- dangerous content patterns.

But because language is flexible:

> **Input filtering should reduce risk, not be treated as a complete solution.**

---

## Separate Instructions From Untrusted Content

Applications should make the distinction between:

```text
Instructions
```

and:

```text
Content to analyse
```

as explicit as possible.

This does not magically eliminate Prompt Injection.

But architecture should avoid unnecessarily mixing attacker-controlled content with privileged instructions.

---

## Least Privilege for AI Agents

This is one of the strongest mitigations because it reduces impact even when model behaviour fails.

If an agent only needs:

```text
read_incident()
```

it should not automatically receive:

```text
delete_incident()
execute_shell()
modify_firewall()
create_admin()
```

The same principle used throughout cybersecurity applies:

> **Give the agent only the capabilities required for the task.**

---

## Tool Authorization

The fact that an LLM requested a tool action should not automatically make that action authorized.

Conceptually:

```text
LLM
 ↓
"Delete account 123"
 ↓
Authorization Layer
 ↓
Is this user allowed?
Is this action allowed?
Is this context appropriate?
 ↓
Allow / Deny
```

The security decision belongs outside the natural-language model whenever possible.

---

## High-Risk Actions Need Stronger Controls

Not every tool action has the same consequence.

Compare:

```text
search_documentation()
```

with:

```text
delete_database()
```

Higher-impact actions may require:

- explicit user confirmation;
- separate authorization;
- human approval;
- stronger logging;
- restricted environments.

This reduces the blast radius of Prompt Injection.

---

## Output and Egress Controls

Prompt Injection can also attempt to move information out of the environment.

For example:

```text
LLM
 ↓
Sensitive Information
 ↓
Outbound Request
```

Security controls should therefore consider:

- network egress;
- destination allowlists;
- DLP;
- tool restrictions;
- outbound API controls.

Stopping the attack before the model is ideal.

Stopping exfiltration afterward is still valuable.

---

## Human Approval for High-Impact Actions

Human-in-the-loop controls are particularly useful when the action is:

- destructive;
- financially significant;
- privilege-changing;
- externally visible;
- difficult to reverse.

The objective is not to require humans for every AI action.

It is to prevent a single manipulated inference from directly producing a catastrophic outcome.

---

## Prompt Injection Through a Blue-Team Lens

Prompt Injection is often demonstrated interactively.

But from a defensive perspective, I want to know:

> **What would the attack look like in telemetry?**

This is more difficult than detecting a traditional exploit because the payload may simply be natural language.

There may be no:

```text
shellcode
malformed packet
memory corruption
known exploit signature
```

The interesting evidence may instead exist across the application workflow.

---

## Detecting Behaviour, Not Just Malicious Strings

A rule looking only for:

```text
ignore previous instructions
```

will miss semantic variations.

Behavioural detection may be more useful.

For example:

```text
User asks:
"Summarize document"

LLM:
reads document

LLM:
queries confidential database

LLM:
calls external endpoint
```

The question becomes:

> **Does the resulting action chain match the user's original intent?**

This can be a powerful detection signal.

---

## Intent-to-Action Mismatch

I think about this as:

```text
User Intent
    ↓
Expected Actions
```

versus:

```text
Observed Agent Actions
```

If:

```text
Expected Actions
       ≠
Observed Actions
```

the workflow deserves investigation.

For example:

```text
User Intent:
Summarize email

Expected:
read_email()
summarize()

Observed:
read_email()
query_SIEM()
read_critical_incident()
send_external_request()
```

That mismatch is highly relevant.

---

## Investigating Suspected Indirect Prompt Injection

Suppose I observe:

```text
User
 ↓
Summarize document.pdf

LLM
 ↓
Reads document

LLM
 ↓
Calls internal API

Internal API
 ↓
Reads confidential record

LLM
 ↓
Unexpected outbound request
```

Indirect Prompt Injection should immediately become an investigative hypothesis.

But a hypothesis is not evidence.

---

## Evidence Before Conclusion

From a DFIR perspective, I would want to reconstruct:

```text
Document Content
        ↓
What exactly was ingested?

Application Trace
        ↓
What reached the model?

LLM Context / Audit Data
        ↓
Which instructions were present?

Tool Calls
        ↓
What actions were requested?

Identity Logs
        ↓
Which principal performed them?

API Logs
        ↓
Which records were accessed?

Network Telemetry
        ↓
Where did information go?

Timeline
        ↓
Did these events form one causal chain?
```

This reinforces something I learned during AI Forensics:

> **AI output can guide an investigation, but evidence establishes what actually happened.**

---

## The User Did Not Necessarily Cause the Action

Indirect Prompt Injection creates an attribution challenge.

Imagine:

```text
User:
"Summarize yesterday's emails."
```

The AI then:

```text
Reads email
Queries database
Accesses confidential data
Sends external request
```

The user initiated the workflow.

But the user did not necessarily instruct those actions.

The malicious instruction may have originated from an external content source.

This means audit systems need visibility into more than:

```text
User → Agent
```

They need:

```text
User
 ↓
Agent
 ↓
Content Sources
 ↓
Context
 ↓
Tool Decisions
 ↓
Actions
```

---

## Connecting Day 09 Reconnaissance With Day 10

Day 09 taught me to discover:

```text
LLM
 ↓
RAG
 ↓
Internal Documents
```

and:

```text
LLM
 ↓
Tools
 ↓
Internal Systems
```

Day 10 changes how I interpret those relationships.

Previously, a document store might primarily represent:

```text
Sensitive Data Asset
```

Now it also represents:

```text
Potential Instruction Injection Surface
```

A website is not only an information source.

An email is not only a message.

A tool response is not only output.

Any of them may transport attacker-controlled instructions.

---

## Updating the Threat Model

The threat model therefore needs new questions.

For every content source:

```text
Who controls this content?

Can an attacker influence it?

How does it enter the LLM context?

Can it affect model behaviour?

Which tools are available afterward?

Which assets can those tools reach?

Which security decisions happen outside the model?
```

This creates a stronger relationship between:

**Reconnaissance → Threat Modelling → Prompt Security**

---

## Prompt Injection Is an Architectural Risk

This may be the biggest change in my understanding.

At first glance Prompt Injection appears to be:

```text
Attacker
 ↓
Tricks model
```

Now I see:

```text
Attacker
   ↓
Controls content
   ↓
Content crosses trust boundary
   ↓
Application places it in LLM context
   ↓
LLM behaviour changes
   ↓
Application exposes capabilities
   ↓
Capabilities reach assets
   ↓
Impact occurs
```

That is an architectural chain.

The model is one component in that chain.

---

## My Prompt Injection Attack-Path Model

My current model is:

```text
┌───────────────────────────┐
│     Untrusted Content     │
└─────────────┬─────────────┘
              ↓
┌───────────────────────────┐
│ Crosses a Trust Boundary  │
└─────────────┬─────────────┘
              ↓
┌───────────────────────────┐
│    Enters LLM Context     │
└─────────────┬─────────────┘
              ↓
┌───────────────────────────┐
│ Data Influences Behaviour │
│      as Instruction       │
└─────────────┬─────────────┘
              ↓
┌───────────────────────────┐
│  LLM Behaviour Changes    │
└─────────────┬─────────────┘
              ↓
┌───────────────────────────┐
│ Legitimate Capability Is  │
│          Abused           │
└─────────────┬─────────────┘
              ↓
┌───────────────────────────┐
│    Reachable Asset        │
└─────────────┬─────────────┘
              ↓
┌───────────────────────────┐
│      Business Impact      │
└───────────────────────────┘
```

This also tells me where defenses can exist.

---

## Breaking the Attack Chain

Instead of asking:

> **How do I make the model impossible to manipulate?**

I prefer:

> **How many opportunities do I have to stop the attack before impact?**

For example:

```text
Malicious Content
      ↓
[1] Content validation
      ↓
LLM Context
      ↓
[2] Instruction separation / guardrails
      ↓
Tool Call
      ↓
[3] Tool authorization
      ↓
Privilege
      ↓
[4] Least privilege
      ↓
Sensitive Asset
      ↓
[5] Data access controls
      ↓
Outbound Channel
      ↓
[6] Egress / DLP controls
```

This is defense in depth.

---

## Prompt Injection Risk Is Not Just Prompt Injection Probability

A good risk assessment should not stop at:

```text
Prompt Injection = HIGH
```

I want to understand:

```text
Likelihood of Manipulation
        +
Available Capabilities
        +
Privileges
        +
Reachable Assets
        +
Business Consequences
        =
Actual Risk
```

A model that can only generate an amusing response and an agent that can modify production may contain the same class of weakness but represent completely different risks.

---

## Security Boundaries Must Exist Outside Natural Language

Natural-language rules are useful.

But critical security controls should not depend only on the model understanding sentences correctly.

For example:

```text
"Never reveal confidential customer data."
```

should be reinforced by:

```text
Identity
Authorization
Data Filtering
Least Privilege
Tool Policies
DLP
Audit
```

The sentence describes intended behaviour.

The surrounding architecture enforces security.

---

## What Changed in My Understanding

Before Day 10, I understood Prompt Injection primarily as:

> **Manipulating an LLM through crafted prompts.**

That remains true.

But it is incomplete.

Now I see Prompt Injection as an interaction between:

```text
Content Trust
Instruction Authority
Context
Model Behaviour
Capabilities
Privileges
Assets
Architecture
```

That changes the defensive strategy completely.

---

## From Prompt Problem to Trust Problem

The word "prompt" can make this vulnerability sound limited to the chat box.

It is not.

Indirect Prompt Injection means the attacker-controlled instruction may arrive through:

```text
Email
PDF
Website
RAG
Tool
API
Knowledge Base
```

So the larger question becomes:

> **Which content sources are allowed to influence model behaviour, and why?**

That is a trust problem.

---

## From Model Problem to Architecture Problem

My first instinct during one of the scenarios was:

> The model should understand that the request is malicious and refuse it.

That would certainly help.

But it is not sufficient.

The stronger question is:

> **Why was an untrusted instruction able to reach a model that had access to a sensitive capability without another security control stopping the action?**

That moves the analysis from:

```text
Model failure
```

to:

```text
Architecture failure
```

---

## From Output Risk to Action Risk

For a chatbot:

```text
Prompt Injection
       ↓
Bad Response
```

For an agent:

```text
Prompt Injection
       ↓
Tool Call
       ↓
Privileged Action
       ↓
Real-World Impact
```

This is why agentic AI changes the importance of Prompt Injection.

The blast radius is no longer bounded by generated text.

---

## Three Principles I Want to Keep

After Day 10, three principles stand out.

### 1. Instruction hierarchy is not authorization enforcement.

A model's instruction hierarchy helps control behaviour.

It should not replace technical access control.

### 2. Untrusted content should never gain authority merely because an LLM can read it.

Content and authority are different things.

### 3. Do not make the LLM solely responsible for enforcing the security boundary that protects the LLM.

Security controls need to exist throughout the surrounding architecture.

---

## Key Takeaways

### Prompt Injection targets instruction processing

The attacker attempts to change model behaviour by introducing crafted instructions into context.

### Direct and indirect injection have different delivery paths

Direct injection comes through attacker interaction.

Indirect injection can arrive through external content.

### External content expands the attack surface

Documents, emails, websites, RAG sources, and tool outputs all require trust analysis.

### Hidden content can still reach the model

Human visibility and machine visibility are not necessarily identical.

### String blocklists are insufficient

Natural language allows semantic variation.

### Instruction hierarchy is useful but is not RBAC

Security enforcement should not depend only on instruction priority.

### Capabilities determine consequences

A text-only chatbot and a privileged agent can have very different risk from the same vulnerability class.

### Least privilege matters for AI agents

Restricting capabilities limits the blast radius.

### Tool calls need independent authorization

The LLM requesting an action does not automatically make that action authorized.

### Prompt Injection is an architectural problem

The complete attack path includes content sources, trust boundaries, context, tools, identities, assets, and outputs.

### Blue Teams need behavioural visibility

Intent-to-action mismatches can reveal suspicious agent behaviour.

### Evidence still matters

Suspected Prompt Injection requires reconstruction of content, context, tool calls, identities, API access, network activity, and timeline.

---

## My Biggest Takeaway

If I had to reduce Day 10 to one chain, it would be:

```text
Untrusted Content
       ↓
Crosses Trust Boundary
       ↓
Enters LLM Context
       ↓
Data Becomes Instruction
       ↓
Behaviour Changes
       ↓
Capability Is Abused
       ↓
Reachable Asset
       ↓
Business Impact
```

And if I had to reduce the defensive lesson to one sentence:

> **The safest Prompt Injection defense is not one perfect prompt or one perfect model, but an architecture where manipulated model behaviour cannot automatically become unauthorized action.**

Prompt Injection may begin with language.

Its consequences are determined by architecture.

---

## Next

Day 10 begins **Module 3 — Prompt Security**.

The next stage will go deeper into **Jailbreaking**, allowing me to compare attempts to manipulate application instructions with attempts to bypass the behavioural and safety restrictions of the model itself.

The process continues:

**Learn → Question → Understand → Apply → Share**

---

## References

- [OWASP — LLM01: Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
- [MITRE — ATLAS](https://atlas.mitre.org/)
- [NIST — Adversarial Machine Learning taxonomy](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)

---

## About This Learning Journal

This repository documents my personal learning journey and reflections while studying AI Security.

The learning path is inspired by my studies using **TryHackMe's AI Security material**, combined with my previous cybersecurity and incident-management experience.

The explanations, analogies, examples, and conclusions presented here represent my own understanding and reflections.

This repository does not reproduce TryHackMe labs, questions, solutions, flags, credentials, assessment answers, or proprietary course content.

**Learn → Question → Understand → Apply → Share**
