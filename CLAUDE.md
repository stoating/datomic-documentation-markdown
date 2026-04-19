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
- Convert all `href` links to local `.md` paths where the target exists in this repo
- External links (https://) stay as-is
- Resolve relative HTML paths (`../`) against the actual repo directory structure, not the original URL structure
- The original site uses paths like `/operation/storage.html` — map these to the equivalent file, e.g. `../../05-operation/01-pro/01-storage-services/storage-services.md`

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
- `datomic-clojure-api/datomic-clojure-api.md` ✓ — this is the target for any `../clojure/index.html` links

Everything else in the repo is an empty `.md` file waiting to be filled. The next file in sequence is `03-tutorials/tutorials.md`.

## Known issue to fix

`01-setup/03-local-setup/local-setup.md` has a heading level jump on line 17 (h1 → h3). The "Setup" and "Configure Local Storage" sections should be `##` and `###` respectively (or promoted to `##`/`###` to follow the h1). Fix this if you touch the file.

## Source site

Original documentation: https://docs.datomic.com  
To find the HTML for a given page, navigate to the URL that corresponds to the `.md` file path and grab the `<div id="content" class="content">` block.
