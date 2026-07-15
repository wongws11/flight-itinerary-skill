---
name: flight-itinerary
description: Convert natural-language flight itineraries into .ics files containing one or multiple flight events. Use only when a user asks to create, generate, export, or write an ICS/iCalendar file for flights.
license: MIT; bundled airport data is CC BY-SA 3.0 (see assets/NOTICE.md)
metadata:
  author: wongws11
  version: "1.0.0"
---

# Flight Itinerary

Create an iCalendar file from a natural-language flight itinerary. This skill creates flight events only. Do not use it for meetings, appointments, accommodation, ground transport, reminders, or general calendar events.

Support direct flights, connecting itineraries, return trips, and multiple unrelated flights in one request. Unless the user explicitly requests separate files, place every flight in one `.ics` file with one `VEVENT` per flight leg.

## Flight Extraction

Extract these details for every flight leg:

- Departure airport
- Arrival airport
- Local departure date and time
- Local arrival date and time
- Flight number, when supplied
- Airline, booking reference, terminal, gate, seat, and notes, when supplied

Treat each separately numbered or separately timed flight as its own leg. Do not combine connecting flights into one event. Apply shared details, such as a booking reference, to every applicable leg while allowing leg-specific details to override them.

Preserve the order of legs in the itinerary unless the user asks for chronological sorting.

## Airport Timezones

Use `assets/airports.csv` as the canonical airport dataset. It contains `iata`, `name`, and `tz` columns, where `tz` is an IANA timezone identifier.

- Match explicit three-letter IATA codes case-insensitively and prefer them over airport-name matches.
- When only an airport name or city is supplied, search `assets/airports.csv`.
- Ask for clarification if a city has multiple plausible airports or a supplied code does not identify an airport in the dataset.
- Interpret departure time using the departure airport's `tz`.
- Interpret arrival time using the arrival airport's `tz`.
- Apply the IANA timezone rules in effect on the flight date, including daylight-saving and historical offset changes. Never substitute a fixed offset.
- Convert departure and arrival to absolute instants before validating their order.
- Serialize the final event times in UTC to avoid incomplete `VTIMEZONE` definitions.

## Date Handling

- Resolve relative dates such as "tomorrow" against the current local date.
- For yearless dates, choose the next reasonable occurrence and report the assumed year.
- Account for arrival dates that differ locally because of overnight travel or crossing the International Date Line.
- Never infer a next-day arrival solely because its clock time is earlier. Use explicit itinerary context and the airport timezones.
- Ask for clarification if the arrival date cannot be established reliably.
- Preserve explicit seconds; otherwise use `00` seconds.

## Clarification Rules

Ask one concise question containing all unresolved items when any required detail is missing or conflicting. Required details are both airports and complete local departure and arrival date-times for every leg.

Always clarify when:

- An airport is ambiguous or absent from the bundled dataset.
- AM versus PM is unclear.
- A date or year cannot be resolved reliably.
- An arrival date is ambiguous across midnight or the International Date Line.
- It is unclear whether the input describes one flight or multiple legs.
- The timezone-aware arrival instant is not after departure.

Do not create a misleading file using guessed critical details. After receiving clarification, proceed without asking for confirmation unless the answer introduces another conflict.

## Event Content

Create one `VEVENT` for each flight leg.

Use this summary format:

- With flight number: `Flight BA123: LHR to JFK`
- Without flight number: `Flight: LHR to JFK`

Include useful supplied details in `DESCRIPTION`, one item per escaped newline. Suitable details include airline, flight number, booking reference, terminals, gates, seat, and user notes. Do not invent missing details.

Use the departure airport name in `LOCATION`. The summary and description must make the destination clear.

## ICS Format

Produce one calendar with this structure:

```ics
BEGIN:VCALENDAR
VERSION:2.0
PRODID:-//Agent Skills//Flight Itinerary//EN
CALSCALE:GREGORIAN
METHOD:PUBLISH
BEGIN:VEVENT
UID:unique-flight-value@example.invalid
DTSTAMP:20260101T120000Z
DTSTART:20260310T091500Z
DTEND:20260310T164500Z
SUMMARY:Flight BA123: LHR to JFK
LOCATION:London Heathrow Airport (LHR)
DESCRIPTION:Airline: British Airways\nFlight: BA123
STATUS:CONFIRMED
TRANSP:OPAQUE
END:VEVENT
END:VCALENDAR
```

Repeat the complete `VEVENT` block for every additional flight leg.

- Use UTC date-times in `YYYYMMDDTHHMMSSZ` format.
- Include a UTC `DTSTAMP` and a distinct, globally unique `UID` for each leg.
- Escape backslashes, commas, semicolons, and newlines in text values as `\\`, `\,`, `\;`, and `\n`.
- Fold content lines longer than 75 octets by inserting a line break followed by one space. Do not split a UTF-8 character.
- Use CRLF line endings when the available writing tool supports them.
- Do not include `VTIMEZONE` when all event date-times are serialized in UTC.
- Do not place Markdown fences, explanations, or comments inside the `.ics` file.

## Output

Write a descriptively named `.ics` file in the current workspace without modifying application source code. After creating it, report:

- The file path
- The number of flight legs included
- Any date or year assumptions

Keep the response concise.

## Example

Input:

> BA117 from LHR to JFK on September 8, departing 8:20 AM and arriving 11:05 AM. Return on BA116 September 15, leaving JFK at 8:10 PM and arriving LHR at 8:10 AM the next day.

Result: one `.ics` file containing two `VEVENT` blocks. Resolve each local time with its airport's IANA timezone, validate both flights as absolute instants, and serialize all four date-times in UTC.
