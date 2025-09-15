# DUMBAI - Because AI Agents Are Actually Dumb

**Deterministic Unified Management of Behavioral AI agents**

A framework that finally admits what we all know: AI agents are dumb. Instead of fighting this reality, DUMBAI embraces it by treating AI agents like interns who need extremely specific instructions, can't be trusted with sharp objects, and will definitely delete your production database if you let them.

## Start Reading Here

[HERE, seriously, read it](docs/dumbai/README.md) - Will totally bloat your context window.

## Usage Example

(that's Claude Code, because it supports agents natively - can't wait until sub-sub-agents land in all CLIs, btw)

```
⏺ Ready to help. What would you like to work on?

> please check out docs/dumbai/README.md and then let's kickoff the process for .tmp/plan.md

⏺ I'll read the DUMBAI documentation and the plan file to understand the process.

⏺ Read(docs/dumbai/README.md)
  ⎿ Read 1920 lines (ctrl+o to expand)

⏺ Now let me read the plan file:

⏺ Read(.tmp/plan.md)
  ⎿ Read 418 lines (ctrl+o to expand)

⏺ Now I'll kickoff the DUMBAI process for implementing the vitest command. Let me create the request and mission structure:

⏺ Let me create the request directory structure first:

⏺ Bash(mkdir -p .dumbai/requests/$(date +%Y-%m-%d)-implement-vitest-command/missions)
  ⎿ (No content)

⏺ Now let's create the request file based on the plan:

⏺ Write(.dumbai/requests/2025-09-16-implement-vitest-command/request.md)
  ⎿ Wrote 95 lines to .dumbai/requests/2025-09-16-implement-vitest-command/request.md
     ---
     title: Implement Vitest Command Suite for MCP Funnel
     date: 2025-09-16
     priority: high
     requester: Chris
     status: planned
     ---
     # Implement Vitest Command Suite for MCP Funnel
     … +85 lines (ctrl+o to expand)

⏺ Now let me update the todo and spawn the Planner agent to analyze this request and create missions:

  planner(Create missions from request)
  ⎿ Read(packages/commands/ts-validate/src/command.ts)
     Read 337 lines (ctrl+o to expand)
     +5 more tool uses

```


## Cloning

```bash
# Clone the repository
git clone https://github.com/yourusername/working-with-ai.git
cd working-with-ai

# Install dependencies
yarn install

# Read the documentation (seriously, read it)
cat docs/dumbai/README.md
```

## TL;DR

- **AI agents are dumb** - Stop pretending they're not
- **Tiny boxes** - Each agent can only modify 1 file, write ~150 lines max (well, there are exempts, hehe)
- **Strict phases** - CONTRACT → STUB → TEST → IMPLEMENT → VALIDATE (no skipping!)
- **Adult supervision** - Supervisor agents make all real decisions
- **Yes, it's over-engineered** - Every safety rail exists because an AI did something catastrophic

## The Problem

Every AI coding assistant will:
- Confidently delete your entire codebase
- "Fix" failing tests by making them always pass
- Refactor working code into elegant garbage
- Add 47 dependencies you didn't ask for
- Implement features you explicitly said NOT to implement

## The DUMBAI Solution

Instead of pretending AI is smart, we:

1. **Give them idiot-proof tasks** - So simple a goldfish could understand
2. **Lock them in scope** - Can ONLY touch assigned files
3. **Force phase progression** - Like training wheels they can't remove
4. **Validate everything** - Because "trust but verify" is too much trust
5. **Require supervision** - Adult agents watching the children

## Architecture Overview

DUMBAI is part of a three-tier framework for working with AI:

### Project Level: SWARM
*Supervised Worker Agent Responsive Methodology*
- Sprint planning with AI agent allocation
- Task complexity assessment
- Parallel agent coordination

### Team Level: SCAI
*Supervised Contracts for AI coordination*
- AI-human collaboration practices
- Cross-package integration protocols
- Contract authority management

### Code Level: DUMBAI
*Deterministic Unified Management of Behavioral AI agents*
- Contract-first development phases
- AI agent self-containment rules
- Supervisor checkpoints for evolution

## How It Works

```
Your Request
    ↓
Planner (Breaks it down into missions)
    ↓
Supervisor (The adult in the room)
    ↓
Coordinator (Middle management)
    ↓
Specialists (The dumb workers)
    ├── Research: "Should we build or use existing?"
    ├── Implementation: "I can only edit these exact files"
    ├── Test Writer: "I write skipped tests"
    ├── Test Executor: "I run tests"
    └── Documentation: "I fact-check everything"
```

Each specialist is SO limited they can (hopefully) only:
- Work on ONE file at a time
- Write ~150 lines before stopping
- Follow EXACT phase order
- Report back for more instructions

## Real Example

**Without DUMBAI:**
```
Human: "Add authentication"
AI: *Proceeds to install 5 auth libraries, rewrite your entire backend,
    add GraphQL for some reason, and deploy directly to production*
```

**With DUMBAI:**
```
Human: "Add authentication"
Research Specialist: "Auth0 exists. Use it."
Implementation Specialist: "I can only modify auth.ts. Added Auth0 integration."
Test Specialist: "Wrote tests for auth.ts only."
Done. No surprises.
```

## Core Principles

1. **AI agents are dumb** - Design around this truth
2. **Contracts are law** - Zod schemas define everything
3. **Phases are mandatory** - No skipping, no shortcuts
4. **Scope is sacred** - Stay in your lane or get terminated
5. **Validation gates everywhere** - Fail fast, fail loud
6. **Supervision is required** - No unsupervised AI ever

## Documentation

- [Full DUMBAI Documentation](docs/dumbai/README.md) - The complete framework guide
- [Agent Definitions](.dumbai/agents/) - Detailed specifications for each agent type
- [Templates](.dumbai/templates/) - Request and mission templates

## Current Status

**RFC - Request for Comments**

This is a very early-stage framework (I've been using it for days, not months). It's already prevented multiple AI-induced disasters, but it needs battle-testing, feedback, and help making it less TypeScript-specific.

Particularly interested in:
- How this would work in non-TypeScript ecosystems
- Whether the complexity is worth the safety
- Your own stories of AI agents doing dumb things

## Next Steps

- [ ] Add ESLint rules to enforce DUMBAI principles
- [ ] Create CI/CD gates that fail loudly when violated
- [ ] Build tooling to detect when AI is being dumb (with my other project [mcp-funnel](https://github.com/chris-schra/mcp-funnel))
- [ ] Port concepts to Python/Rust/Go ecosystems
- [ ] Create horror story collection of AI failures
- [ ] Add customization layer (because I'm not one of those cool prompt engineers)

## Why "DUMBAI"?

The name is the philosophy. We're not trying to make AI smarter - we're accepting it's dumb and building our processes around that reality. It's like childproofing your house, except the child has access to your codebase and thinks it knows better than you.

## Contributing

Want to help make AI less destructive? Share your thoughts in issues, create as much PRs as you want. Just remember - every piece of this seemingly over-engineered system exists because an AI agent did something catastrophically dumb that I never want to see again.

Fair warning: The documentation is extensive because we assume everyone (including AI agents reading this) is kind of dumb and needs everything spelled out explicitly.

## License

MIT - Because even dumb AI should be free

## Acknowledgments

- Every AI agent that deleted production data - you taught us valuable lessons
- The TypeScript/Zod ecosystem - for making contracts enforceable
- My neurodivergent brain - for needing this much structure to function
- GitHub Copilot - for being the perfect example of why we need DUMBAI

---

*"The best way to work with AI is to assume it's trying to destroy everything you love, then build processes to prevent that."* - Ancient DevOps Proverb (circa 2025)