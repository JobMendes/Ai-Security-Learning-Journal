# Day 00 — Why AI Security?

<p align="center">
  <img src="../Pictures/Day0.png" alt="AI Security Learning Journal — Day 00: Why AI Security?" width="100%">
</p>

> Part of my **AI Security Learning Journal**, documenting my personal understanding, questions, mistakes, and cybersecurity reflections while studying AI Security.

## At a Glance

**Reading time:** about 6 minutes

In this entry, I explain why using AI is different from understanding AI Security, where AI creates value for security teams, and why trust must be investigated rather than assumed.

**Key takeaways:**

- AI can support security work while also becoming part of the attack surface.
- A confident output is not automatically a trustworthy one.
- Securing AI begins by asking what can fail, who can influence it, and how its decisions can be validated.

## Why I Started This Journey

Artificial Intelligence is becoming part of almost everything we do in technology.

We use AI to automate repetitive tasks, analyze large volumes of information, summarize complex data, assist investigations, generate content, and accelerate decisions.

In cybersecurity, the possibilities are even more interesting.

A SOC analyst may need to process thousands or millions of events. AI can help identify patterns, correlate information, prioritize alerts, and reduce the amount of repetitive work performed manually.

This can potentially mean faster detection and investigation, less analyst fatigue, and ultimately less time between identifying a threat and responding to it.

But while learning more about AI, I realized something important:

**Using AI and understanding how to secure AI are two very different things.**

---

## AI Is More Than an Interface

For many people, the experience with AI starts with a prompt:

**Question → AI → Answer**

And from the user's perspective, that may be enough.

But from a cybersecurity perspective, I started asking different questions.

Where did this model come from?

Who trained it?

What data was used?

Can I trust those data sources?

Could sensitive information have been included?

Could the training data have been manipulated?

What happens if the model has access to corporate systems?

Who should be allowed to access those systems through the AI?

Can someone manipulate the model into behaving differently from what was intended?

What happens when the model confidently produces the wrong answer?

Those questions changed the way I started looking at AI.

Instead of seeing only:

`Prompt → Model → Response`

I started thinking about a much larger chain:

`Data → Training → Model → Access → Input → Decision → Action`

Every part of that chain can potentially introduce risk.

---

## AI as a Cybersecurity Tool

One of the reasons AI is so interesting for cybersecurity is scale.

Humans have limited time and attention.

Machines can process large amounts of information continuously.

In a defensive environment, AI can assist with tasks such as identifying patterns, helping classify events, supporting investigations, and reducing repetitive analytical work.

For me, one of the most valuable possibilities is not replacing the security analyst.

It is helping the analyst spend less time processing noise and more time investigating what actually matters.

But that creates another question:

**What happens when the security tool itself becomes something we need to secure?**

---

## AI Can Also Become Part of the Attack Surface

The same capabilities that make AI useful can introduce new security concerns.

If a model processes sensitive corporate information, incorrect access controls could expose information to people who should never receive it.

If the data used to build or refine a model are manipulated, its future behavior may also be affected.

If attackers can manipulate how an AI system interprets instructions, the system may behave differently from what its developers intended.

And AI can also help attackers improve the speed, scale, and adaptability of traditional attacks.

This creates an interesting security relationship:

**AI can help defend the organization.**

**AI itself needs to be defended.**

**AI can also help an attacker.**

Understanding all three perspectives is becoming increasingly important.

---

## Trust Became the Central Question

One idea started appearing repeatedly while I was studying:

**Trust.**

When an AI system produces an answer, classification, or recommendation, why should I trust it?

A high-confidence output does not automatically mean a correct output.

A model may behave unexpectedly because of problems involving its data, training, environment, configuration, or the way it is being used.

That means AI output should not automatically be treated as ground truth.

This is particularly important in cybersecurity.

Imagine an AI system recommending:

> Threat detected. Isolate the production database.

If that recommendation is wrong, blindly executing it could create a business incident instead of preventing one.

This led me to an important principle:

**AI can accelerate detection and analysis, but critical decisions still require appropriate validation and human oversight.**

The higher the business impact of a wrong AI decision, the more important that validation becomes.

---

## Thinking Like a Security Analyst

One connection with traditional cybersecurity became very clear to me.

If monitoring shows:

`CPU usage = 100%`

that does not automatically mean:

`DDoS attack`

It is an indicator.

We investigate before determining the root cause.

I believe AI security requires the same mindset.

If model performance suddenly deteriorates, that does not automatically prove that the model was attacked, poisoned, or became outdated.

It tells us that something changed and needs investigation.

A simple way I started thinking about this is:

> **Monitoring finds the change. Investigation finds the cause. Remediation addresses the cause.**

AI does not eliminate the fundamentals of cybersecurity investigation.

In many cases, it makes those fundamentals even more important.

---

## My Starting Perspective

Before beginning this learning journey, much of my interest in AI was focused on what it could do:

- automate repetitive activities;
- analyze large amounts of information;
- improve productivity;
- assist cybersecurity analysts;
- accelerate detection and investigation.

I still believe strongly in those benefits.

But my perspective is becoming broader.

Now I also want to understand:

- where models come from;
- how their data are handled;
- how models are trained and validated;
- what can influence their behavior;
- how access should be controlled;
- how AI systems should be monitored;
- where human oversight remains necessary;
- and how attackers may target or use AI systems.

For me, that is where **AI Security** begins.

Not by assuming that AI is trustworthy or untrustworthy.

But by understanding **what we are trusting, why we are trusting it, and how we can verify that trust over time.**

---

## Key Takeaway

My biggest takeaway from Day 00 is simple:

> **Before learning how to secure AI, I first need to understand what I am actually trying to protect.**

AI is not only a chatbot or a model responding to prompts.

It is part of a larger ecosystem involving data, training, models, infrastructure, users, permissions, outputs, decisions, and sometimes automated actions.

Each component changes the security question.

And that is what I want to explore throughout this learning journal.

---

## Next

**Day 01 — Building Blocks of AI**

Before going deeper into attacks and defenses, I need to understand what exists underneath an AI system:

**AI → Machine Learning → Deep Learning → Neural Networks → Large Language Models**

Because protecting something starts with understanding how it works.

---

## References

- [NIST — AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [MITRE — ATLAS](https://atlas.mitre.org/)
- [OWASP — Machine Learning Security Top Ten](https://owasp.org/www-project-machine-learning-security-top-10/)

---

## About This Learning Journal

This repository documents my personal learning journey and reflections while studying AI Security.

The learning path is inspired by my studies using **TryHackMe's AI Security material**, combined with my previous cybersecurity and incident-management experience.

The explanations, analogies, examples, and conclusions presented here represent my own understanding and reflections.

This repository does not reproduce TryHackMe labs, questions, solutions, or proprietary course content.

The objective is simple:

**Learn → Question → Understand → Apply → Share**
