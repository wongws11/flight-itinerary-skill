# Flight Itinerary Skill

An [Agent Skill](https://agentskills.io/) that converts natural-language
flight itineraries into standards-compliant iCalendar (`.ics`) files.

The skill handles direct flights, connections, return trips, airport timezone
resolution, daylight-saving changes, and multi-leg calendars. It bundles an
airport lookup table so it does not depend on files from another project.

## Contents

```text
flight-itinerary/
├── SKILL.md
└── assets/
    ├── airports.csv
    └── NOTICE.md
```

Agent Skills require only `SKILL.md`. The format also supports optional
`scripts/`, `references/`, and `assets/` directories. This skill stores its
airport and IANA timezone lookup in `assets/`, as recommended for static data.

## Install

Clone the repository first:

```sh
git clone https://github.com/wongws11/flight-itinerary-skill.git
cd flight-itinerary-skill
```

### OpenCode

Copy `flight-itinerary` to one of OpenCode's supported locations:

- Project: `.opencode/skills/flight-itinerary/`
- User: `~/.config/opencode/skills/flight-itinerary/`
- Cross-agent user location: `~/.agents/skills/flight-itinerary/`

OpenCode can also load the cloned repository directly by adding its path to
`skills.paths` in `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "skills": {
    "paths": ["/path/to/flight-itinerary-skill"]
  }
}
```

Restart OpenCode after installing or updating the skill.

### Claude

For a personal [Claude Code](https://code.claude.com/docs/en/skills) skill
available in every project:

```sh
mkdir -p ~/.claude/skills
cp -R flight-itinerary ~/.claude/skills/
```

For a project-only skill, copy the directory to the project's
`.claude/skills/` directory instead:

```sh
mkdir -p /path/to/project/.claude/skills
cp -R flight-itinerary /path/to/project/.claude/skills/
```

Claude Code detects skill changes automatically. Restart it if a newly created
top-level skills directory is not detected. Invoke the skill explicitly with
`/flight-itinerary`, or ask Claude to create an ICS file from an itinerary.

For [claude.ai](https://claude.ai), create an uploadable archive:

```sh
zip -r flight-itinerary.zip flight-itinerary
```

Open **Customize > Skills**, upload `flight-itinerary.zip`, and enable it.
Custom skills require code execution to be enabled. The ZIP must contain the
`flight-itinerary` directory at its root, which the command above preserves.

### ChatGPT and Codex

[Codex](https://developers.openai.com/codex/skills/) discovers personal skills
from `~/.agents/skills`. This installation is available to Codex CLI, the
Codex IDE extension, and the ChatGPT desktop app:

```sh
mkdir -p ~/.agents/skills
cp -R flight-itinerary ~/.agents/skills/
```

For a repository-only skill, copy it into the repository's `.agents/skills/`
directory:

```sh
mkdir -p /path/to/project/.agents/skills
cp -R flight-itinerary /path/to/project/.agents/skills/
```

Alternatively, open Codex and ask its built-in installer to fetch this skill:

```text
$skill-installer
Install the flight-itinerary skill from
https://github.com/wongws11/flight-itinerary-skill/tree/main/flight-itinerary
```

Codex detects installed skills automatically; restart it if the skill does not
appear. Use `/skills` or type `$flight-itinerary` to invoke it explicitly.

ChatGPT Work on the web installs reusable skills through plugins rather than
directly from standalone skill repositories. This repository is currently a
standalone Agent Skill, not a ChatGPT plugin.

## Usage

Ask the agent to create an ICS file from a flight itinerary, for example:

> Create a calendar file for BA117 from LHR to JFK on September 8, departing
> at 8:20 AM and arriving at 11:05 AM.

The skill writes the `.ics` file into the active workspace.

## Validation

Validate the skill with the official Agent Skills reference tool:

```sh
uvx --from skills-ref agentskills validate ./flight-itinerary
```

## License

The skill instructions and repository documentation are licensed under the
MIT License. The airport lookup is separately licensed and attributed in
[`flight-itinerary/assets/NOTICE.md`](flight-itinerary/assets/NOTICE.md).
