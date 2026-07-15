# scene-intel

A scheduled AI research agent that runs weekly against my music label's vault.

**There is no code in this repo.** The system is a prompt, a schedule, and a set of guardrails. That's the point.

## What it does

Every Monday it researches the fast-moving parts of a niche music scene — chart movement, new premieres on a
tastemaker series, festival lineup waves, specific artists I'm tracking, new labels worth watching — and writes
what it found to a staging file in my vault, as its own single commit.

## The guardrails — the actual work

- **Every finding must carry a source URL.** No source, it doesn't get written. Not "try to cite" — a hard gate.
- **Staging is not canonical.** It writes only to an auto-research log, explicitly marked UNVERIFIED. It is
  forbidden from touching the canonical file it reads for context. Blast radius is one file.
- **"No change" is a valid answer.** If nothing moved, it says so. It is instructed not to pad.
- **Only report what's NEW.** It reads the canonical file first specifically so it doesn't re-report known facts.
- **Commit exactly one file.** Named in the prompt.
- **Zero connectors.** It reads the open web — pages strangers write. Giving a web-reading agent write access to
  email or a database is how prompt injection stops being theoretical. It gets web search and one repo.

## The run that matters

First scheduled run, 2026-07-15. Three of its five sources returned 403 at the network layer.

It wrote:

> **Could not access this cycle (blocked — beatport.com unreachable).** No verified chart data to report. Last
> verified snapshot remains the 2026-06-26 entry. — *source: access blocked, no URL retrievable*

Then it diagnosed its own limits, unprompted:

> ⚠️ Operational: Beatport, Bandcamp and SoundCloud are all network-blocked in this Cloud environment (403 at the
> gateway). That means chart tracking and PREMIERE tracking — two of the five things this routine exists to watch —
> didn't run this cycle and won't next cycle either unless that access gets fixed.

It found real things too — an artist's US debut two days out, a festival lineup that had gone from placeholder to
confirmed, a new label worth watching. But the sourced refusal is the part I'd point at. **A guardrail that only
holds when everything works isn't a guardrail.** An agent that invents a plausible chart when the chart is
unreachable is worse than no agent, because you'd believe it.

## Why this exists

I work in B2B/B2C support at a cybersecurity company, where I designed and run a production AI system that triages
real tickets — sourcing discipline, authority and scope gates, a "never assert an absence as fact" rule, a
self-improving correction loop. That system is my employer's, so I can't show it to anyone.

This is the same thinking on my own data, in public, where you can read the prompt and check the output yourself.

*The tracked artist names are redacted from the prompt — they're real people and this repo isn't.*

---

`prompt.md` is the entire system. Runs as a scheduled Claude Code routine against a private vault repo.
