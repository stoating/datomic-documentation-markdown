# Claude Instructions: HTML to Markdown Conversion

This repository is a Markdown mirror of the Datomic documentation at https://docs.datomic.com. The file structure is already scaffolded — every page has an empty `.md` file waiting to be filled in. Your job is to convert HTML page content into Markdown and write it into the correct file.

## How the workflow works

The user pastes the `<div id="content" class="content">...</div>` block from a Datomic docs page. You convert it to Markdown and write it into the corresponding `.md` file in this repo.

To find the right file: the URL path of the source page maps to the repo structure. For example, `/setup/pro-setup.html` → `01-setup/01-pro-setup/pro-setup.md`. Use the directory names (strip the number prefix) to navigate.

After writing a file, **always read it first** (the file exists but is empty — the Write tool requires a prior Read).

## Conversion rules

### Structure
- `<h1>` → `# ` (page title)
- `<h2>` → `## `
- `<h3>` → `### `
- Heading levels must increment by one — if the source HTML skips a level (e.g. jumps from `h1` to `h3`), use the correct Markdown level anyway to keep headings valid
- Strip all `<a class="anchorjs-link">` decorative anchor tags — these are just visual icons, not content

### Links
- Convert **all** `href` links — including `https://docs.datomic.com/...` URLs — to relative `.md` paths within this repo. No `https://docs.datomic.com` links should remain in converted files.
- External links to non-Datomic sites (e.g. Oracle Javadoc, AWS docs) stay as-is.
- Resolve relative HTML paths (`../`) against the actual repo directory structure, not the original URL structure.
- Preserve `#anchor` fragments when converting links so headings within a page are still reachable.

#### URL → repo path mapping

| docs.datomic.com URL path | Repo file |
|---------------------------|-----------|
| `/schema/schema.html` | `06-reference/01-schema/01-schema-reference/schema-reference.md` |
| `/schema/identity.html` | `06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md` |
| `/schema/schema-change.html` | `06-reference/01-schema/02-changing-schema/changing-schema.md` |
| `/schema/data-modeling.html` | `06-reference/01-schema/03-data-modeling/data-modeling.md` |
| `/transactions/transactions.html` | `06-reference/02-transactions/transactions.md` |
| `/query/query.html` | `06-reference/03-query-and-pull/02-query-reference/query-reference.md` |
| `/query/indexes.html` | `06-reference/04-indexes/01-index-model/index-model.md` |
| `/query/pull.html` | `06-reference/03-query-and-pull/03-pull/pull.md` |
| `/operation/storage.html` | `05-operation/01-pro/01-storage-services/storage-services.md` |
| `/operation/aws.html` | `05-operation/01-pro/11-running-on-aws/running-on-aws.md` |
| `/reference/entities.html` | `06-reference/07-entities/entities.md` |
| `/api/log.html` | `04-apis/08-log-api/log-api.md` |
| `/operation/capacity.html` | `05-operation/01-pro/04-capacity-planning/capacity-planning.md` |
| `/operation/excision.html` | `05-operation/01-pro/14-excision/excision.md` |
| `/glossary.html` | `12-glossary/glossary.md` |
| `/reference/database-functions.html` | `06-reference/02-transactions/04-transaction-functions/transaction-functions.md` |
| `/transactions/transaction-processing.html` | `06-reference/02-transactions/03-processing-transactions/processing-transactions.md` |
| `/cloud/time/filters.html` | `06-reference/06-time-in-datomic/time-in-datomic.md` |
| `/cloud/query/raw-index-access.html` | `04-apis/07-index-apis/index-apis.md` |
| `/cloud/query/query-pull.html` | `06-reference/03-query-and-pull/03-pull/pull.md` |
| `/cloud/query/query-data-reference.html` | `06-reference/03-query-and-pull/02-query-reference/query-reference.md` |
| `/cloud/api/io-stats.html` | `04-apis/10-io-stats/io-stats.md` |
| `/cloud/api/query-stats.html` | `04-apis/11-query-stats/query-stats.md` |
| `/cloud/transactions/transaction-data-reference.html` | `06-reference/02-transactions/02-transaction-data/transaction-data.md` |

For any URL not in this table, derive the repo path using the same pattern: strip the domain, map the path segments to the closest matching directory under the numbered top-level folders, and use the leaf `.md` file named after the directory. Add new entries to this table as you encounter and resolve new URLs.

### Code blocks
- `<pre class="src src-sh">` or `src-bash` → fenced block with `sh`
- `<pre class="src src-clojure">` → fenced block with `clojure`
- `<pre class="src src-example">` or plain `<pre class="example">` → plain fenced block (no language tag)
- Strip all `<div class="code-copy-button">` copy widgets — content only
- Strip all inline color styles from syntax-highlighted code — render clean source

### Inline formatting
- `<code>` → backticks
- `<b>` or `<strong>` → `**bold**`
- `<i>` or `<em>` → `*italic*`
- `<span class="underline">` → `_underline_`
- `<sup>N</sup>` → `^N`

### Lists
- `<ul>` → unordered list with `-`
- `<ol>` → ordered list with `1.`, `2.`, etc.
- Preserve nesting

### Images
- `<img src="../images/foo.png">` → `![foo](../images/foo.png)` — adjust the relative path to point from the `.md` file's location to an `images/` directory at the repo root

### Things to strip entirely
- Navigation sidebar (`<div class="d-nav-container">` and everything in it)
- Footer (`<footer>`)
- Copy-to-clipboard buttons (`<div class="code-copy-button">`, `<button class="copy-button">`)
- Anchor icon links (`<a class="anchorjs-link">`)
- All `style=` attributes
- HTML comments

## What has been converted so far

These files have content (as of the last session):

- `introduction.md` — empty (not yet converted)
- `01-setup/setup.md` ✓
- `01-setup/01-pro-setup/pro-setup.md` ✓
- `01-setup/02-cloud-setup/cloud-setup.md` ✓
- `01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md` ✓
- `01-setup/02-cloud-setup/02-cloud-setup/cloud-setup.md` ✓
- `01-setup/03-local-setup/local-setup.md` ✓
- `02-accessing/accessing.md` ✓
- `02-accessing/01-peer-library/peer-library.md` ✓
- `02-accessing/02-client-library/client-library.md` ✓
- `04-apis/01-peer-api-clojuredoc/peer-api-clojuredoc.md` ✓ — this is the target for any `../clojure/index.html` links
- `04-apis/02-peer-api-javadoc/interfaces/connection/connection.md` ✓
- `04-apis/02-peer-api-javadoc/classes/util/util.md` ✓
- `04-apis/02-peer-api-javadoc/interfaces/datom/datom.md` ✓
- `04-apis/02-peer-api-javadoc/interfaces/entity/entity.md` ✓
- `04-apis/02-peer-api-javadoc/interfaces/log/log.md` ✓
- `04-apis/02-peer-api-javadoc/classes/peer/peer.md` ✓
- `04-apis/02-peer-api-javadoc/classes/query-request/query-request.md` ✓
- `04-apis/02-peer-api-javadoc/classes/util/util.md` ✓
- `04-apis/02-peer-api-javadoc/interfaces/attribute/attribute.md` ✓
- `04-apis/02-peer-api-javadoc/interfaces/database/database.md` ✓
- `04-apis/02-peer-api-javadoc/interfaces/database-predicate/database-predicate.md` ✓
- `04-apis/02-peer-api-javadoc/interfaces/listenable-future/listenable-future.md` ✓
- `04-apis/02-peer-api-javadoc/peer-api-javadoc.md` ✓
- `04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md` ✓

## Javadoc directory naming note

The `QueryRequest` class lives at `04-apis/02-peer-api-javadoc/classes/query-request/query-request.md` (not `query-result`). When linking to it, use `classes/query-request/query-request.md`.

Everything else in the repo is an empty `.md` file waiting to be filled. The next file in sequence is `03-tutorials/tutorials.md`.

## Known issue to fix

`01-setup/03-local-setup/local-setup.md` has a heading level jump on line 17 (h1 → h3). The "Setup" and "Configure Local Storage" sections should be `##` and `###` respectively (or promoted to `##`/`###` to follow the h1). Fix this if you touch the file.

## Source site

Original documentation: https://docs.datomic.com  
To find the HTML for a given page, navigate to the URL that corresponds to the `.md` file path and grab the `<div id="content" class="content">` block.
