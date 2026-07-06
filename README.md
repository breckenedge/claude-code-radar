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
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development. npm install deprecated; native installer (no Node.js/npm dependency) is now recommended — run `claude install` or use the install script. Claude Sonnet 5 became the default model (June 30, 2026) with a native 1M-token context window and promotional pricing through Aug 31, 2026. Background agents in worktrees now auto-commit, push, and open a draft PR on completion (v2.1.198, July 2026) instead of stopping to ask. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude is now available as a Copilot agent for Pro+ and Enterprise customers (Feb 2026). |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. |
| [Cowork](https://claude.com/blog/cowork-research-preview) | Trial | Claude Code adapted for general-purpose computing. Reached general availability April 9, 2026, with enterprise connectors, department-specific plugins, and scheduled recurring tasks. Still a distinct product from Claude Code proper, but graduated past early assessment. |
| [Claude Code Security](https://www.anthropic.com/news/claude-code-security) | Assess | AI-driven vulnerability scanning built into Claude Code web interface. Still in limited research preview for Enterprise and Team plans as of July 2026 — no GA date announced. Reasons about code like a human security researcher, not just pattern-matching. Found 500+ vulnerabilities in production OSS. Not in your workflow yet, but worth watching. |
| [Claude Code Review](https://www.anthropic.com/news/claude-code-review) | Assess | Multi-agent parallel PR review: spawns agents in parallel to check logic errors, boundary conditions, API misuse, auth flaws, and convention violations. A false-positive filter attempts to disprove each finding before posting inline GitHub comments. ~20 min review time, ~$15–25/review. Research preview for Team and Enterprise (GitHub only). Customizable via CLAUDE.md and a new REVIEW.md file. Launched March 9, 2026. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard, now governed by the Agentic AI Foundation under the Linux Foundation (donated Dec 2025; OpenAI/Block co-founders, AWS/Google/Microsoft/Cloudflare/Bloomberg as platinum members). Ecosystem scale: 97M+ monthly SDK downloads, 19,000+ servers indexed on Glama. The 2026-07-28 spec release delivers a stateless core over ordinary HTTP, the MCP Apps extension (server-rendered UI), a Tasks extension for long-running work, and authorization hardening. |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Trial | Plugins spread across many marketplaces of varying size and quality (from the curated official directory to community marketplaces claiming hundreds to thousands of packages). API still evolving. |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, blessed plugins. Grew to 101 plugins as of March 2026 (33 Anthropic-built, 68 partner plugins from GitHub, Playwright, Supabase, Figma, Vercel, Linear, Sentry, Stripe, Firebase). Smaller but higher quality bar than community marketplaces. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace. Discovery UX maturing. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. Already integrated into VS Code extensions and plugin ecosystem. First-party skills continue to ship (e.g., `/dataviz` for chart/dashboard guidance, added June 2026). |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. For building custom agents programmatically. Microsoft Agent Framework integration. Available on Pro, Max, Team, and Enterprise plans (not just API billing) as of June 15, 2026, lowering the barrier for individual developers to build on it. |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Smaller, less vetted. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). A June 2026 disclosure showed prompt injection via GitHub issue/PR metadata can chain into RCE and credential exfiltration in CI contexts (see GitHub Actions blip). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Now supports four handler types: command (shell), HTTP (POST JSON to a URL), prompt (LLM-evaluated decision), and agent (subagent with tools). HTTP hooks enable webhook integrations without shell scripts. Recent additions (v2.1.76): `PostCompact` fires after context compaction; `Elicitation`/`ElicitationResult` hooks can intercept and override MCP server input requests. New `Notification` hook events (July 2026) fire on agent completion and blocked sessions — useful for wiring Slack/desktop alerts to background agents. Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. Native git worktree isolation (v2.1.49+) significantly reduces conflicts when running parallel agents — see Git Worktree Isolation below. Subagents now run in the background by default (June 30, 2026), letting the main session keep working while they run. Nested sub-agents (up to 3 levels deep) and `fallbackModel` chains reached GA in the June 2026 release train. |
| [Dynamic Workflows](https://code.claude.com/docs/en/workflows) | Trial | Claude writes a JavaScript orchestration script that coordinates up to 1,000 subagents (16 concurrent) in the background while the session stays responsive; a verification layer of adversarial agents challenges findings before they're reported. Reached GA July 2, 2026, extending access to Pro plans (off by default; on by default for Max/Team). Real signal: drove a 6-day, 960k-line Bun runtime port from Zig to Rust with 99.8% of tests passing. Cost scales linearly with agent count — a 10-agent run costs ~10x a single-agent run. |
| Git Worktree Isolation | Trial | Running agents in isolated git worktrees with `--worktree` flag or `isolation: worktree` in agent definitions. Ships natively as of v2.1.49 (Feb 2026). Eliminates file conflicts between parallel agents; `WorktreeCreate`/`WorktreeRemove` hooks extend support to non-git VCS. `ExitWorktree` tool added (v2.1.72) for clean session teardown. `worktree.sparsePaths` setting (v2.1.76) enables git sparse-checkout integration for large monorepos. The right default for any multi-agent workload. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively via `claude -p`. Growing adoption but auth is a pain point — `setup-token` scoping is [buggy](https://github.com/anthropics/claude-code/issues/23703), most teams fall back to API keys in CI. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. |
| /compact & Context Window Management | Adopt | Essential power-user technique. Everyone hits context limits; knowing when to compact is a core skill. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. |
| Model Switching Strategies | Trial | Haiku for quick passes, Sonnet for daily work, Opus for hard problems. Current lineup: Haiku 4.5, Sonnet 5 (default as of June 30, 2026, promo-priced $2/$10 per Mtok through Aug 31), Opus 4.8. GA'd `fallbackModel` chains (June 2026) let you configure automatic fallback rather than switching manually. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Assess | Tracking what agents changed and why. Important for team accountability. Tooling still immature. |
| Voice Mode | Trial | Native `/voice` command (hold Space to record) shipped in v2.1.71 (Mar 2026); rollout progressed from 5% of users to broadly available across Pro/Max/Team/Enterprise by mid-2026 at no extra cost, with expanded language support. Early transcription bugs from March appear resolved in current builds. Still push-to-talk rather than hands-free, but stable enough for daily use. |
| [Scheduled Tasks (/loop)](https://code.claude.com/docs/en/scheduled-tasks) | Trial | `/loop` command runs prompts on a cron schedule within a session — turns Claude Code into a background worker. Session-scoped (stops when terminal closes), 3-day auto-expiry. For persistent scheduling, use Routines (see below) instead of the GitHub Actions/headless workaround previously recommended here. |
| [Claude Code Routines (/schedule)](https://code.claude.com/docs/en/routines) | Assess | Persistent, session-independent scheduling: configure a prompt, repo, and connectors once, then trigger on a cron schedule, via API, or from GitHub events. Runs on Anthropic's infrastructure, so it survives closed laptops — the gap `/loop` explicitly doesn't cover. Shipped as research preview April 14, 2026 on all paid plans, with daily run caps by tier (5/15/25 for Pro/Max/Team-Enterprise). Early, but a more credible persistent-automation story than `/loop` + cron workarounds. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Assess | Claude automatically saves useful context to persistent memory across sessions (v2.1.59+). Managed with the `/memory` command. Reduces context setup overhead for recurring projects. Behavior is automatic — review and prune regularly to avoid stale context drift. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Trial | Multi-agent support as of Feb 2026. Promising but buggy — extension host crashes, memory issues, rough terminal-to-UI handoff. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Beta quality. Functional but less mature than VS Code integration. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Trial | CI/CD integration for automated code review and PR generation. Reached v1.0 GA (Feb 2026) with breaking changes from beta; migration guide available. Simplified configuration, automatic mode detection, structured JSON outputs. **Security note:** a June 1, 2026 disclosure (independently found by Aonan Guan and Aikido Security) showed prompt injection via GitHub issue/PR metadata could chain into unauthorized code pushes and credential exfiltration in Action contexts. Update to `claude-code-action` v1.0.94+ and `@anthropic-ai/claude-code` v1.0.93+ before relying on this in CI. |
| [Xcode + Claude (via Claude Agent SDK)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Assess | Apple's Xcode 26.3 integrates the Claude Agent SDK directly (not just MCP) — subagents, background tasks, plugins, and Xcode Previews capture for visual iteration on SwiftUI views. Broader Agent SDK availability (June 2026, see Agent SDK blip) lowers the barrier to trying this. Still early but significant for Claude Code users building native Apple apps. |
| Cloud-hosted Agent Fleets | Assess | Running multiple Claude Code instances in cloud for parallel tasks. Coming up fast but still early for small teams. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Individual devs and small teams pay through Max or Pro plans. As of Jan 2026, Claude Code is included with every Team plan standard seat ($20/month). Claude Sonnet 5 (default model as of June 30, 2026) ships with a native 1M-token context window and promotional pricing ($2/$10 per Mtok input/output through Aug 31, 2026, then $3/$15); Opus 4.8 remains at $5/$25. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation from Claude Code via the Claude Chrome extension. Reached general availability in July 2026 with background notifications, draft-PR handoff, and improved session handling. Claude can navigate pages, click, fill forms, read console logs, and capture screenshots directly from the CLI or VS Code. Works with any site you're already logged into. Paid-only; supports Chrome and Edge. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-07-06). Blips move between rings as the ecosystem evolves.

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
30. [Anthropic: Introducing Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) — Official announcement of Sonnet 5 as default Claude Code model (June 30, 2026), 1M context, promotional pricing
31. [The New Stack: Claude Sonnet 5 launch](https://thenewstack.io/claude-sonnet-5-launch/) — Sonnet 5 vs. Opus 4.8 pricing and positioning details
32. [Anthropic: Introducing dynamic workflows in Claude Code](https://claude.com/blog/introducing-dynamic-workflows-in-claude-code) — Primary source for the new Dynamic Workflows blip
33. [Claude Code dynamic workflows docs](https://code.claude.com/docs/en/workflows) — Official documentation for orchestrating subagents at scale, GA plan availability
34. [TechTimes: Claude Code Dynamic Workflows go GA](https://www.techtimes.com/articles/319532/20260702/claude-code-dynamic-workflows-go-ga-pro-users-can-now-spawn-1000-parallel-agents.htm) — GA date, Pro plan availability, Bun port example, cost scaling
35. [aiagentsfirst.com: Claude Code v2.1.198 background agents](https://aiagentsfirst.com/claude-code-v2-1-198-background-agents) — Background agent auto-commit/push/draft-PR behavior and new Notification hook events
36. [Anthropic: Introducing routines in Claude Code](https://claude.com/blog/introducing-routines-in-claude-code) — Primary source for new Claude Code Routines blip, research preview since April 14, 2026
37. [Claude Code routines docs](https://code.claude.com/docs/en/routines) — Trigger types, plan availability, daily run limits for Routines
38. [The New Stack: Anthropic doubled Claude Cowork limits](https://thenewstack.io/anthropic-claude-cowork-promotion/) — Cowork usage and GA context
39. [pasqualepillitteri.it: Anthropic managed agents, Cowork GA (April 9, 2026)](https://pasqualepillitteri.it/en/news/755/anthropic-managed-agents-cowork-ga-april-9-2026) — Cowork GA date and enterprise connector expansion, informing ring move to Trial
40. [MCP Blog: 2026-07-28 release candidate](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) — Stateless HTTP core, MCP Apps, Tasks extension, informing MCP blip update
41. [AI2Work: MCP hits 97M installs as Linux Foundation takes over](https://ai2.work/blog/model-context-protocol-hits-97m-installs-as-linux-foundation-takes-over/) — MCP governance transition and ecosystem scale figures
42. [Microsoft Security Blog: Securing CI/CD in an agentic world — Claude Code GitHub Action case](https://www.microsoft.com/en-us/security/blog/2026/06/05/securing-ci-cd-in-agentic-world-claude-code-github-action-case/) — June 2026 prompt injection vulnerability in claude-code-action, informing GitHub Actions blip security note
43. [Rescana ThreatsDay Bulletin, June 2026](https://www.rescana.com/post/threatsday-bulletin-june-2026-miasma-supply-chain-worm-leak-claude-code-github-action-vulnerability-ai-agent-phishing-to) — Corroborating coverage of the Claude Code GitHub Action prompt injection disclosure and fixed versions
44. [Apple Newsroom: Xcode 26.3 unlocks agentic coding](https://www.apple.com/newsroom/2026/02/xcode-26-point-3-unlocks-the-power-of-agentic-coding/) — Claude Agent SDK integration details (subagents, background tasks, Xcode Previews) informing Xcode blip update
45. [Claude Help Center: Use the Claude Agent SDK with your Claude plan](https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan) — Agent SDK available on Pro/Max/Team/Enterprise plans starting June 15, 2026

## License

MIT
