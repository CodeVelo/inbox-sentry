# Inbox Sentry for Outlook

**What's new:** v1.2 now scans every folder, a day-file verdict store with retention, and an on-demand scan protocol for other bots. See the [changelog](CHANGELOG.md).

This project is a **work in progress**. If something is unclear, wrong for your setup, or could be friendlier, please open a [GitHub Issue](https://github.com/CodeVelo/inbox-sentry/issues) — feedback helps.

Inbox Sentry does not automatically update. To stay informed about changes watch this repo, or follow @jwilkes on X, where every update starts with "Inbox Sentry update:".

**A free [Grok Bot](https://grok.com) template**

Inbox Sentry is built **Microsoft-first**. It is disgned for Microsoft Outlook, personal or business account.

Sentry is a **read-only** guard for your Outlook mail. It watches for email for **prompt injection** attacks where hidden instructions are tucked into ordinary-looking mail — and flags those messages for you to review. It never deletes, moves, or replies on its own.

## What it catches

Email, calendar invites, and contact cards that try to steer an AI assistant, including:

- Hidden instructions (white or tiny text, HTML comments, `display:none` blocks)
- Zero-width or obfuscated “ignore your rules” / tool-call bait
- Fake brand alerts and lookalike phishing wrapped as assistant instructions
- Mismatched links, unrelated Reply-To addresses, and failed sender authentication used as scam wrappers

When Sentry finds something concerning, it sorts that message into a **Sentry Review** folder and tags it so you can see why it matters:

| Tag | Meaning |
|---|---|
| **Block** | Clear injection or agent-directed attack |
| **Suspect** | Likely scam or suspicious wrapper (may not be clear injection) |
| **Note** | Worth a glance (for example, an unrelated Reply-To) |

People you have already replied to count as **trusted hints** — a soft signal that a follow-up is probably ordinary, not a hard override. A trusted sender’s mail can still be Blocked if it contains instructions aimed at an assistant.

A daily scan and a Sunday summary keep you in the loop. You can mark findings right / wrong / missed so the rules improve over time.

## What it never does

Read-only matters because mail is personal. Sentry is designed to **look and warn**, not to take over your inbox:

- Delete mail
- Move mail without your OK
- Reply to senders
- Change importance, pin messages, or create inbox rules on its own
- Follow instructions found inside an email

Inbox Sentry is a **hint**, not a guarantee. Always use your own judgment on anything that looks sensitive.

## How screening works and its limits

A script first scrubs each email to plain text and checks for hidden or off-screen text. Fixed rules decide the clear cases. A model judges only the ambiguous ones. The verdict is **CLEAN**, **NOTE**, **SUSPECT**, or **BLOCK**. Email text is never followed as instructions.

Until a message is marked **CLEAN**, other bots that share the mailbox should read only its headers. Details are in [Other bots and on-demand scans](#other-bots-and-on-demand-scans).

No filter catches everything, which is why those other bots stay on headers only. Very long emails may be only partly scanned today. Inbox Sentry is a second layer alongside Microsoft Defender, not a replacement. Always use your own judgment.

## Install

1. Open the Grok Bot template: **[Install Inbox Sentry](https://x.ai/bot/H0gR9Cfsk3eXurkkcjaV2)**.
2. Create a bot from the template and connect your Outlook account when prompted.
3. Let it run a first scan. Review anything tagged Block or Suspect in **Sentry Review**.
4. Teach it with chat feedback: reply `right`, `wrong`, or `missed one` to digest items.
5. Run through [Verify your setup](docs/how-it-works.md#verify-your-setup) so other bots respect the CLEAN gate.

## Other bots and on-demand scans

If you use more than one bot with the same Outlook mailbox, the others should treat Sentry as a gate. In practice that means other bots ask Sentry to scan an eamil before they read it.

Details, JSON shapes, and a paste-ready instruction block are in [How it works](docs/how-it-works.md#on-demand-protocol-for-other-bots).

## Red Team results (2026-10-29)

Non-destructive testing by my own Red Team bot, against synthetic and live prompt-injection cases:

- **Phase 1:** **19/20** catches on fake attacks; **0 false alarms** on 5 harmless controls; **never followed** an injected instruction. The one shortfall was a fake Microsoft alert that failed sender checks — rated **Suspect** instead of **Block**.
- **Phase 2:** All **5** live test emails were **blocked**. (The fifth, which named Sentry directly, landed in Junk Email and was caught once Sentry began scanning Junk in v1.1.)
- A **weekly Red Team drill** continues so new techniques stay covered.

See [docs/red-team.md](docs/red-team.md) for methodology (attack **categories** only — no working payloads).

## Design choices (and why they matter)

- **Microsoft-first for Outlook.** Sentry is built around the Microsoft mail experience so people who already live in Outlook get the best fit — categories, folders, and Graph field names that match how Outlook actually works.
- **Read-only by design.** It will not “fix” mail for you beyond tagging and optional filing into Sentry Review. That keeps your inbox under your control.
- **Trusted hints, not hard trust.** The “people you have replied to” signal softens suspicion for ordinary follow-ups; it is not proof of safety.
- **One layer of defense.** Prompt injection evolves. Treat Sentry alongside Microsoft’s own protections and your judgment — not as a complete shield.
- **New mail first; older mail on request.** Daily scanning covers new mail (and a small first-pass window). Older mail is scanned on demand by message id, not in bulk, so Sentry stays light on your mailbox.

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

MIT © 2026 Kronchie LLC

**By Kronchie LLC**
