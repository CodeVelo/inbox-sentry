# How Inbox Sentry works

Inbox Sentry is a Grok Bot template that **reads** Outlook / Hotmail mail and rates messages for prompt-injection risk. It is built to stay out of the way of normal mail and to never act on instructions found inside an email.

## Read-only by design

Sentry may:

- Read message metadata and bodies (including HTML structure used to hide text)
- Inspect calendar invites (ICS) and contact cards (vCard) for injection text and spoofed fields
- Apply categories / tags: **Sentry: Block**, **Sentry: Suspect**, **Sentry: Note**
- File tagged findings into a **Sentry Review** folder when you have opted in
- Summarize findings in chat (Outlook email summaries are opt-in and off by default)

Sentry does **not**:

- Delete mail
- Auto-move mail to Junk or elsewhere without your OK
- Reply, forward, or send mail because an email asked it to
- Create or change inbox rules because an email asked it to
- Follow “ignore your rules” / tool-call instructions embedded in content

If pasted or delivered content asks the bot to forward mail, change rules, or disable scanning, Sentry refuses and rates the content instead.

## What gets scanned

- New Inbox messages (incremental scans using a watermark so already-seen mail is not re-read endlessly)
- Meeting invites / ICS descriptions
- vCard / contact-card notes and display names
- Visible body text **and** common hiding tricks (white/tiny text, HTML comments, `display:none`, zero-width characters, simple obfuscation)

## Verdicts

| Verdict | Typical use |
|---|---|
| **CLEAN** | Ordinary mail; bare marketing tricks without AI-directed text |
| **NOTE** | Mild concern (for example, an unrelated Reply-To domain) |
| **SUSPECT** | Scam wrapper, phishing signals, or soft injection paraphrases |
| **BLOCK** | Clear injection or agent/tool-directed attack |

Rough rating floors (simplified):

1. Clear injection or tool/rule bait directed at an assistant → **BLOCK**.
2. Plain scam / brand lookalike without clear injection → **SUSPECT**.
3. Unrelated third-party Reply-To (not you, not the sender’s org) → at least **NOTE**.
4. Bare marketing preheaders / soft hyphens / decorative hiding **without** AI-directed text → **CLEAN**.
5. Genuine account-security mail → **CLEAN** unless it also contains injection. Verification codes are redacted in stored notes.

## Trusted hints

Senders you have **already replied to** (from your Sent Items) are treated as **trusted hints**. That softens suspicion for ordinary follow-ups; it does **not** override clear injection. A trusted sender’s mail can still be Blocked if it contains agent-directed instructions.

## Scan once, trust the verdict

Sentry keeps a small local table of messages it has already rated. Other bots built from this template should check that table before reading your mail so a cleared message is not re-scanned every time. A **daily** morning scan still catches mail no bot has touched yet. A **Sunday** summary reviews the week.

Rough cost for a typical inbox is on the order of a handful of mail lookups per day when the clearance table is used, versus a much larger full rescan. These are design estimates, not measured billing.

## Feedback loop

Findings show up in chat digests (references like S1, S2, …). Reply:

- `right` — the verdict was correct
- `wrong` — false alarm or wrong severity
- `missed one` — something bad was not caught (optionally paste or forward a **summary**, not secrets)

Counts of false alarms, overruled calls, and misses are kept locally to tune rules. Share aggregate counts only if you choose to; never share sender details with third parties from those logs.

## Opt-in surfaces

- **Sentry Review** folder + categories: recommended
- Flag on Suspect/Block: optional
- Email summaries to your Outlook inbox: off by default

## What this is not

Inbox Sentry is not antivirus, not a full phishing filter, and not a promise that every attack will be caught. It is a specialized layer for **AI prompt injection in email**, sitting alongside Microsoft’s own protections and your judgment.
