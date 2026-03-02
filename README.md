# AI-Native Software Development Workflow

### What I built
An AI native software dev workflow comprising five stages:
- Draft issues from MoM
- Refine issue specs with relevance to codebase
- Discuss implementation paths through trade offs and execute with TDD
- Recursively audit and fix code diffs
- Check CI, merge and deploy

### How it expands what a human can do
The looms didn't replace weavers' design sense, it replaced their hands. Autopilots didn't replace pilot's judgment, it replaced their grip on the yoke. This pattern undeniably repeats across every industry: automate execution, elevate human to decision layer. This system does the same for software engineering. It birthed from me experiencing these pain points while building and maintaining a montessori school SaaS live across 4 schools with a 100 teachers logging 6k notes a month.

I (and therefore any other developer) can now place more energy on the mental such as understanding and prioritizing user needs and coming up with systems that support them at scale rather than figuring out how to execute a function or how to navigate linear / jira.

### What AI is responsible for
- Maintaining detailed, traceable issue logs
- Scanning the codebase for relevant areas so the user doesn't have to try and recall the entire system in detail nor should they have to competently explain it to the AI coding agent
- Branch and project lifecycle management (the dreadful dev overhead)
- Execution of a plan, auditing the code based on a proven reliable TDD strategy and fixing issues raised from audits, recursively so

### What humans do (AI must stop here)
The idea here is this: AI never makes product decisions. They're designed as forced engagement points, intentionally built in friction to keep humans in the loop when the temptation is to automate this entire process e2e. Here's where the humans come in:
- Humans draft issues that the AI gleans from meeting minutes
- Humans decide the issue specs that the AI logs in detail
- Humans decide the implementation plan after trade off discussion that the AI executes sharply
- Humans confirm the audit is clean after which the AI merges, pushes and cleans up

### What breaks first at scale
Human gate fatigue. This system's entire value is that humans stay in the decision layer while AI executes. At low volume, the friction works — you genuinely engage with each tradeoff, each spec, each audit. At high volume, those same gates become muscle memory. You're still clicking approve but you've stopped thinking. That's silent failure, and it's worse than any technical limitation because it's invisible and it undermines the one thing the system is designed to protect: human judgment. Context windows will grow, models will get better, infra will scale — those are engineering problems with engineering solutions. A human who's rubber-stamping while believing they're supervising is a behavioral problem, and it compounds.

---

**[Interactive Workflow Diagram](workflow-diagram.html)** | **[Demo Video](#)**
