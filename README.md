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
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development. Default model is now Claude Sonnet 5 (native 1M-token context, shipped v2.1.197, June 2026); Opus 4.7/4.8 remain available for harder tasks. Weathered two public incidents this year, both resolved and publicly explained: an accidentally-shipped npm source map exposed the full TypeScript source (Mar 31, 2026; no credentials involved) and a six-week quality regression traced to three overlapping changes (reasoning-effort downgrade, a caching bug, an overly strict verbosity limit), fixed by v2.1.116 with usage limits reset as remediation. npm install deprecated; native installer remains recommended. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude is now available as a Copilot agent for Pro+ and Enterprise customers (Feb 2026); Claude Sonnet 5 reached GA inside Copilot June 30, 2026. |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. |
| [Cowork](https://claude.com/product/claude-cowork) | Trial | Claude Code adapted for general-purpose computing. Reached general availability on macOS/Windows (Apr 9, 2026) after a research preview, then expanded to web and mobile for Max subscribers (Jul 2026) — tasks now run in the cloud and survive closing the laptop. Enterprise gets RBAC and spend controls. No longer early, but still maturing. |
| [Claude Security](https://www.anthropic.com/news/claude-code-security) | Trial | AI-driven vulnerability scanning, rebranded from "Claude Code Security." Moved from a limited research preview to public beta (May 2026), now built on Opus 4.7 with scheduled/targeted scans and audit-system integration. Reasons about code like a human security researcher rather than pattern-matching. Still Enterprise-only. |
| [Claude Code Review](https://www.anthropic.com/news/claude-code-review) | Assess | Multi-agent parallel PR review: spawns agents in parallel to check logic errors, boundary conditions, API misuse, auth flaws, and convention violations. A false-positive filter attempts to disprove each finding before posting inline GitHub comments. ~20 min review time, ~$15–25/review. Still Team/Enterprise-only and GitHub-only as of Jul 2026, no signal yet of broader rollout. Customizable via CLAUDE.md and a new REVIEW.md file. Launched March 9, 2026. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard, now governed by the Agentic AI Foundation, a Linux Foundation directed fund — Anthropic donated MCP there alongside Block's goose and OpenAI's AGENTS.md (Dec 2025); governance and maintainers unchanged. 97M+ monthly SDK downloads, 10,000+ active servers. MCP Apps (interactive UI in tool responses) shipped as the first official spec extension; stateless Streamable HTTP is now the baseline transport; OAuth 2.1 + PKCE is mandatory for remote servers. |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Trial | Plugin/marketplace counts vary a lot by source — the curated official directory stays small, while community trackers cite tens of thousands of skills and MCP servers spread across thousands of independent marketplaces. API still evolving. |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, blessed plugins. Smaller but higher quality bar. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace. Discovery UX maturing. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. Already integrated into VS Code extensions and plugin ecosystem. |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. For building custom agents programmatically. Microsoft Agent Framework integration. Anthropic now also offers Managed Agents, a hosted API where Anthropic runs the agent and sandbox for you — an alternative to self-hosting the SDK (see Cloud-hosted Agent Fleets below). |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Smaller, less vetted. No fresh signal this update — unchanged. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Now supports four handler types: command (shell), HTTP (POST JSON to a URL), prompt (LLM-evaluated decision), and agent (subagent with tools). HTTP hooks enable webhook integrations without shell scripts. Recent additions (v2.1.76): `PostCompact` fires after context compaction; `Elicitation`/`ElicitationResult` hooks can intercept and override MCP server input requests. Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. Native git worktree isolation (v2.1.49+) significantly reduces conflicts when running parallel agents — see Git Worktree Isolation below. Subagents now run in the background by default (v2.1.198, Jul 2026) instead of blocking the session, and can spawn their own sub-agents up to 5 levels deep; `/fork` (v2.1.212) spawns a full background session from a copied conversation. Agent Teams simplified to an implicit per-session team (manual TeamCreate/TeamDelete removed, Jun 2026). |
| [Auto Mode](https://claude.com/blog/auto-mode) | Trial | Classifier-based permission mode: a model reviews each tool call for destructive potential (mass deletion, data exfiltration, malicious code) and auto-approves what's safe, blocking or redirecting risky calls. A middle ground between manual approval and `--dangerously-skip-permissions`. Research preview on Team plan (Mar 24, 2026), GA for all users (Jul 10, 2026); the prior "Default" permission mode was renamed "Manual" alongside it. Useful for longer autonomous runs, but the classifier can still be fooled by ambiguous intent. |
| Git Worktree Isolation | Trial | Running agents in isolated git worktrees with `--worktree` flag or `isolation: worktree` in agent definitions. Ships natively as of v2.1.49 (Feb 2026). Eliminates file conflicts between parallel agents; `WorktreeCreate`/`WorktreeRemove` hooks extend support to non-git VCS. `ExitWorktree` tool added (v2.1.72) for clean session teardown. `worktree.sparsePaths` setting (v2.1.76) enables git sparse-checkout integration for large monorepos. The right default for any multi-agent workload. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively via `claude -p`. Growing adoption but auth is a pain point — `setup-token` scoping is [buggy](https://github.com/anthropics/claude-code/issues/23703), most teams fall back to API keys in CI. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. |
| /compact & Context Window Management | Adopt | Essential power-user technique. Everyone hits context limits; knowing when to compact is a core skill. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. |
| Model Switching Strategies | Trial | Haiku for quick passes, Sonnet for daily work, Opus for hard problems. Mixing models to balance cost and quality. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Assess | Tracking what agents changed and why. Important for team accountability. No first-party Anthropic product yet — "Session recap" (v2.1.114) auto-drafts a summary after time away, but full audit trails remain community hook-based skills. Tooling still immature. |
| Voice Mode | Trial | Native `/voice` command (hold Space to record, Whisper STT) shipped in v2.1.71 (Mar 2026). Early bugs (broken Windows transcription, "no speech detected") were fixed by v2.1.72; rollout has since expanded beyond the initial 5% to Pro/Max/Team/Enterprise with ~20 languages supported. Stable enough for regular use now. |
| [Scheduled Tasks (/loop)](https://code.claude.com/docs/en/scheduled-tasks) | Trial | `/loop` command (v2.1.71, Mar 2026) runs prompts on a cron schedule within a session. Matured into a three-tier system: session-scoped `/loop`, persistent Desktop Scheduled Tasks, and Cloud Routines (research preview, Apr 14, 2026) for Anthropic-managed recurring agents. The CLI tier still caps out at 50 tasks/session with 3-day auto-expiry; pair with GitHub Actions or headless `claude -p` for fully persistent scheduling. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Trial | Claude automatically saves useful context to persistent memory across sessions (v2.1.59+). Managed with the `/memory` command; now on by default for most plans. An unannounced "Auto Dream" pass periodically prunes and reorganizes stale memory files. Still worth reviewing manually to catch context drift. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Trial | Multi-agent support as of Feb 2026. Promising but buggy — extension host crashes, memory issues, rough terminal-to-UI handoff. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Beta quality. Functional but less mature than VS Code integration. A deeper "Claude as agent provider" integration entered preview inside JetBrains IDEs (Jun 22, 2026), but the plugin itself remains beta. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Trial | CI/CD integration for automated code review and PR generation. Reached v1.0 GA (Feb 2026) with breaking changes from beta; migration guide available. Simplified configuration, automatic mode detection, structured JSON outputs. Still on the v1.0.x line with near-daily patches (v1.0.178 as of Jul 19, 2026) — no breaking v2 yet. GitHub also added direct Claude/Codex model selection on github.com (Apr 14, 2026). Worth adopting for greenfield CI workflows now. |
| [Xcode + Claude (via MCP)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Trial | Apple shipped a native Xcode 27 MCP server (`mcpbridge`, ~20 tools) letting Claude Code query project state, trigger builds/tests, and drive SwiftUI previews and the Swift REPL — enabled via Settings → Intelligence → Model Context Protocol. A real graduation from the Xcode 26.3 preview; worth adopting for native Apple app work. |
| Cloud-hosted Agent Fleets | Trial | Running multiple Claude Code instances in the cloud for parallel tasks. Concretized into named products: Managed Agents (hosted API where Anthropic runs the agent and sandbox, announced at Code with Claude 2026) and Cloud Routines (scheduled agent configs, Apr 2026 preview). The primitives are real and used at scale internally, but tooling for small teams is still catching up. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Individual devs and small teams pay through Max or Pro plans. As of Jan 2026, Claude Code is included with every Team plan standard seat. Claude Sonnet 5 (Jun 2026) is now the default model with native 1M-token context and promotional pricing ($2/$10 per Mtok) through Aug 31, 2026. 5-hour session limits were doubled permanently (May 6, 2026) with peak-hour throttling removed for Pro/Max. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation from Claude Code via the Claude Chrome extension. Reached general availability Jul 1, 2026 (v2.1.198) — out of beta, available on all paid plans. Claude can navigate pages, click, fill forms, read console logs, and capture screenshots directly from the CLI or VS Code. Works with any site you're already logged into. Interesting for build-test-debug loops. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-07-20). Blips move between rings as the ecosystem evolves.

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
30. [Claude Code changelog](https://code.claude.com/docs/en/changelog) — v2.1.170–v2.1.215 (Jun–Jul 2026) informing Sonnet 5/Fable 5/Opus 4.8 defaults, auto mode rollout, background subagents, `/fork`, screen reader mode
31. [Anthropic: How we built Claude Code auto mode](https://www.anthropic.com/engineering/claude-code-auto-mode) and [Auto mode for Claude Code](https://claude.com/blog/auto-mode) — Basis for new Auto Mode Trial blip, rollout timeline (Mar 24 preview → Jul 10 GA)
32. [The Hacker News: Claude Code source leaked via npm packaging error](https://thehackernews.com/2026/04/claude-code-tleaked-via-npm-packaging.html) — Mar 31, 2026 source map leak, informing Claude Code note
33. [Anthropic: An update on recent Claude Code quality reports](https://www.anthropic.com/engineering/april-23-postmortem) — Apr 23, 2026 postmortem on the six-week quality regression, informing Claude Code note
34. [Anthropic: Donating the Model Context Protocol and establishing the Agentic AI Foundation](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) and [GitHub Blog: MCP joins the Linux Foundation](https://github.blog/open-source/maintainers/mcp-joins-the-linux-foundation-what-this-means-for-developers-building-the-next-era-of-ai-tools-and-agents/) — MCP governance move to the Agentic AI Foundation, informing MCP blip update
35. [TestingCatalog: Anthropic launches Claude Cowork in General Availability](https://www.testingcatalog.com/anthropic-launches-claude-cowork-in-general-availability/) and [The New Stack: Claude Cowork now keeps working when you close your laptop](https://thenewstack.io/claude-cowork-cloud-mobile/) — Cowork GA (Apr 9, 2026) and cloud/mobile expansion, informing move from Assess to Trial
36. [DevOps.com: Anthropic brings AI-powered security scanning to enterprise teams with Claude Security](https://devops.com/anthropic-brings-ai-powered-security-scanning-to-enterprise-teams-with-claude-security/) — Rebrand and public beta status, informing Claude Security move from Assess to Trial
37. [Crunchy Bagel: Enabling the Xcode 27 MCP Server in Claude Code](https://crunchybagel.com/enabling-the-xcode-27-mcp-server-in-claude-code/) — Native Xcode 27 MCP server details, informing Xcode + Claude move from Assess to Trial
38. [Claude Code docs: Week 27 (Jun 29 – Jul 3, 2026)](https://code.claude.com/docs/en/whats-new/2026-w27) — Claude in Chrome general availability (Jul 1, 2026), informing move from Assess to Trial
39. [GitHub Blog: Model selection for Claude and Codex agents on github.com](https://github.blog/changelog/2026-04-14-model-selection-for-claude-and-codex-agents-on-github-com/) — Apr 14, 2026 platform integration, informing GitHub Actions blip note
40. [GitHub Blog: New features and Claude as agent provider preview in JetBrains IDEs](https://github.blog/changelog/2026-06-22-new-features-and-claude-as-agent-provider-preview-in-jetbrains-ides/) — Jun 22, 2026, informing JetBrains blip note
41. [Anthropic: Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) and [GitHub Blog: Claude Sonnet 5 GA for GitHub Copilot](https://github.blog/changelog/2026-06-30-claude-sonnet-5-is-generally-available-for-github-copilot/) — Sonnet 5 default-model shift, informing Claude Code and Copilot notes

## License

MIT
