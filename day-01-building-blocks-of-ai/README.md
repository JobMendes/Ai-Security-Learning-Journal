# Day 01 — Building Blocks of AI

<p align="center">
  <img src="../Pictures/Day1.png" alt="AI Security Learning Journal — Day 01: Building Blocks of AI" width="100%">
</p>

> Understanding what happens underneath the prompt before trying to secure the model.

## At a Glance

**Reading time:** about 9 minutes

This entry builds a practical mental model of AI, machine learning, deep learning, neural networks, and LLMs before connecting each layer to security.

**Key takeaways:**

- An LLM generates predictions; it does not simply retrieve a stored answer.
- Data, training, context, inference, and feedback create different security questions.
- Understanding the system beneath the interface reveals where trust can fail.

## From Using AI to Understanding AI

On Day 00, I started with a security question:

**Why should I trust an AI system?**

But before going deeper into AI Security, I realized that I needed to understand what exists underneath the interface.

When we interact with an AI assistant, the experience looks simple:

`Input → AI → Output`

But that simplicity hides several layers of technology.

A useful starting point for my understanding became:

`Artificial Intelligence`
↓
`Machine Learning`
↓
`Deep Learning`
↓
`Neural Networks`
↓
`Large Language Models`

These concepts are related, but they are not interchangeable.

Understanding those relationships gave me a much better foundation for understanding where AI security problems can eventually appear.

---

## Artificial Intelligence

I started thinking about **Artificial Intelligence (AI)** as the broader concept.

AI describes systems designed to perform tasks that normally require some form of human intelligence, such as recognizing patterns, interpreting information, making predictions, or generating responses.

Machine Learning exists inside this broader field.

So instead of thinking:

`AI = Machine Learning`

I started thinking:

`AI → Machine Learning`

AI is the broader field.

Machine Learning is one way of building intelligent behavior.

---

## Machine Learning

Traditional programming often follows a structure such as:

`Rules + Data → Result`

The developer explicitly defines the logic that should be followed.

Machine Learning changes this relationship.

Instead of manually defining every possible rule, we provide data and allow the system to learn patterns that can later be used to make predictions or classifications.

Conceptually:

`Training Data → Learning → Model`

Then:

`New Data → Model → Prediction`

This distinction became important for me from a security perspective.

If behavior is influenced by what the model learns from data, then the **quality, origin, and integrity of those data become security concerns as well.**

---

## Supervised Learning

One approach I studied was **Supervised Learning**.

In this case, the training data contain labels or known outcomes.

A simple cybersecurity example could be:

`Email → Phishing`

`Email → Legitimate`

The model receives examples where the expected classification is already known and learns relationships that may help classify future examples.

What made this particularly intuitive for me was thinking about SOC events.

If I already have historical events that analysts classified correctly, those classifications can become part of the learning process.

The important idea is:

**The model is learning from examples where some form of expected answer is already available.**

---

## Unsupervised Learning

Unsupervised Learning approaches the problem differently.

Instead of providing the model with explicit labels for every example, the system attempts to identify patterns, structures, similarities, or groups within the data.

From a cybersecurity perspective, this immediately reminded me of anomaly detection.

Instead of telling the system:

`This exact event = malicious`

we may be interested in discovering:

`This behavior is significantly different from the patterns normally observed.`

That does not automatically mean the behavior is malicious.

It means it may deserve investigation.

This distinction connects strongly with a principle I already use in cybersecurity:

> **An anomaly is an indicator, not automatically a root cause.**

---

## Semi-Supervised Learning

Semi-Supervised Learning sits between supervised and unsupervised approaches.

Instead of having labels for the entire dataset, we may have a smaller amount of labelled data together with a much larger amount of unlabelled data.

Conceptually:

`Small labelled dataset + Large unlabelled dataset → Learning`

This made sense to me when thinking about a SOC environment.

Security teams may have a smaller number of events that analysts already investigated and classified, while a much larger volume of historical events remains unlabelled.

Manually classifying everything may be expensive and time-consuming.

Semi-supervised learning provides a way to use the smaller amount of known information to help guide learning across the larger dataset.

The distinction I started using to remember the approaches is:

- **Supervised:** I already have expected answers for the training examples.
- **Unsupervised:** I do not provide those answers; the system looks for structure and patterns.
- **Semi-supervised:** I have some labelled examples helping guide learning across many unlabelled examples.

---

## Reinforcement Learning

Another learning approach introduced a different idea:

**feedback based on actions and outcomes.**

Instead of simply receiving labeled examples, an agent can perform actions and receive feedback depending on the result.

Conceptually:

`State → Action → Feedback → Adjustment`

This helped me understand why feedback can influence future behavior.

A positive result can reinforce a behavior.

A negative result can discourage it.

The interesting part from a security perspective is that feedback itself becomes important.

If the feedback mechanism is incorrect, manipulated, or poorly designed, the behavior being reinforced may also become undesirable.

---

## Deep Learning

Deep Learning is a subset of Machine Learning that uses neural networks with multiple layers.

This is where my mental model started becoming more visual.

Instead of thinking about a single decision, I started thinking about information being progressively processed through multiple layers.

Conceptually:

`Input Layer → Hidden Layers → Output Layer`

The **input layer** receives information.

The **hidden layers** progressively process relationships and representations.

The **output layer** produces the resulting prediction or representation.

---

## Thinking About Neural Networks

One analogy that helped me understand neural networks was image recognition.

Imagine that the input is an image.

At a basic level, the system receives numerical information representing pixels.

Earlier processing may identify simple characteristics.

Deeper processing can combine those characteristics into increasingly complex patterns.

Conceptually:

`Pixels`
↓
`Edges / Shapes`
↓
`Features`
↓
`Higher-level representation`
↓
`Prediction`

For example, combinations of pixels may contribute to detecting lines, curves, shapes, and eventually characteristics associated with larger objects.

The important realization for me was that deeper layers can build increasingly complex representations from simpler information.

---

## Input, Hidden Layers, and Output

I also started applying this concept to cybersecurity.

Imagine an email being analyzed.

### Input

The model may receive information such as:

- sender;
- date and time;
- message content;
- request being made;
- other available characteristics.

### Hidden processing

The system may analyze relationships within those inputs and identify patterns learned during training.

### Output

The final result could be something such as:

`Potential phishing`

The key point is that the output is the result of processing learned relationships.

It is not simply a hardcoded `IF/ELSE` rule for every possible email.

That distinction becomes extremely important when we later start discussing how AI systems can fail or be manipulated.

---

## Large Language Models

Large Language Models introduced another important idea for me:

**prediction.**

An LLM does not simply retrieve a complete predefined sentence every time we ask a question.

During pre-training, the model processes enormous amounts of text and repeatedly attempts to predict what word should come next.

A simplified representation is:

`Context → Possible next words → Prediction`

Then the process continues:

`Context + predicted word → New context → Next prediction`

repeatedly building the response.

During training, incorrect predictions are compared with the expected result and the model's parameters are adjusted through **backpropagation**, making better predictions more likely in similar situations later.

This helped me move away from the idea that an LLM is simply searching a huge database for an existing answer.

Instead, it generates a response through a sequence of predictions based on relationships learned during training and the context currently available.

---

## Context Matters

Probability alone is not enough to explain useful language generation.

Context strongly influences which continuation makes sense.

The same word can have different meanings depending on the surrounding information.

For example:

`bank`

could refer to a financial institution or something completely different depending on the context.

The model needs the surrounding information to determine which relationships are more relevant to the current request.

That gave me another important insight:

**The quality of an AI response depends not only on what the model learned, but also on the context it receives when making an inference.**

---

## Training and Refinement

Another important realization was that a useful LLM is not simply exposed to a large amount of text and immediately ready for production.

Training and refinement involve multiple processes.

At a high level, I started thinking about the lifecycle as:

`Large-scale data`
↓
`Pre-training`
↓
`Model`
↓
`Refinement / Feedback`
↓
`More useful behavior`

Human feedback can also contribute to aligning model behavior with expected outcomes.

This helped me understand that the final behavior we see in an AI assistant is the result of much more than simply collecting a large dataset.

---

## Why This Matters for Security

This was the connection I was looking for.

If an AI system depends on:

- training data;
- learned patterns;
- model architecture;
- context;
- feedback;
- probabilities;
- and later refinement;

then security cannot focus only on the final output.

Each part can influence behavior.

That means future security questions naturally appear:

**What if the training data are manipulated?**

**What if sensitive data are included?**

**What if the context is maliciously constructed?**

**What if feedback reinforces undesirable behavior?**

**What if the model performs extremely well on known examples but poorly on new ones?**

These questions lead directly into AI Security.

---

## What Changed in My Understanding

Before studying these building blocks, I mostly saw AI through the interface:

`Prompt → Response`

Now my mental model is closer to:

`Data`
↓
`Training`
↓
`Learned relationships`
↓
`Model`
↓
`Context`
↓
`Inference`
↓
`Output`

That difference matters.

If I only understand the output, I can evaluate whether I like the answer.

If I understand more of the process behind the output, I can start asking **why the system behaved that way and where security problems might originate.**

And that is much closer to the mindset required for AI Security.

---

## Key Takeaway

My biggest takeaway from Day 01 is:

> **Before trying to secure an AI system, I need to understand what is happening underneath the prompt.**

AI is the broader field.

Machine Learning allows systems to learn patterns from data.

Deep Learning uses multi-layer neural networks.

Neural networks progressively transform information into representations and outputs.

Large Language Models use learned relationships and context to generate language through successive predictions.

And every additional layer of understanding gives me another place to ask:

**What could go wrong here?**

---

## Next

**Day 02 — AI Security Threats**

Now that I have a better mental model of what exists underneath an AI system, the next question becomes much more interesting:

> **How can these systems be attacked, manipulated, misused, or behave in ways we did not expect?**

That is where the security discussion really begins.

---

## References

- [NIST — Adversarial Machine Learning: A Taxonomy and Terminology of Attacks and Mitigations](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)
- [Google Research — Attention Is All You Need](https://research.google/pubs/attention-is-all-you-need/)
- [Hugging Face — Natural Language Processing course](https://huggingface.co/learn/nlp-course/)

---

## About This Learning Journal

This repository documents my personal learning journey and reflections while studying AI Security.

The learning path is inspired by my studies using **TryHackMe's AI Security material**, combined with my previous cybersecurity and incident-management experience.

The explanations, analogies, examples, and conclusions presented here represent my own understanding and reflections.

This repository does not reproduce TryHackMe labs, questions, solutions, or proprietary course content.

**Learn → Question → Understand → Apply → Share**
