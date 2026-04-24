# AI for Engineers and Technical Teams: Getting the Most Out of AI Coding Agents
## 30-Minute Talk (20-25 min content + 5-10 min Q&A)

---

# SLIDE 1: Title Slide

**AI for Engineers and Technical Teams**
Arman Bolat | Member of Technical Staff

Today: what I've learned building and using AI coding agents in production engineering.

---

# SLIDE 2: The State of AI in Engineering (The Numbers)

**Pragmatic Engineer Survey, March 2026 (906 engineers)**
- 95% of engineers use AI tools at least weekly
- 75% use AI for at least half their work
- 55% regularly use AI agents (up from ~0% in March 2024)
- Staff+ engineers are the heaviest agent users (63.5%), not juniors
- Engineers juggle 2-4 tools simultaneously

---

# SLIDE 3: The Productivity Gap

- 93% of developers have adopted AI (Stack Overflow, 49K developers, 2025)
- But measured productivity gains are only ~10% (DX, 400 companies, March 2026)
- Why? Most engineers use agents like a fancy autocomplete
- The difference between 10% gains and 10x gains is HOW you use these tools
- You're about to learn the HOW

---

# SLIDE 4: What Is an AI Agent?

An AI agent = LLM + tools + runtime.

- The **LLM** reasons and generates
- **Tools** let it act -- run commands, search the web, read files, call APIs
- The **runtime** orchestrates the loop: read → think → act → observe → repeat

Examples: Droid, Claude Code, Codex, Cursor, Windsurf

---

# SLIDE 5: Your Agent Is Only as Good as Its Instructions

A file automatically included in every user prompt. The agent reads it at the start of every session -- no manual loading needed. In Droid this is `AGENTS.md`. Claude Code uses `CLAUDE.md`. The concept is universal.

**What to include:**
- Build/test/lint commands the agent can't guess
- Code style rules that differ from defaults
- Architectural decisions, common gotchas
- Dev environment quirks

**Keep it SHORT (~100 lines).** OpenAI tried a giant AGENTS.md -- it failed. "Too much guidance becomes non-guidance."

Think of it as onboarding a new hire: tell them the non-obvious stuff.

**The hierarchy:**
- Project root (shared via git, team-wide)
- Nested in subdirectories (loaded on demand)
- Personal/local overrides (not checked in)
- Global across all projects
- Enterprise policies for all employees

---

# SLIDE 6: Skills Turn One-Time Solutions into Reusable Knowledge

Skills are composable procedural knowledge -- folders with instructions and scripts that teach agents how to do specific jobs.

**Examples:**
- `/fix-issue 1234` -- reads GitHub issue, finds code, implements fix, runs tests, opens PR
- `/incident-runbook` -- follows your team's diagnostic procedures
- `/code-review` -- reviews against your team's quality standards

**Progressive disclosure:** Only metadata is shown initially. The agent reads the full skill content only when it decides to use it. Hundreds of skills without bloating context.

**Skills can include scripts as tools.** Unlike traditional tool definitions (which can be ambiguous), scripts are self-documenting, modifiable, and live in the file system until needed.

**Not just for engineers.** Teams in finance, recruiting, legal, and accounting are building skills to extend agents for their own workflows.

---

# SLIDE 7: Use Case 1 -- Codebase Q&A & Onboarding

The simplest starting point. Zero risk, immediate value.

**Ask the agent the same questions you'd ask a senior engineer:**
- "How does authentication work in this codebase?"
- "How do I add a new API endpoint?"
- "Which recent commit broke the login flow?" (agent runs git log, diffs recent changes, identifies the regression)
- "Why does this function have 15 arguments?" (agent checks git history)
- "What did I ship this week?" (looks at git log, gives you a standup summary)

No special prompting required. No indexing. No setup. Just ask.

At Factory, new engineers use a coding agent on day one. Onboarding went from 2 weeks to 2 days.

---

# SLIDE 8: Codebase Q&A -- Going Deeper

**Advanced techniques:**
- Ask the agent to diagram event flows (HTTP -> pubsub -> handlers)
- Pull in context from Linear/Jira tickets, docs, observability tools
- Build a skill that saves learnings for the next person (e.g., `/note_this`)
- Feed learnings back into AGENTS.md for the next new hire

**The data:**
- Time to 10th PR: 91 days -> 33 days with daily AI use (DX, 400 companies, April 2026)
- New engineer shipped customer value by lunch on day two (incident.io blog, 2025)
- Complex workflow guidance from ~2 days to ~30 minutes (Meta Engineering, April 2026)

**Meta's approach: "Compass, not encyclopedia"**
- 50+ AI agents read every file in a 4,100-file pipeline
- Produced 59 concise context files (25-35 lines each)
- Documented 50+ non-obvious patterns no one had written down
- Five questions per module: what does it do, how to modify, what breaks, dependencies, what's undocumented

---

# SLIDE 9: Use Case 2 -- Code Review & Testing

Low risk, high leverage. The natural second step.

**AI-powered code review:**
- Agent as FIRST pass (catches mechanical issues: logic errors, security vulnerabilities, style violations)
- Human review as SECOND pass (business logic, architectural judgment)
- Reduces cycle time by ~3.5 hours per PR
- Can be configured as automated PR reviewers in your CI pipeline

**The writer/reviewer pattern:**
- Two separate agent sessions: one writes code, another reviews it
- Reviewer has fresh context, isn't biased toward code it wrote

**Test generation and TDD with agents:**
- "Write a test for validateEmail covering edge case X. Avoid mocks."
- Agent writes tests, runs them, verifies they pass
- Encourage minimalist end-to-end tests over implementation-specific unit tests

---

# SLIDE 10: Use Case 3 -- Coding

Where most daily work happens. This is not vibe coding -- vibe coding works for prototypes, but production needs structure. The four-phase pattern works across all agents:

1. **Explore**: Agent reads files, understands patterns, no changes made.
2. **Plan**: Agent creates detailed plan. You review and edit it.
3. **Implement**: Agent codes against the plan, runs tests.
4. **Commit**: Agent creates commit, pushes, opens PR.

Stripe ships 1,300+ zero-human-code PRs per week. OpenAI built 1M lines with 0 manually-written code. These aren't toy projects -- but they follow this loop.

You can use subagents to delegate exploration to a separate context, keeping your main session clean.

---

# SLIDE 11: 15 Minutes of Planning Saves 2 Hours of Corrections

The secret to high success rates: invest 15-20 minutes before letting the agent code.

**What to include in your planning prompt:**
- What you want at the end (requirements, not implementation details)
- Which patterns in the codebase to follow
- Constraints and edge cases
- Files that will change
- How to verify it works

**Don't over-constrain.** Think about what you'd give a junior engineer to succeed.

---

# SLIDE 12: Give the Agent a Way to Check Its Own Work

**The #1 rule: Give the agent a way to verify its work.**

This is the single highest-leverage thing you can do -- true across every agent.

**Give it feedback tools:**
- Unit tests and integration tests
- Screenshots (Puppeteer, browser automation)
- A running dev server it can hit
- Expected output to compare against
- Multimodal input -- drop in a design mock, give it a dev server, it builds the UI in a loop

**Context management -- the core constraint:**
- Clear context between unrelated tasks (most important habit)
- After 2 failed corrections: start fresh with a better prompt
- Use subagents for investigation (separate context, report back summary)
- Start new session at natural stopping points

---

# SLIDE 13: Use Case 4 -- PRDs & Engineering Design Docs

Agents as your first draft partner for technical writing.

**Product requirements:** Describe what you want to build in a few sentences, agent generates a structured PRD with user stories, acceptance criteria, edge cases, and success metrics

**Engineering design docs:** Agent reads the codebase, understands existing patterns, and drafts a technical design doc covering architecture, data model, API contracts, and trade-offs

**Deep research:** Agent uses web search and web fetch tools to research prior art, compare approaches, and pull in relevant documentation before drafting

---

# SLIDE 14: Use Case 5 -- Project Management

Agents handling the coordination work that eats into engineering time.

**Ticket triage:** Auto-classify incoming issues by severity and component, route to the right team, draft initial investigation notes

**Standup summaries:** Agent reads git log + ticket updates, generates what you shipped, what's in progress, what's blocked

**Sprint tracking:** Flag at-risk items, surface stale PRs, identify blockers across the team

**Follow-up generation:** Auto-create tickets from PR review comments, post-mortem action items, or meeting notes

**Release notes:** Agent diffs between tags, summarizes user-facing changes, drafts changelog entries

---

# SLIDE 15: Use Case 6 -- Product QA

Agents as your QA team that never sleeps.

**Browser-driven smoke tests:** End-to-end user flow validation against staging or production -- signup, checkout, core workflows

**Visual regression detection:** Screenshot comparison between builds, flagging layout shifts and UI regressions

**Accessibility auditing:** Automated WCAG compliance checks, flagging contrast issues, missing labels, keyboard navigation gaps

**Test plan generation:** Describe what to test in natural language, agent generates and executes the test suite

**Flaky test management:** Detect flaky tests in CI, auto-create tickets, agent attempts the fix and opens a PR

---

# SLIDE 16: Use Case 7 -- Security Review

Agents as a security-aware layer on every change.

**PR security scanning:** Review every PR for hardcoded secrets, injection vulnerabilities, insecure API patterns

**Dependency auditing:** Check third-party dependencies for known CVEs, flag outdated packages with security patches available

**IAM and permissions review:** Audit permission scopes, flag overly broad access, review infrastructure-as-code changes

**Compliance checking:** Validate changes against your security policy -- data handling rules, encryption requirements, logging standards

**Threat modeling:** Agent analyzes new endpoints or data flows and surfaces potential attack vectors for human review

---

# SLIDE 17: Use Case 8 -- Incident Response

The agent as first responder -- investigating while you're still reading the alert.

**The pattern:**
1. Alert fires (PagerDuty, CloudWatch, etc.)
2. Agent pulls logs, queries metrics, checks recent deployments
3. Agent presents structured findings with root cause hypothesis
4. Human validates and approves the fix
5. Agent executes the fix, verifies it works
6. Agent writes the post-mortem

**Skills in action:**
- Skills connect to PagerDuty, Prometheus/Datadog/Grafana, log aggregators, deployment systems
- An `/incident-runbook` skill follows your team's established procedures
- Agent searches past incidents for similar patterns

---

# SLIDE 18: Respect the Exponential

- The length of tasks AI can do is doubling every seven months
- Right now: ~1 hour tasks. Next year: full day. Year after: full week.
- If we demand reading/writing every line of code, we become the bottleneck
- The compiler analogy: early devs read assembly output to verify. That doesn't scale.
- We need to learn to verify at a higher abstraction level

---

# SLIDE 19: Start with Questions, End with Automations

**Here's your adoption path -- start simple, add complexity as you build confidence:**

1. **Codebase Q&A** -- zero risk, immediate value, no code changes
2. **Code review & testing** -- low risk, high leverage, agent as safety net
3. **Feature implementation** -- core daily work, invest in planning
4. **PRDs & design docs, project management, QA, security, incident response** -- agents working on triggers while you focus on what matters
5. **Continuously improve your setup** -- instructions and skills, shared via git, compound over time

The learning flywheel: skills and instructions shared via git compound institutional knowledge. When your teammate clones the repo, they get every skill and configuration you've built. The agent on day 30 is dramatically better than on day one.

**Key takeaways:**
- **The gap is the HOW, not the tools.** 93% adoption, ~10% gains. Your instructions and skills are the difference.
- **Be the PM, not the typist.** 15 minutes of planning > 2 hours of corrections.
- **Verify at the product level.** Tests, screenshots, expected outputs.
- **Respect the exponential.** The task length AI handles doubles every 7 months. Start building the muscle now.
- **Automate yourself away.**

---

# SLIDE 20: Q&A

Arman Bolat | Member of Technical Staff, Factory
factory.ai

Prepared seeds (in speaker notes):
- "What about code quality / AI slop?"
- "Security concerns with agent access?"
- "Will this replace engineers?"
- "Where do agents still fail badly?"
- "How do junior engineers learn in this world?"
