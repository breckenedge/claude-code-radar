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
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development. CLI moved to native binaries (v2.1.105+, Apr 2026); npm install fully deprecated. Opus 5 is now the default model on Max/Enterprise, Sonnet 5 on Pro/Team Standard, both with a native 1M-token context window. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude is now available as a Copilot agent for Pro+ and Enterprise customers (Feb 2026). |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. |
| [Cowork](https://claude.com/blog/cowork-research-preview) | Trial | Claude Code adapted for general-purpose computing. Expanded from a desktop-only research preview to web and mobile (Jul 2026, Max subscribers first), with cloud-hosted sessions that keep running device-off and shared memory with Claude chat (Aug 2026). Pro access not yet announced. |
| [Claude Code Security](https://www.anthropic.com/news/claude-code-security) | Assess | AI-driven vulnerability scanning, now shipped as the "Claude Security" plugin — a multi-agent scan that turns findings into patches you apply yourself. Progressed from a Feb 2026 research preview to public beta for Enterprise (Apr–May 2026); still no GA date or Team/Max/Pro access as of Aug 2026. |
| [Claude Code Review](https://www.anthropic.com/news/claude-code-review) | Trial | Automated PR review has expanded well beyond the March 2026 Team/Enterprise-only preview: `/code-review` runs locally as a background subagent on any plan, and `/ultrareview` (public research preview, Apr 2026) spawns a cloud fleet of bug-hunting agents, invocable from the CLI, CI (`claude ultrareview`), or a GitHub PR. Customizable via CLAUDE.md/REVIEW.md. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard by Anthropic. Widely adopted across editors and platforms. `claude mcp login`/`logout` (Jun 2026) authenticate servers from the shell; published Artifacts can now call a viewer's own MCP connectors to pull live data (Jul 2026). MCP Elicitation and the MCP Apps extension continue to mature under Linux Foundation governance. |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Adopt | Ecosystem has scaled well past early-adopter territory: 2,500+ marketplaces, 4,000+ skills, 770+ MCP servers by mid-2026. Plugins now load from `.zip` archives or `--plugin-url`; enterprises can pin allowed marketplaces with `blockedMarketplaces`, and marketplace name/text hardening shipped after early trust issues. |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, blessed plugins. Grown to 100+ signed/reviewed plugins by mid-2026; still a small, higher-quality-bar subset of the wider marketplace. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace. Discovery UX maturing. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. `/skills` now supports type-to-filter across thousands of community skills; already integrated into VS Code extensions and plugin ecosystem. |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. Anthropic proposed splitting SDK/`-p`/GitHub Action usage into separate monthly credits (announced May 2026) but paused the change the day it was due to take effect (Jun 15, 2026) after developer pushback — pricing model remains unsettled. |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Smaller, less vetted. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Supports four handler types: command (shell), HTTP, prompt (LLM-evaluated decision), and agent (subagent with tools). Hooks can now read the active effort level via `effort.level`/`$CLAUDE_EFFORT` (Apr 2026); conditional `if` hooks shipped Mar 2026. Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Adopt | Spawning sub-agents for parallel work is now core to how Claude Code operates: subagents run in the background by default (Jun 2026), can spawn their own subagents up to five levels deep, and dynamic workflows can orchestrate dozens to hundreds of subagents from a script Claude writes (May 2026). Fork mode — handing a side task to a subagent that inherits the full conversation — is on by default in interactive sessions (Aug 2026). |
| Git Worktree Isolation | Adopt | Running agents in isolated git worktrees is now the settled default for parallel work. GitLab merge request URLs work with `--worktree` (Aug 2026); `worktree.baseRef` and `worktree.sparsePaths` give fine control for monorepos. Eliminates file conflicts between parallel agents. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively via `claude -p`. Growing adoption but auth is a pain point — `setup-token` scoping is [buggy](https://github.com/anthropics/claude-code/issues/23703), most teams fall back to API keys in CI. Anthropic's aborted attempt to move `-p`/SDK/Action usage to separate billing credits (paused Jun 15, 2026) shows this pattern's cost model is still being worked out. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. Auto mode's classifier-based approvals (see below) are starting to change what "review before it runs" looks like for routine actions, but review of substantive changes before commit remains standard. |
| /compact & Context Window Management | Adopt | Essential power-user technique. The 1M-token context default reduces how often you hit the wall, but doesn't eliminate it. The Rewind menu can now "Summarize up to here" (May 2026) as an alternative to a full `/compact`. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. Self-hosted environments (Aug 2026, Team/Enterprise beta) offer a cloud-hosted alternative that runs Claude Code sessions on infrastructure your org controls. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. |
| Model Switching Strategies | Trial | Haiku 4.5 for quick passes, Sonnet 5 for daily work (default on Pro/Team Standard/Enterprise, native 1M context), Opus 5 for hard problems (default on Max, adaptive low/medium/high effort). `fallbackModel` (Jun 2026) configures up to three automatic fallbacks. A new "Mythos" tier (Fable 5, restricted Mythos 5) sits above Opus for frontier work. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Trial | Tracking what agents changed and why is now built in: `/rewind` can resume from before `/clear` (Jun 2026), session recap shows what happened while a terminal was unfocused (Apr 2026), and the Rewind menu can summarize earlier context. Still lacks team-wide audit tooling beyond the individual session. |
| Voice Mode | Trial | Native `/voice` command (push-to-talk, 20 languages). Early builds had transcription and Windows-specific bugs (startup activation, WebSocket drops, CRLF handling); most were fixed through 2026 and rollout has broadened across plans. Community MCP alternatives are no longer clearly more stable. |
| [Scheduled Tasks (/loop)](https://code.claude.com/docs/en/scheduled-tasks) | Trial | `/loop` now self-paces when you omit an interval, and `/goal` (May 2026) keeps Claude working across turns until a completion condition holds. For persistent scheduling, Routines (Apr 2026) fire templated cloud agents from a schedule, GitHub event, or API call — no terminal or headless `claude -p` babysitting required. The session-scoped, 3-day-expiry limitation that held this at Assess is largely resolved. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Assess | Claude automatically saves useful context to persistent memory across sessions. Managed with the `/memory` command. Cowork now shares the same memory store as chat (Aug 2026), signaling it's becoming more central — but behavior is still automatic, so keep reviewing and pruning to avoid stale context drift. |
| [Auto Mode](https://code.claude.com/docs/en/whats-new/2026-w13) | Trial | Classifier-based permission mode replacing per-action approval prompts with background safety checks — the middle ground between "approve everything" and `--dangerously-skip-permissions`. Shipped as a research preview in Mar 2026, expanded to Bedrock/Vertex/Foundry and the Pro plan through Q2, and became the **default permission mode** for new Pro/Max/Team sessions on Aug 14, 2026. Blocks destructive git commands and transcript tampering, asks before `rm -rf` on unresolved variables. Very new as a default — worth watching for misclassifications. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Trial | Multi-agent support as of Feb 2026, Focus view added Aug 2026. Promising but still rough in places — extension host crashes, memory issues, terminal-to-UI handoff. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Beta quality. Functional but less mature than VS Code integration. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Adopt | CI/CD integration for automated code review and PR generation. GA since Feb 2026; `claude ultrareview` (Apr 2026) brings the cloud bug-hunting fleet into CI and scripts. Stable enough to be a default choice for new CI workflows. |
| [Xcode + Claude (via MCP)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Assess | Apple's MCP adoption in Xcode 26.3. Very early but significant for Claude Code users building native apps. |
| Cloud-hosted Agent Fleets | Trial | Running multiple Claude Code instances in the cloud for parallel tasks now has real infrastructure: Claude Managed Agents (launched Apr 8, 2026) offers hosted, sandboxed agent execution for products; Claude Code on the web got a sessions-sidebar redesign (Apr 2026); Routines and self-hosted environments (Aug 2026, Team/Enterprise beta) round out the picture. Still maturing but no longer speculative for small teams. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Sonnet 5 is now the default model on Pro/Team Standard/Enterprise; Opus 5 is default on Max, with adaptive effort control to trade cost for capability. The 50% weekly-limit increase has been repeatedly extended and is in effect through Aug 31, 2026, with Anthropic exploring making it permanent. A plan to move Agent SDK/`-p`/Action usage to separate credits was paused Jun 15, 2026 — watch for it resurfacing. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation from Claude Code. Reached general availability on all direct Anthropic plans (Jun 2026), and the standalone Chrome extension followed with its own GA (Aug 26, 2026) — every action runs through a safety classifier. No longer beta or paid-plan-only; still young as a GA product. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-08-31). Blips move between rings as the ecosystem evolves.

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
30. [Claude Code "What's New" weekly digest](https://code.claude.com/docs/en/whats-new) — Primary source for the Mar–Aug 2026 feature timeline: auto mode's rollout to default, subagent/worktree maturation, /loop → Routines/goal, voice mode fixes, Claude in Chrome GA, and more
31. [Anthropic: Introducing Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) — Jun 30, 2026 release; new default model for Pro/Team Standard/Enterprise, informing Model Switching Strategies and Subscriptions updates
32. [Anthropic: Introducing Claude Opus 5](https://www.anthropic.com/news/claude-opus-5) — Jul 24, 2026 release; new default model for Max, informing Model Switching Strategies and Subscriptions updates
33. [TechCrunch: Claude Cowork expands to mobile and web](https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/) — Jul 2026 Cowork web/mobile expansion informing the Cowork ring move to Trial
34. [The New Stack: Anthropic pauses Claude Agent SDK subscription change](https://thenewstack.io/anthropic-pauses-claude-agent-sdk-subscription-change/) — Jun 15, 2026 pause of the proposed SDK/`-p`/Action credit split, informing Claude Agent SDK, Headless/CI Mode, and Subscriptions notes
35. [Claude blog: Claude in Chrome is generally available](https://claude.com/blog/claude-in-chrome-generally-available) — Aug 26, 2026 GA announcement informing the Claude in Chrome ring move to Trial
36. [Claude Code Week 32 what's new (Aug 3–7, 2026)](https://code.claude.com/docs/en/whats-new/2026-w32) — Auto mode becomes the default permission mode; self-hosted environments beta
37. [Claude Code Week 13 what's new (Mar 23–27, 2026)](https://code.claude.com/docs/en/whats-new/2026-w13) — Auto mode's research-preview origin, basis for the new Auto Mode blip

## License

MIT
