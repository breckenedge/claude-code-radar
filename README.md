# Claude Code Technology Radar

A community-driven technology radar tracking the maturity, adoption, and lifecycle of tools, plugins, patterns, and practices in the Claude Code ecosystem.

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
| Claude Code | Adopt | 55K+ GitHub stars. $1B run rate. The reference agentic coding CLI. |
| Cursor | Adopt | IDE-native AI coding. Large user base, strong editor experience. |
| GitHub Copilot | Adopt | Ubiquitous. 65%+ weekly developer usage per Stack Overflow 2025 survey. |
| OpenAI Codex CLI | Trial | Open-source competitor. Strong momentum but younger ecosystem. |
| Gemini Code Assist | Trial | Google's entry. Deep integration with Google Cloud tooling. |
| DeepSeek Coder V4 | Trial | Strong benchmarks, open-weight. Privacy/supply-chain concerns for some orgs. |
| Aider | Trial | Open-source, git-native AI pair programming. Popular with CLI-first devs. |
| Cline | Assess | VS Code agent. Active development, smaller community than leaders. |
| Cowork (Anthropic) | Assess | Claude Code adapted for general-purpose computing. Very early. |
| Windsurf (Codeium) | Hold | Acquired, direction uncertain. Users migrating to alternatives. |

### Plugins & Extensions

| Blip | Ring | Notes |
|------|------|-------|
| MCP (Model Context Protocol) | Adopt | Open standard by Anthropic. Adopted by Apple (Xcode), VS Code, and others. |
| Claude Code Plugin System | Trial | 9,000+ plugins across marketplaces. API still evolving. |
| Anthropic Official Plugin Directory | Trial | Curated, blessed plugins. Smaller but higher quality bar. |
| ClaudePluginHub | Trial | Largest third-party marketplace. Discovery UX maturing. |
| Firecrawl Plugin | Trial | Web scraping/research. Popular for data extraction workflows. |
| Agent Skills (Anthropic) | Assess | Open standard for distributing specialized agent capabilities. New. |
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
| Prompt Caching & Context Management | Assess | Strategies for managing long conversations and token costs. |
| Human-in-the-loop Agent Review | Assess | Formal review workflows for agent-generated code. Emerging practice. |
| Vibe Coding (fully autonomous) | Hold | Letting agents code without review. High risk, poor outcomes at scale. |

### Platforms & Infrastructure

| Blip | Ring | Notes |
|------|------|-------|
| Anthropic API | Adopt | Primary model provider. Claude Opus 4.6, Sonnet 4.5, Haiku 4.5. |
| VS Code + Claude Code Extension | Adopt | First-class IDE integration. Multi-agent support as of Feb 2026. |
| JetBrains + Claude Code Plugin | Trial | Beta quality. Functional but less mature than VS Code integration. |
| GitHub Actions + Claude Code | Trial | CI/CD integration for automated code review, PR generation. |
| Xcode + Claude (via MCP) | Assess | Apple's recent MCP adoption. Very early but significant signal. |
| Cloud-hosted Agent Fleets | Assess | Running multiple Claude Code instances in cloud for parallel tasks. |
| Amazon Bedrock (Claude) | Trial | Enterprise deployment path. Cross-model flexibility. |
| Google Vertex AI (Claude) | Trial | Alternative enterprise hosting. GCP-native integration. |

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
