# Persistent Research Assistant Agent Loop

A cost-efficient, self-waking, self-developing research assistant and secretary agent that runs 24/7 using GitHub Pro's premium request allowances and GitHub Actions for orchestration.

> **New to this project? Start with the [FAQ](#faq) below.**

## Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                  GitHub Actions (Scheduler)               │
│  ┌──────────┐   ┌──────────┐   ┌──────────────────────┐ │
│  │ Cron Job │──▶│ Dispatch │──▶│ Agent Loop Workflow   │ │
│  │(hourly)  │   │ Event    │   │ (runs Copilot agent)  │ │
│  └──────────┘   └──────────┘   └──────────────────────┘ │
└─────────────────────┬───────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│              GitHub Copilot Agent Mode                    │
│  ┌──────────────┐  ┌───────────┐  ┌──────────────────┐  │
│  │ Task Queue   │  │ Research  │  │ Self-Development  │  │
│  │ (Issues/     │  │ Engine    │  │ (Code changes,    │  │
│  │  Projects)   │  │ (Web,API) │  │  improvements)    │  │
│  └──────────────┘  └───────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

## Key Design Principles

### 1. Cost Efficiency via GitHub Pro

**The core insight:** GitHub Pro includes premium Copilot requests that provide access to Claude Opus 4.6 in agent mode. A single prompt in agent mode can trigger extended autonomous work sessions (30-60+ minutes) that would cost significantly more via direct API calls.

| Method | Cost per Hour of Opus Work | Notes |
|--------|---------------------------|-------|
| Direct Anthropic API | Token-based pricing | Pay per input/output token |
| GitHub Copilot Agent Mode | Included in Pro subscription | Per-prompt premium credits |
| **Savings** | **Substantial** | Limited by monthly premium quota |

### 2. Self-Waking via GitHub Actions

GitHub Actions provides free scheduled workflows (cron jobs) that can:
- Run on a schedule (every hour, every 6 hours, daily)
- Create issues with task descriptions for the agent
- Trigger Copilot agent mode via issue assignments
- Monitor progress and chain tasks

### 3. Persistent State via Repository

The repository itself serves as persistent memory:
- **`state/`** — Runtime state, task queues, research notes
- **`docs/research/`** — Accumulated research findings
- **`agent/`** — Agent configuration and task definitions
- **Issues & PRs** — Task tracking and work history

## How It Works

### The Agent Loop

1. **Wake** — GitHub Actions cron triggers a scheduled workflow
2. **Check** — Workflow reads the task queue from `agent/tasks.md`
3. **Dispatch** — Creates a GitHub Issue with the next task description
4. **Execute** — GitHub Copilot (Opus 4.6) picks up the issue and works autonomously
5. **Persist** — Agent commits results back to the repository
6. **Sleep** — Workflow completes; next cron trigger restarts the loop

### Task Types

The agent can handle several categories of work:

- **Research Tasks** — Web research, summarization, analysis
- **Code Tasks** — Writing, refactoring, testing code
- **Secretary Tasks** — Organizing notes, scheduling, reminders
- **Self-Improvement** — Optimizing its own prompts, workflows, and code

## Project Structure

```
├── .github/
│   └── workflows/
│       └── agent-loop.yml      # Cron-based agent wake cycle
├── agent/
│   ├── tasks.md                # Current task queue
│   ├── config.yml              # Agent behavior configuration
│   └── prompts/
│       └── research.md         # Research task template
├── state/
│   └── .gitkeep                # Runtime state directory
├── docs/
│   └── research/
│       └── cost-analysis.md    # Cost analysis research
├── .gitignore
└── README.md                   # This file
```

## Getting Started

### Prerequisites

- GitHub Pro subscription (for Copilot premium requests with Opus 4.6)
- GitHub Copilot enabled on your account with agent mode
- This repository set to **Public** or accessible to Copilot

### Setup

1. **Enable GitHub Actions** on this repository
2. **Configure Copilot** to use Claude Opus 4.6 as the model in agent mode
3. **Add tasks** to `agent/tasks.md` following the template format
4. **Enable the cron workflow** by pushing the `.github/workflows/agent-loop.yml`

### Adding Tasks

Edit `agent/tasks.md` and add tasks in this format:

```markdown
## Task: [Title]
- **Priority:** high | medium | low
- **Type:** research | code | secretary | self-improvement
- **Description:** What needs to be done
- **Acceptance Criteria:** How to know it's done
```

## Research Findings

### GitHub Copilot Agent Mode — Cost Analysis

GitHub Copilot in agent mode (VS Code or github.com) provides a unique cost advantage:

1. **Per-prompt pricing** — Each prompt costs a small number of premium request credits
2. **Extended autonomy** — A single prompt can trigger extended autonomous agent work
3. **Model access** — Claude Opus 4.6 is available as a model choice
4. **Tool use** — The agent can read/write files, run commands, search code, and browse the web
5. **No token metering** — Unlike API usage, you're not charged per input/output token

### Self-Waking Strategies Compared

| Strategy | Cost | Reliability | Complexity |
|----------|------|-------------|------------|
| GitHub Actions Cron | Free (2000 min/month) | High | Low |
| External Cron (Railway/Render) | Low monthly cost | High | Medium |
| Webhook-based (Zapier/n8n) | Variable | High | Medium |
| Self-hosted (Raspberry Pi) | One-time hardware | Medium | High |
| **GitHub Actions (chosen)** | **Free** | **High** | **Low** |

### Persistence Strategies

| Strategy | Cost | Durability | Access Speed |
|----------|------|------------|-------------|
| Git repo files | Free | Very High | Fast |
| GitHub Issues/Projects | Free | Very High | Fast |
| SQLite in repo | Free | High | Fast |
| External DB (Supabase) | Variable | Very High | Medium |
| **Git repo + Issues (chosen)** | **Free** | **Very High** | **Fast** |

## Configuration

See [`agent/config.yml`](agent/config.yml) for full configuration options.

---

## FAQ

### Do I need to install anything?

**No.** This project runs entirely inside GitHub — no local software installation is required.

Everything is orchestrated by GitHub Actions (scheduled cloud workflows) and GitHub Copilot in agent mode. You interact with it through:

- **github.com** — browser-based chat, Issues, and Pull Requests
- **VS Code + Copilot extension** *(optional)* — if you prefer a local IDE experience

There is no server to host, no Docker image to build, and no `npm install` or `pip install` needed.

---

### Does it work on a Windows PC?

**Yes.** Because the agent loop runs entirely on GitHub's cloud infrastructure, your local operating system doesn't matter. You only need:

| What you need | Where to get it |
|---------------|----------------|
| A web browser (Chrome, Edge, Firefox…) | Already on your PC |
| A GitHub account with GitHub Pro | [github.com/pricing](https://github.com/pricing) |
| GitHub Copilot enabled on your account | GitHub account settings |

If you want to edit files locally (e.g. add new tasks), any text editor on Windows works — Notepad, VS Code, Cursor, etc.

---

### How do I use it?

#### Quick start (5 minutes)

1. **Fork or copy this repository** to your own GitHub account.
2. **Enable GitHub Actions** — go to the *Actions* tab and click "I understand my workflows, go ahead and enable them".
3. **Add a task** — edit [`agent/tasks.md`](agent/tasks.md) and add a task block:
   ```markdown
   ## Task: [Your task title]
   - **Priority:** high
   - **Type:** research
   - **Description:** What you want the agent to do
   - **Acceptance Criteria:**
     - [ ] Deliverable 1
   ```
4. **Trigger the agent** — either wait for the next scheduled cron run (every 6 hours), or go to *Actions → Agent Loop → Run workflow* to trigger it immediately.
5. **Watch it work** — the workflow creates a GitHub Issue; Copilot picks it up and starts working. Results are committed back to the repository (check `docs/research/`).

#### Day-to-day use

- **Add tasks:** edit `agent/tasks.md`
- **See results:** browse `docs/research/` for research outputs
- **Change the schedule:** edit the `cron` value in `.github/workflows/agent-loop.yml`
- **Adjust agent behavior:** edit `agent/config.yml`

---

### What tools does it have? How does it compare to Claude Code, Claude SDK, or Copilot Pro Chat?

The agent runs inside **GitHub Copilot in agent mode**, which gives it access to the same tool set you see in Copilot Pro Chat in agent mode. Here is how the environments compare:

| Capability | This agent loop | Copilot Pro Chat (agent mode) | Claude Code (CLI) | Anthropic SDK (API) |
|------------|:--------------:|:-----------------------------:|:-----------------:|:-------------------:|
| Read & write files in the repo | ✅ | ✅ | ✅ | ✅ (via your code) |
| Run terminal commands | ✅ | ✅ | ✅ | ✅ (via your code) |
| Web / internet search | ✅ | ✅ | ✅ | ❌ (not built-in) |
| Create GitHub Issues & PRs | ✅ | ✅ | ❌ (needs gh CLI) | ❌ (needs gh CLI) |
| Scheduled / autonomous wake-up | ✅ | ❌ (you must prompt it) | ❌ (you must invoke) | ❌ (you must invoke) |
| Persistent memory across sessions | ✅ (git repo) | ❌ (conversation only) | ❌ (conversation only) | ❌ (you must build it) |
| Self-improving (edits own prompts) | ✅ | ❌ | ❌ | ❌ |
| Access to Claude Opus 4 | ✅ | ✅ | ✅ | ✅ |
| Cost model | Per premium request (Pro plan) | Per premium request (Pro plan) | Per token (API key) | Per token (API key) |
| Requires local install | ❌ | ❌ | ✅ | ✅ |

**The main thing this agent adds on top of plain Copilot Pro Chat is autonomy and persistence:**

- It wakes itself up on a schedule without you needing to open a browser.
- It remembers everything it has done by storing results in the git repository.
- It can queue up and chain tasks over days or weeks.

**Current limitations:**

- It cannot access services outside GitHub without adding extra workflow steps (e.g. sending emails, posting to Slack) — but those are straightforward to add in the workflow YAML.
- It shares the same Copilot Premium request quota as your regular Copilot Pro Chat usage.
- The agent mode tool set is determined by GitHub Copilot, not this repository — if GitHub adds or removes tools, the agent gains or loses them accordingly.

---

## License

MIT