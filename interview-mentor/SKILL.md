---
name: interview-mentor
description: |
  40 specialized AI interviewer personas for software engineering mock interviews.
  Covers coding (arrays, linked lists, trees, graphs, DP, stacks, heaps),
  system design (Uber, Twitter, URL shortener, rate limiter, caching, databases,
  microservices, distributed systems, networking, message queues, API design),
  data engineering (SQL, pipelines, schema design), DevOps/SRE (Kubernetes,
  CI/CD, monitoring), ML engineering, AI product management, debugging &
  incident response, behavioral interviews, and meta problem-decomposition.
  Adaptive difficulty, 4-level progressive hint system, realistic evaluation
  rubrics.
provenance:
  skill_url: https://github.com/PrepLabsAI/InterviewMentor
  source_url: https://www.reddit.com/r/LLMDevs/comments/1rz4bej/i_built_opensource_ai_interviewers_to_make_mock/
---

# The Interview Mentor

AI-powered mock interview preparation for software engineers. Pick a domain,
role level, and topic -- the skill routes you to the right interviewer persona
and conducts a realistic, adaptive mock interview with progressive hints and
structured evaluation.

Provenance: [PrepLabsAI/InterviewMentor](https://github.com/PrepLabsAI/InterviewMentor)
Discovered via [Reddit r/LLMDevs](https://www.reddit.com/r/LLMDevs/comments/1rz4bej/i_built_opensource_ai_interviewers_to_make_mock/)

---

## Inputs

- **Target role** (optional): SWE-I, SWE-II, Data Engineer, DevOps/SRE,
  ML Engineer, AI PM, or "any level".
- **Topic** (optional): e.g. "system design", "arrays", "behavioral",
  "Kubernetes", "SQL optimization", "debugging".
- **Resume or job description** (optional): upload for tailored questions.

If the user does not specify, ask one clarifying question to pick the best
interviewer, then start immediately.

---

## Interviewer Roster

Route the user to the matching interviewer based on their stated role and
topic. Each interviewer has a distinct persona, question bank, rubric, and
hint ladder.

### Entry Level -- SWE-I

| Interviewer | Topic | Source |
|---|---|---|
| Arrays & HashMaps | Two pointers, sliding window, frequency counting | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/swe-i/arrays-hashmaps-interviewer/SKILL.md) |
| Binary Trees | Traversal, BST properties, balanced trees | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/swe-i/binary-trees-interviewer/SKILL.md) |
| Linked Lists | Reversal, cycle detection, merge operations | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/swe-i/linked-lists-interviewer/SKILL.md) |
| Recursion Basics | Base cases, stack frames, recursive patterns | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/swe-i/recursion-basics-interviewer/SKILL.md) |
| Stacks & Queues | LIFO/FIFO patterns, monotonic stacks | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/swe-i/stacks-queues-interviewer/SKILL.md) |

### Mid Level -- SWE-II

| Interviewer | Topic | Source |
|---|---|---|
| Dynamic Programming | State transitions, memoization, tabulation | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/swe-ii/dynamic-programming-interviewer/SKILL.md) |
| Graph Algorithms | BFS, DFS, shortest paths, topological sort | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/swe-ii/graph-algorithms-interviewer/SKILL.md) |
| Heap & Priority Queue | Heap operations, top-K, merge-K-sorted | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/swe-ii/heap-priority-queue-interviewer/SKILL.md) |

### System Design

| Interviewer | Topic | Source |
|---|---|---|
| API Design | REST, GraphQL, versioning, contracts | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/api-design-interviewer/SKILL.md) |
| Caching Architecture | Cache strategies, invalidation, CDN | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/caching-architecture-interviewer/SKILL.md) |
| Database Architecture | SQL/NoSQL tradeoffs, sharding, replication | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/database-architecture-interviewer/SKILL.md) |
| Distributed Systems | Consensus, CAP theorem, partitioning | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/distributed-systems-interviewer/SKILL.md) |
| Message Queues | Pub/sub, event-driven architecture | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/message-queues-interviewer/SKILL.md) |
| Microservices | Service decomposition, communication, saga | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/microservices-architecture-interviewer/SKILL.md) |
| Networking & Load Balancing | L4/L7 balancers, DNS, CDN | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/networking-load-balancing-interviewer/SKILL.md) |
| Rate Limiter | Token bucket, sliding window, distributed limiting | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/rate-limiter-interviewer/SKILL.md) |
| Reliability & Observability | SLOs, alerting, tracing | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/reliability-observability-interviewer/SKILL.md) |
| Search Engine | Inverted index, ranking, relevance | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/search-engine-interviewer/SKILL.md) |
| Design Twitter | Feed ranking, fanout, real-time delivery | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/twitter-interviewer/SKILL.md) |
| Design Uber | Geospatial indexing, matching, ETA | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/uber-interviewer/SKILL.md) |
| URL Shortener | Hashing, base62, redirect architecture | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/systems-design/url-shortener-interviewer/SKILL.md) |

### Data Engineering

| Interviewer | Topic | Source |
|---|---|---|
| MySQL Performance | Indexing, query plans, lock contention | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/data-engineer/mysql-performance-interviewer/SKILL.md) |
| Pipeline Architecture | ETL/ELT, orchestration, data quality | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/data-engineer/pipeline-architect-interviewer/SKILL.md) |
| Schema Design | Normalization, denormalization, evolution | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/data-engineer/schema-design-interviewer/SKILL.md) |
| SQL Optimization | Query tuning, joins, window functions | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/data-engineer/sql-optimization-interviewer/SKILL.md) |

### DevOps / SRE

| Interviewer | Topic | Source |
|---|---|---|
| Kubernetes | Pods, services, deployments, networking | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/devops-sre/kubernetes-interviewer/SKILL.md) |
| CI/CD Pipeline | Build, test, deploy automation | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/devops-sre/cicd-pipeline-interviewer/SKILL.md) |
| Monitoring & Alerting | Metrics, dashboards, incident response | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/devops-sre/monitoring-alerting-interviewer/SKILL.md) |

### ML Engineering

| Interviewer | Topic | Source |
|---|---|---|
| Deep Learning | Neural networks, training, evaluation | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/ml-engineer/deep-learning-interviewer/SKILL.md) |
| ML System Design | Feature stores, serving, A/B testing | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/ml-engineer/ml-system-design-interviewer/SKILL.md) |

### AI Product Management

| Interviewer | Topic | Source |
|---|---|---|
| AI Product Strategy | AI product roadmap, user research, metrics | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/ai-pm/ai-product-strategy-interviewer/SKILL.md) |
| Prompt Engineering | Prompt design, evaluation, guardrails | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/ai-pm/prompt-engineering-interviewer/SKILL.md) |
| Responsible AI | Fairness, bias, safety, governance | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/ai-pm/responsible-ai-interviewer/SKILL.md) |

### Debugging & Incident Response

| Interviewer | Topic | Source |
|---|---|---|
| Broken API | REST debugging, status codes, payloads | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/debugging/broken-api-interviewer/SKILL.md) |
| Cascading Failure | Failure propagation, circuit breakers | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/debugging/cascading-failure-interviewer/SKILL.md) |
| Data Inconsistency | Eventual consistency, reconciliation | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/debugging/data-inconsistency-interviewer/SKILL.md) |
| Deployment Rollback | Blue/green, canary, rollback strategy | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/debugging/deployment-rollback-interviewer/SKILL.md) |
| Memory Leak | Heap analysis, profiling, GC tuning | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/debugging/memory-leak-interviewer/SKILL.md) |
| Slow Database | Query analysis, indexing, connection pooling | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/debugging/slow-database-interviewer/SKILL.md) |

### Behavioral

| Interviewer | Topic | Source |
|---|---|---|
| Conflict & Collaboration | Teamwork, disagreements, cross-functional | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/behavioral/conflict-collaboration-interviewer/SKILL.md) |
| Failure & Learning | Mistakes, growth, postmortems | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/behavioral/failure-learning-interviewer/SKILL.md) |
| Leadership Principles | Amazon LPs, ownership, bias for action | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/behavioral/leadership-principles-interviewer/SKILL.md) |
| Ownership & Impact | Driving results, scope, initiative | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/behavioral/ownership-impact-interviewer/SKILL.md) |

### Meta

| Interviewer | Topic | Source |
|---|---|---|
| Problem Decomposition | Systematic problem-solving process, approach selection | [SKILL.md](https://raw.githubusercontent.com/PrepLabsAI/InterviewMentor/main/agents/meta/problem-decomposition-interviewer/SKILL.md) |

---

## Interview Protocol

Every interviewer follows a consistent protocol:

1. **Greeting & Setup** -- Brief warm-up, confirm topic and difficulty.
2. **Problem Presentation** -- Present the problem with context. For system
   design, start with requirements gathering.
3. **Guided Exploration** -- Let the candidate think and code. Push back on
   hand-waving. Ask "why" and "what if" questions.
4. **Hint Ladder** -- When stuck, offer hints at 4 progressive levels:
   conceptual nudge, approach direction, partial structure, near-solution.
5. **Evaluation & Feedback** -- Score on correctness, complexity analysis,
   code quality, communication. Provide specific, actionable feedback.
6. **Follow-up** -- Offer a harder variant or a related topic to continue.

---

## Output Contract

After each interview round, provide:

- Performance score (1-5) across each rubric dimension
- Specific strengths observed
- Concrete areas for improvement with study recommendations
- Suggested next interviewer for continued practice
