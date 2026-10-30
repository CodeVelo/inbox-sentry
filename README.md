# Inbox Sentry

**What's new:** v1.2 adds every-folder scanning, a day-file verdict store with retention, and an on-demand scan protocol for other bots. See the [changelog](CHANGELOG.md). This release is a **work in progress** — feedback welcome via [GitHub Issues](https://github.com/CodeVelo/inbox-sentry/issues).

**A free [Grok Bot](https://grok.com) template by Jeff Wilkes.**

Inbox Sentry is a **read-only** guard for Outlook and Hotmail. It watches for emails that try to trick AI assistants — prompt injection hidden in ordinary-looking mail — and flags them for you to review. It never deletes, moves, or replies on its own.

## What it catches

Emails (and calendar invites / contact cards) that try to steer an AI assistant, including:

- Hidden instructions (white or tiny text, HTML comments, `display:none` blocks)
- Zero-width or obfuscated “ignore your rules” / tool-call bait
- Fake brand alerts and lookalike phishing wrapped as assistant instructions
- Booby-trapped calendar invites (ICS) and contact cards (vCard)
- Mismatched links, unrelated Reply-To addresses, and failed sender authentication used as scam wrappers

Findings are sorted into a **Sentry Review** folder and tagged:

| Tag | Meaning |
|---|---|
| **Block** | Clear injection or agent-directed attack |
| **Suspect** | Likely scam or suspicious wrapper (may not be clear injection) |
| **Note** | Worth a glance (for example, an unrelated Reply-To) |

People you have already replied to count as **trusted hints** — not hard overrides. A daily scan and a Sunday summary keep you in the loop. You can mark findings right / wrong / missed so the rules improve over time.

## What it never does

- Delete mail
- Move mail without your OK (except filing tagged findings into **Sentry Review** when you have opted in)
- Reply to senders
- Change importance, pin messages, or create inbox rules on its own
- Follow instructions found inside an email

Inbox Sentry is a **hint**, not a guarantee. Always use your own judgment on anything that looks sensitive.

## Install

1. Open the Grok Bot template: **[Install Inbox Sentry](https://x.ai/bot/H0gR9Cfsk3eXurkkcjaV2)**.
2. Create a bot from the template and connect your Microsoft / Outlook account when prompted.
3. Let it run a first scan. Review anything tagged Block or Suspect in **Sentry Review**.
4. Teach it with chat feedback: reply `right`, `wrong`, or `missed one` to digest items.
5. Run through [Verify your setup](docs/how-it-works.md#verify-your-setup) so other bots respect the CLEAN gate.

## Other bots and on-demand scans

Other bots should list **headers only** until Sentry has rated a message. Before reading any body, ask Sentry to scan specific message ids (max 25) and read only mail marked **CLEAN**. Details, JSON shapes, and a paste-ready instruction block are in [How it works](docs/how-it-works.md#on-demand-protocol-for-other-bots).

## Red Team results (2026-10-29)

Independent non-destructive testing against synthetic and live prompt-injection cases:

- **Phase 1:** **19/20** catches on fake attacks; **0 false alarms** on 5 harmless controls; **never followed** an injected instruction. The one shortfall was a fake Microsoft alert that failed sender checks — rated **Suspect** instead of **Block**.
- **Phase 2:** All **5** live test emails were **blocked**. (The fifth, which named Sentry directly, landed in Junk Email and was caught once Sentry began scanning Junk in v1.1.)
- A **weekly Red Team drill** continues so new techniques stay covered.

See [docs/red-team.md](docs/red-team.md) for methodology (attack **categories** only — no working payloads).

## Limits

- **Microsoft accounts only** today (Outlook / Hotmail). Gmail is not supported yet.
- Read-only by design: it will not “fix” mail for you beyond tagging and optional filing into Sentry Review.
- Trusted-hint logic is based on people you have replied to; it is a signal, not proof of safety.
- Prompt injection evolves. Treat Sentry as one layer of defense, not a complete shield.
- Daily scanning covers new mail (and a small first-pass window). Older mail is scanned on demand by message id, not in bulk.

## Report problems

Use [GitHub Issues](../../issues) with the forms provided:

- **False alarm** — something clean was flagged
- **Missed attack** — something bad slipped through
- **Feature request** — ideas for the template or docs

**Never paste real email contents, headers, or personal info into Issues.** Summarize the pattern instead. To report a bypass privately, see [SECURITY.md](SECURITY.md).

## Docs

- [How it works](docs/how-it-works.md) (storage, scan timing, on-demand protocol, verify checklist)
- [Red Team](docs/red-team.md)
- [Security policy](SECURITY.md)

## License

MIT © 2026 Jeff Wilkes

**By Jeff Wilkes**
