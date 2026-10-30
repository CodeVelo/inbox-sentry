# Changelog

All notable changes to the Inbox Sentry template. Dates are UTC.

## v1.2 — 2026-10-30

Work in progress. Feedback welcome via [GitHub Issues](https://github.com/CodeVelo/inbox-sentry/issues).

- **Every-folder scanning.** Daily scan walks every folder recursively in each connected mailbox. Each folder has its own watermark (`folderWatermarks` in `state.json`). Each run scans only mail after that watermark, at most **40 messages per folder** (oldest first; the watermark only advances past what was scanned, so overflow rolls to the next run), and skips anything already indexed.
- **First-connection backfill.** One-time pass: the newest 40 messages per folder from the last **30 days**. Older mail is never bulk-scanned; use on-demand by message id.
- **Verdict store layout.** Canonical store is `injection-guard/verdicts/`: day files `YYYY-MM-DD.json` (UTC), `index.json` keyed `<internetMessageId>||<userPrincipalName>`, `prune.py` retention, and permanent `summaries/YYYY-MM.json`. CLEAN kept 30 days; flagged (SUSPECT/BLOCK/NOTE/ERROR) kept 90 days.
- **Retired / deprecated files.** `junk-verdicts.json` is retired. Root `verdicts.json` is a deprecated compatibility copy. Prefer `verdicts/`.
- **On-demand protocol.** Other bots may list headers only, then ask Sentry to scan specific Graph message ids (max **25** per request) and read bodies only when the verdict is **CLEAN**. Docs include request/response shapes and a paste-ready instruction block.
- **Verify checklist.** New setup checklist: confirm watermarks advanced, test an on-demand scan, and confirm other bots refuse unscanned bodies.
- **Docs.** Expanded [How it works](docs/how-it-works.md) for storage, scan timing, and the other-bot gate. Still a work in progress.

## v1.1 — 2026-10-30
- **Junk Email scanning.** Sentry now scans the Junk Email folder (read-only, same rules as Inbox). Junk results are recorded, not alerted.
- **Verdict list.** Each scanned Junk message gets an entry in `junk-verdicts.json` (`CLEAN`, `SUSPECT`, `BLOCK`, or `ERROR`) so other bots can act only on mail Sentry has cleared.
- **Microsoft naming.** Stored fields use Microsoft Graph property names and lower camelCase (`userPrincipalName`, `parentFolderId`, `id`, `internetMessageId`, `receivedDateTime`, `from`, `subject`); Sentry's own fields follow suit (`scannedDateTime`, `verdict`, `reasons`, `linkDomains`). All timestamps are UTC.

## v1.0 — 2026-10-30
- Initial public release: read-only guard for Outlook/Hotmail that flags emails designed to hijack AI agents (hidden instructions, white text, fake "ignore your rules" prompts). Never deletes, moves, or replies.
