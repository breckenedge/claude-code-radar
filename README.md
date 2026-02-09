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
| Claude Code | Adopt | The reference agentic coding CLI. Largest ecosystem, most active development. |
| Cursor | Hold | IDE-native AI coding. Market fragmented. CLI-first devs migrating to Claude Code + VS Code + Copilot. |
| GitHub Copilot | Adopt | Ubiquitous autocomplete layer. Complements Claude Code well for inline suggestions. |
| OpenAI Codex CLI | Adopt | Invaluable for multi-agent reviews. `codex review` + Claude Code gives you cross-model code review out of the box. |
| Gemini Code Assist | Trial | Google's entry. Deep integration with Google Cloud tooling. |
| Aider | Trial | The OG. Open-source, git-native, model-agnostic. Still actively maintained. Important fallback if a major provider has issues. |
| Cline | Hold | VS Code agent. Community fragmented into forks (Roo Code, Kilo Code). Eclipsed by Claude Code + Copilot. |
| Cowork (Anthropic) | Assess | Claude Code adapted for general-purpose computing. Very early. |
| Windsurf (Codeium) | Hold | Acquired, direction uncertain. Users migrating to alternatives. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| MCP (Model Context Protocol) | Adopt | Open standard by Anthropic. Widely adopted across editors and platforms. |
| Claude Code Plugin System | Trial | 9,000+ plugins across marketplaces. API still evolving. |
| Anthropic Official Plugin Directory | Trial | Curated, blessed plugins. Smaller but higher quality bar. |
| ClaudePluginHub | Trial | Largest third-party marketplace. Discovery UX maturing. |
| Firecrawl Plugin | Trial | Niche. Web scraping/crawling MCP server. Useful if your workflow needs live web data. |
| Agent Skills (Anthropic) | Adopt | Open standard for distributing specialized agent capabilities. Already integrated into VS Code extensions and plugin ecosystem. |
| Claude Agent SDK | Trial | Same engine as Claude Code, exposed as a Python/TypeScript library. For building custom agents programmatically. Microsoft Agent Framework integration. |
| Claude-Plugins.dev | Assess | Alternative community marketplace. Smaller, less vetted. |

### Techniques & Patterns

| Blip | Ring | Notes |
|------|------|-------|
| CLAUDE.md Project Instructions | Adopt | De facto standard for project-level agent configuration. |
| Hooks (shell commands on events) | Adopt | Stable, well-documented. Standard for CI/build integration. |
| Multi-agent Task Delegation | Trial | Spawning sub-agents for parallel work. Effective but needs orchestration discipline. |
| Specialized Plugin Stacks | Trial | Separate agent configs per domain (frontend, backend, security). Gaining traction. |
| README-driven Development | Trial | Using Claude Code to iterate on design docs before implementation. Meta. |
| Headless / CI Mode | Trial | Running Claude Code non-interactively in pipelines. Growing adoption. |
| Prompt Caching & Context Management | Adopt | Essential. Built into the platform. Every serious user manages context and caching. |
| Human-in-the-loop Agent Review | Trial | Reviewing agent-generated code before committing. Most teams already do this informally. |
| /compact & Context Window Management | Adopt | Essential power-user technique. Everyone hits context limits; knowing when to compact is a core skill. |
| Docker Dev Containers + Claude Code | Trial | Common local setup pattern. Reproducible, isolated agent environments. |
| Context Hygiene / .claudeignore | Adopt | Scoping what the agent sees. Narrow git scopes, ignore patterns, workspace-per-feature. Essential for large repos. |
| Model Switching Strategies | Trial | Haiku for quick passes, Sonnet for daily work, Opus for hard problems. Mixing models to balance cost and quality. |
| Agent-driven Test Generation | Trial | Using Claude Code to generate tests, then human review. Accelerates coverage without blind trust. |
| Session Replay / Audit Trails | Assess | Tracking what agents changed and why. Important for team accountability. Tooling still immature. |
| Vibe Coding | Adopt | Mainstream since late 2025. The default way most developers start new projects and prototypes. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| VS Code + Claude Code Extension | Trial | Multi-agent support as of Feb 2026. Promising but buggy — extension host crashes, memory issues, rough terminal-to-UI handoff. |
| JetBrains + Claude Code Plugin | Trial | Beta quality. Functional but less mature than VS Code integration. |
| GitHub Actions + Claude Code | Assess | CI/CD integration for automated code review, PR generation. Buggy in practice. Not reliable enough for Trial yet. |
| Xcode + Claude (via MCP) | Assess | Apple's MCP adoption in Xcode 26.3. Very early but significant for Claude Code users building native apps. |
| Cloud-hosted Agent Fleets | Assess | Running multiple Claude Code instances in cloud for parallel tasks. Coming up fast but still early for small teams. |
| Claude Max / Pro Subscriptions | Adopt | The economics of agent coding. Most individual devs and small teams pay through Max or Pro plans. |

---

## How to Read This

Each **blip** is a tool, plugin, technique, or platform relevant to Claude Code developers. Its **ring** reflects our assessment of its current maturity and adoption. **Notes** provide brief context.

This is opinionated and point-in-time (last updated: February 2026). Blips move between rings as the ecosystem evolves.

## Contributing

This radar is maintained through community input. To suggest changes:

1. Open an issue describing the blip and proposed ring placement
2. Include evidence: adoption metrics, community sentiment, personal experience
3. Discuss in the issue before submitting a PR

Movement between rings should be justified with observable signals, not hype.

## License

MIT
