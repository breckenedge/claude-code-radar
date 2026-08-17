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
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development. npm install deprecated; native installer (no Node.js/npm dependency) is now recommended — run `claude install` or use the install script. As of Aug 14, 2026, **Auto Mode** (see Techniques & Patterns) is the default permission mode for new sessions on Pro/Max/Team, replacing per-action approval prompts. Claude Sonnet 5 is now the default model for Pro/Team Standard; Claude Opus 5 is default for Max/Team Premium. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude is now available as a Copilot agent for Pro+ and Enterprise customers (Feb 2026); Copilot now offers Claude Opus 5, Sonnet 5, and Haiku 4.5 as GA model options across github.com, the IDEs, and Copilot CLI. |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. Also ships alongside Claude Agent integration in Xcode 26.3 as a competing agentic option. |
| [Cowork](https://claude.com/blog/cowork-web-mobile) | Trial | Claude Code adapted for general-purpose computing. Expanded from a desktop-only app (Jan 2026) to web and mobile for Max subscribers (Jul 7, 2026), with background cloud execution and cross-device handoff. Still Max-only. |
| [Claude Code Security](https://www.anthropic.com/news/claude-code-security) | Trial | AI-driven vulnerability scanning for Claude Code. The lightweight `security-guidance` plugin (hook-based pattern matching, no model calls) reached general availability and is free on all plans (May 27, 2026). The heavier `Claude Security` plugin (multi-agent scanner that traces data flows and turns findings into patches) is in public beta (Jul 21, 2026). |
| [Claude Code Review](https://www.anthropic.com/news/claude-code-review) | Assess | Multi-agent parallel PR review: spawns agents in parallel to check logic errors, boundary conditions, API misuse, auth flaws, and convention violations. A false-positive filter attempts to disprove each finding before posting inline GitHub comments. ~20 min review time, ~$15–25/review. Research preview for Team and Enterprise (GitHub only) since March 9, 2026. As of Aug 2026, still in beta — no GA announcement found. Distinct from the local `/code-review` slash command (see Techniques & Patterns), which runs as a background subagent inside a session. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard by Anthropic. Widely adopted across editors and platforms. MCP Elicitation (v2.1.76, March 2026) enables servers to request structured user input mid-task via an interactive dialog. Anthropic donated MCP to the newly formed Linux Foundation Agentic AI Foundation (Dec 2025). The 2026-07-28 spec release added Multi Round-Trip Requests (replacing server-initiated sampling/elicitation with a stateless request/response pattern) and made MCP Apps — sandboxed interactive HTML UIs returned by tools — an official extension. |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Trial | 9,000+ plugins across marketplaces. API still evolving. |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, blessed plugins. Smaller but higher quality bar. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace. Discovery UX maturing. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. Already integrated into VS Code extensions and plugin ecosystem. |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. For building custom agents programmatically. Microsoft Agent Framework integration. Managed Agents (announced at Code with Claude 2026, May) added reflective "Dreaming" memory consolidation between sessions, self-grading "Outcomes" evaluation against a written rubric, and native multi-agent orchestration; early enterprise adopters (Harvey, Wisedocs) report large gains, though these are vendor-cited figures. |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Smaller, less vetted. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Now supports four handler types: command (shell), HTTP (POST JSON to a URL), prompt (LLM-evaluated decision), and agent (subagent with tools). HTTP hooks enable webhook integrations without shell scripts. Recent additions (v2.1.76): `PostCompact` fires after context compaction; `Elicitation`/`ElicitationResult` hooks can intercept and override MCP server input requests. Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. Native git worktree isolation (v2.1.49+) significantly reduces conflicts when running parallel agents — see Git Worktree Isolation below. Nested sub-agents (5-level cap) shipped June 2026; "dynamic workflows" (Opus 4.8, May 2026) let Claude orchestrate dozens to hundreds of subagents from a self-written script for codebase-scale migrations. |
| Git Worktree Isolation | Adopt | Running agents in isolated git worktrees with `--worktree` flag or `isolation: worktree` in agent definitions. Ships natively as of v2.1.49 (Feb 2026). Eliminates file conflicts between parallel agents; `WorktreeCreate`/`WorktreeRemove` hooks extend support to non-git VCS. `ExitWorktree` tool added (v2.1.72) for clean session teardown. `worktree.sparsePaths` setting (v2.1.76) enables git sparse-checkout integration for large monorepos; `worktree.baseRef` and GitLab merge-request URL support followed by mid-2026. Stable for ~6 months of continuous refinement — the right default for any multi-agent workload. |
| Auto Mode | Trial | Classifier-based permission handling that approves or blocks tool calls without per-action prompts, catching destructive or out-of-bounds actions and asking before things like `rm -rf` or transcript tampering. Research preview from Mar 23, 2026; became the **default** permission mode for new sessions on Pro/Max/Team on Aug 14, 2026 (Enterprise/API/cloud platforms remain opt-in for now). Anthropic cites an internal study of 1,053 testers where auto mode caught 89% of harmful actions vs. 13.6% for manual human review. Days old as the default at this scale — worth watching for real-world failure modes. |
| `/code-review` Command | Trial | Built-in slash command (shipped ~May 2026) that runs a correctness-focused review as a background subagent within a session — local and fast, distinct from the cloud-hosted Claude Code Review PR bot. Complements `/ultrareview` (Apr 2026), a cloud fleet of bug-hunting agents that cross-verifies findings before reporting them. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively via `claude -p`. Growing adoption but auth is a pain point — `setup-token` scoping is [buggy](https://github.com/anthropics/claude-code/issues/23703), most teams fall back to API keys in CI. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. Auto Mode's Aug 2026 default rollout has sparked debate about whether classifier-based approval reduces the need for manual review or simply shifts where humans should focus attention. |
| /compact & Context Window Management | Adopt | Essential power-user technique. Everyone hits context limits; knowing when to compact is a core skill. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. |
| Model Switching Strategies | Trial | Haiku 4.5 for quick passes, Sonnet 5 for daily work (default for Pro/Team Standard as of Jun 2026), Opus 5 for hard problems (default for Max/Team Premium as of Jul 2026). Mixing models to balance cost and quality. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Assess | Tracking what agents changed and why. Important for team accountability. Tooling still immature; Artifacts (see below) helps with sharing session output but isn't a purpose-built audit trail. |
| Voice Mode | Assess | Native `/voice` command (hold Space to record, Whisper STT) shipped in v2.1.71 (Mar 2026) with gradual rollout to 5% of users. Has known bugs in early builds — transcription pipeline disabled in some releases. Community MCP alternatives (e.g., VoiceMode MCP) are more stable today. No further maturity signals found through Aug 2026 — still too early for daily use. |
| [Scheduled Tasks (/loop)](https://code.claude.com/docs/en/scheduled-tasks) | Trial | `/loop` command (v2.1.71, Mar 2026) runs prompts on a cron schedule within a session — turns Claude Code into a background worker. Session-scoped (stops when terminal closes), max 50 tasks per session, 3-day auto-expiry; gained self-pacing (Apr 2026, omit the interval and Claude sets its own cadence). Now pairs with cloud-side **Routines** (Apr 2026) — templated agents triggered by cron, GitHub events, or API calls — for persistent scheduling without needing GitHub Actions. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Trial | Claude automatically saves useful context to persistent memory across sessions (v2.1.59+). Managed with the `/memory` command. Reduces context setup overhead for recurring projects. ~8 months in daily use with no major regressions reported; behavior is still automatic — review and prune regularly to avoid stale context drift. |
| Artifacts | Assess | Beta (Team/Enterprise, Jun 2026) that turns a Claude Code session's output into a live, shareable page on claude.ai. Gained MCP-connector calling and public sharing links by Jul 2026. Early — mainly useful for demoing agent output to non-technical stakeholders, not yet an audit trail. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Trial | Multi-agent support as of Feb 2026. Promising but buggy — extension host crashes, memory issues, rough terminal-to-UI handoff. Focus view and sidebar session groups added Aug 2026. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Beta quality. Functional but less mature than VS Code integration. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Trial | CI/CD integration for automated code review and PR generation. Reached v1.0 GA (Feb 2026) with breaking changes from beta; migration guide available. Simplified configuration, automatic mode detection, structured JSON outputs. A prompt-injection vulnerability chain (authorization bypass via `[bot]`-suffixed actors, combined with indirect prompt injection) was disclosed June 1, 2026 by independent researchers; fixed ahead of disclosure in Claude Code v2.1.128 (May 5, 2026). Pin to `claude-code-action` v1.0.94+ and `@anthropic-ai/claude-code` v1.0.93+ before using in CI. |
| [Xcode + Claude (via MCP)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Trial | Apple's native Claude Agent SDK/MCP integration in Xcode 26.3 (shipped Feb 2026), now roughly six months in production alongside a competing OpenAI Codex integration. Includes Xcode Previews screenshot capture for SwiftUI iteration. Significant for Claude Code users building native Apple apps. |
| Cloud-hosted Agent Fleets | Trial | Running multiple Claude Code instances in the cloud for parallel tasks. Matured quickly through 2026: Ultraplan (cloud plan drafting, Apr), `/ultrareview` (verified-findings review fleet, Apr), dynamic workflows (dozens–hundreds of subagents for large migrations, May), and self-hosted environments (public beta, Aug 6, 2026, Team/Enterprise — run cloud sessions on customer-controlled infrastructure). Still requires real orchestration know-how. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Individual devs and small teams pay through Max or Pro plans. As of Jan 2026, Claude Code is included with every Team plan standard seat ($20/month). Claude Sonnet 5 (Jun 30, 2026) became the default model for Pro and Team Standard seats; Claude Opus 5 (Jul 24, 2026) became the default Opus model for Max and Team Premium seats — both with native 1M-token context windows. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation from Claude Code via the Claude Chrome extension. Claude can navigate pages, click, fill forms, read console logs, and capture screenshots directly from the CLI or VS Code. Works with any site you're already logged into. Reached general availability on all direct Anthropic paid plans (~late Jun 2026), up from beta. Still Chrome/Edge-only, no free tier. Interesting for build-test-debug loops. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-08-17). Blips move between rings as the ecosystem evolves.

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
30. [Claude Code "What's New" weekly digest](https://code.claude.com/docs/en/whats-new) — Primary source for the Mar–Aug 2026 update: Auto Mode rollout and default, Sonnet 5/Opus 5 model defaults, Claude in Chrome GA, Artifacts beta, self-hosted environments, `/code-review` command, worktree feature additions
31. [TechCrunch: Anthropic is turning Claude Code's auto mode on by default](https://techcrunch.com/2026/08/09/anthropic-is-turning-claude-codes-auto-mode-on-by-default/) — Aug 9, 2026; informs Auto Mode blip and Aug 14 default rollout
32. [InfoWorld: Anthropic makes Claude Code's auto mode default for paid users](https://www.infoworld.com/article/4207959/anthropic-makes-claude-codes-auto-mode-default-for-paid-users.html) — Additional Auto Mode rollout detail
33. [Anthropic: Introducing Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) — Jun 30, 2026 model launch, informing Claude Code and subscription blip updates
34. [9to5Mac: Anthropic upgrades Claude with new Opus 5 model](https://9to5mac.com/2026/07/24/anthropic-upgrades-claude-with-new-opus-5-model-details-here/) — Jul 24, 2026 model launch details
35. [MacRumors: Anthropic launches Claude Sonnet 5](https://www.macrumors.com/2026/06/30/anthropic-claude-sonnet-5/) — Additional Sonnet 5 launch confirmation
36. [The Hacker News: Claude Code GitHub Action flaw let one malicious issue hijack repositories](https://thehackernews.com/2026/06/claude-code-github-action-flaw-let-one.html) — Jun 2026 prompt-injection disclosure informing GitHub Actions blip security note
37. [Microsoft Security Blog: Securing CI/CD in an agentic world — Claude Code GitHub Action case](https://www.microsoft.com/en-us/security/blog/2026/06/05/securing-ci-cd-in-agentic-world-claude-code-github-action-case/) — Corroborating analysis of the claude-code-action vulnerability
38. [MarkTechPost: Anthropic releases Claude Security plugin for Claude Code in beta](https://www.marktechpost.com/2026/07/22/anthropic-releases-claude-security-plugin-for-claude-code-in-beta-a-multi-agent-vulnerability-scanner-that-runs-in-your-terminal/) — Jul 21, 2026 public beta, informing Claude Code Security blip update
39. [Help Net Security: Claude now reviews and fixes vulnerabilities as you write code](https://www.helpnetsecurity.com/2026/05/27/anthropic-claude-code-security-guidance-plugin/) — May 27, 2026, security-guidance plugin reaching free GA on all plans
40. [Claude blog: Self-hosted environments for Claude Code](https://claude.com/blog/run-claude-code-sessions-on-your-own-compute) — Aug 6, 2026 public beta, informing Cloud-hosted Agent Fleets update
41. [TechCrunch: Claude Cowork expands to mobile and web](https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/) — Jul 7, 2026, informing Cowork blip's Assess → Trial move
42. [MCP Blog: 2026-07-28 spec release](https://blog.modelcontextprotocol.io/posts/2026-07-28/) — Multi Round-Trip Requests and MCP Apps extension, informing MCP blip update
43. [InfoQ: Code with Claude 2026 — Managed Agents](https://www.infoq.com/news/2026/05/code-with-claude/) — May 2026 conference announcements (Dreaming, Outcomes, multi-agent orchestration) informing Claude Agent SDK blip update
44. [9to5Mac: Apple announces agentic coding in Xcode with Claude Agent and Codex integration](https://9to5mac.com/2026/02/03/apple-announces-agentic-coding-in-xcode-with-claude-agent-and-codex-integration/) — Feb 3, 2026, informing Xcode + Claude blip's Assess → Trial move

## License

MIT
