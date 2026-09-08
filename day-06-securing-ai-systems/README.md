# Day 06 — Securing AI Systems

<p align="center">
  <img src="../Pictures/Day6.png" alt="AI Security Learning Journal — Day 06: Securing AI Systems" width="100%">
</p>

> Securing AI means protecting the architecture, permissions, data flows, trust boundaries, and actions surrounding the model.

## At a Glance

**Reading time:** about 8 minutes

This entry moves from model-level concerns to the complete AI application architecture and the controls required around it.

**Key takeaways:**

- The model is only one component of an AI system.
- OWASP, MITRE ATLAS, and NIST AI RMF answer complementary security and governance questions.
- Least privilege, monitoring, validation, and human approval limit the consequences of model failure.

## TryHackMe AI Security Journey

Today’s room changed the way I think about AI security.

Until now, much of my focus had been on the model itself: adversarial inputs, bias, data poisoning, leakage, and how machine learning systems behave.

Day 6 moved the discussion one layer higher.

The main lesson was simple:

> Securing AI is not only about securing the model. It is about securing the entire architecture around it.

A production AI system can include the user interface, API gateway, orchestration layer, prompt construction, the LLM itself, tools, external data sources, logging systems, output processing, and vector databases.

Every connection between these components can become a trust boundary — and every trust boundary can become an attack surface.

---

## From Traditional Applications to AI Systems

Traditional web applications normally expect relatively structured input.

A field may expect:

* a username;
* a date;
* an ID;
* a number;
* a predefined option.

Security controls can validate expected formats and reject unexpected content.

AI applications introduce something fundamentally different:

**free-form natural language.**

A user can send almost anything to the model.

This means security teams need to consider not only traditional injection and validation problems, but also questions such as:

* Is the user trying to discover internal instructions?
* Is the request attempting to manipulate the model?
* Could the request trigger an external tool?
* Is the user consuming an unreasonable amount of tokens or compute?
* Does the model have permissions it does not actually need?
* Could sensitive information be exposed in the response?
* What information is being retained in logs?

This significantly increases the security surface.

---

## AI Architecture and Trust Boundaries

The exercise introduced five important trust boundaries:

1. **User-to-System**
2. **System-to-LLM**
3. **LLM-to-Tools**
4. **System-to-External-Data**
5. **System-to-User**

One distinction that became clearer to me during the review is the difference between a **component** and a **boundary**.

For example:

**Prompt Construction** is a layer responsible for combining the system prompt, user input, and retrieved context.

**System-to-LLM**, on the other hand, is the trust boundary crossed when that constructed prompt is sent to the model.

Understanding this distinction helps when threat modelling an AI application.

---

## Three Frameworks, Three Different Questions

Another important concept was understanding how OWASP, MITRE, and NIST complement one another.

### OWASP LLM Top 10 — WHAT?

OWASP helps classify important vulnerabilities and risks affecting LLM applications.

### MITRE ATLAS — HOW?

MITRE ATLAS looks at adversary tactics and techniques used against AI and machine learning systems.

### NIST AI RMF — HOW DO WE GOVERN THE RISK?

NIST AI RMF provides an organisational approach around:

* Govern
* Map
* Measure
* Manage

I found this relationship particularly useful:

```text
OWASP → What can go wrong?
MITRE ATLAS → How can an attacker do it?
NIST AI RMF → How should the organisation govern and manage the risk?
```

---

## Five System-Level AI Risks

The room focused on five OWASP categories.

### LLM10 — Unbounded Consumption

An attacker can abuse token usage, request size, concurrency, or computational resources.

Possible impacts include:

* denial of service;
* degraded performance;
* infrastructure exhaustion;
* unexpected cloud/API costs.

Relevant controls include rate limiting, request size limits, quotas, and cost thresholds.

---

### LLM07 — System Prompt Leakage

System prompts should never be treated as secure storage.

If internal URLs, credentials, architecture details, or sensitive instructions are included in a prompt, disclosure can reveal valuable information about the environment.

My takeaway:

**Design system prompts assuming that one day someone may be able to inspect their contents.**

Secrets do not belong there.

---

### LLM05 — Improper Output Handling

LLM output should be considered untrusted.

A model may generate:

* SQL;
* shell commands;
* HTML;
* URLs;
* structured instructions.

If another system directly executes that content, the LLM can become part of an injection chain.

A key distinction I learned is that an incorrect model response alone is not necessarily Improper Output Handling.

The dangerous situation appears when generated output reaches another component and is processed or executed unsafely.

---

### LLM06 — Excessive Agency

This became one of the most interesting risks for me.

Excessive Agency can appear through:

* **Excessive Functionality**
* **Excessive Permissions**
* **Excessive Autonomy**

The TryAssist example demonstrated all three.

A code review assistant should not automatically require administrator access to a production database, the ability to deploy applications, modify repositories, communicate across private channels, and independently merge code.

AI does not remove the principle of least privilege.

It makes least privilege even more important.

---

### LLM02 — Sensitive Information Disclosure

Sensitive data can leak even when there is no attacker.

Developers may paste:

* credentials;
* SSH keys;
* API tokens;
* internal source code;
* personal information;
* architectural information.

If conversations are stored without filtering, encryption, proper access control, or retention policies, the application itself becomes a source of exposure.

This was an important reminder:

**A security incident does not always require exploitation. Poor architecture can leak information while functioning exactly as designed.**

---

## Auditing TryAssist

The practical exercise showed several high-risk architectural decisions.

The system had capabilities far beyond what a code review assistant should normally require.

Examples included:

* repository read/write access;
* automatic pull request merging;
* deployment capabilities;
* production database administrator privileges;
* Slack read/write access;
* conversation logging without PII filtering.

The database example was particularly important.

The AI operated using an administrative database role capable of reading, inserting, modifying, deleting, creating, and dropping objects.

For a code review assistant, this violates least privilege dramatically.

---

## Read-Only Does Not Mean Risk-Free

One point that became clearer to me during my review was that changing database access from administrator privileges to `SELECT` would significantly reduce risk — but would not eliminate it.

For example, an AI system with read-only access might still expose:

* employee salaries;
* customer information;
* financial information;
* credentials stored incorrectly;
* sensitive business records.

Therefore:

```text
Read-only → reduces integrity/destructive risk
Read-only ≠ removes confidentiality risk
```

Least privilege needs to go deeper than simply changing WRITE access to READ.

The system should ideally access only the specific tables, views, fields, APIs, and data required for its function.

---

## Human-in-the-Loop

TryAssist automatically merged pull requests after approving them.

There was no independent human approval gate.

That is an example of **Excessive Autonomy**.

A safer workflow would look more like:

```text
AI Review
    ↓
Recommendation
    ↓
Human Validation
    ↓
Approved Action
```

rather than:

```text
AI Review
    ↓
AI Decision
    ↓
Automatic Action
```

For operations that modify state, deploy code, send communications, delete data, or affect production, human approval can be an important security boundary.

---

## Defence in Depth for AI

One of my main takeaways from Day 6 was how traditional defence-in-depth translates into AI architectures.

Instead of relying on a single control:

```text
User
 ↓
Input Validation
 ↓
Prompt Construction
 ↓
LLM
 ↓
Output Validation
 ↓
Least-Privilege Tool
 ↓
Human Approval
 ↓
Action
```

Every boundary should assume that the previous security control might eventually fail.

For example, even if prompt injection detection exists, tools should still operate with limited permissions.

If the injection detector fails, the tool boundary can still prevent the compromise from progressing.

That is real defence in depth.

---

## Monitoring AI Systems

AI monitoring also introduces additional signals beyond traditional infrastructure monitoring.

Important examples include:

* unusual request patterns;
* token consumption;
* abnormal tool invocations;
* unexpected response behaviour;
* system prompt extraction attempts;
* sudden increases in cost.

Monitoring only CPU, memory, HTTP errors, and availability is no longer sufficient.

We also need visibility into how the AI is being used and how it is interacting with other systems.

---

## MLSecOps

MLSecOps connects security with the entire machine learning lifecycle.

I see it as applying a security-by-design and shift-left mindset to AI and ML:

```text
Design
 ↓
Development
 ↓
Testing
 ↓
Deployment
 ↓
Monitoring
 ↓
Incident Response
```

Security should not be something added after the model reaches production.

It needs to be considered throughout the lifecycle.

---

## My Main Takeaway

The biggest lesson from Day 6 was that an AI application can be perfectly functional and still be dangerously insecure.

A model connected to excessive permissions, poorly protected data, unrestricted tools, unsafe output handling, and inadequate monitoring can turn a simple AI assistant into a path toward critical infrastructure.

The model is only one part of the problem.

**AI security means protecting the architecture, the permissions, the data flows, the trust boundaries, and the actions surrounding the model.**

---

## Concepts Reinforced Today

* AI system architecture
* Trust boundaries
* Prompt Construction
* OWASP LLM Top 10
* MITRE ATLAS
* NIST AI RMF
* LLM02 — Sensitive Information Disclosure
* LLM05 — Improper Output Handling
* LLM06 — Excessive Agency
* LLM07 — System Prompt Leakage
* LLM10 — Unbounded Consumption
* Least Privilege
* Human-in-the-Loop
* Defence in Depth
* Input and Output Validation
* AI Monitoring and Observability
* MLSecOps

---

## References

- [OWASP — Top 10 for LLM and Generative AI Applications](https://genai.owasp.org/llm-top-10/)
- [MITRE — ATLAS](https://atlas.mitre.org/)
- [NIST — AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST — AI RMF Playbook](https://www.nist.gov/itl/ai-risk-management-framework/nist-ai-rmf-playbook)

---

**Day 6 completed. ✅**
