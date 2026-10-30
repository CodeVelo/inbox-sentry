# Changelog

All notable changes to the Inbox Sentry template. Dates are UTC.

## v1.1 — 2026-10-30
- **Junk Email scanning.** Sentry now scans the Junk Email folder (read-only, same rules as Inbox). Junk results are recorded, not alerted.
- **Verdict list.** Each scanned Junk message gets an entry in `junk-verdicts.json` (`CLEAN`, `SUSPECT`, `BLOCK`, or `ERROR`) so other bots can act only on mail Sentry has cleared.
- **Microsoft naming.** Stored fields use Microsoft Graph property names and lower camelCase (`userPrincipalName`, `parentFolderId`, `id`, `internetMessageId`, `receivedDateTime`, `from`, `subject`); Sentry's own fields follow suit (`scannedDateTime`, `verdict`, `reasons`, `linkDomains`). All timestamps are UTC.

## v1.0 — 2026-10-30
- Initial public release: read-only guard for Outlook/Hotmail that flags emails designed to hijack AI agents (hidden instructions, white text, fake "ignore your rules" prompts). Never deletes, moves, or replies.
