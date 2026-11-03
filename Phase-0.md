# Phase 0: Engineering Thinking
### Why We Start Here (And Not With Databases or Servers):-

---
Most people begin System Design by learning what Kafka is, what Redis is, what a load balancer is. Then they memorize when to use them.
That is the wrong starting point. And here is why.

Imagine you gave a first-year medical student a textbook listing every drug and its dosage. They memorize it. Now give them a patient.
They cannot diagnose. They can only match symptoms to what they memorized. The moment the patient presents something slightly unusual, they are lost.
A real doctor reasons from first principles. They understand how the body works, so they can reason about any condition, even one they've never seen.

Engineering is the same. We must build your reasoning machinery first. Once that machinery exists, every technology you learn becomes a tool you can evaluate, not a fact you memorize.

---
### Chapter 1: The Engineering Mindset:-
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

### Real-Life Analogy: The Hospital:-
You run a hospital. You face a decision: should your emergency room operate as first-come, first-served, or should you triage by severity?

Option A - First Come, First Served-
- Gain: Simple. Fair in one sense. No judgment required.
- Cost: Someone with a broken finger is seen before someone with a heart attack.

Option B - Triage by Severity-
- Gain: Critical cases are handled first. Lives saved.
- Cost: Complex. Requires trained staff to judge severity. Feels unfair to someone who waited three hours with a broken finger.

Notice: both options are valid. The right choice depends on what the hospital optimizes for. A field hospital during war optimizes differently than a suburban clinic.

This is exactly how engineers think. Every architecture decision is a trade-off. The engineer's job is to understand what the system needs to optimize for, and then choose accordingly - not to find the "best" technology in some abstract sense.

---

### 1.3 - The Five Dimensions of Every System:-
Every production system can be evaluated along five dimensions. These are in constant tension with each other:

- Performance - How fast does it respond? How much load can it handle?
- Reliability - Does it work when it should? Does it stay working under failure?
- Scalability - Can it grow when demand increases? Without a complete rewrite?
- Maintainability - Can engineers understand it, change it, and debug it over time?
- Cost - What does it cost to build and operate? Both financially and in engineering effort?

The hard truth: you cannot maximize all five simultaneously. Optimizing hard for performance often hurts maintainability. Maximizing reliability often increases cost. Building for extreme scalability early often hurts simplicity.

A Staff Engineer's judgment lies in knowing which dimensions matter most for the current context - and accepting the trade-offs that come with that choice.

---

<img width="2720" height="1520" alt="five_dimensions_tension" src="https://github.com/user-attachments/assets/b4634382-60e4-4634-8c93-78879b57aa14" />
<!-- <img width="1266" height="760" alt="image" src="https://github.com/user-attachments/assets/4fc6a9e9-39dc-4465-9e10-99e14ea39326" /> -->

---

### 1.4 - How Requirements Become Technical Decisions
In a real company, systems don't start with "let's se kubernetes" They start with a conversation between a product manager and engineers. Let's watch how that unfolds.

#### The Company Workflow - How a Feature Becomes Architecture-

---

#### Product Manager to Engineering Manager-
> We need to add an approval workflow to our internal tool. When someone submits a request, it should go to their manager for approval, then to finance, then automatically update the database.

This sounds simple. But watch what a senior engineer thinks when they hear this.

---

#### Senior Engineer's internal questions (this is the mindset we're building):-
"How many approvals per day? 10? 10,000? That changes everything."
"What happens if the manager is on vacation and never approves? Does the request expire? Who gets notified?"
"What id the system crashes between the manager approving and finance receiving it? Do we lose that approval?"
"What does 'automatically update the database' mean - is it one database? Which one?"
"What if finance rejects something the manager already approved? Can a decision be reversed?"
"How do we audit who approved what and when? Compliance may require this."

NOTICE: the engineer hasn't touched a single technology yet. They're still understanding the problem. This is the right sequence.

---

### 1.5 - The Approval Management System: Version 0.1
This is the living system we will build throughout this entire course. Right now, it starts as the simplest possible thing. We'll envolve it phase by phase.
#### Today's requirement from the PM: Build a basic approval workflow syestem.
Our first engineering decision meeting:

---

> Junior Engineer: "We could use React for the frontend and Node.js for the backend, and store everything in PostgreSQL. Should we also add Kafka for the approval notifications?"

> Senior Engineer: "Stop. We don't know enough yet to make any of those decisions. What do we actually need to do first?"

> Tech Lead: "We need to understand the problem. How many users? What's the approval flow? What are the failure cases?"

> PM: "We have 50 internal users right now. A user submits a request, one manager approves it, then it's done. Maybe later we add multi-step approvals."

> Senior Engineer: "OK. 50 users. Single approver. No mention of audit logs or compliance yet. No mention of high availability. No mention of 24/7 uptime requirements. What does that tell us?"

> Tech Lead: "That tells us we do NOT need kafka. We do NOT need distributes systems. We do NOT need a microservice architecture. We need the simplest thing that works."

> Architect: "Exactly. The biggest mistake engineers make is building for scale they don't have. Complexity you introduce today has to be maintained forever. A junior engineer adds kafka because it's cool. A Staff Engineer asks: what problem are we solving that requires kafka? If there's no answer, we don't add it."

---

This conversation captures something essential: engineering restraint. The ability to not use a technology is just as important as knowing when to use it.
Here's is what our Approval Management System looks like today:

---

<img width="2720" height="1680" alt="approval_system_v0_1" src="https://github.com/user-attachments/assets/27944299-cf84-418e-953a-f21fb3c5c97d" />

---

This is your system right now. It is embarrassingly simple. And that is exactly right.

You will evolve it to handle 1 million users. But we will only add complexity when the problem demands it - not before. That discipline is what separates Staff Engineers from everyone else.

---

### Chapter 2: How Experienced Engineers Actually Think

This is something no textbook teaches, so we’ll study it directly.

#### 2.1 - First Principles vs. Analogy
There are two ways to solve engineering problems:
By Analogy: “Netflix uses microservices, so we should too.”

By First Principles: “What are our actual bottlenecks? What breaks first as we grow? What is the simplest architecture that handles our real load?”
First principles thinking is slower. It requires more mental effort. But it produces engineers (future u) who can solve new problems, not just recognize known patterns.

Every chapter in this course will train you guys in first principles reasoning on that topic.

#### 2.2 - The Engineer’s Decision Framework
When a senior engineer faces a design decision, they run through a mental checklist. Over time, this becomes instinct. We want to build this instinct in you.
Here is the framework i’ll use for every major decision throughout this course:

---

The Engineering Decision Framework

1. What problem are we actually solving? (Not what technology to use. The actual problem.)
2. What are the constraints? (Scale, budget, team size, existing systems, timeline)
3. What options exist? (At least three - never jump to the first idea)
4. What are the trade-offs of each option? (What does each gain? What does each cost?)
5. What does the future look like? (How does this decision age? What breaks at 10× scale?)
6. What do we choose, and why? (Not the “best” option - the most appropriate option for this context)
7. What metrics tell us this decision is wrong? (How will we know when to revisit it?)

---

This framework will appear, explicitly or implicitly, in every design decision we make throughout this course.

#### 2.3 - The Biggest Traps Engineers Fall Into
Understanding these traps now will help you recognize them when they appear later:

Trap 1: Premature Optimization
Building for scale you don’t have. Adding Redis before you understand if your database is even slow. Adding Kafka before you have a single queue consumer. The system you build at Day 1 should handle Day 1 load. Day 1000 load gets Day 1000 architecture.
