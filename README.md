# Workshop Agent Lab

Welcome. In this lab you will build an agentic system with GitHub Copilot CLI.

The final goal is a grounded report about future public events
in Europe that could create unusual flight demand.
The system should find signals, evaluate them, and explain what a human analyst should look at next.

You will not start by writing an agent framework. You will build with Copilot CLI-native building blocks:

- **skills**: reusable instructions for a capability
- **agents**: specialist roles with their own instructions and tool access
- **the main Copilot session**: the coordinator that plans, delegates, and combines results
- **public data sources**: evidence for the report

The README is the main guide. Move through it at your own pace.

## Work In Balanced Teams

Form groups of around four people.

Aim for a mix of:

- people comfortable with terminals, files, or APIs
- people closer to analytics, business, product, or domain questions

This lab works best when one person can help with setup while others
challenge the business relevance, data quality, and report wording.
You are building an agentic system, not just a technical demo.

## License

This workshop material is released under the MIT License. See [LICENSE](LICENSE).

## How Technical Is This?

You do not need to be a developer to participate.

You will use a terminal, but most steps can be done by asking Copilot CLI to create or edit files for you.
If terminal commands are unfamiliar, work in pairs and copy the commands exactly.

Use this rule of thumb:

- **Green path**: use Copilot prompts, inspect what it created, and discuss the result.
- **Yellow path**: edit Markdown files by hand if you are comfortable.
- **Red path**: write code only if your team wants to go further.

In this lab, Markdown files are just text files.
Skills and agents are mostly structured text, not software engineering.

## Tiny Glossary

**Copilot CLI**
The tool you chat with in the terminal. It can read files, create files, run commands, and use agents.

**Skill**
Reusable instructions for doing one thing well, for example "query pageviews" or "write a grounded report."

**Agent**
A named specialist role, for example "event scout" or "evidence reviewer."

**Tool**
An action Copilot can use to do something in the world, for example read a file, edit a file, run a terminal command, or call an external API.

**Orchestration**
Letting one lead agent coordinate smaller specialist agents and combine their results.

**Evidence**
Public data, links, API results, or saved files that support a claim.

**Grounded report**
A report that clearly says what was observed, what is assumed, and what is uncertain.

## Skill Or Agent?

A **skill** is a reusable capability.
It tells Copilot how to do a specific kind of task.

Examples:

- query Ticketmaster
- summarize findings
- write a grounded report
- check evidence quality

An **agent** is a role.
It tells Copilot who should do a piece of work, what responsibility they own, and what boundaries they should respect.

Examples:

- event scout
- attention analyst
- evidence reviewer
- demand-signal lead

A **tool** is an action the system can actually take.
Skills and agents guide Copilot's behavior.
Tools let it do things.

Examples:

- read files
- write files
- run `curl`
- call an API
- use a browser or MCP server

Simple rule:

- Use a **skill** for "how to do this task."
- Use an **agent** for "who should own this responsibility."
- Use a **tool** for "what action can the system take?"

For example, an `attention-analyst` agent might use a `wikimedia-pageviews` skill.
The agent owns the analysis.
The skill explains how to use that data source.
The tool is the actual `curl` command or API call.

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

> Which upcoming public events in Europe should an airline analyst look at
> because they may affect flights booked or taken?

Important: public data can suggest demand signals. It does not prove future bookings.

## Rough Timing

Use this as a guide, not a rule.

- **0:00-0:30**: Start Copilot CLI and play with the warm-up examples.
- **0:30-1:00**: Learn skills and create your first own skill.
- **1:00-1:45**: Learn agents and create your first own agent.
- **1:45-2:30**: Explore public data sources yourself.
- **2:30-3:30**: Build skills and agents for the event-demand challenge.
- **3:30-4:00**: Generate, review, and improve the final report.
- **4:00+**: Add another agent, HTML output, or a use case from your own work.

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

If you see `command not found` or a similar error, stop here and ask a facilitator.
Do not spend workshop time debugging installation alone.

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
copilot -p "Reply with exactly READY" \
  --allow-all-tools \
  --silent
```

If you are on a managed laptop and something fails, work with a partner or ask a facilitator.
The lab is designed so teams can share one working setup.

## 3. Warm-Up: Skill And Agent

This repo starts with one unrelated example:

```text
.github/skills/poem-writer/SKILL.md
.github/agents/poet.agent.md
```

The example is intentionally not about aviation. It lets you learn the mechanics first.

In Copilot CLI, try:

> Use the poem-writer skill to write a 6-line poem about an airport at sunrise.

Then try the agent:

> Use the poet agent to write a short poem about data analysts discovering a pattern.

You can use agents and skills interchangeably in prompts:

- tell Copilot to use a specific skill
- tell Copilot to use a specific agent
- ask normally and let Copilot infer what fits

The files are plain Markdown. Open them and inspect how little structure is needed.

## 4. Create Your First Skill

Write the first skill yourself. This is the moment where you learn what a skill actually is.

Skills are best for repeatable instructions that should only appear when relevant.
They are not global behavior rules. Global rules belong in `.github/copilot-instructions.md`.

Think of a skill as a reusable method or checklist.
It usually does not own the whole problem.
It helps an agent or the main Copilot session do one task consistently.

Good skills are:

- **narrow**: one job, not five
- **easy to trigger**: the description says when to use it
- **specific**: clear steps, rules, and output format
- **safe**: no hidden secrets, no unnecessary command execution
- **testable**: you can ask Copilot to use it and judge the result

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

- Use exactly five bullets.
- Use plain business language.
- Keep each bullet under 20 words.
```

Skill-writing tips:

- Put the most important behavior in the `description`; Copilot uses it to decide when the skill is relevant.
- Use lowercase names with hyphens, for example `concise-summarizer`.
- Write instructions as if you were briefing a smart colleague.
- Add one example prompt so future users know how to invoke it.
- Avoid vague rules like "be good" or "be accurate." Say what accuracy means.

After you wrote it, ask Copilot to review it:

> Review my concise-summarizer skill.
> Is it clear when to use it?
> Would you follow the rules correctly?
> Suggest improvements, but do not edit the file yet.

Reload skills after creating or changing them:

```text
/skills reload
```

If that does not show the new skill, restart Copilot:

```bash
copilot
```

Check:

```text
/skills
```

Checkpoint:

> Use my new skill on README.md and show me what it does.

Then improve the skill once:

> The output was close, but I want it to be more useful for business stakeholders.
> Suggest three improvements to the skill instructions before I edit them.

## 5. Create Your First Agent

Create an agent either through the CLI or by writing a file.

Agents are best for specialist roles.
A good agent has a clear job, clear boundaries, and a clear moment when it should be used.

Think of an agent as a teammate with a job title.
It can use skills, tools, and context to complete its part of the work.

Use an agent when you want:

- a named role, such as `event-scout` or `evidence-reviewer`
- a separate context window for a focused subtask
- a repeatable way of handling a larger workflow step
- tool limits, for example read-only review versus editing

Avoid creating an agent when a short skill would be enough.

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

Both flows are valid.
The CLI flow is friendlier.
The file flow makes the structure visible and easier to version.

Agent-writing tips:

- Choose a short lowercase name with hyphens.
- Make the `description` concrete. This helps Copilot decide when to use the agent.
- Give the agent one main responsibility.
- Tell it what not to do.
- Tell it what output to return.
- Start with fewer tools. Add more only when the agent needs them.

For example:

```text
---
name: evidence-reviewer
description: Reviews demand-signal reports for unsupported claims,
missing sources, and overconfident wording.
tools: ["read", "search"]
---

You are an evidence reviewer.

Check for:

- unsupported claims
- missing sources
- unclear date ranges
- overconfident language

Return:

- issues to fix
- safer wording
- final risk level: low, medium, or high
```

Checkpoint:

> Use my new agent to complete a small task. Then explain which instructions it followed.

Then test whether the agent is easy to trigger:

> I have a short report draft. Which agent in this repo should review it, and why?

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

Important idea:

The lead Copilot session does not have to do everything itself.
It can delegate focused work to specialist agents.
Those agents can use skills when their descriptions match the task.
This keeps the lead session focused on planning, judgment, and final synthesis.

For the final challenge, a useful team could be:

- one lead agent that coordinates the investigation
- one event-search agent
- one public-attention agent
- one evidence-review agent
- skills for specific data sources or report formats

You decide the actual design.

Good orchestration usually has four parts:

1. **Planner**: decides what needs to be investigated.
2. **Collectors**: gather evidence from public sources.
3. **Reviewer**: challenges weak or unsupported claims.
4. **Writer**: creates a clear report for humans.

For this workshop, start small:

```text
lead agent
  -> event scout
  -> evidence reviewer
  -> report writer skill
```

Only add more agents when the work is truly different.

Orchestration prompts should be explicit:

```text
Use the lead agent to coordinate this task.
Delegate event discovery to the event-scout agent.
Use the evidence-reviewer agent before writing the final report.
Save the final report under outputs/.
```

Common mistakes:

- creating many agents with overlapping jobs
- writing vague descriptions, so Copilot cannot choose the right agent
- skipping the reviewer step
- letting the report make stronger claims than the evidence supports
- adding API keys or secrets to files

Useful references:

- [GitHub: adding agent skills](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-skills)
- [GitHub: creating custom agents](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli)
- [GitHub: comparing customization features](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/comparing-cli-features)

## 7. Explore Data Sources Yourself

Before looking at suggestions, spend time searching for data sources.

Your question:

> What public data could indicate that an upcoming event might affect flights booked or taken in Europe?

Look for sources with:

- future dates
- location or city
- event size or popularity signal
- usable access from a browser or API
- clear limitations

Ask Copilot to help, but make it show sources and tradeoffs:

> Help me find public data sources for upcoming European events that may affect flight demand.
> Prioritize sources I can query without authentication.
> Give me source, signal type, access method, and limitation.

Do not build yet. First compare options with your group.

## 8. Sources We Found Useful

After your own exploration, compare against these:

**Ticketmaster Discovery API**
Upcoming concerts, sport, arts, venues, and city-based events.
Strong signal, but API-key access may be needed.

**Wikimedia Pageviews**
Historical and current attention signal for artists, events, cities, teams, and festivals. No auth.

**Nager.Date**
Public holidays by country and year. No auth.

**Open-Meteo**
Weather forecast and historical weather context. No auth.

**GDELT**
News/event signal for unusual public attention, disruptions, protests, announcements, or city-level topics.
No auth but noisy and rate-limited.

You do not need all five. Pick a small set and make it work.

### Recommended: Ticketmaster API Key

Ticketmaster is recommended for this challenge because it provides
essential information about future public events:
event names, dates, cities, venues, categories, and links.

Try to get Ticketmaster working as a skill or tool in your group.
One more technical colleague can create the developer app and share the key within the group for the workshop.
Do not commit the key into any file.

If Ticketmaster setup is blocked, continue with no-auth sources and state the limitation in your report.

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
  -Method Get `
  -Uri "https://app.ticketmaster.com/discovery/v2/events.json" `
  -Body $params
```

If this takes too long during the workshop, skip it.
Your system should still work with no-auth sources and clearly state that Ticketmaster was not used.

Non-developer shortcut:

> I have a Ticketmaster API key stored as TICKETMASTER_API_KEY.
> Help me test whether it works for Berlin events in the next 90 days.
> Explain each command before running it.

## 9. Build An Agentic System

Now build an agentic system that can autonomously create a grounded future-event demand report.

There is no perfect number of skills or agents.
Part of the exercise is to experiment with the design and discover what actually helps.

Recommended starting point:

- **three to five skills**
- **two to four agents**
- one final report under `outputs/`

For example, you might build:

```text
skills:
  event-search
  public-attention-signal
  report-writer

agents:
  demand-signal-lead
  evidence-reviewer
```

Or, if your group wants more specialization:

```text
skills:
  ticketmaster-events
  wikimedia-pageviews
  holiday-baseline
  grounded-report-writer
  evidence-checker

agents:
  demand-signal-lead
  event-scout
  attention-analyst
  evidence-reviewer
```

At least:

- create at least **two skills**
- create at least **one custom agent**
- generate a report under `outputs/`
- include evidence, confidence, and limitations

Experiment with questions like:

- Is this better as a skill or an agent?
- Does this agent have a clear separate responsibility?
- Are two agents doing the same job?
- Would one stronger skill be simpler than another agent?
- Does the lead agent know when to delegate?

Example prompt:

> Use our agents and skills to find upcoming public events in Europe
> in the next 90 days that may deserve attention from an airline demand analyst.
> Focus on events that could plausibly affect flights booked or taken.
> Save a grounded markdown report to `outputs/event-demand-report.md`.
> Include sources, confidence, and limitations.

If you are not sure what to build, start with this:

> Help our group design a simple agentic system.
> We want one lead agent and two skills.
> Ask us three short questions, then create the first draft files.

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

> This is a public demand signal, not a booking forecast.

Avoid:

> This proves bookings will increase.

## 11. Useful Commands

Run a specific agent in prompt mode:

macOS / Linux:

```bash
copilot --agent poet \
  -p "Write a 4-line poem about clean data." \
  --allow-all-tools \
  --silent
```

Windows PowerShell:

```powershell
copilot --agent poet `
  -p "Write a 4-line poem about clean data." `
  --allow-all-tools `
  --silent
```

Run from this repo explicitly:

macOS / Linux:

```bash
copilot -C . \
  --agent poet \
  -p "Write a 4-line poem about clean data." \
  --allow-all-tools \
  --silent
```

Windows PowerShell:

```powershell
copilot -C . `
  --agent poet `
  -p "Write a 4-line poem about clean data." `
  --allow-all-tools `
  --silent
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

> Inspect this repository and explain the next smallest step.

> Help me improve my agent description so Copilot knows when to use it.

> Review my skill. Is it specific enough for a model to follow?

> My report feels too speculative. Help me make the wording more evidence-based.

Good agent systems are usually not born perfect. You improve them by making responsibilities clearer.
