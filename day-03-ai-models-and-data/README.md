# Day 03 — AI Models & Data

<p align="center">
  <img src="../Pictures/Day3.png" alt="AI Security Learning Journal — Day 03: AI Models & Data" width="100%">
</p>

> Before trusting an AI model's output, I first need to understand what I am actually trusting underneath it.

## At a Glance

**Reading time:** about 13 minutes

This entry examines the evidence needed to trust models and datasets, including provenance, representation, privacy, validation, overfitting, and supply-chain risk.

**Key takeaways:**

- Dataset size does not guarantee dataset quality.
- Good test results do not answer every security or trust question.
- Provenance and validation must follow both data and models through their lifecycle.

## From Protecting AI to Trusting AI

Day 02 changed the way I think about unexpected AI behavior.

An unexpected output is an indicator that deserves investigation — not proof that I already know the root cause.

But that raised another question:

> **Before investigating whether a model has been attacked, what do I actually know about the model itself?**

Where did it come from?

Who developed it?

What data were used to train it?

Where did those data come from?

Were sensitive data identified and treated?

Was the dataset representative of the environment where the model will actually operate?

How was the model validated?

What happened across different versions of the model?

Those questions moved my attention further back in the AI lifecycle.

Instead of starting with:

`Model → Output`

I started thinking about:

`Data Source → Dataset → Training → Validation → Model → Deployment → Output`

And from a security perspective, every step creates a trust question.

---

## The First Question: Where Did This Model Come From?

One of the strongest lessons for me was that downloading a model and successfully running it does not automatically make it trustworthy.

Before adopting a pre-trained model, I would want to understand things such as:

- Who developed and trained it?
- What is its origin?
- What license governs how it can be used?
- What data were used?
- How large was the dataset?
- How diverse was the dataset?
- What versions existed before the current model?
- Which versions achieved the expected quality?
- What training or refinement techniques were used?
- Were provenance and pre-training checks performed?
- Were sensitive or personally identifiable data identified and treated?

This is not only documentation.

It is part of the model's **security posture**.

A model with unknown origins introduces uncertainty before I even provide it with my own data.

---

## Provenance

I started thinking about **provenance** as the history behind the model and its data.

Conceptually:

> **Who → What → Where → When → How**

For a dataset, provenance helps answer where the information came from and how it was collected, processed, transformed, and validated.

For a model, it helps establish who created it, how it was trained, what versions existed, and what happened before the artifact reached me.

This becomes particularly important when using third-party or pre-trained models.

Without provenance, I may know that the model works.

But I know much less about **why I should trust it**.

And that distinction matters.

---

## My Takeaway on Model Trust

---

## Dataset Size Does Not Mean Dataset Quality

One assumption I challenged during this part of my learning was that more data automatically means a better model.

It does not.

A dataset can contain an enormous number of records and still introduce problems if those records are:

- incorrect;
- duplicated;
- poorly labelled;
- irrelevant;
- outdated;
- manipulated;
- unbalanced;
- or not representative of the real environment.

From a security perspective, high volume without appropriate validation may simply create more noise.

My mental model became:

> **More data ≠ Better data**

Before incorporating information into training, I want to understand its origin, relevance, integrity, and quality.

---

## Diversity and Representation Matter

Dataset quality is also about **what the dataset represents**.

Imagine a cybersecurity model designed to operate across Windows and Linux environments.

The model may perform extremely well during testing.

But if most of its training examples came from Windows environments, that performance does not necessarily tell me how well it understands Linux behavior.

The model may not be broken.

It may not have been poisoned.

It may simply have insufficient examples representing that environment.

Conceptually:

`Predominantly Windows Data → Stronger Windows Representation`

while:

`Limited Linux Data → Limited Linux Representation`

This distinction matters because poor performance in one environment should not immediately be classified as an attack.

First, I need to investigate what the model actually learned from.

---

## Bias Can Be Embedded in the Data

This also helped me understand **bias** differently.

Bias does not necessarily mean that somebody intentionally manipulated the model.

It can emerge from the distribution of the training data itself.

If one architecture, behavior, population, scenario, or pattern dominates the dataset, the model may learn relationships that work better for that dominant representation.

The important security question becomes:

> **Does the dataset represent the environment where this model will actually operate?**

That requires looking beyond the final accuracy number and understanding how performance is distributed across different scenarios.

A model can appear accurate overall while still performing poorly in an important subset of the environment.

---

## Sensitive Data Changes the Risk

Dataset inspection also needs to include privacy.

Before training, I would want to know whether the dataset contains:

- personally identifiable information (PII);
- credentials;
- internal identifiers;
- confidential corporate information;
- customer information;
- other sensitive data.

If sensitive information exists, simply assuming that the model will never expose it is not a sufficient control.

The safer approach begins earlier in the lifecycle.

Before training, the data should be identified, classified, and treated according to the organization's requirements.

Depending on the use case, that may involve techniques such as:

- minimisation;
- sanitisation;
- masking;
- anonymisation or pseudonymisation;
- removal of unnecessary sensitive fields.

The important idea for me is:

> **Do not wait for the model to expose sensitive information before asking whether that information should have entered the training process in the first place.**

---

## Removing the Source File May Not Solve the Problem

This introduced another important consideration.

Suppose sensitive information was accidentally included in a training dataset.

Later, somebody discovers the mistake and deletes the original file.

That does not automatically mean the problem has disappeared from the trained model.

Training has already influenced the model's parameters.

The model is not simply reading that original file every time somebody sends a prompt.

This makes prevention extremely important.

It is much easier to validate and sanitize sensitive information **before training** than to assume that deleting the original source later completely reverses what the model learned.

---

## Pre-Training Checks Become Security Controls

This is why I started seeing dataset validation as a security control rather than only a data-science activity.

Before training, I would want checks around:

`Source`
↓
`Integrity`
↓
`Quality`
↓
`Diversity`
↓
`Sensitive Data`
↓
`Suitability`
↓
`Training`

The objective is not to guarantee that nothing can ever go wrong.

It is to reduce avoidable problems before they become embedded in the model.

And if sensitive information still reaches later stages, additional controls around access, outputs, monitoring, and guardrails provide further layers of defense.

This is **defense in depth applied to the AI lifecycle**.

---

## My Takeaway on Data

My main takeaway from this part is:

> **A model can only learn from the world we show it — and that world may be incomplete, biased, sensitive, noisy, or wrong.**

So before evaluating the model itself, I need to evaluate the data foundation underneath it.

The questions are no longer only:

**How much data do I have?**

They become:

**Where did it come from?**

**Is it trustworthy?**

**Is it representative?**

**Does it contain information that should not be there?**

**What security checks happened before training?**

The first change in my thinking on Day 03 was simple:

> **Model performance and model trust are not the same thing.**

A model can produce good results during testing and still leave important security questions unanswered.

Before asking:

**"How accurate is this model?"**

I also want to ask:

> **"Where did this model and its data come from, and what happened to them before they reached me?"**

---

## Training Is Not the Same as Validation

Another distinction that became important to me was separating **training performance** from **validation performance**.

During training, the model repeatedly learns from the training dataset.

But evaluating the model only against the same data it has already seen does not tell me enough about how well it will behave with new information.

That is why a separate validation set matters.

Conceptually:

`Training Set → Learn`

`Validation Set → Evaluate`

The validation data provide a different perspective: can the model generalize what it learned to examples that were not simply part of the learning process?

From a security perspective, this matters because excellent training results can create a false sense of confidence.

---

## Epochs and Overfitting

An **epoch** represents a complete pass through the training dataset.

Multiple epochs allow the model to repeatedly adjust what it has learned.

But more training is not automatically better.

If the model repeatedly adapts too closely to the same training examples, it can become extremely effective at recognizing those examples while becoming less effective with new data.

This helped me understand **overfitting**.

A simplified mental model is:

> **Training performance improves while generalization gets worse.**

In cybersecurity, that can be dangerous.

A detection model might perform extremely well against the attack patterns represented in its training data but fail when confronted with variations it has never seen before.

The model learned the training environment too specifically instead of learning relationships that generalize effectively.

---

## Validation Helps Reveal the Problem

This is why I now see the validation set as more than a machine-learning concept.

It is part of establishing confidence in model behavior.

If training performance continues improving while validation performance begins degrading, that difference can indicate that the model is learning the training data too specifically.

Conceptually:

`Training Accuracy ↑`

while:

`Validation Accuracy ↓`

becomes something worth investigating.

The objective is not simply:

> **Make the model perform perfectly on what it already knows.**

It is:

> **Build a model capable of performing reliably on data it has not seen before.**

And from a security perspective, that distinction is critical because attackers, environments, and behaviors will not remain identical to the training dataset.

---

## Reusing and Adapting Models Introduces New Trust Questions

Building a model entirely from scratch is not always necessary.

Organizations can start with **pre-trained models** and adapt them to a specific use case through techniques such as **fine-tuning**.

This can save significant time, data, and computational resources.

But from a security perspective, it also means inheriting something.

A pre-trained model already has a history:

`Previous Data → Previous Training → Pre-trained Model → My Fine-Tuning`

Adding my own trusted data does not automatically remove risks or unwanted behavior inherited from the original model.

That brings me back to provenance.

Before trusting a third-party model, I want to understand its origin, license, intended use, documentation, training history, and known limitations.

> **Fine-tuning adds my knowledge. It does not erase the model's history.**

---

## Synthetic Data Can Expand Coverage — But Needs Validation

Synthetic data can help when real-world examples are limited, expensive, sensitive, or difficult to collect.

In cybersecurity, this could help expand the variety of scenarios represented during training.

But generated data should not automatically be treated as trustworthy simply because it was created artificially.

Poor synthetic data could reinforce unrealistic patterns, introduce bias, or reduce the quality of what the model learns.

The security principle remains familiar:

> **The quality of the output still depends on the quality and control of the input.**

Synthetic data can increase coverage.

It does not eliminate the need for validation.

---

## Optimization Creates Trade-Offs

Models may also be optimized to reduce size, computational requirements, or inference time.

One example I explored was **pruning**, where connections considered less important can be removed from a neural network.

That can improve efficiency.

But from a cybersecurity perspective, I would not evaluate optimization only through:

`Smaller Model → Faster Model`

I also want to ask:

`What capability was lost?`

If optimization reduces the model's ability to recognize rare but security-relevant behavior, the performance improvement may introduce operational risk.

This reinforced another principle for me:

> **Optimization is a trade-off, not automatically an improvement.**

The right question is whether the resulting model still satisfies the security and business requirements for its intended use.

---

## The Model Supply Chain Matters

All of these concepts eventually brought me to a broader security concern: **supply-chain trust**.

Imagine downloading a model from an unknown source.

It works.

I fine-tune it with my organization's data.

Its performance looks acceptable.

Can I now trust it?

Not necessarily.

I may still know very little about:

- who originally created it;
- where its training data came from;
- whether the artifact was modified;
- what behavior it inherited;
- whether poisoning occurred earlier in its lifecycle;
- what limitations or biases already existed.

This feels very similar to other cybersecurity supply-chain problems.

A working artifact is not automatically a trustworthy artifact.

> **Functionality proves that something works. Provenance helps establish why I should trust it.**

That is why model origin, integrity, documentation, licensing, testing, and validation all become part of the security decision.

---

## What Changed in My Understanding

Day 03 changed the question I ask before trusting an AI model.

Previously, I might have started with:

> **Does the model work?**

Now I want to ask much more:

> **Where did it come from?**

> **What data shaped it?**

> **How was it trained and validated?**

> **What might it have inherited?**

> **What changed when it was fine-tuned or optimized?**

> **What evidence do I have that this artifact is trustworthy?**

This made me realize that AI trust cannot begin at the output.

It begins much earlier in the lifecycle.

---

## Key Takeaway

My biggest takeaway from Day 03 is:

> **A model producing good results does not automatically make it a trustworthy model.**

Trust depends on understanding the chain behind it:

`Provenance → Data → Training → Validation → Model → Deployment`

Each stage can introduce assumptions, limitations, bias, privacy concerns, security risks, or inherited behavior.

Performance matters.

But in a security context, I also need **traceability, validation, integrity, and evidence**.

---

## Closing This First Learning Block

Across these first four entries, my mental model of AI Security changed considerably.

**Day 00** asked why AI itself needs to be secured.

**Day 01** helped me understand what exists underneath the prompt.

**Day 02** explored what can go wrong and why unexpected behavior requires investigation.

**Day 03** moved even further back and asked whether the model and data should have been trusted in the first place.

The progression became:

`Understand → Threat Model → Investigate → Establish Trust`

And one principle connects all of them:

> **Do not trust something simply because it works. Understand what you are trusting and why.**

---

## References

- [NIST — AI Risk Management Framework 1.0](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10)
- [NIST — Managing Bias in Artificial Intelligence](https://www.nist.gov/publications/towards-standard-identifying-and-managing-bias-artificial-intelligence)
- [Google Research — Model Cards for Model Reporting](https://research.google/pubs/model-cards-for-model-reporting/)
- [Hugging Face — Model Cards](https://huggingface.co/docs/hub/model-cards)

---

## About This Learning Journal

This repository documents my personal learning journey and reflections while studying AI Security.

The learning path is inspired by my studies using **TryHackMe's AI Security material**, combined with my previous cybersecurity and incident-management experience.

The explanations, analogies, examples, and conclusions presented here represent my own understanding and reflections.

This repository does not reproduce TryHackMe labs, questions, solutions, or proprietary course content.

**Learn → Question → Understand → Apply → Share**
