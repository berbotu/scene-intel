# scene-intel

A scheduled AI research agent that runs weekly and **reports what it couldn't find**.

**There is no code in this repo.** The whole system is `prompt.md`, a cron expression, and a repo to write to.
That's deliberate — the engineering here is in the constraints, not the plumbing.

---

## The problem it exists for

A research agent that says *"nothing new this week"* is useful. A research agent that invents a plausible chart
position because the source was down is **worse than nothing** — you'll believe it, you'll act on it, and you
won't find out for weeks.

Every guardrail below exists to make the first outcome likely and the second impossible.

---

## What happened on the first run

Three of five sources returned 403 at the network layer. Verbatim, what it wrote:

```markdown
### Beatport / Charts
- **Could not access this cycle (blocked — beatport.com unreachable).** No verified chart data
  to report. Last verified snapshot remains the 2026-06-26 entry.
  — *source: access blocked, no URL retrievable*
```

Then, unprompted, it diagnosed its own limits:

```
⚠️ Operational: Beatport, Bandcamp and SoundCloud are all network-blocked in this Cloud
environment (403 at the gateway). That means chart tracking and PREMIERE tracking — two of
the five things this routine exists to watch — didn't run this cycle and won't next cycle
either unless that access gets fixed.
```

It found real things too — a festival lineup that had gone from placeholder to confirmed, a new label worth
watching, a booking two days out. But **the refusal is the part worth looking at.** Every portfolio has a demo
that worked. A guardrail that only holds when everything works isn't a guardrail.

---

## The guardrails, and why each one is there

**Every claim carries a source URL, or it isn't written.**
Not "please cite sources" — a hard gate: *"EVERY finding MUST carry a source URL. No source = do NOT write it."*
The failure mode of research agents isn't laziness, it's confident synthesis from nothing. A URL is falsifiable.
An assertion isn't.

**Staging is not canonical.**
It reads the canonical file for context and is **forbidden from writing to it**. Everything goes to a separate
log, stamped `UNVERIFIED`. Worst case is a wrong line in a file marked "don't trust this yet" — not a corrupted
source of truth. Blast radius is one file, and that file is quarantined by design.

**"No change" is a valid answer.**
*"If a topic has nothing genuinely new, write 'No change since last update' — don't pad."* Without this, an agent
graded on output produces output. Padding is how a research log becomes noise, and noise is how it gets ignored.

**Only report what's NEW.**
Step 1 is always: read canonical first, don't re-report what's known. On the first run it correctly rejected three
finds as stale — an old set, an old track, an old collab — each flagged *"already old, not new."*

**Commit exactly one file.**
Named in the prompt. An agent with repo access and vague instructions is a merge conflict with opinions.

**Zero connectors.**
The scheduler offers Gmail, Drive, Slack, a database — with write access and **no permission prompt during runs**.
This agent reads the open web: pages written by strangers, which is exactly where prompt injection comes from. A
web-reading agent holding your email is the one combination worth refusing outright. It gets web search and one
repo. Nothing else.

---

## How it runs

A scheduled Claude Code routine. Weekly, Monday morning, pointed at a private vault repo. It checks out the repo,
researches, appends a dated section to the staging log, commits, and the result waits in the client.

**Nothing is pushed to me.** No notification, no message, no ping. It updates the file; I read it when I open the
vault. That's the rule the whole system runs on — an alert that arrives 200 times a week gets muted, and a muted
alert is a deleted alert with extra steps.

## Honest limits

- **The sandbox is blocked from the sources that matter most.** Two of five topics can't run from the cloud at
  all. It says so every week rather than quietly returning less. Not fixed — surfaced.
- **One run so far.** It gets less thin every Monday, and that's the only way it does.
- **The prompt hardcodes a domain.** It's not a general research agent, it's this one. Generalising it would mean
  weakening the constraints that make it work.
- **Tracked artist names are redacted** — they're real people and this repo isn't.

---

## Why this exists

I work in B2B/B2C support at a cybersecurity company, where I designed and run a production AI system that triages
real tickets — sourcing discipline, authority and scope gates, a *"never assert an absence as fact"* rule, a
self-improving correction loop. That system is my employer's, so I can't show it to anyone.

This is the same thinking on my own data, in public, where you can read the prompt and check the output yourself.

`prompt.md` is the entire system.
