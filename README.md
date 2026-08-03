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
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development, now shipping weekly. npm install deprecated; native installer (no Node.js/npm dependency) is recommended. Claude Sonnet 5 became the default model for Pro/Team Standard/Enterprise seats (June 30, 2026); Claude Opus 5 shipped July 24, 2026 (v2.1.219) as the new default Opus with 1M context and 2.5x-faster fast mode. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude is now available as a Copilot agent for Pro+ and Enterprise customers (Feb 2026). |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. |
| [Cowork](https://claude.com/blog/cowork-research-preview) | Trial | Claude Code adapted for general-purpose computing. Reached general availability April 9, 2026 with RBAC, group spend limits, and analytics for Enterprise. Expanded beyond desktop to web and mobile for Max subscribers (July 7, 2026), running as remote sessions on Anthropic's servers that keep working with no device online. Real adoption signal, but still a distinct product from core Claude Code. |
| [Claude Code Security](https://www.anthropic.com/news/claude-code-security) | Assess | AI-driven vulnerability scanning, now also exposed as a lightweight `security-guidance` plugin that reviews changes for vulnerabilities live as Claude works (May 2026). The full scanning product remains a research preview/beta limited to Team and Enterprise plans — not yet in most individual workflows. |
| [Claude Code Review](https://www.anthropic.com/news/claude-code-review) | Trial | Multi-agent PR review, launched March 9, 2026. Since expanded well beyond the original GitHub-only, Team/Enterprise-gated preview: `/code-review` (May 2026) is a bundled command available to any user for local correctness-bug review, and `claude ultrareview` / `/ultrareview` (April 2026) brings the same cloud bug-hunting fleet to CI and scripts. The enterprise GitHub PR automation is still Team/Enterprise-only, but the local/CLI variants are broadly usable now. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard by Anthropic. Widely adopted across editors and platforms. `claude mcp login`/`logout` (June 2026) authenticate servers from the shell instead of the interactive menu. MCP Elicitation continues to mature; broader 2026 roadmap (stateless Streamable HTTP, enterprise auth/audit, Linux Foundation governance) still in progress. |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Adopt | Marketplace ecosystem has grown well past 9,000 plugins into the tens of thousands. Discovery UX has matured: plugin details now show a context-cost estimate, last-updated date, and a "will install" manifest of commands/agents/hooks/MCP servers before you install. Plugins can now load from `.zip` archives or a URL (`--plugin-dir`, `--plugin-url`, May 2026), removing the last friction for one-off distribution. |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, blessed plugins. Smaller but higher quality bar. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace. Discovery UX maturing. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. Already integrated into VS Code extensions and plugin ecosystem; skill count has grown into the tens of thousands. |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. Renamed from "Claude Code SDK" to reflect broader agent-building ambitions beyond coding. New operational wrinkle: since June 15, 2026, SDK usage and headless `claude -p` runs on subscription plans draw from a separate monthly Agent SDK credit pool ($20 Pro / $100 Max 5x / $200 Max 20x) rather than interactive limits — worth budgeting for before relying on it in CI. Anthropic's Managed Agents (scheduler, rubric-graded "outcomes", multi-agent orchestration; public beta since May–June 2026) are built on top of this SDK. |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Smaller, less vetted. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Now supports four handler types: command (shell), HTTP (POST JSON to a URL), prompt (LLM-evaluated decision), and agent (subagent with tools). HTTP hooks enable webhook integrations without shell scripts. Recent additions (v2.1.76): `PostCompact` fires after context compaction; `Elicitation`/`ElicitationResult` hooks can intercept and override MCP server input requests. Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. Sub-agents can now spawn their own sub-agents, capped at five levels deep (June 2026); `fallbackModel` (June 2026) configures up to three fallback models tried in order for resilience. Dynamic workflows (May 2026) let Claude write and run a script that orchestrates dozens to hundreds of subagents. `/usage` now breaks spend down by skill, subagent, plugin, and MCP server, making cost attribution far easier than before. Native git worktree isolation (v2.1.49+) significantly reduces conflicts when running parallel agents — see Git Worktree Isolation below. |
| Git Worktree Isolation | Adopt | Running agents in isolated git worktrees with `--worktree` flag or `isolation: worktree` in agent definitions. Ships natively as of v2.1.49 (Feb 2026). Eliminates file conflicts between parallel agents; `worktree.baseRef` (May 2026) and `worktree.sparsePaths` (March 2026) round out the feature for monorepos and non-default branches. Stable for months now and the uncontested default for any multi-agent workload. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively via `claude -p`. Growing adoption but auth is a pain point — `setup-token` scoping is [buggy](https://github.com/anthropics/claude-code/issues/23703), most teams fall back to API keys in CI. Since June 15, 2026, `-p` runs on subscription plans also draw from a separate Agent SDK credit pool rather than interactive plan limits — a new budgeting consideration for CI usage. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. |
| /compact & Context Window Management | Adopt | Essential power-user technique. Everyone hits context limits; knowing when to compact is a core skill. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. `sandbox.network.strictAllowlist` (v2.1.219, July 2026) extends this to the network: it rejects sandboxed commands' requests to non-allowlisted hosts outright instead of prompting — though it currently only works locally, not in cloud execution environments. |
| Model Switching Strategies | Trial | Mixing models to balance cost and quality. The lineup has moved fast: Opus 4.7 (Apr) → Opus 4.8 (May) → Claude Sonnet 5 as the new default for Pro/Team/Enterprise (June 30) → Claude Opus 5 (July 24), alongside Haiku 4.5 for cheap passes. `/effort` (xhigh recommended for most coding, Apr 2026) and per-model "fast mode" pricing add a second axis beyond model choice. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Trial | Tracking what agents changed and why. The tooling gap called out here previously has narrowed: `claude agents` (Agent view, May 2026) gives one screen showing what's running, blocked, or done across sessions, and `/usage` (May 2026) breaks spend down by skill, subagent, plugin, and MCP server. Still short of full audit trails, but no longer immature. |
| Voice Mode | Assess | Native `/voice` command (hold Space to record) shipped in v2.1.71 (Mar 2026) with gradual rollout starting at 5% of users. Incremental bug fixes have landed since (push-to-talk modifier combos, Windows WebSocket upgrade failures), but there's no evidence of a broader GA rollout as of Aug 2026. Community MCP alternatives remain more stable today. |
| [Scheduled Tasks (/loop & Routines)](https://code.claude.com/docs/en/scheduled-tasks) | Trial | `/loop` matured significantly: self-pacing when you omit the interval (Apr 2026), the `Monitor` tool for event-driven waits instead of polling, and expiry extended from 3 to 7 days. Critically, the earlier "no persistent scheduling" gap is now addressed natively: Routines (Apr 2026) run templated agents on Anthropic-managed cloud infrastructure on a schedule/GitHub event/API call with no machine required, complementing local `/loop` and Desktop scheduled tasks. Three clearly documented tiers (Cloud/Desktop/`/loop`) make this a solid choice now, not just an experiment. |
| Auto Mode | Trial | A classifier-based permission-prompt replacement: safe actions run automatically, risky ones still get blocked, sitting between approve-everything and `--dangerously-skip-permissions`. Entered research preview in March 2026 (v2.1.83) and had, by mid-2026, become the default on Amazon Bedrock, Google Cloud's Agent Platform, and Microsoft Foundry, plus an opt-in on the Pro plan (May 2026) and Opus 4.7/4.8. Also gained hard-deny rules and blocks on destructive git commands / transcript tampering. Real adoption in a few months, but still new enough to watch for edge cases. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Assess | Claude automatically saves useful context to persistent memory across sessions (v2.1.59+). Managed with the `/memory` command. Reduces context setup overhead for recurring projects. Behavior is automatic — review and prune regularly to avoid stale context drift. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Trial | Multi-agent support as of Feb 2026. Promising but buggy — extension host crashes, memory issues, rough terminal-to-UI handoff. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Beta quality. Functional but less mature than VS Code integration. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Trial | CI/CD integration for automated code review and PR generation. Reached v1.0 GA (Feb 2026); worth adopting for greenfield CI workflows. A researcher-reported flaw allowing a malicious issue to hijack repositories was patched within 4 days (Jan 2026, hardened through spring in v1.0.94), and a separate prompt-injection issue was mitigated in Claude Code 2.1.128 (May 2026) — both fixed promptly, but a reminder to pin versions and review workflow permissions. |
| [Xcode + Claude (via MCP)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Trial | Apple's MCP adoption, introduced in Xcode 26.3, has continued through Xcode 27 (mid-2026) with a settings-panel toggle for the MCP server. Visual verification via Xcode Previews lets Claude capture and iterate on SwiftUI screenshots. Sustained investment across two Xcode releases moves this past "very early" for native-app developers using Claude Code. |
| Cloud-hosted Agent Fleets | Trial | Running multiple Claude Code instances in cloud for parallel tasks. No longer just "coming up fast": Anthropic's Managed Agents (scheduler, rubric-graded outcomes, multi-agent orchestration) and Routines (cloud-scheduled agents) both reached public beta by June 2026, and Cowork gained remote web/mobile sessions in July. Concrete infrastructure now exists, though it spans several distinct products worth evaluating separately. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Individual devs and small teams pay through Max or Pro plans. Claude Code is included with every Team plan standard seat. Claude Sonnet 5 (June 30, 2026) became the default model for Pro/Team Standard/Enterprise seats with a native 1M-token context window; Claude Opus 5 (July 24, 2026) is the new default Opus at the same $5/$25 per-MTok pricing as its predecessor. Since June 15, 2026, Agent SDK/headless usage draws from a separate monthly credit pool rather than interactive plan limits. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation from Claude Code via the Claude Chrome extension. Reached general availability the week of June 29–July 3, 2026, and is now available on every paid Claude plan (previously Max-only). Claude can navigate pages, click, fill forms, read console logs, and capture screenshots directly from the CLI or VS Code. Interesting for build-test-debug loops. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-08-03). Blips move between rings as the ecosystem evolves.

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
30. [Anthropic: Introducing Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) — Sonnet 5 default-model announcement (June 30, 2026), informing Claude Code, Model Switching, and Subscriptions notes
31. [9to5Mac: Anthropic upgrades Claude with new Opus 5 model](https://9to5mac.com/2026/07/24/anthropic-upgrades-claude-with-new-opus-5-model-details-here/) — Opus 5 launch details (July 24, 2026)
32. [Claude Code Docs: What's new](https://code.claude.com/docs/en/whats-new) — Weekly digest (Weeks 13–29, 2026) informing Auto Mode, Multi-agent Delegation, Session Replay, Plugin System, and Cloud Agent Fleet updates
33. [Claude Code Docs: Scheduled tasks](https://code.claude.com/docs/en/scheduled-tasks) — Current `/loop`/Routines/Desktop scheduling comparison, 7-day expiry, and self-pacing, informing Scheduled Tasks blip update
34. [TestingCatalog: Anthropic launches Claude Cowork in General Availability](https://www.testingcatalog.com/anthropic-launches-claude-cowork-in-general-availability/) — Cowork GA (April 9, 2026), informing move to Trial
35. [TechCrunch: Claude Cowork expands to mobile and web](https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/) — Cowork remote session expansion (July 7, 2026)
36. [9to5Mac: Anthropic updates Claude Managed Agents with three new features](https://9to5mac.com/2026/05/07/anthropic-updates-claude-managed-agents-with-three-new-features/) — Managed Agents Outcomes (rubric grading) public beta
37. [Tech Times: Claude Managed Agents add cron schedules and credential vaults](https://www.techtimes.com/articles/318163/20260610/claude-managed-agents-add-cron-schedules-credential-vaultsanthropic-beta-puts-agents-autopilot.htm) — Managed Agents scheduler public beta (June 2026), informing Cloud-hosted Agent Fleets update
38. [Totalum: Claude Agent SDK Credits in 2026](https://www.totalum.app/blog/claude-agent-sdk-credits-2026) — Agent SDK credit billing change (June 15, 2026), informing Claude Agent SDK and Headless/CI Mode notes
39. [crunchybagel: Enabling the Xcode 27 MCP Server in Claude Code](https://crunchybagel.com/enabling-the-xcode-27-mcp-server-in-claude-code/) — Continued Xcode MCP integration, informing move to Trial
40. [The Hacker News: Claude Code GitHub Action flaw let one malicious issue hijack repositories](https://thehackernews.com/2026/06/claude-code-github-action-flaw-let-one.html) — GitHub Action security incident and fix, informing GitHub Actions security note
41. [Microsoft Security Blog: Securing CI/CD in an agentic world](https://www.microsoft.com/en-us/security/blog/2026/06/05/securing-ci-cd-in-agentic-world-claude-code-github-action-case/) — Additional context on the Claude Code GitHub Action security incident
42. [DevelopersIO: Claude Code v2.1.219 major updates](https://dev.classmethod.jp/en/articles/20260725-cc-updates-v2-1-219/) — Opus 5 default and `sandbox.network.strictAllowlist` (v2.1.219, July 24, 2026), informing Claude Code and Context Hygiene notes

## License

MIT
