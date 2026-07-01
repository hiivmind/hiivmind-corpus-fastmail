# Fastmail & JMAP Documentation Corpus

> **Sources:** Fastmail API docs, JMAP specifications, code examples
> **Last updated:** 2026-07-01
> **Total:** 5 documentation files + code samples across 4 languages

---

## Getting Started

- **Fastmail API Overview** `fastmail-dev:index.md` - Supported protocols: mail via JMAP (RFC8621), IMAP (RFC9051), or POP (RFC1939), send via JMAP or SMTP (RFC5321); contacts via JMAP (RFC9610) or CardDAV (RFC6352); calendars via CalDAV (RFC4791, JMAP calendar access pending finalized spec); files via WebDAV (RFC4918). Covers authentication requirements and API token / app password generation via Settings → Privacy & Security
  - Keywords: `protocols`, `overview`, `getting started`

- **JMAP Crash Course** `fastmail-dev:crash-course.md` - Foundational tutorial (now at `jmap.io/crash-course/`) covering request/response model, authentication & autodiscovery via `_jmap._tcp` SRV records, the JMAP session resource, result references, and a worked example with Perl code
  - Keywords: `tutorial`, `crash course`, `basics`, `introduction`

---

## Authentication & Authorization

- **Authentication Methods** `fastmail-dev:index.md` (lines 18-26) - API tokens for development, OAuth 2.0 for distributed apps, generated via Settings → Privacy & Security → Manage API tokens / Manage app passwords and access
  - Keywords: `authentication`, `api tokens`, `app passwords`

- **OAuth 2.0 Implementation** `fastmail-dev:index.md` (lines 39-58) - OAuth registration, scopes (now includes `urn:ietf:params:jmap:contacts` for RFC9610 contacts access alongside core/mail/submission/vacationresponse/maskedemail), authorization flow, PKCE requirements
  - Keywords: `oauth`, `authorization`, `pkce`, `scopes`

- **Masked Email API** `fastmail-dev:index.md` (lines 35-38) - JMAP extension for managing masked email addresses with metadata fields (`createdBy`, `forDomain`, `url`, `description`, `emailPrefix`)
  - Keywords: `masked email`, `privacy`, `jmap extension`

---

## JMAP Core Concepts

- **Request and Response Model** `fastmail-dev:crash-course.md` - Structure of JMAP requests (`using`, `methodCalls`) and responses (`methodResponses`, `sessionState`)
  - Keywords: `request`, `response`, `method calls`, `json`

- **Session Object & Autodiscovery** `fastmail-dev:crash-course.md` - Session discovery via `_jmap._tcp` DNS SRV lookup and `/.well-known/jmap`, capabilities, accounts, primaryAccounts, URL templates (`apiUrl`, `uploadUrl`, `downloadUrl`, `eventSourceUrl`)
  - Keywords: `session`, `discovery`, `capabilities`, `accounts`, `autodiscovery`

- **Result References** `fastmail-dev:crash-course.md` - Chaining dependent operations with `#` prefix, `resultOf`, `name`, and `path` for efficient single-request workflows
  - Keywords: `result references`, `chaining`, `batch operations`

- **JMAP Specifications Overview** `jmap-spec:spec.md` - Landing page for finalized and in-progress RFCs, now reorganized under `jmap.io/spec/`. **Core:** RFC8620 (core protocol), RFC9749 (VAPID for JMAP Push), RFC9670 (JMAP Sharing), RFC8887 (JMAP via WebSocket), RFC9425 (JMAP Quotas), RFC9404 (JMAP Blob Management). **Mail:** RFC8621 (JMAP Mail), RFC9661 (Sieve Scripts Management), RFC9007 (MDN Handling), RFC9219 (S/MIME Signature Verification). **Contacts & Calendars:** RFC9610 (JMAP Contacts, finalized), JMAP Calendars (in progress). **Data formats:** RFC9553 (JSContact), JSCalendar 2.0 (in progress). Note: the previously-tracked "Tasks" spec is no longer listed as a separate in-development item
  - Keywords: `rfc`, `specifications`, `standards`, `sieve`, `s/mime`, `sharing`, `websocket`, `quotas`, `blob management`, `jscontact`

---

## Client Development

### Initial Setup

- **Cold Boot Initialization** `jmap-spec:client.md` - First login flow: fetch mailbox list with `Mailbox/get`, understand role attributes, permission flags, counts
  - Keywords: `cold boot`, `initialization`, `mailbox list`, `first login`

- **Initial Data Loading** `jmap-spec:client.md` - Chained request pattern for loading message view: `Email/query` → `Email/get` (threadId) → `Thread/get` → `Email/get` (full details)
  - Keywords: `initial load`, `message view`, `chained requests`

### Navigation & Display

- **Pagination** `jmap-spec:client.md` - Scrolling through messages with position-adjusted `Email/query` calls, optimization strategies
  - Keywords: `pagination`, `scrolling`, `lazy loading`

- **Opening Thread Details** `jmap-spec:client.md` - Fetching complete message details with properties (blobId, messageId, headers, body values)
  - Keywords: `thread details`, `message content`, `email body`

### Synchronization

- **Staying in Sync** `jmap-spec:client.md` - Comprehensive sync sequence using `Mailbox/changes`, `Email/queryChanges`, `Email/changes`, `Thread/changes`
  - Keywords: `synchronization`, `push notifications`, `state changes`, `delta updates`

- **Processing Sync Responses** `jmap-spec:client.md` - Applying mailbox changes, email query changes, handling `updatedProperties`, removing/adding messages
  - Keywords: `sync processing`, `change application`, `state reconciliation`

### Error Handling

- **tooManyChanges Error** `jmap-spec:client.md` - Recovery strategies when changes exceed `maxChanges` limits
  - Keywords: `error handling`, `too many changes`, `recovery`

- **Limited Delta Support** `jmap-spec:client.md` - Handling servers without `canCalculateChanges` capability
  - Keywords: `delta support`, `fallback`, `compatibility`

### Actions

- **Moving Messages** `jmap-spec:client.md` - Updating mailbox membership with `Email/set`
  - Keywords: `move messages`, `mailbox membership`, `email set`

- **Optimistic Updates** `jmap-spec:client.md` - Apply changes immediately: update counts, splice query lists, modify message objects
  - Keywords: `optimistic updates`, `latency`, `preemptive changes`

- **Select All Operations** `jmap-spec:client.md` - Fetch complete message list, handle thread-wide actions with `collapseThreads: false`
  - Keywords: `select all`, `bulk operations`, `threads`

### Full Mailbox Sync

- **Complete Synchronization** `jmap-spec:client.md` - For apps maintaining local copies: `Mailbox/changes`, `Email/changes`, fetch added/updated with minimal properties
  - Keywords: `full sync`, `local storage`, `offline`

- **Performance Principles** `jmap-spec:client.md` - Minimize round trips, request only necessary properties, sparse caching, optimistic updates, prioritize recent messages
  - Keywords: `performance`, `optimization`, `best practices`

---

## Server Implementation

> **Note:** This section was substantially rewritten upstream (`jmap.io/server/`). The database design now separates mutable metadata from immutable content and adds a dedicated search index table; the old EmailChangeLog/ThreadChangeLog/HighLowModSeqCache tables have been replaced by a modseq-indexed scan approach.

### Modification Sequences

- **Modseq Fundamentals** `jmap-spec:server.md` - A modseq is a 64-bit unsigned monotonically incrementing counter, one per data type per account, incremented on every change and used to drive all the change-calculation algorithms below
  - Keywords: `modseq`, `change tracking`, `versioning`

### Data Structures

- **EmailMetadata Table** `jmap-spec:server.md` - Mutable Email state kept narrow for fast list scans: id, threadId, mailboxIds, keywords, receivedAt, blobId, size, createdModSeq, updatedModSeq, deleted tombstone; indexed `byModSeq` and `byThreadId`
  - Keywords: `database schema`, `email metadata`, `storage`

- **EmailContent Table** `jmap-spec:server.md` - Immutable parsed content keyed by the same id as EmailMetadata: headers (from/to/cc/bcc/sender/replyTo), subject, preview, messageId/inReplyTo/references, bodyStructure, bodyValues
  - Keywords: `email content`, `headers`, `body values`

- **EmailSearch Table** `jmap-spec:server.md` - Inverted index from normalised (NFKD, case-folded) text tokens to messages, with a field bitmask (FROM/TO/CC/BCC/SUBJECT/BODY/ATTACHMENT/LIST_ID/MESSAGE_ID) supporting freetext, address, list-id, and message-id filters
  - Keywords: `search index`, `full text search`, `inverted index`, `freetext`

- **MailboxEmailList Table** `jmap-spec:server.md` - Per-mailbox membership list including historical add/remove pairs, keyed by `(mailboxUid, removedModSeq, addedModSeq)`; powers the fast path for `Email/query`/`Email/queryChanges` on a mailbox filter
  - Keywords: `query optimization`, `message list`, `indexing`

- **MailboxEmailIndex Table** `jmap-spec:server.md` - Secondary index over live MailboxEmailList rows keyed by `(mailboxUid, emailId)`, enabling O(1) mailbox-plus-email lookups when a message moves or is destroyed
  - Keywords: `secondary index`, `mailbox lookup`

- **Mailboxes Table** `jmap-spec:server.md` - Hierarchy and permissions: id, name, parentId, role, sortOrder, may* permission flags, counts, createdModSeq/updatedModSeq/updatedNotCountsModSeq, mailboxUid, emailHighestModSeq, emailListLowModSeq
  - Keywords: `mailboxes`, `folders`, `labels`

- **Refs to Thread Table** `jmap-spec:server.md` - RFC5322 header mapping: `hash(rfc822id) . hash(subject)` → threadId, with subject normalization rules (strip bracketed prefixes, leading `word:` tags, whitespace) and a `lastSeen` cleanup timestamp
  - Keywords: `threading`, `message headers`, `references`

- **Raw Messages** `jmap-spec:server.md` - Full RFC5322 messages kept in a blob store addressable by blob id, fetched by clients via standard JMAP blob endpoints
  - Keywords: `blob storage`, `raw messages`

### Algorithms

- **Email/changes Algorithm** `jmap-spec:server.md` - Range-scan EmailMetadata `byModSeq` from the client's modseq, bucketing into created/updated/destroyed via `createdModSeq` and `deleted`
  - Keywords: `email changes`, `delta calculation`, `change detection`

- **Thread/changes Algorithm** `jmap-spec:server.md` - Range-scan EmailMetadata `byModSeq`, then use `byThreadId` to determine whether each affected thread is new, updated, or fully destroyed
  - Keywords: `thread changes`, `delta calculation`

- **Mailbox/changes Algorithm** `jmap-spec:server.md` - Compare each mailbox's `updatedModSeq` to the client modseq; `deleted` marks destruction, `updatedNotCountsModSeq` distinguishes count-only changes
  - Keywords: `mailbox changes`, `folder updates`

- **Email/query Algorithm** `jmap-spec:server.md` - Three strategies by cost: the **fast mailbox path** (MailboxEmailList range scan for `inMailbox` filters), the **fast search path** (single EmailSearch token cursor), and a **generic fallback** (candidate hydration or full scan)
  - Keywords: `email query`, `filtering`, `sorting`, `query strategy`

- **Email/queryChanges Algorithm** `jmap-spec:server.md` - For mailbox filters, diff live plus recently-removed MailboxEmailList rows via an exemplar-tracking scan (`SeenExemplar`/`SeenOldExemplar`) to compute added/removed with positions; other filters fall back to a `byModSeq` scan expanded to full threads
  - Keywords: `query changes`, `delta query`, `position tracking`, `exemplar`

- **Email/set Algorithm** `jmap-spec:server.md` - Single choke point per mutation: write EmailMetadata/tombstone, diff `mailboxIds` (including the `$flagged` virtual mailbox) against MailboxEmailList/MailboxEmailIndex, bump thread/count modseqs, write EmailContent and EmailSearch tokens
  - Keywords: `email set`, `mutation`, `mailbox membership`

- **Email ID Assignment** `jmap-spec:server.md` - Recommends a time-reversed prefix in server-assigned email ids so ascending-id scans (e.g. EmailSearch cursors) are already approximately date-ordered
  - Keywords: `email id`, `id assignment`, `time-reversed prefix`

### Protocol Compliance

- **RFC5322 to JMAP Conversion** `jmap-spec:server.md` - Attachment filename encoding: RFC2231 in Content-Disposition `filename`, and (for compatibility) RFC2047 in Content-Type `name`
  - Keywords: `rfc5322`, `mime`, `encoding`, `attachments`

- **Vacation Response** `jmap-spec:server.md` - RFC-5230 compliance: avoid mailing lists and automation addresses, set `Auto-Submitted: auto-replied`, track sent notifications, follow RFC-3834 personal-responder conventions and RFC-2822 References generation
  - Keywords: `vacation`, `auto-reply`, `out of office`

### Upload Errors

- **Binary Upload Error Codes** `jmap-spec:server.md` - 400 Bad Request, 401 Unauthorized (with `WWW-Authenticate`), 413 Request Entity Too Large, 415 Unsupported Media Type, 429 Rate Limited (with optional `Retry-After`)
  - Keywords: `upload`, `error codes`, `http status`

---

## Code Examples

### JavaScript Examples

- **Hello World (JavaScript)** `jmap-samples:javascript/hello-world.js` - Minimal JMAP client: authenticate, fetch session, make first request
  - Keywords: `javascript`, `hello world`, `basic example`, `node.js`

- **Top Ten Messages (JavaScript)** `jmap-samples:javascript/top-ten.js` - Retrieve and display 10 most recent inbox messages with subjects and dates
  - Keywords: `javascript`, `email query`, `inbox`, `message list`

### Python Examples

- **Hello World (Python)** `jmap-samples:python3/hello-world.py` - Python JMAP client basics: session discovery, authentication, first request
  - Keywords: `python`, `hello world`, `basic example`

- **Top Ten Messages (Python)** `jmap-samples:python3/top-ten.py` - Python implementation of retrieving recent messages
  - Keywords: `python`, `email query`, `inbox`

- **Tiny JMAP Library (Python)** `jmap-samples:python3/tiny_jmap_library.py` - Minimal Python library wrapping JMAP session and request logic
  - Keywords: `python`, `library`, `helper`, `wrapper`

### Lua Examples

- **Tiny JMAP (Lua)** `jmap-samples:lua/tiny_jmap.lua` - Minimal Lua JMAP client library
  - Keywords: `lua`, `library`, `client`

- **Hello World (Lua)** `jmap-samples:lua/hello_world.lua` - Basic Lua JMAP example
  - Keywords: `lua`, `hello world`, `basic example`

- **Top Ten Messages (Lua)** `jmap-samples:lua/top_ten.lua` - Fetch recent messages in Lua
  - Keywords: `lua`, `email query`, `inbox`

- **Lua README** `jmap-samples:lua/README.md` - Documentation for Lua examples and setup instructions
  - Keywords: `lua`, `documentation`, `setup`

### Perl Examples

- **Perl Samples Directory** `jmap-samples:perl5/` - Perl5 implementations of hello-world and top-ten examples (similar to other languages)
  - Keywords: `perl`, `perl5`, `examples`

---

## Quick Reference

**Common Operations:**
- First login → `fastmail-dev:crash-course.md` + `jmap-spec:client.md` (Cold Boot)
- Send email → Look for submission examples in code samples
- Sync changes → `jmap-spec:client.md` (Synchronization section)
- Build a client → `jmap-spec:client.md` (full guide)
- Build a server → `jmap-spec:server.md` (full guide)
- OAuth setup → `fastmail-dev:index.md` (OAuth section)

**Fastmail API Endpoints:**
- JMAP Session: `https://api.fastmail.com/jmap/session`
- OAuth Authorize: `https://api.fastmail.com/oauth/authorize`
- OAuth Token: `https://api.fastmail.com/oauth/refresh`
- OAuth Revoke: `https://api.fastmail.com/oauth/revoke`
