# Tools

This skill owns no MCP tools. It reads mailbox state with tools owned by `mermail-manage-inbox`. Use only the read tools below.

## Conventions

- Pass structured arguments as **native JSON objects**. Never stringify an object into a string field such as `query`.
- Use the exact tool identifier exposed by the current host (for example `list_emails` or a host-qualified form like `Mermail:list_emails`). Do not manually add, strip, or invent prefixes inconsistently.
- Prefer mailbox `public_id` as `mailboxId` when `list_mailboxes` returns it.

## Tool notes

| Tool | Purpose | Risk |
| --- | --- | --- |
| `list_mailboxes` | Resolve the test mailbox and its `public_id` | read |
| `list_emails` | Snapshot and re-read the `inbox`, `draft`, `sent`, and `trash` folders | read |
| `get_email_context` | Read one selected message plus a bounded sanitized thread page | read |

## Examples

Baseline snapshot of one folder, metadata only:

```json
{
  "mailboxId": "<public_id from list_mailboxes>",
  "query": {
    "folder": "draft",
    "limit": 50,
    "metadata_only": true,
    "sortColumn": "date",
    "sortDirection": "DESC"
  }
}
```

Reading bodies of candidate messages for the audit:

```json
{
  "mailboxId": "<public_id from list_mailboxes>",
  "query": {
    "folder": "draft",
    "limit": 20,
    "agent_safe_content": true,
    "sortColumn": "date",
    "sortDirection": "DESC"
  }
}
```

Do not pass `"query": "{\"folder\":\"draft\"}"`.

## Bounds

- At most four folders, one page of up to 50 messages each, plus one follow-up page if `totalCount` exceeds the limit.
- At most ten `get_email_context` calls per audit.
- No polling loops.
