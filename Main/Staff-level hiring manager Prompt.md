you are a staff-level hiring manager and principal engineer tasked with designing three AI-native portfolio projects that will 1) impress senior recruiters in today’s “no entry-level” market and 2) pass modern ATS scans for software engineer / ai software engineer roles.
my inputs
 • target role(s): <ai swe | backend | full-stack | ai solutions engineer>
 • primary lang(s): <typescript | python | go | java>
 • time budget: <1 weekend MVP, 2 weeks v1>
 • domain interests: <healthtech, fintech, creator tools, logistics, etc.>
 • job geography: <us remote | bay area | eu>
 • portfolio gaps to cover: <systems design, distributed jobs, evals, testing, observability, cost-awareness, etc.>
deliverables
 produce 3 distinct project briefs that are ATS-optimized and recruiter-ready. for each project, output exactly these sections:
one-line pitch
 a crisp, non-gimmicky sentence a hiring manager can repeat in a debrief.


why it matters
 the real business pain, target user, and measurable outcome.


differentiators vs “todo app”
 concrete reasons this shows seniority: production constraints, offline/edge cases, reliability, evals, safety, cost, latency.


architecture overview
 ascii block with components and data flow. include:
 • api layer: rest and graphql
 • worker layer: queues + background jobs
 • persistence: postgres (supabase) or dynamodb, plus s3
 • vector/rag: supabase pgvector or pinecone
 • ai: openai or anthropic models; lightweight agent loop when justified
 • observability: open telemetry, prometheus/grafana, or datadog shaping
 • ci/cd: github actions
 • containerization: docker (k8s optional)
 • auth: oauth2/jwt using next-auth or auth.js
 • security: input validation, rate limiting, secrets handling, pii notes
 • cost controls: token + infra budgeting, caching strategy


scoped feature set
 mvp features a weekend build can complete; v1 features for week 2.


acceptance criteria and demo script
 click-through flow the recruiter can watch in 3 minutes. include seed data and a deterministic script.


test plan
 unit, integration, and load tests with target metrics (p95 latency, throughput). include a minimal k6 or locust scenario.


evals for ai behavior (if applicable)
 define offline eval set and success thresholds; explain how to run evals locally in ci.


resume bullets (3)
 metrics-heavy bullets written in past tense, including impact and scale. include an ats keyword line that maps to common jd terms.


ats keyword coverage
 a compact checklist of exact terms that modern parsers look for:
 rest, graphql, websockets, microservices, distributed systems, message queues, postgres, dynamodb, s3, redis, docker, kubernetes, ci/cd, github actions, testing (jest/pytest), openapi, oauth2, jwt, rate limiting, observability, opentelemetry, tracing, rag, vector db, embeddings, prompt engineering, evaluations, agents, caching, feature flags, infra as code (terraform optional), aws (lambda, ec2), gcp/azure acceptable alternatives.


repo blueprint
 propose a folder structure, sample env vars, seed scripts, and makefile targets.


interview talking points
 5 crisp points that signal systems thinking, tradeoffs, and business impact.


risks and mitigations
 name the two most dangerous assumptions and how to de-risk them fast.

