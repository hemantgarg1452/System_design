# Exact Execution Plan
### Start this. Do not change it.

---

## 1. Topic Buckets

### Study Separately First
These need conceptual foundation before you touch code. You cannot build your way into understanding them — you'll just write broken code confidently.

- Java concurrency (threads, volatile, synchronized, locks, executors, CompletableFuture)
- JVM internals (heap/stack/metaspace, GC basics, String pool)
- HashMap internals + equals/hashCode contract
- Spring Security filter chain (how authentication + authorization actually flows)
- JPA persistence context, dirty checking, flush modes, entity lifecycle
- @Transactional internals (propagation, self-call trap, rollback rules)
- SQL indexes (B-tree, when indexes aren't used, EXPLAIN plan reading)
- Transaction isolation levels (what dirty read / phantom read actually mean)
- CAP theorem + eventual consistency (understand before you design any distributed system)
- Kafka core model (topics, partitions, consumer groups, offset commit, at-least-once)

---

### Learn Alongside Projects
These topics only make real sense when you hit the actual problem while building. Reading about them first wastes time.

- Redis (rate limiting, distributed lock, cache-aside) → add to Webhook project when you reach V3
- Kafka implementation → implement as you build Webhook V2
- Idempotency in real APIs → implement in Webhook V1
- Circuit breaker pattern → implement in Webhook V3 using Resilience4j
- Retry with exponential backoff → Webhook V1
- Dead letter queue → Webhook V2
- Spring ApplicationEvent (async event publishing) → add to Approval System
- Testcontainers → write 3–4 tests in Approval System this week
- Structured logging + correlation IDs → Webhook V5
- Docker multi-stage builds → when packaging Webhook for deployment
- GitHub Actions CI → wire it up when Webhook V1 is done

---

### Interview-Focused Preparation
These don't need deep implementation. You need fluent verbal answers, cross-question chains, and scenario reasoning.

- REST design principles (idempotency, status codes, versioning, pagination)
- Microservices tradeoffs (when to use, service boundaries, communication, failure patterns)
- OAuth2/OIDC conceptual flow (how it differs from JWT-only)
- gRPC (what it is, when you'd choose it over REST)
- HLD patterns (load balancing, caching strategies, sharding, rate limiting, API gateway)
- LLD + SOLID (practiced through timed design sessions, not reading)
- AWS basics (EC2, RDS, S3, IAM — enough to explain, not to architect)
- Spring Boot auto-configuration (how it works conceptually)
- Testing strategy questions (unit vs integration vs e2e, what to mock and what not to)
- Project deep-dive chains (both projects — every decision, every tradeoff)

---

## 2. Project Decision

**Right now: Approval System for 1 week. Then Webhook System as your main project.**

### Approval System — This Week Only
Make these exact changes. Nothing else.
- Add refresh token endpoint + in-memory token blacklist for revocation
- Add global exception handler (RFC 7807 problem detail format)
- Write 3 integration tests using Testcontainers (auth flow, approval flow, rejection flow)
- Fix JWT_SECRET to fail startup if not set or under 32 characters

After this week, Approval System is done. You discuss it in interviews. You don't keep building it.

### Webhook System — Your Main Project (Weeks 2 onward)
What you learn through it:
- V1: Idempotency, HTTP client (WebClient), retry logic, DB schema for delivery tracking
- V2: Kafka producers/consumers, offset commit, DLQ, at-least-once semantics
- V3: Redis rate limiting, distributed locking, circuit breaker
- V4: Multi-tenancy, Kafka partition strategy, quota enforcement
- V5: Metrics (Micrometer), structured logging, correlation IDs, custom health checks

**Do NOT add to Webhook:**
- Any UI
- RabbitMQ (you have Kafka)
- gRPC endpoints
- OAuth2 server
- Kubernetes config
- More than MySQL + Kafka + Redis as infrastructure

### Time Split
- Weeks 1–5 (Phase 1): 60% study / 30% project / 10% interview questions
- Weeks 6–14 (Phase 2): 40% project / 35% study / 25% interview questions
- Weeks 15–20 (Phase 3): 30% project polish / 30% interview questions / 40% HLD+LLD practice

---

## 3. Interview Question Timing and Format

**Start interview questions from Day 3, not after finishing a topic.**

The rule: study a concept for 2 days → start its question chain on Day 3 → continue both in parallel.

You do not finish learning before practicing questions. By the time you "finish" learning a topic, you've forgotten the early parts. Questions drive retention.

---

### Exact Format for Every Topic

**Level 1 — Basic:** What is it? What problem does it solve?
**Level 2 — Why:** Why does it work this way? What would break without it?
**Level 3 — Internals:** What happens inside? Trace the execution.
**Level 4 — Practical Scenario:** "In your project, how did you use this?"
**Level 5 — Debugging:** "This is failing. Walk me through how you'd debug it."
**Level 6 — Cross-Question Chain:** Answer L1 → interviewer goes deeper → you follow.

---

### Concrete Example: @Transactional

**L1 — Basic:**
Q: What does @Transactional do?
A: It wraps a method in a database transaction. If the method completes normally, the transaction commits. If a RuntimeException is thrown, it rolls back.

**L2 — Why:**
Q: Why can't you just manage transactions manually with JDBC?
A: You can, but it pollutes business logic with infrastructure concerns. Spring's @Transactional uses AOP to wrap the method transparently. The service class stays clean.

**L3 — Internals:**
Q: How does Spring actually implement @Transactional?
A: Spring creates a proxy around your bean. When you call the annotated method, the proxy intercepts the call, opens a transaction via PlatformTransactionManager, invokes the real method, then commits or rolls back. This is why self-calls don't work — calling `this.method()` bypasses the proxy.

**L4 — Practical Scenario:**
Q: You have a method that saves a request and creates an audit log entry. How do you ensure both succeed or both fail?
A: Annotate the service method with @Transactional. Both operations run in the same transaction. If audit log insertion fails, the request save rolls back too.

**L5 — Debugging:**
Q: You're using @Transactional but your data is still being committed even when an exception is thrown. What do you check?
A: First — is it a checked exception? @Transactional only rolls back on RuntimeException by default. Use rollbackFor=Exception.class if needed. Second — is the method called from within the same class? Self-call bypasses the proxy. Third — is the method public? Spring proxies only intercept public methods.

**L6 — Cross-Question Chain:**
Q: What's the difference between REQUIRED and REQUIRES_NEW propagation?
→ Q: When would you actually use REQUIRES_NEW?
→ Q: What's the risk of REQUIRES_NEW?
→ Q: How does isolation level relate to propagation?
→ Q: What isolation level does MySQL use by default and what does that mean for your queries?

---

### How to Practice (Critical)

**Answer yourself first. Always.**
Do not look at an answer and say "yes, I understood that." That is not preparation. That is comfortable reading.

Process:
1. Read the question. Close everything. Speak the answer aloud.
2. If you get stuck — note exactly where. That's your gap.
3. Then verify. Correct only what was wrong.

**How to avoid memorizing:**
Never write out a full answer to memorize. Write only the skeleton — 3–5 bullet points of what your answer must cover. Then speak the full answer from those bullets every time. Different words each time = real understanding.

**How to practice cross-questions:**
After answering L3, immediately ask yourself: "What would an interviewer ask next?" Generate the follow-up yourself. Answer it. This is more valuable than any question bank.

**When to revise:**
- Day 1: Learn
- Day 3: Answer chains aloud (first practice)
- Day 7: Answer chains without any notes (test)
- Day 14: Answer chains cold (retention check)
- Before interviews: one full pass of all chains

If Day 7 check fails → that topic goes back to the front of your study queue.

---

## 4. Daily Workflow

**The sequence (every day, same order):**

```
Block 1 — 06:00 to 08:00 (2 hours)
Deep concept study.
One topic. No jumping. No phone.
Read → draw the mental model on paper → write a 10-line code example.

Block 2 — 08:00 to 08:20
Active recall from yesterday.
Pick yesterday's topic. Explain it aloud for 3 minutes without notes.
If you can't → add it to today's study time.

Block 3 — 08:30 to 11:30 (3 hours)
Project work.
Implement the next concrete task in your current project.
Do not study during this block. Build. Hit problems. Solve them.

Block 4 — 11:30 to 12:30 (1 hour)
Interview question chains.
3–4 chains, spoken aloud. Time each answer (target: 90 seconds for L1–L3).
Write the one question you couldn't answer. That is tomorrow's Block 1 priority.

12:30 to 13:30 — Lunch. No screens.

Block 5 — 13:30 to 16:00 (2.5 hours)
DSA (your separate track — keep it isolated here).

Block 6 — 16:00 to 17:30 (1.5 hours)
HLD or LLD (alternating days).
HLD days: design one system, write components, identify bottlenecks.
LLD days: one design problem, timed 45 minutes, no reference.

Block 7 — 17:30 to 19:30 (2 hours)
Project continuation or concept deepening.
If today's Block 3 left an unresolved technical problem → solve it now.
If project is running smoothly → read one concept that came up while building.

Block 8 — 19:30 to 20:00 (30 minutes)
End-of-day protocol:
Write 3 things you can now explain that you couldn't yesterday.
Write 1 thing still fuzzy.
Write tomorrow's Block 1 topic (specific, not "study Java").
```

**Total: ~10 hours of real work. Do not try to do 12. 10 deep hours beats 12 distracted ones.**

---

## 5. Next 7 Days — Exact Plan

---

### Day 1 — Monday

**Study (Block 1):**
Java Thread lifecycle: NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED.
Thread vs Runnable. How to create threads. What `start()` does vs `run()`. Why you never call `run()` directly.
Draw the state diagram on paper.

**Build (Block 3):**
Approval System: Add global exception handler.
Create `GlobalExceptionHandler` with @RestControllerAdvice. Handle MethodArgumentNotValidException, ResourceNotFoundException (or equivalent), AccessDeniedException. Return RFC 7807 format: type, title, status, detail, instance.

**Interview Questions (Block 4):**
Topic: JWT (you know this — start with something you can answer confidently).
Chain: What is JWT → structure → where validated → which filter → what if expired → revocation problem → access vs refresh token.
Speak each answer aloud. Time yourself.

**Revision:**
Re-read your Approval System security config. Trace one request from HTTP hit to response. Understand every filter it passes through.

**End-of-day output:**
- GlobalExceptionHandler committed and tested manually
- Can explain Thread lifecycle without notes
- JWT chain answered fluently

---

### Day 2 — Tuesday

**Study (Block 1):**
synchronized keyword: what it does at the object level. Intrinsic locks. synchronized method vs synchronized block. Why synchronized on `this` is different from synchronized on a static object.
volatile: what it guarantees (visibility, not atomicity). When to use it. When it's not enough.

**Build (Block 3):**
Approval System: Add Testcontainers integration tests.
Write 3 tests:
1. POST /auth/login with valid credentials → 200 + JWT
2. POST /requests with valid EMPLOYEE token → 201 created
3. PUT /requests/{id}/approve with MANAGER token → 200 approved
Use @SpringBootTest + Testcontainers MySQL container.

**Interview Questions (Block 4):**
Topic: Spring @Transactional.
Chain (full L1→L6 from the example above). Speak every level. Identify where you got stuck.

**Revision:**
Block 2: Explain Thread lifecycle aloud. No notes.

**End-of-day output:**
- 3 Testcontainers tests passing
- Can explain synchronized vs volatile with a concrete example
- @Transactional chain done, gaps noted

---

### Day 3 — Wednesday

**Study (Block 1):**
ReentrantLock vs synchronized: why ReentrantLock exists, tryLock, fairness, condition variables.
Deadlock: what causes it (4 conditions), how to detect, how to prevent (lock ordering).
Write a deadlock example in code. Then fix it.

**Build (Block 3):**
Approval System: Add refresh token endpoint.
POST /auth/refresh → accepts refresh token in body → validates → returns new access token.
In-memory token blacklist (ConcurrentHashMap<String, Instant>) for revoked tokens.
Add revoke endpoint: POST /auth/logout → adds current token to blacklist.
Update JwtFilter to check blacklist before accepting token.

**Interview Questions (Block 4):**
Topic: JPA — N+1 problem.
Chain: What is N+1 → how to detect it (enable SQL logging) → JOIN FETCH → EntityGraph → projection → when lazy loading is fine vs dangerous.
Speak aloud. Where do you get stuck? That's tomorrow's Block 1 addition.

**HLD (Block 6):**
First HLD design: URL Shortener.
No references. Whiteboard/paper. Cover: functional requirements, data model, encoding strategy, read path, write path, where to add caching.
Don't worry about being perfect. Identify what you couldn't answer.

**End-of-day output:**
- Refresh token + blacklist committed
- Can explain deadlock conditions and prevention
- URL shortener design on paper with gaps documented

---

### Day 4 — Thursday

**Study (Block 1):**
ExecutorService: ThreadPoolExecutor internals (corePoolSize, maxPoolSize, queue, rejectionPolicy).
What happens when queue is full. Fixed vs Cached vs Scheduled thread pools — when to use each.
CompletableFuture: thenApply, thenCompose, thenCombine, exceptionally. Async execution model.

**Build (Block 3):**
Start Webhook System V1.
Create new Spring Boot project.
Database schema: webhooks table (id, client_id, endpoint_url, secret, active), delivery_attempts table (id, webhook_id, event_type, payload, status, attempt_number, next_retry_at, created_at).
Implement: POST /webhooks (register), POST /events/trigger (trigger delivery).
Delivery is synchronous for now — direct HTTP call using WebClient.

**Interview Questions (Block 4):**
Topic: JPA persistence context.
Chain: What is persistence context → managed vs detached state → dirty checking → when does flush happen → first-level cache → LazyInitializationException cause.
Speak aloud.

**LLD (Block 6):**
Problem: Design a Notification Service that sends via Email, SMS, or Push based on user preference.
45 minutes. No reference.
Design classes, interfaces, relationships. Apply what you know of SOLID.
After timer: identify which principles you applied and which you violated.

**End-of-day output:**
- Webhook V1 schema + basic endpoints running
- Can explain ExecutorService pool internals
- Notification service designed on paper with SOLID reflection

---

### Day 5 — Friday

**Study (Block 1):**
Spring Security filter chain.
Exact filter order. What OncePerRequestFilter is. Where JWT validation fits.
How SecurityContextHolder works. Why ThreadLocal matters. What happens in async requests.
How @PreAuthorize translates to an AOP interceptor.
Draw the full request flow: HTTP → filters → dispatcher servlet → controller → security check.

**Build (Block 3):**
Webhook V1 continued.
Implement retry logic: if delivery fails, schedule next attempt with exponential backoff (1min, 2min, 4min, 8min).
Use a @Scheduled job that polls for delivery_attempts where status=PENDING and next_retry_at <= now.
Max 5 attempts then mark FAILED.
Implement idempotency: each event trigger takes an idempotency_key. Duplicate key within 24 hours → return cached response, no new delivery.

**Interview Questions (Block 4):**
Topic: Spring Security.
Chain: How does Spring Security know who the user is → filter chain → JWT filter → SecurityContext → how @PreAuthorize works → what if token is expired → what if blacklisted → authentication vs authorization objects.

**Revision:**
Block 2: Explain @Transactional self-call trap aloud. Explain N+1 solution aloud.

**End-of-day output:**
- Webhook V1 complete: register, trigger, retry, idempotency
- Can trace HTTP request through Spring Security filter chain without notes
- Security chain answered fluently

---

### Day 6 — Saturday — Mock Day

**Morning (2 hours): Self-mock.**
Pretend you're in an interview. Speak, don't type.

Round 1 (20 min): "Tell me about your Approval System."
Cover: problem, architecture, security model, DB schema, key decisions, tradeoffs, what you'd improve.
Then answer: How do you revoke a JWT? How does your authorization work at 3 layers? What happens if two managers approve the same request simultaneously?

Round 2 (20 min): Java technical.
Questions to answer aloud:
- Explain HashMap internal structure including collision handling
- What is the difference between synchronized and ReentrantLock?
- You have a @Transactional method calling another @Transactional method in the same class. What happens?
- How does CompletableFuture work? What thread runs thenApply?
- What is a race condition? Give a concrete example.

Round 3 (20 min): System design.
Design a rate limiter. Cover: requirements, algorithm choice (token bucket vs sliding window), where state lives, distributed scenario, Redis implementation.

**Afternoon: Fix what you failed.**
Every question you couldn't answer clearly → study it for 2 hours right now.

**End-of-day output:**
- Explicit list of 3 weakest areas from the mock
- Those 3 areas are your Monday–Tuesday priorities

---

### Day 7 — Sunday

**Morning: Concept deepening (based on mock gaps).**
Take the weakest area from Saturday's mock. Study it properly. Not surface-level — go to L3 internals.

**Midday: Project planning.**
Map out Webhook V2 (Kafka integration). List exactly:
- What Kafka concepts you need to understand before starting (study list)
- What you'll build first (producer → consumer → offset commit → DLQ)
- What you'll NOT add (keep scope tight)

**Afternoon: Rest. No studying. Mandatory.**

**End-of-day output:**
- Webhook V2 plan written
- Kafka study list ready for Week 2
- Week 2 Day 1 topic already decided (written down)

---

## Repeatable Weekly Template (Week 2 onward)

```
Monday    — Deep concept study (Phase topic) + Webhook project + Interview chains
Tuesday   — Deep concept study (Phase topic) + Webhook project + Interview chains
Wednesday — Concept + Webhook + HLD design session (1 system, timed)
Thursday  — Concept + Webhook + LLD design session (1 problem, 45 min timed)
Friday    — Webhook project + Interview chains + Revision of week's topics
Saturday  — Mock interview (self) + Fix weakest area from mock
Sunday    — Fix gaps from Saturday + Plan next week + Rest afternoon
```

**What "Interview chains" means each day:**
3–4 chains spoken aloud. One from the current week's topic. One from a previous week's topic (spaced repetition). One from your project (project deep-dive practice).

---

## The Single Anti-Distraction Rule

**When you feel confused about what to study next, follow this exactly:**

> Open your end-of-day notes from yesterday. Find the item you wrote as "still fuzzy." Study that. If you didn't write end-of-day notes, write them now before doing anything else.

That's it.

You are not allowed to study something because you saw a video about it, a job description mentioned it, or someone else is learning it.

You study the thing that your own preparation revealed as a gap — yesterday's fuzzy item, or this week's mock failure.

**If the fuzzy item is on the current phase list → study it now.**
**If it's on a future phase list → write it down and ignore it until that phase.**

The plan is already sequenced correctly. Your job is execution, not re-planning.

---

*Start Day 1 tomorrow morning. Block 1 begins at 06:00.*
