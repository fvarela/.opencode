---
description: List available golden patterns and check implemented status and drift for the current project
---

# Golden Patterns

Available patterns:
!`python3 ~/patterns/scripts/list.py`

Implemented in this project:
!`sed -n '/## Implemented patterns/,/^## /p' AGENTS.md 2>/dev/null || echo "none recorded"`

Display the pattern status for this project. Keep output terse; this is a lookup, not an essay.

Print three sections:
1. **Available patterns**: Display the table of available patterns from the library above.
2. **Implemented patterns**: Display the patterns implemented in this project from the section above (or indicate "None recorded" if the section is missing or empty).
3. **Drift**: Compare each pattern implemented in this project against the library:
   - If an implemented pattern's recorded version trails the library version, report it as an update available (e.g., `<pattern>: <recorded_version> -> <library_version>`).
   - If an implemented pattern no longer exists in the library, report it as missing from the library.
   - If all implemented patterns match the library version (or if no patterns are implemented), report no drift detected.

Follow up in natural language to apply, update, or capture a pattern.
