# From ML Researcher to AI Engineer

You understand neural networks at a level most engineers never reach. You read papers, implement novel architectures, and think about ML problems with mathematical rigor. The transition to AI engineering requires building a completely different muscle: shipping production systems under real-world constraints.

## What You Already Have

Your theoretical depth is unmatched:

- Deep understanding of ML algorithms, architectures, and training dynamics
- Paper reading and implementation skills
- Novel model design and experimentation
- Mathematical foundations: linear algebra, probability, optimization, information theory
- Understanding of transformer architectures, attention mechanisms, training objectives
- Experience with large-scale experiments and ablation studies
- Familiarity with the frontier of what is possible

## What You Need to Learn

The gap is engineering practice. This is the biggest transition on this list because the mindset shift is significant.

Software engineering fundamentals (4-6 weeks):
- Code architecture: clean code, SOLID principles, design patterns
- Testing: unit tests, integration tests, test-driven development
- Version control: Git workflows, code review, branching strategies
- API design: REST, authentication, error handling, documentation
- Databases: SQL, NoSQL, when to use what, schema design

Production deployment (3-4 weeks):
- Containers: Docker, basic Kubernetes
- CI/CD pipelines: automated testing, deployment, rollback
- Cloud platforms: AWS, GCP, or Azure basics
- Monitoring and observability: logging, metrics, alerting, dashboards
- Infrastructure as code: Terraform or similar

LLM application engineering (2-3 weeks):
- RAG systems: practical chunking, vector databases, retrieval optimization
- Agent frameworks: tool use, function calling, error handling
- Prompt engineering for production: versioning, testing, evaluation
- Cost management: token optimization, model routing, caching

Software engineering practices (3-4 weeks):
- Working in a team: code reviews, documentation, communication
- Agile development: sprints, standups, retrospectives
- Estimation and project planning
- On-call, incident response, postmortems
- Writing code that someone else will maintain at 2 AM

## The Hard Truth: Research Code is Not Production Code

I need to be direct about this because it trips up many researchers.

Research code optimizes for flexibility and experimentation. Production code optimizes for reliability and maintainability. These are fundamentally different goals, and the habits you developed in research actively work against you in production.

Research code has:
- Global variables and hardcoded paths
- No error handling (if it crashes, you fix it and rerun)
- No tests (you verify by looking at metrics)
- Minimal documentation (you are the only reader)
- Complex one-off scripts that do everything

Production code needs:
- Clean interfaces and modular design
- Comprehensive error handling and retry logic
- Automated tests that run on every commit
- Documentation that enables someone else to debug at 3 AM
- Separation of concerns, single responsibility

This is not a criticism. Research code serves its purpose well. But bringing research habits into a production codebase creates problems that compound over time. The sooner you internalize this distinction, the faster your transition will be.

## Suggested Timeline: 3-5 Months

Weeks 1-4: Software engineering bootcamp. Pick a production-grade open source project and study its architecture. Contribute a feature with tests. Learn Git workflow, code review, CI/CD.

Weeks 5-8: Build and deploy. Take one of your research projects and rebuild it as a production service. FastAPI backend, Docker container, automated tests, monitoring. Deploy it. Keep it running.

Weeks 9-12: LLM application stack. Build a RAG system and an agent system. Focus on reliability, evaluation, and cost management. Write tests for non-deterministic outputs.

Weeks 13-16: Interview preparation. Practice coding rounds (algorithms, not just ML). Practice system design with a focus on production concerns. Practice behavioral interviews with stories that demonstrate engineering discipline.

Weeks 17-20 (if needed): Mock interviews, refine weak areas, build portfolio projects.

## Your Advantage

When you walk into a system design interview and the question involves model selection, training tradeoffs, or architecture decisions, you operate at a depth no other candidate can match. You understand why certain models work better for certain tasks. You can discuss attention mechanisms, positional encoding, or training stability with precision.

This depth becomes a superpower once you pair it with production skills. The combination of "I understand the theory deeply AND I can ship it reliably" is rare. Most candidates have one or the other.

I found that researchers who make this transition successfully share one trait: humility about engineering practices. They treat software engineering as a craft worth learning properly, not as a necessary annoyance between them and the interesting ML work. The researchers who struggle are the ones who view production engineering as beneath them.

For interview preparation including how to translate research experience into interview answers, see [The 0→1 AI Engineer Interview Playbook](https://valenx.org/books/aie).
