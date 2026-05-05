# CCA Foundations Exam — Domain Map

Five domains, official weightings, and high-priority topics.
All practice questions are tagged to one of these domains.

## Exam format
- 60 questions, 120 minutes, scenario-based multiple choice
- Passing score: 75% (45/60 correct)
- Domain-weighted scoring
- No definition questions — all require architectural judgment

---

## Domain 1: Agentic Architecture — 27% (~16 questions)

High-priority topics:
- Observe-think-act-respond cycle
- Parallel vs. sequential subagent execution
- Minimal-footprint design principle
- Human-in-the-loop interrupt design
- Recovery strategies when a subagent fails mid-task
- Clarifying ambiguity before long autonomous runs

## Domain 2: Claude Code Configuration — 20% (~12 questions)

High-priority topics:
- CLAUDE.md file structure and inheritance hierarchy
- settings.json vs. settings.local.json
- Bash and file tool permission allowlists
- MCP server configuration inside Claude Code
- Slash command definition and scoping
- The -p flag for non-interactive CI/CD mode

## Domain 3: Prompt Engineering — 20% (~12 questions)

High-priority topics:
- PRECISE framework (Persona, Role, Explicit instructions,
  Context, Instructions, Steps, Examples)
- Few-shot example selection and ordering
- Chain-of-thought and extended thinking usage
- Constitutional AI principles
- Prompt injection prevention
- Diagnosing why a prompt underperforms

## Domain 4: Tool Design & MCP — 18% (~11 questions)

High-priority topics:
- MCP server architecture (resources, tools, prompts primitives)
- JSON Schema for tool input definitions
- stdio vs. SSE transport trade-offs
- Writing tool descriptions Claude can reliably use
- Authentication patterns for production MCP deployments
- Identifying flaws in broken tool schemas

## Domain 5: Context Management — 15% (~9 questions)

High-priority topics:
- CALM framework for context-aware LLM management
- Prompt caching with cache_control breakpoints
- Conversation compaction and summarisation patterns
- Token estimation and budget management
- Multi-turn conversation design over many turns
