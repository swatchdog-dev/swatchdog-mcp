# Swatchdog in a live claude.ai session
A real claude.ai conversation with Swatchdog's connector endpoint added as a
custom connector — keyless (Settings → Connectors → Add custom connector, URL
only, no credentials). Nothing below is mocked; these are screenshots of the
session and the raw transcript of an automated client run against the deployed
service.

<img width="479" height="453" alt="Screenshot 2026-06-11 221625" src="https://github.com/user-attachments/assets/0a729feb-ab6f-4067-afd1-3fcac3282437" />

On connect, claude.ai lists the server's single tool — `check\_design\_drift` —
under Read-only tools, from the server's own annotations
(`readOnlyHint: true`), with per-tool approval controls.
> The loop is the client's throughout: the model decides when to call the check
> and what to do with the result. Swatchdog reports drift; it never edits code.
---
1 · Build-and-check (the intended loop)
> \*"My design system: primary color #b06ed0, radius 6px. Build me a styled button
> and check it with swatchdog before showing me."\*

<img width="1308" height="576" alt="prompt-1-build-and-check" src="https://github.com/user-attachments/assets/258faf96-eb1e-4a70-89d9-9b59531680a7" />

What to notice: the model composed a complete reference set from two user tokens,
ran the check before presenting, and summarized the clean pass in one line —
no report dump.
2 · Direct check (drift caught)
> \*"Use swatchdog to check `.x{color:#ff00ff;border-radius:9px}` against those tokens."\*

> <img width="736" height="545" alt="prompt-2-direct-check" src="https://github.com/user-attachments/assets/2a3e1912-e663-43c6-89cf-a97ae457e610" />

Both violations surfaced with the expected token and value
(`#ff00ff` → `color.primary` `#b06ed0`; `9px` → `radius.md` `6px`), plus the
corrected rule.
3 · Empty token set (the error path)
> \*"Run a swatchdog check on `a{color:#fff}` but with an empty token set `{}`."\*

<img width="744" height="555" alt="prompt-3-empty-tokens" src="https://github.com/user-attachments/assets/01728761-2f59-46b0-beb9-7235f0f806f4" />

With nothing to check against, Swatchdog declines rather than rubber-stamping
"0 violations" — a structured, actionable error, not a 5xx.
---
> Automated client run (every case, over the open internet)
The same service exercised by a scripted MCP client — keyless free lane, tool
metadata, clean pass, drift caught, empty-tokens error, and the bad-key `401`:
see `part-b-transcript.txt` for the raw output — all
cases green.
> 
---
Connector endpoint and tool reference: see the README.
Questions: hey@swatchdog.dev.
