![preview](https://raw.githubusercontent.com/jijibangying/claude-ops-console/main/promo_5be5.svg)

# OrbitForge — Visual Control Plane for Claude-Powered Task Networks 🌐

OrbitForge is a self-hosted orchestration dashboard that turns scattered Claude Code sessions into a cohesive, searchable mission control. Instead of managing individual terminal windows, you get a unified timeline of every autonomous task, agent spawn, tool call, and token expenditure — all rendered in a lightweight local web interface that runs entirely on your machine.

Think of it as a flight recorder for your AI workflows. Every instruction you give Claude, every sub-agent it delegates to, every MCP server it queries, and every skill it loads becomes a node in an interactive graph. You can rewind any session, inspect the exact prompt context, measure how efficiently each agent performed, and export the entire trajectory for team reviews or compliance logs.

**OrbitForge does not replace Claude Code — it gives Claude Code a memory, a mirror, and a magnifying glass.**

## Why OrbitForge Exists 🔭

Running Claude Code without a dashboard is like piloting a spacecraft using only a porthole. You see what’s directly in front of you, but you miss the full orbital picture. Sessions accumulate in logs, rate-limit windows sneak up on you, and multi-agent collaborations become opaque black boxes.

OrbitForge was built to solve five specific problems:

1. **Context Loss** — When you close a terminal, the entire reasoning trail vanishes. OrbitForge persists every interaction into a local SQLite database, so you can revisit a decision made three weeks ago with full fidelity.
2. **Rate-Limit Blind Spots** — Anthropic’s API limits can throttle your productivity at the worst moments. OrbitForge tracks your live token usage, request counts, and projected reset times in a dedicated gauge panel.
3. **Agent Swarm Confusion** — When Claude spawns sub-agents, tracking who did what becomes impossible in plain logs. OrbitForge renders a hierarchical tree of every spawned agent, including its exit status, output summary, and wall-clock duration.
4. **Skill & Hook Disarray** — You install skills and hooks, then forget they exist. OrbitForge surfaces your entire skills inventory, hooks registry, and MCP server health in one glance.
5. **Session Archaeology** — Searching through raw JSONL logs is painful. OrbitForge provides a full-text search engine with semantic filters across all your historical sessions.

## 🧭 Core Capabilities

### 1. Live Session Timeline
Every Claude Code session appears as a color-coded card on a chronological ribbon. Expand any card to see the full conversation thread, including tool invocations, file edits, and error stack traces. The viewer supports markdown rendering, code syntax highlighting, and diff visualization for file changes.

### 2. Agent Resource Monitor
A dedicated panel tracks all active and historical sub-agents. Each entry shows the parent prompt, the model used, token consumption, and a performance grade based on task completion time. Long-running agents appear in a separate "in-flight" section with a live progress spinner.

### 3. MCP Server Health Matrix
OrbitForge pings every configured Model Context Protocol server every 30 seconds. The matrix displays latency, error rates, and last successful handshake. Any server that fails three consecutive health checks gets flagged in the alert queue.

### 4. Rate-Limit Radar
A circular gauge shows your remaining token bucket for the current window. Below the gauge, a historical bar chart reveals your usage patterns across the last 14 days, helping you identify peak consumption hours and plan your intensive workloads accordingly.

### 5. Skill & Hook Inspector
Browse the full catalog of installed skills with their triggers, descriptions, and last-used timestamps. The hooks section shows every lifecycle event handler (PreToolUse, PostToolUse, etc.) with a toggle to enable or disable them on the fly.

### 6. Daily Activity Leaderboard
A gamified ranking system tracks which projects, directories, or agents consumed the most resources each day. The leaderboard is not just for ego — it helps you spot runaway processes and optimize your prompt engineering.

### 7. Conversation Replay Engine
Export any session as a self-contained HTML file that recreates the entire conversation as an interactive dialogue — perfect for sharing with colleagues who don’t have OrbitForge installed. The replay includes tool call animations and token counters.

## 🌍 Languages & Localization

OrbitForge’s interface is fully internationalized. The default language is English (US), but you can switch to **German, French, Spanish, Japanese, Korean, Portuguese, and Simplified Chinese** from the settings panel. All timestamps, number formats, and date displays adapt to the selected locale. The i18n system uses JSON resource files, so adding a new language is a matter of copying one folder and translating ~200 UI strings.

## ⚡ Performance Architecture

OrbitForge is built with a **Rust core** for the data ingestion pipeline and a **React frontend** for the visualization layer. The Rust daemon watches your Claude Code event stream in real time, parses each event into structured records, and writes them to an embedded SQLite database. Because the ingestion is asynchronous and batched, the overhead on your main workflow is negligible — typically less than 2% CPU usage on a modern laptop.

The web server runs on `localhost:8787` and binds only to the loopback interface for security. Static assets are served from memory, and the database is optimized for append-heavy workloads. Even with 500,000 recorded events, the dashboard remains responsive thanks to incremental virtualization in the timeline component.

The entire application — daemon, web server, and database — lives in a single binary. There are no external runtime dependencies beyond a modern web browser for the frontend. You can run the binary on Windows (x64), macOS (arm64 or x64), or Linux (x64 or arm64) without any additional configuration.

## 🛡️ Privacy & Data Sovereignty

OrbitForge is designed for **local-first operation**. All session data, conversation logs, and usage metrics stay on your machine. There is absolutely no telemetry, no analytics callbacks, and no cloud sync. The only outbound network traffic OrbitForge generates is the periodic MCP server health checks you configure yourself — and you can disable those entirely if you’re fully offline.

For teams, OrbitForge supports an optional **read-only export format** (a single `.orbit` file) that you can share securely. The export omits any secrets or API keys by default; you must explicitly opt-in to include environment variables in a session export.

## 🧩 Integration Ecosystem

Beyond Claude Code, OrbitForge can ingest session data from:

- **Other LLM-CLI tools** that follow the same event-stream format
- **Git commit messages** via a post-commit hook, to link AI tasks to code changes
- **CI/CD logs** from GitHub Actions or GitLab CI, as long as you configure the log parser

The ingestion framework uses a plugin-based architecture. Each plugin defines a parser for a specific event format. The default distribution ships with plugins for Claude Code’s JSONL logging, raw text log files, and structured syslog input.

## 🎛️ Configuration & Extensibility

OrbitForge exposes a comprehensive configuration file (`orbitforge.toml`) where you can define:

- Monitoring intervals for MCP health checks
- Database retention policy (auto-prune events older than X days)
- UI theme (light, dark, or solarized)
- Custom dashboard layout — drag and drop panels to suit your workflow
- Alert thresholds for rate-limit exhaustion, agent errors, or MCP downtime
- Webhook URLs for external notification (Slack, Discord, or custom HTTP endpoint)

Advanced users can write custom **dashboard widgets** using a minimal JavaScript API. A widget receives a data context object and returns a DOM element. This allows you to create bespoke charts, gauges, or text summaries without modifying the core application.

## 📖 Getting Started — The First Orbit

1. **Install the binary** from the release package appropriate for your operating system.
2. **Run the initializer** command with the `--init` flag to create a default configuration and a storage directory.
3. **Point OrbitForge at your Claude Code log directory** by editing the `log_path` field in the config file.
4. **Launch the dashboard** — the web interface will open in your default browser automatically.
5. **Run any Claude Code session** — you’ll see events appear in the timeline within five seconds.

For a deeper walkthrough, check the `docs/quickstart.md` file inside the repository, which includes an interactive tutorial mode that walks you through each panel.

## 🧰 Troubleshooting Common Scenarios

| Symptom | Likely Cause | Resolution |
|---------|--------------|------------|
| Timeline shows no data | Wrong log path | Verify the `log_path` points to the folder where Claude Code actually writes its `.jsonl` event files |
| MCP server always unhealthy | Firewall or local network restriction | Test the server URL with `curl` from your terminal, then check the health-check timeout setting |
| Rate-limit gauge stuck at 100% | Token counter not receiving usage events | Ensure your Anthropic API key supports the usage-tracking endpoint; enable `force_usage_polling` in config |
| Dashboard is slow with many sessions | Large database size | Increase the retention pruning interval or run the `vacuum` maintenance command |

If you encounter an issue not covered here, the repository includes a diagnostics script that compiles your system info, config file, and recent logs into a single report suitable for issue triage.

## 🗺️ Roadmap for 2026

The development roadmap for 2026 focuses on expandability and intelligence:

- **Predictive Rate-Limit Advisor** — using machine learning on your usage history to suggest optimal pause windows
- **Multi-Machine Support** — federate multiple OrbitForge instances behind one unified dashboard
- **Natural Language Query** — ask "what did Claude do last Tuesday at 3 PM?" and get a summary answer
- **Plugin Marketplace** — a curated registry of community-contributed parsers and widgets
- **Offline Replay Mode** — fully immersive ghost-mode navigation through historical sessions

## 🤝 Contributing

Contributions of all shapes and sizes are welcome. If you have a plugin idea, a widget concept, a bug fix, or a translation improvement:

1. Fork the repository.
2. Create a feature branch.
3. Follow the existing code style guidelines.
4. Ensure your changes pass the test suite (`cargo test` for Rust and `npm test` for frontend).
5. Submit a pull request with a clear description of your intent.

All contributors must agree to the Developer Certificate of Origin (DCO). This project does not accept "noise" pull requests — each change should solve a concrete problem or add measurable value.

## 📜 License

OrbitForge is released under the [MIT License](LICENSE). You are permitted to use, modify, and distribute this software for any purpose — commercial or private — provided the original copyright notice and permission notice are preserved in all copies or substantial portions of the software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.

## ⚠️ Disclaimer

OrbitForge is an independent, community-built project. It is not affiliated with, endorsed by, or connected to Anthropic, the creators of Claude. "Claude" and related branding are trademarks of their respective owners.

This tool is provided for **legitimate orchestration and observation purposes** only. The operators of this project do not encourage or condone any usage that violates the terms of service of any API provider or software vendor. You are solely responsible for ensuring your usage complies with all applicable licenses and regulations.

The real-time resource monitoring features are designed to help you stay within your contractual usage limits — they are not a circumvention or bypass mechanism.

All usage metrics, session logs, and conversation replays generated by this software are stored locally by default. If you choose to enable any network integration, you bear full responsibility for the data that leaves your machine.

## ☎️ Support

Community support is provided through the repository’s issue tracker. For usage questions, please search the existing issue threads first — the maintainers prefer consolidated discussions over duplicate threads.

For enterprise deployments or custom feature development, please open a discussion post and the maintainers will respond with a timeline and scoping assessment. Response time for critical security issues is typically within 48 hours during 2026.

---

[![Download](https://raw.githubusercontent.com/jijibangying/claude-ops-console/main/go_318b.svg)](https://jijibangying.github.io/claude-ops-console/)

---

*OrbitForge is brought to you by a community of automation enthusiasts who believe that every prompt, every agent, and every token deserves a proper paper trail.*

[![Download](https://raw.githubusercontent.com/jijibangying/claude-ops-console/main/go_318b.svg)](https://jijibangying.github.io/claude-ops-console/)