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

Clone the repository and copy or symlink the `flight-itinerary` directory into
a skills directory supported by your agent.

For OpenCode, supported locations include:

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

Restart the agent after installing or updating the skill.

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
