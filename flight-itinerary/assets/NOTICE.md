# Airport Data Notice

`airports.csv` is a transformed lookup containing IATA code, airport name, and
IANA timezone fields extracted from the airport data used by
[flight-calendar-app](https://github.com/wongws11/flight-calendar-app).

Airport names originate from the
[Open Travel Data](https://github.com/opentraveldata/optd) public point of
reference dataset. Open Travel Data distributes that dataset under the
[Creative Commons Attribution-ShareAlike 3.0 Unported License](https://creativecommons.org/licenses/by-sa/3.0/).
The transformed lookup is distributed under the same license.

Timezone values are IANA timezone identifiers. Timezone behavior is supplied
by the timezone database available in the agent's runtime; this file does not
bundle timezone transition rules.

The data is provided as-is and may contain stale airport names, codes, or
timezone mappings. Agents must ask for clarification when a lookup is absent
or ambiguous rather than guessing.
