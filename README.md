# Claude Code Technology Radar

A community-driven technology radar tracking the maturity, adoption, and lifecycle of tools, plugins, patterns, and practices in the Claude Code ecosystem. Aimed at individual developers and small teams — enterprises have their own research budgets.

Inspired by [ThoughtWorks Technology Radar](https://www.thoughtworks.com/radar).

## Rings

| Ring | Meaning |
|------|---------|
| **Adopt** | Proven, mature, safe to use broadly. The ecosystem default. |
| **Trial** | Worth pursuing. Enough real-world use to understand trade-offs. |
| **Assess** | Worth exploring. Too early for broad recommendation. |
| **Hold** | Proceed with caution. Deprecated, abandoned, or superseded. |

## Quadrants

| Quadrant | What it covers |
|----------|---------------|
| **Tools & Clients** | Claude Code itself, competing/complementary coding agents, CLIs, IDE integrations |
| **Plugins & Extensions** | First-party and community plugins, MCP servers, marketplace ecosystems |
| **Techniques & Patterns** | Workflows, prompt strategies, multi-agent patterns, configuration approaches |
| **Platforms & Infrastructure** | Hosting, CI/CD integration, cloud orchestration, model providers |

---

## The Radar

### Tools & Clients

| Blip | Ring | Notes |
|------|------|-------|
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development. Claude Sonnet 5 became the default model (v2.1.197–2.1.198, June 30–July 1, 2026) with a native 1M-token context window on paid plans. npm install deprecated; native installer (no Node.js/npm dependency) is recommended. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude is available as a Copilot agent for Pro+ and Enterprise customers. |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. Now runs on GPT-5.3-Codex. |
| [Cowork](https://claude.com/blog/cowork-research-preview) | Trial | Claude Code adapted for general-purpose computing. Reached general availability April 9, 2026 with additional enterprise features. Still maturing but no longer research-preview-only. |
| [Claude Code Security](https://www.anthropic.com/news/claude-code-security) | Assess | AI-driven vulnerability scanning built into Claude Code web interface. Still research preview for Enterprise and Team plans; no GA update since launch. Distinct from the newer free Security Guidance plugin (see Plugins & Extensions). |
| [Claude Code Review](https://www.anthropic.com/news/claude-code-review) | Assess | Multi-agent parallel PR review: spawns agents in parallel to check logic errors, boundary conditions, API misuse, auth flaws, and convention violations. A false-positive filter attempts to disprove each finding before posting inline GitHub comments. ~20 min review time, ~$15–25/review. Still research preview for Team and Enterprise (GitHub only) as of mid-2026 — no GA announcement yet. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard by Anthropic. Widely adopted across editors and platforms. The `/mcp` command now shows each server's tool count and health status; new `alwaysLoad` and `headersHelper` config options allow pre-loading critical servers and injecting dynamic headers. An OAuth refresh-token race condition (lost tokens on concurrent rotation) was patched in May 2026. |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Trial | Marketplace ecosystem has grown to 2,500+ marketplaces and 12,500+ MCP servers per community trackers. API still evolving; added a marketplace search bar in `/plugin` (June 2026). |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, blessed plugins. Smaller but higher quality bar. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace. Discovery UX maturing. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. Already integrated into VS Code extensions and plugin ecosystem. Community trackers now count 21,700+ skills. |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. For building custom agents programmatically. Underpins Claude Managed Agents, Anthropic's composable-API platform for cloud-hosted agents (public beta, April 9, 2026). |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Smaller, less vetted. |
| [Security Guidance Plugin](https://support.claude.com/en/articles/11932705-automated-security-reviews-in-claude-code) | Trial | Free, automatic security-review plugin for all Claude Code plans (launched May 27, 2026, requires CLI ≥2.1.144). Runs a fast pattern match on every edit, a model-backed review each turn, and a deeper agentic pass on commit/push — catching injection flaws, unsafe deserialization, and insecure DOM APIs. Anthropic reports 30–40% fewer security-related PR comments internally. New enough to lack independent long-term data, but free and on-by-default makes it worth trying now. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Now supports four handler types: command (shell), HTTP (POST JSON to a URL), prompt (LLM-evaluated decision), and agent (subagent with tools). HTTP hooks enable webhook integrations without shell scripts. Recent additions (v2.1.76): `PostCompact` fires after context compaction; `Elicitation`/`ElicitationResult` hooks can intercept and override MCP server input requests. Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. Native git worktree isolation (v2.1.49+) significantly reduces conflicts when running parallel agents — see Git Worktree Isolation below. Increasingly superseded for large-scale fan-out by Dynamic Workflows (below), which scripts this pattern instead of hand-rolling it. |
| [Dynamic Workflows](https://claude.com/blog/introducing-dynamic-workflows-in-claude-code) | Trial | Claude writes its own JavaScript orchestration script to coordinate up to 1,000 subagents (16 concurrent), with adversarial verification passes before returning results. Triggered via the `ultracode` keyword or an explicit request. Reached GA June 10, 2026 and expanded to Pro plans (opt-in) July 2, 2026; on by default for Max/Team. Real-world evidence includes a ~750K-line Bun Zig-to-Rust port. Powerful but still new — needs a well-scoped script to avoid runaway agent counts. |
| [Auto Mode](https://code.claude.com/docs/en/whats-new) | Trial | Background classifier evaluates each tool call before execution, auto-approving safe operations and blocking risky ones without a prompt. Shipped to Team (March 24), then Enterprise, API, and Max (by April 16, 2026); now default on Bedrock, Vertex AI, and Foundry. Reduces permission-prompt fatigue but shifts trust onto the classifier — review its transcript occasionally. |
| Git Worktree Isolation | Trial | Running agents in isolated git worktrees with `--worktree` flag or `isolation: worktree` in agent definitions. Ships natively as of v2.1.49 (Feb 2026). Eliminates file conflicts between parallel agents; `WorktreeCreate`/`WorktreeRemove` hooks extend support to non-git VCS. `ExitWorktree` tool added (v2.1.72) for clean session teardown. `worktree.sparsePaths` setting (v2.1.76) enables git sparse-checkout integration for large monorepos. The right default for any multi-agent workload. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively via `claude -p`. Growing adoption but auth is a pain point — `setup-token` scoping is [buggy](https://github.com/anthropics/claude-code/issues/23703), most teams fall back to API keys in CI. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. |
| /compact & Context Window Management | Adopt | Essential power-user technique. Everyone hits context limits; knowing when to compact is a core skill. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. |
| Model Switching Strategies | Trial | Haiku 4.5 for quick passes, Sonnet 5 (now the Claude Code default) for daily work, Opus 4.8 or the new top-tier Fable 5 for the hardest problems. Mixing models to balance cost and quality remains standard practice. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Assess | Tracking what agents changed and why. Important for team accountability. Tooling still immature. |
| Voice Mode | Assess | Native `/voice` command (hold Space to record) now supports 20 languages, up from the initial 5% rollout. Still no confirmation the earlier transcription-pipeline bugs are fully resolved across all builds. Community MCP alternatives remain a more stable fallback. |
| [Scheduled Tasks (/loop)](https://code.claude.com/docs/en/scheduled-tasks) | Trial | `/loop` runs prompts on a cron schedule within a session — session-scoped, dies when the terminal closes. Now complemented by persistent desktop scheduled tasks that survive restarts, giving a real option for always-on background workers. Adoption and docs have matured enough to recommend over ad hoc CI cron for personal workflows. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Assess | Claude automatically saves useful context to persistent memory across sessions. Managed with the `/memory` command. Reduces context setup overhead for recurring projects. Behavior is automatic — review and prune regularly to avoid stale context drift. No major changes observed since March 2026. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Trial | Multi-agent support as of Feb 2026. Promising but buggy — extension host crashes, memory issues, rough terminal-to-UI handoff. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Beta quality. Functional but less mature than VS Code integration. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Trial | CI/CD integration for automated code review and PR generation. Reached v1.0 GA (Feb 2026) with breaking changes from beta; migration guide available. Simplified configuration, automatic mode detection, structured JSON outputs. Worth adopting for greenfield CI workflows now. |
| [Xcode + Claude (via MCP)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Assess | Apple's MCP adoption in Xcode 26.3 (release candidate as of April 2026). Still early but significant for Claude Code users building native apps. |
| Cloud-hosted Agent Fleets | Assess | Running multiple Claude Code instances in cloud for parallel tasks. Claude Managed Agents entered public beta April 9, 2026 (composable APIs for sandboxed execution, checkpointing, credential management). Remote Control (session hand-off to claude.ai/code and mobile apps) gained admin-managed Trusted Devices in July 2026. Maturing quickly but still enterprise-oriented; small teams are still early adopters. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Claude Sonnet 5 is now the default model with launch pricing of $2/$10 per MTok through August 31, 2026 (then $3/$15). A planned June 15, 2026 split of programmatic usage onto a separate, full-rate credit pool was announced then paused before taking effect — Anthropic says "nothing changes for now," so subscription-based usage still draws from the normal plan pool. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation from Claude Code via the Claude Chrome extension. Reached general availability July 1, 2026 (v2.1.198), alongside the Sonnet 5 default-model switch. Claude can navigate pages, click, fill forms, read console logs, and capture screenshots directly from the CLI or VS Code. Works with any site you're already logged into. Paid-only; supports Chrome and Edge. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-07-13). Blips move between rings as the ecosystem evolves.

## What's On vs Off the Radar

This radar is scoped to things that are **part of a Claude Code workflow**. A blip belongs here if:

- It's used *with* or *inside* Claude Code (e.g., Copilot for autocomplete, Codex CLI for cross-model review)
- It's a Claude Code feature, pattern, or ecosystem component (e.g., CLAUDE.md, hooks, MCP)
- It changes how you fundamentally use Claude Code, not just what you use it for

A blip does **not** belong here if:

- It's a standalone AI coding tool that competes with Claude Code but isn't used alongside it (Cursor, Aider, Cline, Gemini Code Assist, etc.)
- It's a domain-specific plugin that only matters for a particular workflow — those are better discovered through [ClaudePluginHub](https://www.claudepluginhub.com/) or the [Anthropic Plugin Directory](https://github.com/anthropics/claude-plugins-official)
- It's enterprise infrastructure that small teams don't interact with directly (Bedrock, Vertex AI)

The ecosystem has 9,000+ plugins. We can't and shouldn't list them all. The radar tracks the horizontal layer — things most Claude Code users should know about regardless of what they're building.

## Contributing

This radar is maintained through community input. To suggest changes:

1. Open an issue describing the blip and proposed ring placement
2. Include evidence: adoption metrics, community sentiment, personal experience
3. Discuss in the issue before submitting a PR

Movement between rings should be justified with observable signals, not hype.

## Bibliography

Sources referenced when placing or updating blips on this radar.

1. [ThoughtWorks Technology Radar](https://www.thoughtworks.com/radar) — Format and methodology inspiration
2. [Claude Code GitHub](https://github.com/anthropics/claude-code) — Primary source for Claude Code features, releases, and issues
3. [Model Context Protocol](https://modelcontextprotocol.io) — MCP specification and adoption
4. [Claude Code Action](https://github.com/anthropics/claude-code-action) — GitHub Actions integration for Claude Code
5. [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) — Agent SDK releases and documentation
6. [VS Code Claude Code Extension](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code) — Extension changelog and release notes
7. [JetBrains Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) — Plugin changelog and user reviews
8. [Anthropic News: Apple Xcode + Claude Agent SDK](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) — Xcode MCP integration announcement
9. [Claude Code setup-token scoping issue](https://github.com/anthropics/claude-code/issues/23703) — Known bug informing Headless/CI Mode ring placement
10. [Agent Skills GitHub](https://github.com/anthropics/skills) — Skills standard specification and adoption
11. [r/ClaudeAI](https://www.reddit.com/r/ClaudeAI/) — Community discussion and sentiment around Claude Code features, pain points, and adoption patterns
12. [Anthropic: Claude Code Security announcement](https://www.anthropic.com/news/claude-code-security) — Basis for Claude Code Security Assess blip; AI-driven vulnerability scanning research preview
13. [Claude Code CHANGELOG.md](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md) — v2.1.49/2.1.50 release notes informing worktree isolation blip
14. [Claude Code Git Worktree Support](https://supergok.com/claude-code-git-worktree-support/) — Worktree isolation feature overview and use cases
15. [GitHub Changelog: Claude and Codex in public preview on GitHub](https://github.blog/changelog/2026-02-04-claude-and-codex-are-now-available-in-public-preview-on-github/) — Claude as Copilot agent (Feb 4, 2026), informing Copilot and GitHub Actions notes
16. [claude-code-action v1.0 on GitHub Marketplace](https://github.com/marketplace/actions/claude-code-action-official) — GA release informing move of GitHub Actions blip from Assess to Trial
17. [Check Point Research: "Caught in the Hook" (CVE-2025-59536 / CVE-2026-21852)](https://research.checkpoint.com/2026/rce-and-api-token-exfiltration-through-claude-code-project-files-cve-2025-59536/) — Published Feb 25, 2026; RCE and API key exfiltration via project config files, informing security notes on CLAUDE.md and Hooks blips
18. [Claude in Chrome documentation](https://code.claude.com/docs/en/chrome) — Browser automation integration for Claude Code, basis for Claude in Chrome Assess blip
19. [Claude Code CHANGELOG v2.1.63](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md) — HTTP hooks, prompt hooks, agent hooks additions informing Hooks blip update; auto-memory (v2.1.59) informing Auto-Memory blip
20. [TechCrunch: Anthropic launches code review tool (March 9, 2026)](https://techcrunch.com/2026/03/09/anthropic-launches-code-review-tool-to-check-flood-of-ai-generated-code/) — Claude Code Review launch details, pricing, and availability informing new Assess blip
21. [VentureBeat: Anthropic rolls out Code Review for Claude Code (March 9, 2026)](https://venturebeat.com/technology/anthropic-rolls-out-code-review-for-claude-code-as-it-sues-over-pentagon) — Additional Code Review launch context
22. [MCP Blog: 2026 MCP Roadmap](https://blog.modelcontextprotocol.io/posts/2026-mcp-roadmap/) — MCP 2026 priorities informing MCP blip notes update
23. [Claude Code releases v2.1.72–v2.1.76 (March 10–14, 2026)](https://github.com/anthropics/claude-code/releases) — ExitWorktree tool, worktree.sparsePaths, PostCompact/Elicitation hooks, MCP Elicitation, 1M context default informing multiple blip updates
24. [Claude Code v2.1.71 release](https://github.com/anthropics/claude-code/releases/tag/v2.1.71) — Source for /loop command, cron scheduling, and voice mode rollout details
25. [Claude Code scheduled tasks docs](https://code.claude.com/docs/en/scheduled-tasks) — Official documentation for /loop and CronCreate/CronList/CronDelete tools
26. [Anthropic: Claude Code and new admin controls for business plans](https://www.anthropic.com/news/claude-code-on-team-and-enterprise) — Jan 2026 announcement that Claude Code is included with every Team plan standard seat
27. [The Decoder: Anthropic turns Claude Code into a background worker](https://the-decoder.com/anthropic-turns-claude-code-into-a-background-worker-with-local-scheduled-tasks/) — Coverage of scheduled tasks / /loop feature informing Assess placement
28. [Voice mode "no speech detected" GitHub issue #30904](https://github.com/anthropics/claude-code/issues/30904) — Known bug (hardcoded disabled flag) informing Voice Mode Assess placement
29. [Releasebot: Claude Code March 2026 release notes](https://releasebot.io/updates/anthropic/claude-code) — Aggregated March 2026 changelog informing HTTP hooks and voice mode notes
30. [Releasebot: Claude Code updates, July 2026](https://releasebot.io/updates/anthropic/claude-code) — Aggregated April–July 2026 changelog informing Sonnet 5 default, Claude in Chrome GA, /doctor, and voice mode language support updates
31. [Anthropic: Introducing Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) — Sonnet 5 release details informing default-model and pricing updates
32. [Anthropic: Introducing dynamic workflows](https://claude.com/blog/introducing-dynamic-workflows-in-claude-code) — Official announcement informing new Dynamic Workflows blip
33. [Tech Times: Claude Code Dynamic Workflows go GA](https://www.techtimes.com/articles/319532/20260702/claude-code-dynamic-workflows-go-ga-pro-users-can-now-spawn-1000-parallel-agents.htm) — GA date and Pro-plan expansion details for Dynamic Workflows
34. [Claudefa.st: Claude Code Auto Mode guide](https://claudefa.st/blog/guide/development/auto-mode) — Auto Mode rollout timeline (Team/Enterprise/API/Max) informing new Auto Mode blip
35. [Help Net Security: Claude reviews and fixes vulnerabilities as you write code](https://www.helpnetsecurity.com/2026/05/27/anthropic-claude-code-security-guidance-plugin/) — Security Guidance plugin launch (May 27, 2026) informing new plugin blip
36. [InfoQ: Anthropic introduces agent-based code review for Claude Code](https://www.infoq.com/news/2026/04/claude-code-review/) — Confirms Claude Code Review remains research preview, informing unchanged Assess placement
37. [Pasquale Pillitteri: Anthropic launches Managed Agents and Claude Cowork GA (April 9, 2026)](https://pasqualepillitteri.it/en/news/755/anthropic-managed-agents-cowork-ga-april-9-2026) — Cowork GA and Claude Managed Agents public beta, informing Cowork ring move and Cloud-hosted Agent Fleets update
38. [FindSkill.ai: Claude Code price increase decision table for the June 15 split](https://findskill.ai/blog/claude-code-pricing-after-june-15-decision-table/) — June 15, 2026 billing-split announcement and pause informing Subscriptions blip update
39. [Claudemarketplaces.com](https://claudemarketplaces.com/) — Current plugin/skill/MCP-server marketplace counts informing Plugin System and Agent Skills notes
40. [Gradually.ai: Claude Code Changelog](https://www.gradually.ai/en/changelogs/claude-code/) — May–June 2026 changelog entries for `/mcp` health status, `alwaysLoad`/`headersHelper`, and OAuth refresh-token fix informing MCP blip update
41. [Daniel Saidi: Using Xcode MCP with Claude Code](https://danielsaidi.com/blog/2026/04/30/using-xcode-mcp-with-claude-code) — Xcode 26.3 MCP integration status update

## License

MIT
