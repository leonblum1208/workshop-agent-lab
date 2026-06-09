# Workshop Agent Lab

Welcome. In this lab you will build an agentic system with GitHub Copilot CLI.

The final goal is a grounded report about future public events in Europe that could create unusual flight demand. The system should find signals, evaluate them, and explain what a human analyst should look at next.

You will not start by writing an agent framework. You will build with Copilot CLI-native building blocks:

- **skills**: reusable instructions for a capability
- **agents**: specialist roles with their own instructions and tool access
- **the main Copilot session**: the coordinator that plans, delegates, and combines results
- **public data sources**: evidence for the report

The README is the main guide. Move through it at your own pace.

## License

This workshop material is released under the MIT License. See [LICENSE](LICENSE).

## How Technical Is This?

You do not need to be a developer to participate.

You will use a terminal, but most steps can be done by asking Copilot CLI to create or edit files for you. If terminal commands are unfamiliar, work in pairs and copy the commands exactly.

Use this rule of thumb:

- **Green path**: use Copilot prompts, inspect what it created, and discuss the result.
- **Yellow path**: edit Markdown files by hand if you are comfortable.
- **Red path**: write code only if your team wants to go further.

In this lab, Markdown files are just text files. Skills and agents are mostly structured text, not software engineering.

## Tiny Glossary

| Term | Meaning in this lab |
| --- | --- |
| Copilot CLI | The tool you chat with in the terminal. It can read files, create files, run commands, and use agents. |
| Skill | Reusable instructions for doing one thing well, for example "query pageviews" or "write a grounded report." |
| Agent | A named specialist role, for example "event scout" or "evidence reviewer." |
| Orchestration | Letting one lead agent coordinate smaller specialist agents and combine their results. |
| Evidence | Public data, links, API results, or saved files that support a claim. |
| Grounded report | A report that clearly says what was observed, what is assumed, and what is uncertain. |

## What You Will Build

By the end, your repo should contain:

```text
.github/
  agents/
    one-or-more-custom-agents.agent.md
  skills/
    multiple-skill-folders/
      SKILL.md
outputs/
  event-demand-report.md
```

Your final report should answer:

```text
Which upcoming public events in Europe should an airline analyst look at because they may affect flights booked or taken?
```

Important: public data can suggest demand signals. It does not prove future bookings.

## Rough Timing

Use this as a guide, not a rule.

| Time | Focus |
| --- | --- |
| 0:00-0:30 | Start Copilot CLI and play with the warm-up examples |
| 0:30-1:00 | Learn skills and create your first own skill |
| 1:00-1:45 | Learn agents and create your first own agent |
| 1:45-2:30 | Explore public data sources yourself |
| 2:30-3:30 | Build skills and agents for the event-demand challenge |
| 3:30-4:00 | Generate, review, and improve the final report |
| 4:00+ | Add another agent, HTML output, or a use case from your own work |

## 1. Start Copilot CLI

Open a terminal in this folder:

macOS / Linux:

```bash
cd workshop-agent-lab
```

Windows PowerShell:

```powershell
cd workshop-agent-lab
```

Check that Copilot CLI is installed:

```bash
copilot --version
```

If you see `command not found` or a similar error, stop here and ask a facilitator. Do not spend workshop time debugging installation alone.

If this works, start Copilot:

```bash
copilot
```

Then try:

```text
/env
/skills
/agent
```

These commands show what Copilot loaded from the repository.

## 2. Authenticate

Use GitHub SSO as the default path:

```bash
copilot login
```

Follow the browser flow and sign in with your GitHub account.

If SSO does not work, ask a facilitator for the LiteLLM fallback details. Then use:

macOS / Linux:

```bash
export COPILOT_PROVIDER_BASE_URL="<LITELLM_BASE_URL>/v1"
export COPILOT_PROVIDER_API_KEY="<WORKSHOP_KEY>"
export COPILOT_MODEL="<MODEL_NAME>"
```

Windows PowerShell:

```powershell
$env:COPILOT_PROVIDER_BASE_URL = "<LITELLM_BASE_URL>/v1"
$env:COPILOT_PROVIDER_API_KEY = "<WORKSHOP_KEY>"
$env:COPILOT_MODEL = "<MODEL_NAME>"
```

Then test:

```bash
copilot -p "Reply with exactly READY" --allow-all-tools --silent
```

If you are on a managed laptop and something fails, work with a partner or ask a facilitator. The lab is designed so teams can share one working setup.

## 3. Warm-Up: Skill And Agent

This repo starts with one unrelated example:

```text
.github/skills/poem-writer/SKILL.md
.github/agents/poet.agent.md
```

The example is intentionally not about aviation. It lets you learn the mechanics first.

In Copilot CLI, try:

```text
Use the poem-writer skill to write a 6-line poem about an airport at sunrise.
```

Then try the agent:

```text
Use the poet agent to write a short poem about data analysts discovering a pattern.
```

You can use agents and skills interchangeably in prompts:

- tell Copilot to use a specific skill
- tell Copilot to use a specific agent
- ask normally and let Copilot infer what fits

The files are plain Markdown. Open them and inspect how little structure is needed.

## 4. Create Your First Skill

Write the first skill yourself. This is the moment where you learn what a skill actually is.

Create a new folder and file:

```text
.github/skills/concise-summarizer/SKILL.md
```

You can copy the structure from:

```text
templates/skill-template.md
```

Write a simple skill for summarizing long findings into five business-readable bullets.

Minimum content:

```text
---
name: concise-summarizer
description: Use this skill to summarize long findings into five business-readable bullets.
license: MIT
---

# Concise Summarizer

Use this skill when ...

Rules:

- ...
- ...
- ...
```

After you wrote it, ask Copilot to review it:

```text
Review my concise-summarizer skill.
Is it clear when to use it?
Would you follow the rules correctly?
Suggest improvements, but do not edit the file yet.
```

Restart Copilot CLI after creating skills or agents so they load reliably:

```text
/exit
```

Then:

```bash
copilot
```

Check:

```text
/skills
```

Checkpoint:

```text
Use my new skill on README.md and show me what it does.
```

## 5. Create Your First Agent

Create an agent either through the CLI or by writing a file.

Friendly CLI flow:

```text
/agent
```

Choose **Create new agent**. You can create it in the project so your team can share it:

```text
.github/agents/
```

File flow:

```text
.github/agents/<your-agent-name>.agent.md
```

You can copy:

```text
templates/agent-template.agent.md
```

Both flows are valid. The CLI flow is friendlier. The file flow makes the structure visible and easier to version.

If you want Copilot to create the file for you, paste:

```text
Create a new project agent called evidence-reviewer.
It should review a report for unsupported claims, missing sources,
and overconfident wording.
Put it in .github/agents/evidence-reviewer.agent.md.
Keep it beginner-friendly.
```

Checkpoint:

```text
Use my new agent to complete a small task. Then explain which instructions it followed.
```

## 6. Understand Orchestration

In this lab, orchestration means:

```text
Lead Copilot session
  -> delegates to specialist agents
  -> specialist agents use skills
  -> tools or shell commands fetch evidence
  -> lead combines everything into a report
```

Useful commands:

```text
/agent    browse or create agents
/skills   inspect available skills
/tasks    inspect subagents and commands
/fleet    enable parallel subagent execution
/env      inspect loaded instructions, skills, agents, and tools
```

For the final challenge, a useful team could be:

- one lead agent that coordinates the investigation
- one event-search agent
- one public-attention agent
- one evidence-review agent
- skills for specific data sources or report formats

You decide the actual design.

## 7. Explore Data Sources Yourself

Before looking at suggestions, spend time searching for data sources.

Your question:

```text
What public data could indicate that an upcoming event might affect flights booked or taken in Europe?
```

Look for sources with:

- future dates
- location or city
- event size or popularity signal
- usable access from a browser or API
- clear limitations

Ask Copilot to help, but make it show sources and tradeoffs:

```text
Help me find public data sources for upcoming European events
that may affect flight demand.
Prioritize sources I can query without authentication.
Give me source, signal type, access method, and limitation.
```

Do not build yet. First compare options with your group.

## 8. Sources We Found Useful

After your own exploration, compare against these:

**Ticketmaster Discovery API**
Upcoming concerts, sport, arts, venues, and city-based events. Strong signal, but API-key access may be needed.

**Wikimedia Pageviews**
Historical and current attention signal for artists, events, cities, teams, and festivals. No auth.

**Nager.Date**
Public holidays by country and year. No auth.

**Open-Meteo**
Weather forecast and historical weather context. No auth.

**GDELT**
News/event signal for unusual public attention, disruptions, protests, announcements, or city-level topics. No auth but noisy and rate-limited.

You do not need all five. Pick a small set and make it work.

### Optional: Ticketmaster API Key

Ticketmaster is useful for upcoming events, but it may require an API key.

This is optional. If account creation or API keys are annoying, skip Ticketmaster and use no-auth sources.

To get one:

1. Go to the Ticketmaster Developer Portal: `https://developer.ticketmaster.com/`
2. Create or sign in to a developer account.
3. Create an app/project.
4. Copy the Consumer Key / API key for the Discovery API.
5. Store it as an environment variable.

macOS / Linux:

```bash
export TICKETMASTER_API_KEY="<YOUR_TICKETMASTER_KEY>"
```

Windows PowerShell:

```powershell
$env:TICKETMASTER_API_KEY = "<YOUR_TICKETMASTER_KEY>"
```

Test with a browser or terminal:

macOS / Linux:

```bash
CITY="Berlin"
START="2026-06-10"
END="2026-09-10"

curl -sS "https://app.ticketmaster.com/discovery/v2/events.json" \
  --get \
  --data-urlencode "apikey=${TICKETMASTER_API_KEY}" \
  --data-urlencode "city=${CITY}" \
  --data-urlencode "startDateTime=${START}T00:00:00Z" \
  --data-urlencode "endDateTime=${END}T23:59:59Z" \
  --data-urlencode "size=5" \
  --data-urlencode "sort=date,asc"
```

Windows PowerShell:

```powershell
$CITY = "Berlin"
$START = "2026-06-10"
$END = "2026-09-10"

$params = @{
  apikey = $env:TICKETMASTER_API_KEY
  city = $CITY
  startDateTime = "${START}T00:00:00Z"
  endDateTime = "${END}T23:59:59Z"
  size = 5
  sort = "date,asc"
}

Invoke-RestMethod `
  -Uri "https://app.ticketmaster.com/discovery/v2/events.json" `
  -Body $params
```

If this takes too long during the workshop, skip it. Your system should still work with no-auth sources and clearly state that Ticketmaster was not used.

Non-developer shortcut:

```text
I have a Ticketmaster API key stored as TICKETMASTER_API_KEY.
Help me test whether it works for Berlin events in the next 90 days.
Explain each command before running it.
```

## 9. Final Challenge

Build an agentic system that can autonomously create a grounded future-event demand report.

Minimum:

- create at least **two skills**
- create at least **one custom agent**
- generate a report under `outputs/`
- include evidence, confidence, and limitations

Suggested skills:

- event-source-scout
- public-attention-signal
- holiday-baseline
- report-writer
- evidence-checker

Suggested agents:

- demand-signal-lead
- event-scout
- attention-analyst
- evidence-reviewer

Example prompt:

```text
Use our agents and skills to find upcoming public events in Europe
in the next 90 days that may deserve attention from an airline demand analyst.
Focus on events that could plausibly affect flights booked or taken.
Save a grounded markdown report to outputs/event-demand-report.md.
Include sources, confidence, and limitations.
```

If you are not sure what to build, start with this:

```text
Help our group design a simple agentic system for the final challenge.
We want one lead agent and two skills.
Ask us three short questions, then create the first draft files.
```

If your team is faster, extend the system:

- create an HTML report
- add a source-specific skill
- add an evidence-review agent
- build a second agentic system for a problem from your own work

## 10. Report Checklist

Before you are done, your report should include:

- event or signal name
- location
- date or date range
- source
- why it might matter for air travel
- confidence level
- limitations
- recommended human follow-up

Use cautious language:

```text
This is a public demand signal, not a booking forecast.
```

Avoid:

```text
This proves bookings will increase.
```

## 11. Useful Commands

Run a specific agent in prompt mode:

macOS / Linux:

```bash
copilot --agent poet -p "Write a 4-line poem about clean data." --allow-all-tools --silent
```

Windows PowerShell:

```powershell
copilot --agent poet -p "Write a 4-line poem about clean data." --allow-all-tools --silent
```

Run from this repo explicitly:

macOS / Linux:

```bash
copilot -C . --agent poet -p "Write a 4-line poem about clean data." --allow-all-tools --silent
```

Windows PowerShell:

```powershell
copilot -C . --agent poet -p "Write a 4-line poem about clean data." --allow-all-tools --silent
```

Start interactively:

```bash
copilot
```

Inspect what is loaded:

```text
/env
/skills
/agent
```

## 12. When You Get Stuck

Try one of these prompts:

```text
Inspect this repository and explain the next smallest step.
```

```text
Help me improve my agent description so Copilot knows when to use it.
```

```text
Review my skill. Is it specific enough for a model to follow?
```

```text
My report feels too speculative. Help me make the wording more evidence-based.
```

Good agent systems are usually not born perfect. You improve them by making responsibilities clearer.
