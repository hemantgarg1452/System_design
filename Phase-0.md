# Phase 0: Engineering Thinking
### Why We Start Here (And Not With Databases or Servers)

---
Most people begin System Design by learning what Kafka is, what Redis is, what a load balancer is. Then they memorize when to use them.
That is the wrong starting point. And here is why.

Imagine you gave a first-year medical student a textbook listing every drug and its dosage. They memorize it. Now give them a patient.
They cannot diagnose. They can only match symptoms to what they memorized. The moment the patient presents something slightly unusual, they are lost.
A real doctor reasons from first principles. They understand how the body works, so they can reason about any condition, even one they've never seen.

Engineering is the same. We must build your reasoning machinery first. Once that machinery exists, every technology you learn becomes a tool you can evaluate, not a fact you memorize.

---
### Chapter 1: The Engineering Mindset
1.1 - The Problem That Always Exists
Before any system is built, a problem exists. That problem is real. It affects real users, costs real money, or represents real risk.

Here is what separates junior engineers from senior engineers:
A junior engineer hears a problem and immediately thinks about solutions. Which technology? Which database? Which framework?

A senior engineer hears a problem and first asks: What is actually happening here? Do I fully understand the problem? Am I solving the right thing?
This instinct - to slow down before speeding up - is what we will build throughout this course.

1.2 - The Most Important Question in Engineering
Every decision in System Design comes back to one fundamental question:
"What are we optimizing for, and at the cost of what?"
Nothing in engineering is free. Every decision that gains something also loses something. This is not a limitation of technology. This is the nature of reality.

Let's make this concrete with a real example.

---

### Real-Life Analogy: The Hospital
You run a hospital. You face a decision: should your emergency room operate as first-come, first-served, or should you triage by severity?

Option A - First Come, First Served:
- Gain: Simple. Fair in one sense. No judgment required.
- Cost: Someone with a broken finger is seen before someone with a heart attack.

Option B - Triage by Severity-
- Gain: Critical cases are handled first. Lives saved.
- Cost: Complex. Requires trained staff to judge severity. Feels unfair to someone who waited three hours with a broken finger.

Notice: both options are valid. The right choice depends on what the hospital optimizes for. A field hospital during war optimizes differently than a suburban clinic.

This is exactly how engineers think. Every architecture decision is a trade-off. The engineer's job is to understand what the system needs to optimize for, and then choose accordingly - not to find the "best" technology in some abstract sense.

---

### 1.3 - The Five Dimensions of Every System
Every production system can be evaluated along five dimensions. These are in constant tension with each other:

- Performance - How fast does it respond? How much load can it handle?
- Reliability - Does it work when it should? Does it stay working under failure?
- Scalability - Can it grow when demand increases? Without a complete rewrite?
- Maintainability - Can engineers understand it, change it, and debug it over time?
- Cost - What does it cost to build and operate? Both financially and in engineering effort?

The hard truth: you cannot maximize all five simultaneously. Optimizing hard for performance often hurts maintainability. Maximizing reliability often increases cost. Building for extreme scalability early often hurts simplicity.

A Staff Engineer's judgment lies in knowing which dimensions matter most for the current context - and accepting the trade-offs that come with that choice.
