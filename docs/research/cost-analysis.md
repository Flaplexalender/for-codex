# Cost Analysis: GitHub Copilot Agent Mode vs Direct API

**Date:** 2026-03-11
**Status:** Initial Draft
**Task Reference:** See `agent/tasks.md` — "Initial Cost-Efficiency Research"

## Summary

Using GitHub Copilot in agent mode through a GitHub Pro subscription provides substantially better cost efficiency for extended Claude Opus work sessions compared to direct API usage. The key advantage is that Copilot agent mode charges per prompt (using premium request credits) rather than per token, meaning long autonomous sessions that generate thousands of tokens cost the same as a short interaction.

## Key Findings

### Finding 1: GitHub Pro Subscription Includes Copilot Premium Requests

GitHub Pro subscriptions include access to GitHub Copilot with a monthly allowance of premium requests. These premium requests enable:
- Access to advanced models including Claude Opus 4.6
- Agent mode in VS Code, github.com chat, and GitHub Copilot Coding Agent
- Extended autonomous work sessions from a single prompt
- Tool use (file read/write, terminal commands, web search)

The critical insight is that a single premium request can trigger an agent session that runs for 30-60+ minutes autonomously, performing complex multi-step tasks.

### Finding 2: Direct API Pricing Is Token-Based

When using the Anthropic API directly, pricing is based on input and output tokens:
- Costs scale linearly with conversation length and complexity
- Extended agent loops that make many tool calls accumulate significant token costs
- A 60-minute autonomous agent session could process hundreds of thousands of tokens

### Finding 3: The Leverage Multiplier

The fundamental cost advantage of GitHub Copilot agent mode:

| Aspect | Direct API | Copilot Agent Mode |
|--------|-----------|-------------------|
| Pricing model | Per token (input + output) | Per premium request |
| Long sessions | Cost scales with tokens | Flat per-prompt cost |
| Tool calls | Each call adds tokens | Included in session |
| Model access | Pay market rate | Included in subscription |

**The leverage:** One premium request → one extended autonomous session → substantial work output at a fraction of per-token cost.

### Finding 4: Maximizing the GitHub Pro Value

Strategies to maximize cost efficiency:

1. **Batch work into single prompts** — Give the agent comprehensive instructions that enable long autonomous work sessions rather than many short interactions
2. **Use the task queue pattern** — Pre-define detailed tasks so the agent can work independently
3. **Leverage agent mode features** — File operations, terminal commands, and web search are all included
4. **Schedule strategically** — Space out agent sessions to stay within premium request limits
5. **Use Copilot Coding Agent for PRs** — The coding agent (triggered via issues/PRs) can run extended sessions

### Finding 5: GitHub Actions as Free Orchestration

GitHub Actions provides free workflow minutes for public repositories and included minutes for Pro accounts:
- **Public repos:** 2,000 minutes/month free
- **Pro accounts:** 3,000 minutes/month included
- Cron scheduling enables self-waking behavior
- Can create issues that trigger Copilot agent sessions

## Architecture Recommendation

The most cost-efficient architecture combines:

```
GitHub Actions (free cron) → Create Issue → Copilot Agent Mode (premium request) → Commit Results
```

This gives you:
- **Zero infrastructure cost** — Everything runs on GitHub
- **Minimal per-task cost** — One premium request per agent session
- **24/7 operation** — Cron scheduling handles wake cycles
- **Persistent state** — Git repository stores all results
- **Self-improving** — Agent can modify its own tasks and prompts

## Optimization Strategies

### Prompt Engineering for Maximum Autonomy

Structure prompts to maximize the work done per premium request:

1. **Be specific about deliverables** — Tell the agent exactly what files to create/modify
2. **Provide context upfront** — Include relevant background so the agent doesn't need to ask
3. **Define acceptance criteria** — Clear done conditions prevent premature completion
4. **Chain tasks in a single prompt** — "Do A, then B, then C" in one prompt

### Scheduling Strategy

For a 24/7 agent with budget constraints:

| Frequency | Sessions/Month | Use Case |
|-----------|---------------|----------|
| Every 6 hours | ~120 | Active development |
| Every 12 hours | ~60 | Steady research |
| Daily | ~30 | Light maintenance |
| Every other day | ~15 | Minimal operation |

Choose based on your monthly premium request budget.

## Sources

- GitHub Copilot documentation
- GitHub Pro subscription details
- Anthropic API pricing page
- GitHub Actions documentation on scheduled workflows

## Next Steps

- [ ] Monitor actual premium request consumption over a week
- [ ] Benchmark: measure actual work output per agent session
- [ ] Refine scheduling frequency based on quota usage data
- [ ] Experiment with prompt structures to maximize session length
