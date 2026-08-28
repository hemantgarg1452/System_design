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
