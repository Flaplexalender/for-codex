# Persistent Research Assistant Agent Loop

A cost-efficient, self-waking, self-developing research assistant and secretary agent that runs 24/7 using GitHub Pro's premium request allowances and GitHub Actions for orchestration.

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

## License

MIT