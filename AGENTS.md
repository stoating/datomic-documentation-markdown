# Codex Instructions: HTML to Markdown Conversion

This repository is a Markdown mirror of the Datomic documentation at https://docs.datomic.com. The file structure is already scaffolded, and most pages are empty `.md` files waiting to be filled in. Convert Datomic documentation HTML into Markdown and write it into the corresponding file.

## Workflow

The user typically pastes the `<div id="content" class="content">...</div>` block from a Datomic docs page. Convert that content to Markdown and write it into the matching `.md` file in this repo.

To find the right file, map the source URL path to the repo structure. For example, `/setup/pro-setup.html` maps to `01-setup/01-pro-setup/pro-setup.md`. Use the directory names, ignoring numeric prefixes, to navigate.

Before editing, inspect the target file and nearby completed files so the conversion matches local style. Many files already exist but are empty.

## Conversion Rules

### Structure

- `<h1>` -> `# `
- `<h2>` -> `## `
- `<h3>` -> `### `
- Heading levels must increment by one. If the source HTML skips a level, use the correct Markdown level anyway so the output has valid hierarchy.
- Strip all `<a class="anchorjs-link">` decorative anchor tags. They are visual icons, not content.

### Links

- Convert all Datomic documentation links, including `https://docs.datomic.com/...` URLs, to relative `.md` paths within this repo.
- No `https://docs.datomic.com` links should remain in converted files.
- External links to non-Datomic sites, such as Oracle Javadoc, Maven Central, AWS docs, Clojure docs, or GitHub project pages, stay as-is.
- Resolve relative HTML paths such as `../` against the repo directory structure for the Markdown target, not by copying the original URL text blindly.
- Preserve `#anchor` fragments when converting links.
- Match the style used in `02-accessing`:
  - Same-directory child page: `[Peer library](01-peer-library/peer-library.md)`
  - Parent section page: `[setup](../01-setup/setup.md)`
  - Cross-section page from a nested file: `[peer tutorial](../../03-tutorials/01-peer-tutorial/peer-tutorial.md)`
  - Cross-section page with anchor: `[Clojure CLI REPL](../../05-operation/02-cloud/11-how-to/how-to.md#clojure-cli)`

#### URL to Repo Path Mapping

| docs.datomic.com URL path | Repo file |
|---------------------------|-----------|
| `/schema/schema.html` | `06-reference/01-schema/01-schema-reference/schema-reference.md` |
| `/schema/identity.html` | `06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md` |
| `/schema/schema-change.html` | `06-reference/01-schema/02-changing-schema/changing-schema.md` |
| `/schema/data-modeling.html` | `06-reference/01-schema/03-data-modeling/data-modeling.md` |
| `/transactions/transactions.html` | `06-reference/02-transactions/transactions.md` |
| `/reference/filters.html` | `06-reference/06-time-in-datomic/time-in-datomic.md` |
| `/query/query.html` | `06-reference/03-query-and-pull/02-query-reference/query-reference.md` |
| `/query/indexes.html` | `06-reference/04-indexes/01-index-model/index-model.md` |
| `/query/raw-index-access.html` | `04-apis/07-index-apis/index-apis.md` |
| `/query/pull.html` | `06-reference/03-query-and-pull/03-pull/pull.md` |
| `/operation/architecture.html` | `introduction.md` |
| `/operation/storage.html` | `05-operation/01-pro/01-storage-services/storage-services.md` |
| `/operation/transactor.html` | `05-operation/01-pro/02-transactor-reference/transactor-reference.md` |
| `/operation/deployment.html` | `05-operation/01-pro/03-datomic-deployment/datomic-deployment.md` |
| `/operation/monitoring.html` | `05-operation/01-pro/05-monitoring-and-performance/monitoring-and-performance.md` |
| `/operation/ha.html` | `05-operation/01-pro/06-high-availability/high-availability.md` |
| `/operation/backup.html` | `05-operation/01-pro/07-backup-and-restore/backup-and-restore.md` |
| `/operation/system-properties.html` | `05-operation/01-pro/10-system-properties/system-properties.md` |
| `/operation/aws.html` | `05-operation/01-pro/11-running-on-aws/running-on-aws.md` |
| `/operation/aws-access-control.html` | `05-operation/01-pro/13-aws-access-control/aws-access-control.md` |
| `/overview/storage.html` | `05-operation/01-pro/01-storage-services/storage-services.md` |
| `/reference/entities.html` | `06-reference/07-entities/entities.md` |
| `/reference/log.html` | `04-apis/08-log-api/log-api.md` |
| `/api/log.html` | `04-apis/08-log-api/log-api.md` |
| `/clojure/` | `04-apis/01-peer-api-clojuredoc/peer-api-clojuredoc.md` |
| `/clojure/index.html` | `04-apis/01-peer-api-clojuredoc/peer-api-clojuredoc.md` |
| `/javadoc/datomic/Peer.html` | `04-apis/02-peer-api-javadoc/classes/peer/peer.md` |
| `/javadoc/datomic/package-summary.html` | `04-apis/02-peer-api-javadoc/peer-api-javadoc.md` |
| `/client-api/datomic.client.api.html` | `04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md` |
| `/reference/client-reference.html` | `04-apis/04-client-api/client-api.md` |
| `/api/datomic-local.html` | `04-apis/05-datomic-local-api/datomic-local-api.md` |
| `/indexes/index-pull.html` | `04-apis/06-index-pull/index-pull.md` |
| `/indexes/index-apis.html` | `04-apis/07-index-apis/index-apis.md` |
| `/reference/rest.html` | `04-apis/09-rest-api/rest-api.md` |
| `/reference/io-stats.html` | `04-apis/10-io-stats/io-stats.md` |
| `/reference/query-stats.html` | `04-apis/11-query-stats/query-stats.md` |
| `/reference/tx-stats.html` | `04-apis/12-tx-stats/tx-stats.md` |
| `/api/error-handling.html` | `04-apis/13-error-handling/error-handling.md` |
| `/datomic-overview.html` | `introduction.md` |
| `/changes/pro.html` | `11-releases/01-datomic-pro/02-pro-change-log/pro-change-log.md` |
| `/release-notices.html` | `11-releases/01-datomic-pro/03-pro-release-notices/pro-release-notices.md` |
| `/operation/capacity.html` | `05-operation/01-pro/04-capacity-planning/capacity-planning.md` |
| `/operation/excision.html` | `05-operation/01-pro/14-excision/excision.md` |
| `/getting-started/dev-setup.html` | `03-tutorials/01-peer-tutorial/01-run-a-transactor/run-a-transactor.md` |
| `/peer-tutorial/connect-to-a-database.html` | `03-tutorials/01-peer-tutorial/02-connect-to-a-database/connect-to-a-database.md` |
| `/glossary.html` | `12-glossary/glossary.md` |
| `/reference/database-functions.html` | `06-reference/02-transactions/04-transaction-functions/transaction-functions.md` |
| `/transactions/acid.html` | `06-reference/02-transactions/05-acid/acid.md` |
| `/transactions/transaction-processing.html` | `06-reference/02-transactions/03-processing-transactions/processing-transactions.md` |
| `/transactions/transaction-functions.html` | `06-reference/02-transactions/04-transaction-functions/transaction-functions.md` |
| `/cloud/time/filters.html` | `06-reference/06-time-in-datomic/time-in-datomic.md` |
| `/cloud/operation/storage-template.html` | `05-operation/02-cloud/04-storage-template/storage-template.md` |
| `/cloud/operation/growing-your-system.html` | `05-operation/02-cloud/03-growing-your-system/growing-your-system.md` |
| `/cloud/operation/cli-tools.html` | `05-operation/02-cloud/07-cli-tools/cli-tools.md` |
| `/cloud/operation/vpc-access.html` | `05-operation/02-cloud/09-vpc-access/vpc-access.md` |
| `/cloud/operation/high-availability.html` | `05-operation/02-cloud/10-high-availability-ha/high-availability-ha.md` |
| `/cloud/operation/howto.html` | `05-operation/02-cloud/11-how-to/how-to.md` |
| `/cloud/operation/architecture.html` | `05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md` |
| `/cloud/changes.html` | `11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md` |
| `/cloud/releases.html` | `11-releases/releases.md` |
| `/cloud/analytics/analytics-concepts.html` | `08-analytics/01-analytics-concepts/analytics-concepts.md` |
| `/cloud/analytics/analytics-configuring.html` | `08-analytics/03-cloud-configuration/cloud-configuration.md` |
| `/cloud/query/raw-index-access.html` | `04-apis/07-index-apis/index-apis.md` |
| `/cloud/query/query-pull.html` | `06-reference/03-query-and-pull/03-pull/pull.md` |
| `/cloud/query/query-data-reference.html` | `06-reference/03-query-and-pull/02-query-reference/query-reference.md` |
| `/cloud/api/io-stats.html` | `04-apis/10-io-stats/io-stats.md` |
| `/cloud/api/query-stats.html` | `04-apis/11-query-stats/query-stats.md` |
| `/cloud/transactions/transaction-data-reference.html` | `06-reference/02-transactions/02-transaction-data/transaction-data.md` |

For any URL not in this table, derive the repo path using the same pattern: strip the domain, map the path segments to the closest matching directory under the numbered top-level folders, and use the leaf `.md` file named after the directory. Add new entries to this table as URLs are encountered and resolved.

### Code Blocks

- `<pre class="src src-sh">` or `src-bash` -> fenced block with `sh`
- `<pre class="src src-clojure">` -> fenced block with `clojure`
- `<pre class="src src-example">` or plain `<pre class="example">` -> plain fenced block with no language tag
- Preserve other obvious languages when present, such as `xml`
- Strip all copy widgets such as `<div class="code-copy-button">` or `<button class="copy-button">`
- Strip inline color styles and syntax-highlighting spans from code. Render clean source.

### Inline Formatting

- `<code>` -> backticks
- `<b>` or `<strong>` -> `**bold**`
- `<i>` or `<em>` -> `*italic*`
- `<span class="underline">` -> `_underline_`
- `<sup>N</sup>` -> `^N`

### Lists

- `<ul>` -> unordered list with `-`
- `<ol>` -> ordered list with `1.`, `2.`, etc.
- Preserve nesting.

### Images

- Convert images to Markdown image syntax.
- Adjust paths so they are relative from the target `.md` file to the repo-root `images/` directory. For example, from a nested file use a path like `../../images/foo.png` when needed.
- Use useful alt text, usually the image filename or nearby caption text if no alt text exists.

### Strip Entirely

- Navigation sidebar (`<div class="d-nav-container">` and everything in it)
- Footer (`<footer>`)
- Copy-to-clipboard buttons (`<div class="code-copy-button">`, `<button class="copy-button">`)
- Anchor icon links (`<a class="anchorjs-link">`)
- All `style=` attributes
- HTML comments

## Current Conversion State

These files have content:

- `01-setup/setup.md`
- `01-setup/01-pro-setup/pro-setup.md`
- `01-setup/02-cloud-setup/cloud-setup.md`
- `01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md`
- `01-setup/02-cloud-setup/02-cloud-setup/cloud-setup.md`
- `01-setup/03-local-setup/local-setup.md`
- `02-accessing/accessing.md`
- `02-accessing/01-peer-library/peer-library.md`
- `02-accessing/02-client-library/client-library.md`
- `04-apis/01-peer-api-clojuredoc/peer-api-clojuredoc.md`
- `04-apis/02-peer-api-javadoc/interfaces/connection/connection.md`
- `04-apis/02-peer-api-javadoc/classes/util/util.md`
- `04-apis/02-peer-api-javadoc/interfaces/datom/datom.md`
- `04-apis/02-peer-api-javadoc/interfaces/entity/entity.md`
- `04-apis/02-peer-api-javadoc/interfaces/log/log.md`
- `04-apis/02-peer-api-javadoc/classes/peer/peer.md`
- `04-apis/02-peer-api-javadoc/classes/query-request/query-request.md`
- `04-apis/02-peer-api-javadoc/interfaces/attribute/attribute.md`
- `04-apis/02-peer-api-javadoc/interfaces/database/database.md`
- `04-apis/02-peer-api-javadoc/interfaces/database-predicate/database-predicate.md`
- `04-apis/02-peer-api-javadoc/interfaces/listenable-future/listenable-future.md`
- `04-apis/02-peer-api-javadoc/peer-api-javadoc.md`
- `04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md`

Known empty or not-yet-converted starting points:

- `introduction.md`
- `03-tutorials/tutorials.md`

## Javadoc Directory Naming Note

The `QueryRequest` class lives at `04-apis/02-peer-api-javadoc/classes/query-request/query-request.md`, not `query-result`. When linking to it, use `classes/query-request/query-request.md`.

## Known Issue

`01-setup/03-local-setup/local-setup.md` has a heading level jump near the beginning (`h1` -> `h3`). The "Setup" and "Configure Local Storage" sections should be promoted to `##` and `###` respectively if that file is touched.

## Source Site

Original documentation: https://docs.datomic.com

To find the HTML for a given page, navigate to the URL that corresponds to the `.md` file path and use the `<div id="content" class="content">` block.
