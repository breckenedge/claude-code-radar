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
| [Claude Code](https://github.com/anthropics/claude-code) | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development. npm install deprecated; native installer (no Node.js/npm dependency) is now recommended — run `claude install` or use the install script. Claude Sonnet 5 became the default model (June 30, 2026, native 1M-token context); Claude Opus 5 became the default Opus model (July 24, 2026). Subagents now run in background by default (v2.1.198), with concurrency caps (20 by default) to prevent runaway spawning. |
| [GitHub Copilot](https://github.com/features/copilot) | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. Claude reached general availability as a Copilot agent for Business and Pro customers (Feb 26, 2026), expanding from the earlier Pro+/Enterprise preview; per-agent model selection added April 14, 2026. |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. |
| [Cowork](https://claude.com/blog/cowork-research-preview) | Trial | Claude Code adapted for general-purpose computing. Reached general availability on macOS/Windows for all paid plans (April 9, 2026) with enterprise features (role-based access, group spend limits, OpenTelemetry); expanded to mobile/web in beta by July 2026. No longer "very early," but still a distinct product from Claude Code proper. |
| [Claude Security](https://claude.com/blog/claude-security-public-beta) | Assess | AI-driven vulnerability scanning (renamed from "Claude Code Security"). Reasons about code like a human security researcher, tracking data flows across files rather than just pattern-matching. Moved from limited preview to public beta for Enterprise customers (April 30, 2026); Team and Max access expected to follow but not yet available to individual devs or small teams. |
| [Claude Code Review](https://claude.com/blog/code-review) | Trial | Multi-agent parallel PR review: spawns agents in parallel to check logic errors, boundary conditions, API misuse, auth flaws, and convention violations. A false-positive filter attempts to disprove each finding before posting inline GitHub comments. ~20 min review time, ~$15–25/review. Still a research preview/beta for Team and Enterprise (GitHub only), but adoption data is real: substantive PR review comments jumped from 16% to 54% after deployment, with under 1% of findings marked incorrect. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| [MCP (Model Context Protocol)](https://modelcontextprotocol.io) | Adopt | Open standard by Anthropic. Widely adopted across editors and platforms — 10,000+ servers, 97M+ downloads. Governance formally moved from Anthropic to the Linux Foundation's new Agentic AI Foundation (announced late 2025/early 2026); maintainer hierarchy and SEP process unchanged. A July 28, 2026 spec release candidate delivers a stateless Streamable HTTP core, an MCP Apps extension for interactive UI components, and a Tasks extension for long-running work. |
| [Claude Code Plugin System](https://github.com/anthropics/claude-code/blob/main/plugins/README.md) | Trial | Tens of thousands of plugins across marketplaces (ClaudePluginHub alone indexes 36,000+ as of June 2026, up from 9,000). API still evolving. |
| [Anthropic Official Plugin Directory](https://github.com/anthropics/claude-plugins-official) | Trial | Curated, higher-visibility plugin listing (officially launched May 22, 2026; 32.7k GitHub stars) with contributions from partners like GitHub, GitLab, Linear, and Playwright. Still no formal vetting — the repo's own README warns "Anthropic does not control what MCP servers, files, or other software are included in plugins and cannot verify that they will work as intended," so "curated" means visibility, not a safety guarantee. |
| [ClaudePluginHub](https://www.claudepluginhub.com/) | Trial | Largest third-party marketplace — 36,600+ plugins from 16,600+ authors as of June 2026. Discovery UX maturing. |
| [Agent Skills](https://github.com/anthropics/skills) | Adopt | Open standard for distributing specialized agent capabilities. Already integrated into VS Code extensions and plugin ecosystem. |
| [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. For building custom agents programmatically. Microsoft Agent Framework integration. A planned June 2026 change to bill SDK/headless usage separately from subscription limits was announced, then paused — programmatic usage still draws from Pro/Max/Team/Enterprise plan limits. |
| [Claude-Plugins.dev](https://claude-plugins.dev) | Assess | Alternative community marketplace. Still small (~20 plugins) and less vetted, but active. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| [CLAUDE.md Project Instructions](https://code.claude.com/docs/en/claude-md) | Adopt | De facto standard for project-level agent configuration. Supply chain risk: researcher-disclosed CVEs showed malicious repos could use project-scoped config files (CLAUDE.md, .mcp.json) to trigger RCE before trust prompts appeared (CVE-2025-59536, CVE-2026-21852, patched 2025–26). Always verify repo trust before opening with Claude Code. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Adopt | Stable, well-documented. Standard for CI/build integration. Now supports four handler types: command (shell), HTTP (POST JSON to a URL), prompt (LLM-evaluated decision), and agent (subagent with tools). HTTP hooks enable webhook integrations without shell scripts. Recent additions (v2.1.76): `PostCompact` fires after context compaction; `Elicitation`/`ElicitationResult` hooks can intercept and override MCP server input requests. Note: hooks in project config files were a supply chain RCE vector (CVE-2025-59536, patched in v1.0.111+). |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. Native git worktree isolation (v2.1.49+) significantly reduces conflicts when running parallel agents — see Git Worktree Isolation below. Subagents now run in the background by default (v2.1.198, July 2026) with default concurrency (20) and nesting-depth (3) caps to bound runaway spawning. |
| Workflow (Deterministic Multi-agent Orchestration) | Assess | A scripted orchestration primitive (`agent()`/`parallel()`/`pipeline()`) for fanning out and pipelining subagents deterministically instead of relying on the model to plan delegation. Shipped as a feature-flagged addition (~v2.1.147). Powerful for large, repeatable review/migration/research sweeps, but new enough that patterns and best practices are still forming. |
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
| Session Replay / Audit Trails | Assess | Tracking what agents changed and why. Important for team accountability. Tooling still immature. |
| Voice Mode | Trial | Native `/voice` command (hold Space to record). The "no speech detected"/disabled-transcription bug from the initial rollout was fixed (v2.1.72). As of July 23, 2026, voice mode runs on Opus/Sonnet instead of only the lightweight fast model, and supports roughly 20 languages. Maturing quickly; community MCP alternatives (e.g., VoiceMode MCP) are no longer clearly ahead. |
| [Scheduled Tasks (/loop)](https://code.claude.com/docs/en/scheduled-tasks) | Assess | `/loop` command (v2.1.71, Mar 2026) runs prompts on a cron schedule within a session — turns Claude Code into a background worker. Session-scoped (stops when terminal closes), max 50 tasks per session, 3-day auto-expiry. For persistent scheduling, pair with GitHub Actions (`schedule:` trigger) or headless `claude -p`. Opens up new autonomous workflow patterns; too early to recommend broadly. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |
| Auto-Memory (/memory) | Assess | Claude automatically saves useful context to persistent memory across sessions (v2.1.59+). Managed with the `/memory` command. Reduces context setup overhead for recurring projects. Behavior is automatic — review and prune regularly to avoid stale context drift. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| [VS Code + Claude Code Extension](https://code.claude.com/docs/en/vs-code) | Adopt | Multi-agent support since Feb 2026, and the stability complaints that kept this at Trial have largely been addressed: a memory-leak fix for teammate conversation history (v2.1.69) and subagent concurrency caps. VS Code 1.128 (July 8, 2026) added multi-chat support for parallel Claude sessions. |
| [JetBrains + Claude Code Plugin](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) | Trial | Beta quality. Functional but less mature than VS Code integration. |
| [GitHub Actions + Claude Code](https://github.com/anthropics/claude-code-action) | Trial | CI/CD integration for automated code review and PR generation. Reached v1.0 GA (Feb 2026) with breaking changes from beta; migration guide available. Simplified configuration, automatic mode detection, structured JSON outputs. Worth adopting for greenfield CI workflows now. |
| [Xcode + Claude (via MCP)](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk) | Assess | Apple's MCP adoption in Xcode 26.3. Very early but significant for Claude Code users building native apps. |
| [Claude Managed Agents](https://claude.com/blog/claude-managed-agents) | Trial | Composable APIs for cloud-hosted agents (sandboxed execution, checkpointing, credential management, scoped permissions, tracing). Reached public beta April 8, 2026 on the Claude Developer Platform; billed at standard token rates plus $0.08/session-hour. Real partner adoption (GitHub, Vercel, Datadog) discussed at Anthropic's Code with Claude 2026 conference (May 6). Graduated from a vague "cloud-hosted agent fleets" idea to an actual product. |
| [Claude Max / Pro / Team Subscriptions](https://claude.com/pricing) | Adopt | The economics of agent coding. Individual devs and small teams pay through Max or Pro plans. As of Jan 2026, Claude Code is included with every Team plan standard seat ($20/month). Claude Sonnet 5 (default model, June 30, 2026) and Claude Opus 5 (default Opus model, July 24, 2026) both ship with native 1M-token context. |
| [Claude in Chrome](https://code.claude.com/docs/en/chrome) | Trial | Browser automation from Claude Code via the Claude Chrome extension. Claude can navigate pages, click, fill forms, read console logs, and capture screenshots directly from the CLI or VS Code. Works with any site you're already logged into. Reached general availability July 1, 2026 (v2.1.198); still paid-only, supports Chrome and Edge. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: 2026-07-27). Blips move between rings as the ecosystem evolves.

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
30. [Claude Code changelog](https://code.claude.com/docs/en/changelog) — Primary source for v2.1.196–v2.1.220 (June–July 2026): Sonnet 5/Opus 5 as default models, Claude in Chrome GA, background subagents by default, sandbox settings
31. [Anthropic: Introducing Claude Sonnet 5](https://www.anthropic.com/news/claude-sonnet-5) — Official announcement, June 30, 2026, informing default-model update across Claude Code and pricing blips
32. [TechCrunch: Anthropic launches Opus 5](https://techcrunch.com/2026/07/24/anthropic-launches-opus-5/) — Opus 5 launch coverage, July 24, 2026
33. [Anthropic: Donating the Model Context Protocol and establishing the Agentic AI Foundation](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation) — MCP governance transfer to Linux Foundation, informing MCP blip update
34. [Linux Foundation: Formation of the Agentic AI Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation) — AAIF founding announcement and MCP governance structure
35. [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) — Official plugin directory repo; star count and trust-disclaimer language informing Anthropic Official Plugin Directory blip update
36. [TechTimes: Claude Code plugins get official directory](https://www.techtimes.com/articles/317139/20260525/claude-code-plugins-get-official-directory-anthropic-flags-unverified-mcp-risks.htm) — Official directory launch coverage, May 22, 2026
37. [ClaudePluginHub: Browse Plugins](https://www.claudepluginhub.com/plugins) — Plugin/author counts as of June 2026, informing ClaudePluginHub and Plugin System blip updates
38. [claude-plugins.dev](https://claude-plugins.dev) — Direct site check confirming small, active community plugin registry, informing Claude-Plugins.dev blip
39. [TestingCatalog: Anthropic launches Claude Cowork in General Availability](https://www.testingcatalog.com/anthropic-launches-claude-cowork-in-general-availability/) — Cowork GA details, April 9, 2026
40. [TechCrunch: Claude Cowork expands to mobile and web](https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/) — Cowork mobile/web beta expansion, July 2026
41. [Claude: Claude Security is now in public beta](https://claude.com/blog/claude-security-public-beta) — Official announcement of rename and public beta, April 30, 2026
42. [InfoQ: Anthropic Introduces Agent-Based Code Review for Claude Code](https://www.infoq.com/news/2026/04/claude-code-review/) — Claude Code Review adoption metrics informing Trial move
43. [Claude: Code Review for Claude Code](https://claude.com/blog/code-review) — Official Code Review product page
44. [Visual Studio Magazine: Claude AI Gets Yet Another Boost in VS Code 1.128](https://visualstudiomagazine.com/articles/2026/07/08/claude-ai-gets-yet-another-boost-in-vs-code-1-128.aspx) — VS Code 1.128 multi-chat feature, informing VS Code extension Adopt move
45. [Claude: Claude Managed Agents](https://claude.com/blog/claude-managed-agents) — Official Managed Agents beta announcement and pricing, informing Cloud-hosted Agent Fleets blip rename/update
46. [InfoQ: Anthropic's Code with Claude Announces Managed Agents, Proactive Workflows, Capability Curve](https://www.infoq.com/news/2026/05/code-with-claude/) — Code with Claude 2026 conference coverage, partner adoption signals
47. [GitHub Changelog: Claude and Codex now available for Copilot Business & Pro users](https://github.blog/changelog/2026-02-26-claude-and-codex-now-available-for-copilot-business-pro-users/) — GitHub Copilot GA expansion, Feb 26, 2026
48. [GitHub Changelog: Model selection for Claude and Codex agents on github.com](https://github.blog/changelog/2026-04-14-model-selection-for-claude-and-codex-agents-on-github-com/) — Per-agent model selection, April 14, 2026
49. [VoiceOS: Claude Voice Mode Gets Smarter](https://www.voiceos.com/blog/claude-voice-mode) — Voice mode model upgrade (Opus/Sonnet) and language expansion, July 23, 2026, informing Voice Mode Trial move

## License

MIT
