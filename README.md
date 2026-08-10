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
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development, continuing rapid weekly release cadence (v2.1.7x in March 2026 → v2.1.22x by August). npm install path is fully deprecated in favor of the native installer. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude as a Copilot agent expanded from Pro+/Enterprise (Feb 2026) to Business/Pro (Feb 26, 2026), added model selection (Apr 14, 2026), and reached JetBrains IDEs via Copilot (Jun 22, 2026). |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. Still actively developed (v0.147+ as of Aug 2026) with its own subagents, hooks, and cloud tasks — worth pairing rather than picking one. |
| [Cowork](https://claude.com/blog/cowork-research-preview) | Assess | Claude Code adapted for general-purpose computing. Expanded from Max-only desktop app to Pro tier, and from desktop to web and mobile (Jul 7, 2026) so tasks persist and can be checked remotely. Still labeled research preview by Anthropic — real progress, but not yet a broad recommendation. |
| [Claude Code Security](https://www.anthropic.com/news/claude-code-security) | Trial | AI-driven vulnerability scanning built into Claude Code. Graduated from limited research preview (Feb 2026) to public beta for all Claude Enterprise customers (Apr 30, 2026), built on a newer Opus model; a plugin version is in beta for all Claude Code users. Team/Max access expected to follow. Reasons about code like a human security researcher rather than pure pattern-matching. |
| [Claude Code Review](https://code.claude.com/docs/en/code-review) | Assess | Multi-agent parallel PR review, still in research preview for Team/Enterprise (GitHub only; GitLab available only via self-hosted CI/CD, not the managed service). Matured: reviews now run as a background subagent, `@claude review always` is required to subscribe a PR to push-triggered re-reviews (changed Jul 2026, previously automatic), and `/ultrareview` escalates to a deeper cloud review. Separately, the local `/code-review` command (formerly `/simplify` pre-v2.1.147) is GA for everyone on every plan — run it in any session to review a diff without the GitHub App. ~20 min / $15–25 per managed review. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard, no longer Anthropic-only: donated to the Linux Foundation on Dec 9, 2025 as founding project of the new Agentic AI Foundation (co-founded with Block and OpenAI; platinum members include AWS, Google, Microsoft, Cloudflare, Bloomberg). A 2026-07-28 spec update ("MCP 2.0") introduced a stateless protocol core, an Extensions framework, and Tasks; single-round-trip Elicitation is being generalized into Multi Round-Trip Requests (SEP-2322). |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Trial | Marketplace and plugin count continue to grow; exact figures vary by source and are hard to verify, so treat any specific count with skepticism. Plugin/tunnel API headers kept churning through mid-2026 — still not a stable surface to build against. |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, blessed plugins. Now browsable in-CLI via `/plugin` → Discover (v2.1.143+). Smaller but higher quality bar than the open marketplace. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace. Added tech-stack-based recommendations and a local-setup diagnostic that flags broken installs and risky hooks. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. Published as a formal cross-vendor spec at [agentskills.io](https://agentskills.io) (Dec 18, 2025); by mid-2026 adopted well beyond Claude Code, including OpenAI Codex, GitHub Copilot, Cursor, and Gemini CLI. |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. For building custom agents programmatically. Gained multi-agent orchestration primitives and Managed Agents (Anthropic-hosted, sandboxed cloud execution, Apr 8, 2026); a planned separate credit pool for SDK usage was announced then paused before taking effect. |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Broadened in 2026 from a Claude-only registry into a cross-agent one (also installs for Cursor, Codex, OpenCode, and others) — worth watching whether it stays useful specifically for Claude Code users as its focus widens. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Supports four handler types: command (shell), HTTP, prompt (LLM-evaluated decision), and agent (subagent with tools). Continued additions through mid-2026 (e.g. a `DirectoryAdded` event); a hook-bypass bug affecting background agents was patched (v2.1.222, Aug 2026). Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. Anthropic shipped a native "Agent Teams" orchestrator (Feb 2026, experimental/off by default) where a lead session coordinates teammates via messaging and a shared task list — see also Claude Code Workflows below for the deterministic alternative. |
| Git Worktree Isolation | Adopt | Running agents in isolated git worktrees, natively supported since v2.1.49 (Feb 2026) and now the default `isolation: "worktree"` option on the Agent/Workflow tools. Extended to cover destructive git commands (v2.1.222, Aug 2026). Eliminates file conflicts between parallel agents and has become the standard default for any multi-agent workload, not just an emerging pattern. |
| Claude Code Workflows | Assess | A newer alternative to free-form multi-agent delegation: you write a small JS script (`agent()`/`pipeline()`/`parallel()`) that the harness executes deterministically, fanning out and synchronizing subagents on fixed control flow instead of turn-by-turn model decisions. Useful for review/research pipelines that need predictable structure; still new enough that best practices (sizing, cost control) are being worked out in the community. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively via `claude -p`. Growing adoption but auth is a pain point — `setup-token` scoping is [buggy](https://github.com/anthropics/claude-code/issues/23703), most teams fall back to API keys in CI. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. |
| /compact & Context Window Management | Adopt | Essential power-user technique. Everyone hits context limits; knowing when to compact is a core skill. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. |
| Model Switching Strategies | Trial | Haiku for quick passes, Sonnet for daily work, Opus for hard problems. A full "Claude 5" model wave (Sonnet 5, Opus 5, Fable 5, alongside the existing Haiku 4.5) shipped between June and July 2026, with Sonnet 5 now positioned as a near-Opus-quality default at lower cost — shifting more day-to-day work off Opus without much loss of quality. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Assess | Tracking what agents changed and why. Important for team accountability. Tooling still immature. |
| Voice Mode | Trial | Native `/voice` command (push-to-talk) reached general availability across Pro/Max/Team/Enterprise plans by late March 2026 and gained Opus/Sonnet model support (not just a lightweight STT pipeline) by July 2026. Terminal-only, no Agent SDK support, no always-listening/wake-word mode. Reliability has improved since the rocky March rollout but a tail of bugs is still being reported — worth using, not yet invisible. |
| [Scheduled Tasks (/loop, /schedule)](https://code.claude.com/docs/en/scheduled-tasks) | Assess | `/loop` remains session-scoped (stops when the terminal closes, multi-day auto-expiry) — that hasn't changed since March. Anthropic has since added `/schedule`, a separate persistent, cloud-run scheduler (1-hour minimum interval, survives machine shutdown) available on all paid plans, filling the gap the March radar entry flagged. Together they cover short-lived local loops and durable cloud automation; still early days for either as a default workflow. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Assess | Claude automatically saves useful context to persistent memory across sessions (v2.1.59+). Managed with the `/memory` command. Reduces context setup overhead for recurring projects. Behavior is automatic — review and prune regularly to avoid stale context drift. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Trial | Multi-agent support since Feb 2026, since extended to multi-chat (VS Code 1.128) for running and comparing parallel Claude sessions in one workspace, plus a "Focus View" (v2.1.221) that collapses tool-activity noise. Broad adoption signal, but no confirmation the earlier stability complaints (extension host crashes, memory issues) are fully resolved. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Still explicitly labeled Beta on the JetBrains Marketplace as of August 2026 — has not graduated past the VS Code integration in maturity. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Trial | CI/CD integration for automated code review and PR generation. Reached v1.0 GA (Feb 2026). A vulnerability disclosed in June 2026 let a malicious issue hijack the action on public repos; fixed in v1.0.94 — pin to that version or later before adopting. |
| [Xcode + Claude (via MCP)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Trial | Apple shipped Xcode 26.3 with a native Claude Agent SDK integration and its own MCP bridge (~20 tools via XPC), including screenshot-based iteration on SwiftUI Previews. Moved from announcement to a real, documented integration that Swift developers are using alongside third-party Xcode MCP servers. |
| Cloud-hosted Agent Fleets | Trial | Running multiple Claude Code instances in the cloud for parallel tasks. Anthropic shipped Claude Managed Agents (Apr 8, 2026, hosted/sandboxed execution) and a `self-hosted-runner` option (Aug 2026, Team/Enterprise), alongside community orchestrators (Ruflo, Claude Squad). Real infrastructure now exists for this rather than DIY scripting — still mostly an Enterprise/power-user pattern for small teams. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Individual devs and small teams pay through Max or Pro plans. Claude Code has been included with every Team plan standard seat since Jan 2026. Pricing shifted in 2026: Pro dropped from $20 to $17/month (annual billing) and Team Standard from $25 to $20/seat; Anthropic also doubled the 5-hour rate limit for Pro/Max/Team/Enterprise (May 2026). Opus 5 (Jul 24, 2026) carries the same per-token pricing as its predecessor. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation from Claude Code via the Claude Chrome extension. Claude can navigate pages, click, fill forms, read console logs, and capture screenshots directly from the CLI or VS Code. Expanded from Max-only to all paid plans by August 2026, though still beta with no free tier and Chrome desktop only. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-08-10). Blips move between rings as the ecosystem evolves.

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
30. [Claude Code releases](https://github.com/anthropics/claude-code/releases) and [CHANGELOG.md](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md) — v2.1.76–v2.1.226 (Mar–Aug 2026) version history informing worktree, hooks, Opus 5, self-hosted runner, and Workflows updates
31. [Claude Code Review docs](https://code.claude.com/docs/en/code-review) — Primary source confirming research-preview status, `/code-review` vs `/ultrareview`, GitLab CI/CD option, and the July 2026 `@claude review always` semantics change
32. [TechCrunch: Claude Code rolls out voice mode (Mar 3, 2026)](https://techcrunch.com/2026/03/03/claude-code-rolls-out-a-voice-mode-capability/) and [TechCrunch: Claude Cowork expands to web and mobile (Jul 7, 2026)](https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/) — Voice Mode and Cowork status updates
33. [Help Net Security: Anthropic's Claude Code Security exits research preview into public beta (May 4, 2026)](https://www.helpnetsecurity.com/2026/05/04/anthropic-claude-security-public-beta/) — Basis for moving Claude Code Security to Trial
34. [Anthropic: Introducing Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) — Claude 5 model wave (Sonnet 5, Opus 5, Fable 5) informing Model Switching Strategies update
35. [Linux Foundation: Formation of the Agentic AI Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation) and [MCP blog: 2026-07-28 spec update](https://blog.modelcontextprotocol.io/posts/2026-07-28/) — MCP governance donation and MCP 2.0 spec informing MCP blip update
36. [GitHub Changelog: Claude/Codex Copilot agent expansion](https://github.blog/changelog/2026-02-26-claude-and-codex-now-available-for-copilot-business-pro-users/) — Feb–Jun 2026 rollout to Business/Pro plans, model selection, and JetBrains IDEs informing Copilot blip update
37. [The Hacker News: Claude Code GitHub Action flaw (Jun 2026)](https://thehackernews.com/2026/06/claude-code-github-action-flaw-let-one.html) — Security fix requirement informing GitHub Actions blip update
38. [fatbobman.com: Xcode 26.3 + Claude Agent](https://fatbobman.com/en/posts/xcode-263-claude/) — Native Claude Agent SDK/MCP bridge in Xcode informing move to Trial
39. [Claude Managed Agents cloud deployment guide (Apr 2026)](https://www.aimagicx.com/blog/claude-managed-agents-cloud-deployment-guide-2026) — Basis for Cloud-hosted Agent Fleets move to Trial
40. [alexop.dev: Claude Code Workflows — deterministic multi-agent orchestration](https://alexop.dev/posts/claude-code-workflows-deterministic-orchestration/) — Basis for new Claude Code Workflows blip
41. [Claude Help Center: Get started with Claude in Chrome](https://support.claude.com/en/articles/12012173-get-started-with-claude-in-chrome) and [CloudZero: Claude pricing 2026](https://www.cloudzero.com/blog/claude-pricing/) — Claude in Chrome plan expansion and subscription pricing changes
42. [Agent Skills ecosystem report 2026 — Agentman Blog](https://agentman.ai/blog/agent-skills-ecosystem-report-2026) — Cross-vendor adoption of the Agent Skills spec (agentskills.io) informing Agent Skills blip update

## License

MIT
