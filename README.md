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
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development. npm install deprecated; native installer (no Node.js/npm dependency) is now recommended — run `claude install` or use the install script. Claude Opus 5 (July 24, 2026) is now the default model with a 1M-token context window; `ANTHROPIC_DEFAULT_MODEL` sets the default for new sessions. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude is now available as a Copilot agent for Pro+ and Enterprise customers (Feb 2026). Copilot CLI reached GA Feb 25, 2026; Copilot moved from premium requests to metered AI Credits (June 1, 2026). |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. Open source (Apache-2.0, Rust); moved to token-based credits pricing (April 2, 2026). |
| [Cowork](https://claude.com/blog/cowork-research-preview) | Trial | Claude Code adapted for general-purpose computing. Expanded from desktop-only to web and mobile in beta (July 2026), running as cloud-hosted sessions that keep working with no device online. Chrome side panel now opens a full Cowork session (Aug 2026). Still rough at the edges but real day-to-day usage is growing beyond early preview. |
| [Claude Code Security](https://www.anthropic.com/news/claude-code-security) | Assess | AI-driven vulnerability scanning built into Claude Code web interface. Research preview for Enterprise and Team plans. Reasons about code like a human security researcher, not just pattern-matching. Found 500+ vulnerabilities in production OSS. Very early — not in your workflow yet, but worth watching. |
| [Claude Code Review](https://www.anthropic.com/news/claude-code-review) | Assess | Multi-agent parallel PR review: spawns agents in parallel to check logic errors, boundary conditions, API misuse, auth flaws, and convention violations. A false-positive filter attempts to disprove each finding before posting inline GitHub comments. ~20 min review time, ~$15–25/review. Research preview for Team and Enterprise (GitHub only). Customizable via CLAUDE.md and a new REVIEW.md file. Launched March 9, 2026. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard by Anthropic. Widely adopted across editors and platforms. The 2026-07-28 spec revision shipped the roadmap items from earlier this year: a stateless Streamable HTTP core, stronger OAuth/OIDC-based authorization, and versioned Apps and Tasks extensions for interactive UI components. |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Trial | 9,000+ plugins across marketplaces. GitLab repos and merge requests joined GitHub as a supported marketplace/source (v2.1.232–233). API still evolving. |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, blessed plugins. Smaller but higher quality bar. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace. Discovery UX maturing. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. Already integrated into VS Code extensions and plugin ecosystem. Skills can now sync down from claude.ai (shown as `name@synced`) and coexist with local plugins of the same name. |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. For building custom agents programmatically. Microsoft Agent Framework integration. As of June 15, 2026, SDK and `claude -p` usage no longer counts against your Claude plan's usage limits — eligible plans get a separate monthly credit pool instead. |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Smaller, less vetted. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Now supports four handler types: command (shell), HTTP (POST JSON to a URL), prompt (LLM-evaluated decision), and agent (subagent with tools). HTTP hooks enable webhook integrations without shell scripts. Recent additions: `PostCompact` fires after context compaction; `Elicitation`/`ElicitationResult` hooks can intercept and override MCP server input requests; `DirectoryAdded` fires after `/add-dir` (v2.1.219). Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. Native git worktree isolation significantly reduces conflicts when running parallel agents — see Git Worktree Isolation below. Nested subagent depth increased to 3 by default; per-session caps (20 concurrent, 200 spawns) now exist to bound runaway fan-out. The productized version of this pattern is the Agent Teams blip below. |
| Git Worktree Isolation | Adopt | Running agents in isolated git worktrees with `--worktree` flag or `isolation: worktree` in agent definitions. Ships natively since v2.1.49 (Feb 2026) and has hardened steadily since — nested-repo trust confirmations, symlink-following fixes, `worktree.sparsePaths` for monorepos, and `/fork` now creates its own worktree by default instead of sharing a checkout. Five months of continuous hardening with no serious regressions; the default for any multi-agent or parallel-session workload. |
| [Agent Teams](https://code.claude.com/docs/en/agent-teams) | Assess | Native multi-agent orchestration (research preview, opt-in via `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`): a lead session delegates to teammates that work in parallel, share a task list, message each other directly, and coordinate merges through git. Distinct from ad hoc subagent spawning — teammates keep their own context windows and can be addressed with `@name` or cross-session `SendMessage`. Promising for PR review, competing-hypothesis debugging, and independent implementation-plus-verification, but still experimental and disabled by default. |
| [Auto Mode (Permissions)](https://claude.com/blog/auto-mode) | Trial | New default permission mode (became default for new Pro/Max/Team sessions Aug 14, 2026, after a July 10 opt-in launch): a classifier reviews each tool call for destructive/out-of-bounds actions and auto-approves the rest instead of prompting. Anthropic cites 97% historical prompt-approval rates and controlled tests showing the classifier catching far more dangerous commands than manual review. Convenient and reduces prompt fatigue, but it's a large behavioral default change that shipped only weeks ago — worth watching before trusting fully unattended. `claude auto-mode reset` restores prior behavior; manual/plan modes remain available. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively via `claude -p`. Growing adoption but auth is a pain point — `setup-token` scoping is [buggy](https://github.com/anthropics/claude-code/issues/23703), most teams fall back to API keys in CI. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. |
| /compact & Context Window Management | Adopt | Essential power-user technique. Everyone hits context limits; knowing when to compact is a core skill. Auto-compaction has had several reliability fixes over the past few months (recursive-compaction loops, silent failures on 1M-context models); a beta "explain why compaction couldn't recover" error was added to make failures legible. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. |
| Model Switching Strategies | Trial | Haiku for quick passes, Sonnet for daily work, Opus for hard problems. Mixing models to balance cost and quality. Now paired with explicit effort controls (low/medium/high/max, introduced with Opus 4.8) that tune reasoning depth independently of model choice. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Assess | Tracking what agents changed and why. Important for team accountability. Tooling still immature. |
| Voice Mode | Assess | Native `/voice` command (hold Space to record, Whisper STT) shipped in v2.1.71 (Mar 2026) with gradual rollout. Anthropic has kept patching connection and startup bugs through August (v2.1.232, v2.1.239), suggesting it's still not fully baked five months in. Community MCP alternatives (e.g., VoiceMode MCP) remain more stable today. Too early for daily use but worth watching. |
| [Scheduled Tasks (/loop)](https://code.claude.com/docs/en/scheduled-tasks) | Assess | `/loop` command (v2.1.71, Mar 2026) runs prompts on a cron schedule within a session — turns Claude Code into a background worker. Session-scoped (stops when terminal closes), max 50 tasks per session, 3-day auto-expiry. For persistent scheduling, pair with GitHub Actions (`schedule:` trigger) or headless `claude -p`. Opens up new autonomous workflow patterns; too early to recommend broadly. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Assess | Claude automatically saves useful context to persistent memory across sessions (v2.1.59+). Managed with the `/memory` command. Reduces context setup overhead for recurring projects. Behavior is automatic — review and prune regularly to avoid stale context drift. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Trial | Multi-agent support as of Feb 2026. Promising but buggy — extension host crashes, memory issues, rough terminal-to-UI handoff. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Beta quality. Functional but less mature than VS Code integration. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Trial | CI/CD integration for automated code review and PR generation. Reached v1.0 GA (Feb 2026) with breaking changes from beta; migration guide available. Simplified configuration, automatic mode detection, structured JSON outputs. Worth adopting for greenfield CI workflows now. |
| [Xcode + Claude (via MCP)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Trial | Xcode 26.3 ships a built-in MCP server exposing builds, tests, the simulator, and the debugger to any MCP client, including Claude Code — no more pasting errors between tools. Third-party servers (e.g. XcodeBuildMCP, acquired by Sentry in Feb 2026) extend coverage further. Concrete tooling and community traction now, not just a research preview. |
| Cloud-hosted Agent Fleets | Trial | Running multiple Claude Code instances in the cloud for parallel tasks — now shipping in both directions: Anthropic-hosted Cowork sessions run untethered from any device (web/mobile, July 2026), and `claude self-hosted-runner` (public beta, Team/Enterprise) turns a customer's own machines into Claude Code hosts with internal network access and compliance controls. Real tooling exists now; still maturing operationally. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Individual devs and small teams pay through Max or Pro plans. As of Jan 2026, Claude Code is included with every Team plan standard seat ($20/month). Opus 5 (July 2026) ships with a 1M-token context window by default for Max, Team, and Enterprise. Sonnet 5's introductory $2/$10-per-Mtok pricing was made permanent on Aug 11, 2026 — the planned Sept 1 price increase was cancelled. Since June 15, 2026, Claude Agent SDK / `claude -p` usage draws from a separate credit pool instead of counting against plan limits. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation via the Claude Chrome extension, reaching general availability for all paid plans in late June 2026 (out of beta). Claude can navigate pages, click, fill forms, read console logs, and capture screenshots. The side panel now opens a full Cowork session (Aug 2026, Max/Team first) rather than a one-off browsing task. Useful for build-test-debug loops; still young as a daily-driver habit. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-08-24). Blips move between rings as the ecosystem evolves.

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
30. [Claude Code changelog](https://code.claude.com/docs/en/changelog) — Primary source for March–August 2026 changes: Opus 5, auto mode, agent teams, worktree hardening, hooks, MCP, voice, self-hosted runners, output styles
31. [What's New in Claude Code — August 2026 (Origami)](https://origami.sa/en/blog/claude-code-august-2026/) — Design skill, Concise output style, auto mode default rollout
32. [9to5Mac: Claude Code enabling auto mode as default next week](https://9to5mac.com/2026/08/07/psa-claude-code-enabling-auto-mode-as-default-next-week-anthropic-says/) — Auto mode default rollout timing
33. [Claude: Auto mode for Claude Code](https://claude.com/blog/auto-mode) — Official auto-mode announcement, classifier rationale, and approval-rate telemetry
34. [The Register: Claude Code puts auto mode in the driver's seat](https://www.theregister.com/ai-and-ml/2026/08/10/claude-code-puts-auto-mode-in-the-drivers-seat/5285326) — Independent coverage of auto-mode classifier effectiveness claims
35. [Claude Code docs: Orchestrate teams of Claude Code sessions](https://code.claude.com/docs/en/agent-teams) — Official Agent Teams documentation, basis for new Assess blip
36. [TechCrunch: Claude Cowork expands to mobile and web](https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/) — Cowork cloud/web/mobile expansion informing Trial promotion
37. [9to5Mac: Claude's Chrome side panel is now a full Cowork session](https://9to5mac.com/2026/08/12/claude-cowork-chrome/) — Claude in Chrome / Cowork integration (Aug 2026)
38. [TechCrunch: Anthropic launches Opus 5](https://techcrunch.com/2026/07/24/anthropic-launches-opus-5/) — Opus 5 release (July 24, 2026), now default Claude Code model
39. [Anthropic: Introducing Claude Opus 4.7](https://www.anthropic.com/news/claude-opus-4-7) — Opus 4.7 release and benchmark notes
40. [Anthropic: Introducing Claude Opus 4.8](https://www.anthropic.com/news/claude-opus-4-8) — Dynamic Workflows and effort controls, informing Model Switching Strategies update
41. [Use the Claude Agent SDK with your Claude plan (Anthropic Help Center)](https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan) — Agent SDK usage decoupled from plan limits (June 15, 2026)
42. [Get started with Claude in Chrome (Anthropic Help Center)](https://support.claude.com/en/articles/12012173-get-started-with-claude-in-chrome) — Claude in Chrome GA and feature scope
43. [Xcode 26.3 + Claude Agent (fatbobman.com)](https://fatbobman.com/en/posts/xcode-263-claude/) — Xcode native MCP server details informing Trial promotion
44. [DevLeader: GitHub Copilot CLI vs Claude Code](https://www.devleader.ca/2026/07/31/github-copilot-cli-vs-claude-code-which-terminal-agent-should-you-use-in-2026) — Copilot CLI GA date and AI Credits pricing change
45. [Sitefluence: Codex vs Claude Code vs Copilot — 2026 Numbers](https://sitefluence.com/resources/codex-claude-code-copilot-pick-by-feel) — Codex CLI open-sourcing and credits pricing change
46. [explainx.ai: Claude Sonnet 5 Pricing Locked at $2/$10](https://www.explainx.ai/blog/anthropic-sonnet-5-permanent-pricing-august-2026) — Sonnet 5 price-increase cancellation (Aug 11, 2026)

## License

MIT
