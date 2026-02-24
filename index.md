# Fastmail & JMAP Documentation Corpus

> **Sources:** Fastmail API docs, JMAP specifications, code examples
> **Last updated:** 2025-12-23
> **Total:** 5 documentation files + code samples across 4 languages

---

## Getting Started

- **Fastmail API Overview** `fastmail-dev:index.md` - Supported protocols (JMAP, IMAP, POP, SMTP, CardDAV, CalDAV, WebDAV), authentication methods, and API endpoints
  - Keywords: `protocols`, `overview`, `getting started`

- **JMAP Crash Course** `fastmail-dev:crash-course.md` - Foundational tutorial covering request/response model, authentication, server discovery, and result references with Perl code examples
  - Keywords: `tutorial`, `crash course`, `basics`, `introduction`

---

## Authentication & Authorization

- **Authentication Methods** `fastmail-dev:index.md` (lines 18-26) - API tokens for development, OAuth 2.0 for distributed apps
  - Keywords: `authentication`, `api tokens`, `app passwords`

- **OAuth 2.0 Implementation** `fastmail-dev:index.md` (lines 39-58) - OAuth registration, scopes, authorization flow, PKCE requirements
  - Keywords: `oauth`, `authorization`, `pkce`, `scopes`

- **Masked Email API** `fastmail-dev:index.md` (lines 35-38) - JMAP extension for managing masked email addresses with metadata fields
  - Keywords: `masked email`, `privacy`, `jmap extension`

---

## JMAP Core Concepts

- **Request and Response Model** `fastmail-dev:crash-course.md` (lines 7-28) - Structure of JMAP requests (using, methodCalls) and responses (methodResponses, sessionState)
  - Keywords: `request`, `response`, `method calls`, `json`

- **Session Object** `fastmail-dev:crash-course.md` (lines 34-42) - Session discovery via `/.well-known/jmap`, capabilities, accounts, primaryAccounts, URL templates
  - Keywords: `session`, `discovery`, `capabilities`, `accounts`

- **Result References** `fastmail-dev:crash-course.md` (lines 51-78) - Chaining dependent operations with `#` prefix, resultOf, name, and path for efficient single-request workflows
  - Keywords: `result references`, `chaining`, `batch operations`

- **JMAP Specifications Overview** `jmap-spec:spec.md` - Landing page referencing finalized RFCs (core protocol, JMAP Mail, WebSocket, Blob Management, Quotas) and in-development specs
  - Keywords: `rfc`, `specifications`, `standards`

---

## Client Development

### Initial Setup

- **Cold Boot Initialization** `jmap-spec:client.md` (lines 11-39) - First login flow: fetch mailbox list with `Mailbox/get`, understand role attributes, permission flags, counts
  - Keywords: `cold boot`, `initialization`, `mailbox list`, `first login`

- **Initial Data Loading** `jmap-spec:client.md` (lines 43-74) - Chained request pattern for loading message view: `Email/query` → `Email/get` (threadId) → `Thread/get` → `Email/get` (full details)
  - Keywords: `initial load`, `message view`, `chained requests`

### Navigation & Display

- **Pagination** `jmap-spec:client.md` (lines 78-94) - Scrolling through messages with position-adjusted `Email/query` calls, optimization strategies
  - Keywords: `pagination`, `scrolling`, `lazy loading`

- **Opening Thread Details** `jmap-spec:client.md` (lines 98-114) - Fetching complete message details with properties (blobId, messageId, headers, body values)
  - Keywords: `thread details`, `message content`, `email body`

### Synchronization

- **Staying in Sync** `jmap-spec:client.md` (lines 120-165) - Comprehensive sync sequence using `Mailbox/changes`, `Email/queryChanges`, `Email/changes`, `Thread/changes`
  - Keywords: `synchronization`, `push notifications`, `state changes`, `delta updates`

- **Processing Sync Responses** `jmap-spec:client.md` (lines 167-189) - Applying mailbox changes, email query changes, handling `updatedProperties`, removing/adding messages
  - Keywords: `sync processing`, `change application`, `state reconciliation`

### Error Handling

- **tooManyChanges Error** `jmap-spec:client.md` (lines 193-204) - Recovery strategies when changes exceed maxChanges limits
  - Keywords: `error handling`, `too many changes`, `recovery`

- **Limited Delta Support** `jmap-spec:client.md` (lines 206-213) - Handling servers without `canCalculateChanges` capability
  - Keywords: `delta support`, `fallback`, `compatibility`

### Actions

- **Moving Messages** `jmap-spec:client.md` (lines 219-231) - Updating mailbox membership with `Email/set`
  - Keywords: `move messages`, `mailbox membership`, `email set`

- **Optimistic Updates** `jmap-spec:client.md` (lines 233-259) - Apply changes immediately: update counts, splice query lists, modify message objects
  - Keywords: `optimistic updates`, `latency`, `preemptive changes`

- **Select All Operations** `jmap-spec:client.md` (lines 261-263) - Fetch complete message list, handle thread-wide actions with `collapseThreads: false`
  - Keywords: `select all`, `bulk operations`, `threads`

### Full Mailbox Sync

- **Complete Synchronization** `jmap-spec:client.md` (lines 269-310) - For apps maintaining local copies: `Mailbox/changes`, `Email/changes`, fetch added/updated with minimal properties
  - Keywords: `full sync`, `local storage`, `offline`

- **Performance Principles** `jmap-spec:client.md` (lines 314-322) - Minimize round trips, request only necessary properties, sparse caching, optimistic updates, prioritize recent messages
  - Keywords: `performance`, `optimization`, `best practices`

---

## Server Implementation

### Data Structures

- **Email ID Assignment** `jmap-spec:server.md` (lines 7-9) - Using secure hash of RFC5322 message for ID generation
  - Keywords: `email id`, `hashing`, `unique identifiers`

- **Modification Sequences** `jmap-spec:server.md` (lines 11-13) - 64-bit monotonic counter (ModSeq) for efficient change tracking
  - Keywords: `modseq`, `change tracking`, `versioning`

- **Emails Table** `jmap-spec:server.md` (lines 19-32) - Mutable state: id, blobId, threadId, mailboxIds, keywords, headers, metadata, createdModSeq, updatedModSeq, deleted
  - Keywords: `database schema`, `emails table`, `storage`

- **Threads Table** `jmap-spec:server.md` (lines 34-41) - Thread grouping: id, emails array (sorted with drafts after parent), ModSeq tracking
  - Keywords: `threads`, `conversations`, `email grouping`

- **Mailboxes Table** `jmap-spec:server.md` (lines 43-54) - Hierarchy: id, name, parentId, role, sortOrder, permissions, counts, ModSeq, IMAP compatibility
  - Keywords: `mailboxes`, `folders`, `labels`

- **MailboxEmailList Table** `jmap-spec:server.md` (lines 56-67) - Optimized queries: composite id (mailboxId + date + uid), messageId, threadId, updatedModSeq
  - Keywords: `query optimization`, `message list`, `indexing`

- **EmailChangeLog Table** `jmap-spec:server.md` (lines 69-73) - Change tracking: ModSeq as id, created/updated/destroyed email ID arrays
  - Keywords: `change log`, `audit trail`, `history`

- **ThreadChangeLog Table** `jmap-spec:server.md` (lines 75-79) - Thread-level changes: mirrors EmailChangeLog structure
  - Keywords: `thread changes`, `change log`

- **Refs to Thread Table** `jmap-spec:server.md` (lines 81-91) - RFC5322 header mapping: hash(rfc822id + subject) → threadId, subject normalization rules
  - Keywords: `threading`, `message headers`, `references`

- **HighLowModSeqCache Table** `jmap-spec:server.md` (lines 93-100) - Singleton: highModSeq (global + per-type), lowModSeq (recalculable minimums)
  - Keywords: `cache`, `modseq tracking`, `state management`

- **Additional Storage** `jmap-spec:server.md` (lines 102-106) - Raw RFC5322 messages in blob store, email text search index
  - Keywords: `blob storage`, `text search`, `full text index`

### State Management

- **State Property Values** `jmap-spec:server.md` (lines 108-116) - Return appropriate ModSeq per query type: Email/Thread/Mailbox getters, Email/query encoding
  - Keywords: `state`, `modseq values`, `query state`

### Change Calculation

- **Email/changes Algorithm** `jmap-spec:server.md` (lines 120-122) - Compare against highModSeqEmail, read EmailChangeLog forward from client ModSeq
  - Keywords: `email changes`, `delta calculation`, `change detection`

- **Thread/changes Algorithm** `jmap-spec:server.md` (lines 124-126) - Mirror Email/changes using ThreadChangeLog
  - Keywords: `thread changes`, `delta calculation`

- **Mailbox/changes Algorithm** `jmap-spec:server.md` (lines 128-130) - Iterate mailboxes comparing updatedModSeq, check deleted field, detect count-only changes via updatedNotCountsModSeq
  - Keywords: `mailbox changes`, `folder updates`

- **Email/query Algorithm** `jmap-spec:server.md` (lines 132-142) - Build filtered list from MailboxEmailList or full scan, apply thread collapsing
  - Keywords: `email query`, `filtering`, `sorting`

- **Email/queryChanges Algorithm** `jmap-spec:server.md` (lines 144-198) - For mailbox filters: leverage MailboxEmailList, track seen exemplars, compute added/removed positions, handle mutable sorts
  - Keywords: `query changes`, `delta query`, `position tracking`

### Protocol Compliance

- **RFC5322 to JMAP Conversion** `jmap-spec:server.md` (lines 202-204) - Attachment filename encoding: RFC2231 in Content-Disposition, RFC2047 in Content-Type name parameter
  - Keywords: `rfc5322`, `mime`, `encoding`, `attachments`

- **Vacation Response** `jmap-spec:server.md` (lines 206-225) - RFC-5230 compliance: avoid mailing lists, automation addresses, set Auto-Submitted header, track notifications, RFC-3824 headers
  - Keywords: `vacation`, `auto-reply`, `out of office`

### Upload Errors

- **Upload Error Codes** `jmap-spec:server.md` (lines 229-248) - 400 Bad Request, 401 Unauthorized, 413 Too Large, 415 Unsupported Type, 429 Rate Limited
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
