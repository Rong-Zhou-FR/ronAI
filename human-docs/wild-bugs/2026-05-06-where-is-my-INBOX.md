# IMAP INBOX missing — 7 PRs, 8 bugs, 2 repos

**Date:** 2026-05-06
**Repos:** A-core, A-lien
**Issue:** [A-lien#31](https://github.com/Ron-RONZZ-org/A-lien/issues/31)
**Author:** Robotika R.

## Symptoms

User ran `A retposto preni`. It reported success but the latest emails, which user sees in webmail, were never fetched. No errors message reported.

## Timeline

Each fix revealed the next bug, like peeling an onion:

| # | Bug | How we found it | How long it hid |
|---|---|---|---|
| 1 | Sync used Message-ID header for dedup instead of IMAP UID. `hash(raw_data)` fallback randomized per Python process → every sync re-downloads everything. | Code review of `sync_folder()` | Original code |
| 2 | `get_db()` created tables via DDL but never called `migrate()`. Schema changes (new columns) were silently ignored on existing databases. | `OperationalError: no such column: imap_uid` | PR #32 |
| 3 | DDL loop ran index on `imap_uid` before migration added the column. `CREATE TABLE IF NOT EXISTS` is a no-op on existing tables — doesn't add columns. | Same crash, second occurrence | PR #33 |
| 4 | Migration v2 tried `ALTER TABLE mesagxoj RENAME TO mesagoj` but DDL had already created empty `mesagoj`. Target table exists → rename fails. | Test failure after PR #33 | Years (v2 never ran before) |
| 5 | Old index was named `idx_mesagxoj_konto_uid` (diacritic). Migration v3 tried to drop `idx_mesagoj_konto_uid` (no diacritic). Old index survived → `DROP COLUMN uid` blocked by surviving index. | Test failure | since before A-ecosystem project started. The problem was also in autish legacy code.|
| 6 | Non-tuple FETCH responses (FLAGS-only) at even indices broke the `range(0, len, 2)` alternation loop. Subsequent tuples land at wrong indices → phantom unknown UIDs → duplicate storage. | Architect code review | Original code |
| 7 | `UID SEARCH ALL` capped at ~5000 results on some servers. Pagination only triggered on exact round numbers (5000/10000/20000). A 4999-result SEARCH stops paginating → newest UIDs never fetched. | autish-legacy comparison | PR #36 |
| 8 | **`list_folders()` parsed the separator `"/"` as the folder name.** IMAP LIST response: `(\Flags) "/" INBOX`. Split by `"`: `parts[-2]` = `'/'` (the separator). The rescue path checked for special-use flags (`\Archive`, `\Drafts`, etc.) — INBOX has none → `continue` → **INBOX silently dropped from the folder list.** | Live debugging on user's Migadu server | Original code, forever |

## Lessons Learned

### 1. A silent skip is worse than a crash

Bug #8: INBOX was silently dropped from `list_folders()` — no error, no warning, no hint. It just wasn't there. The `except Exception: pass` at line 154 of `client.py` swallowed any parsing anomalies.

**Action:** Remove bare `except Exception: pass`. If you must swallow, log a warning with the offending data.

### 2. Special-case rescue paths hide bugs

Bugs #5 and #8: The special folder name fallback (matching `\Archive`, `\Drafts`, etc.) accidentally "rescued" 5 of 6 folders. INBOX had no special-use flag on Migadu → it alone was dropped. The rescue path created the illusion that folder parsing worked.

**Action:** Test the unqualified path. If a fallback exists, also test what happens when the fallback doesn't match.

### 3. `CREATE TABLE IF NOT EXISTS` does not add columns

Bug #3: A common misconception. The `IF NOT EXISTS` clause checks table existence, not schema version. `CREATE TABLE` never alters an existing table.

**Action:** Migrations must handle column additions explicitly. Never rely on DDL replay to update existing tables.

### 4. "Works on my server" is not a pass

Bug #8 was invisible on Gmail, Outlook, and Dovecot default configs — they all include `\Inbox` in the LIST response. Only Migadu (and potentially other providers) omit it.

**Action:** Test against at least two different IMAP providers. Use different server software (Dovecot, Cyrus, Exchange).

### 5. autish-legacy was right (mostly)

Comparing against autish-legacy's `retposto.py` revealed multiple bugs:
- Regex-based folder parsing instead of `split('"')` — autish had the correct approach
- Per-message UID fetch instead of batch — autish was simpler and more robust
- Global MAX UID filter instead of per-folder set — autish was less error-prone

**Action:** When porting working code, don't assume the new implementation is better. Benchmark against the original.

### 6. Fragment the IMAP response parsing

Bug #6: The `range(0, len(data), 2)` pattern assumes perfect alternation of tuple/non-tuple in FETCH responses. A single FLAGS-only response breaks the entire loop.

**Action:** Always filter by type (`isinstance(item, tuple)`) rather than relying on positional alternation.

## Systemic Issues

### Test gaps

- `test_list_folders` used quoted folder names (`"INBOX"`, `"Sent"`) — never tested the unquoted format (`INBOX`) that real servers use
- No integration test that connects to a real IMAP server
- No test verifying migration v3 + v4 produce a working database

### Error reporting gaps

- UID regex failure: logged to `result.errors` but errors were displayed with `warning()` (yellow) not `error()` (red/stderr). User could miss them.
- `except Exception: pass` in `list_folders()` — completely silent

### Architectural gaps

- `get_db()` and `migrate()` were separate concerns with no contract. Every module had to remember to call `migrate()` — and A-lien forgot.
- DDL statements and migration steps both modify the same schema. No mechanism ensures they're compatible.

## Recommendations

1. **Add an IMAP smoke test** that connects to a disposable test account and verifies fetch/list/sync.
2. **Ban bare `except Exception: pass`** — enforce via ruff linting.
3. **Document the DDL-vs-migration contract** in AGENTS.md: DDL creates, migration alters.
4. **Audit all `split('"')` patterns** in IMAP responses — they're universally fragile. Use regex or an IMAP parser.
5. **Add a `--debug-imap` flag** that logs raw IMAP command/response to stderr for field debugging.

## Commit History

| PR | Repo | Description |
|----|------|-------------|
| A-lien#32 | A-lien | IMAP UID dedup + chunked fetch |
| A-lien#33 | A-lien | `get_db()` calls `migrate()` |
| A-lien#34 | A-lien | DDL-before-migration ordering fix |
| A-lien#36 | A-lien | Paginated SEARCH + robust parsing |
| A-lien#37 | A-lien | Mailbox count cross-validation |
| A-core#66 | A-core | `format_connection_error()` module |
| A-lien#38 | A-lien | Typed connection errors |
| A-lien#40 | A-lien | **Folder parser fix (the real bug)** |
