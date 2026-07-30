# Easiest-Markdown-Editor-Reader
A single stand alone HTML page to open, read, create, save, export and edit Markdown files.

# Markdown Reader

A single-file markdown reader and two-way editor. Open `easiest-markdown-editor-reader.html` in any
browser — no server, no build step, no dependencies, no network access required.

Left pane: the markdown source, with line numbers and syntax highlighting.
Right pane: the rendered document, which you can also **edit directly** like a word
processor. Changes made on either side are written back to the other.

---

## Quick start

1. Double-click `markdown-reader.html`.
2. **Open** (or drag a `.md` file onto the window).
3. Edit in either pane.
4. **Save** (`Ctrl+S`) writes back to the file you opened.

The current document and all preferences are kept in `localStorage`, so reopening the
page restores your last session.

---

## Two-way editing

This is the headline feature. Every rendered block carries the source line range it came
from (`data-ls` / `data-le`). When you edit a block in the preview, only those lines are
rewritten — the rest of the file, including spacing, reference definitions, footnote
definitions and anything else the parser doesn't own, is preserved byte-for-byte.

### What's editable in the preview

| Element | Behavior |
|---|---|
| Headings | Text rewritten in place; `#` prefix and setext underlines preserved |
| Paragraphs | Inline formatting round-trips; hard-wrapped paragraphs are re-wrapped to their original column width |
| List items | Marker, indentation and task-box prefix preserved |
| Blockquotes / callouts | `>` prefix and `[!NOTE]`-style markers preserved |
| Table cells | Patched inside the row line — other cells keep their exact spacing |
| Code blocks | Body replaced; fences, language tag and indentation preserved |
| Task checkboxes | Clicking one flips `[ ]` ↔ `[x]` in the source |

Inline formatting is serialized back to markdown: `**bold**`, `*italic*`, `` `code` ``,
`~~strike~~`, `==mark==`, links, images, footnote references, `<kbd>` and `<u>`.

### Editing keys (preview pane)

| Key | Action |
|---|---|
| `Enter` | Split the block, or start a new paragraph / list item below |
| `Enter` on an empty bullet | End the list, start a paragraph |
| `Shift+Enter` | Hard line break (`  ` at end of line) |
| `Backspace` in an emptied block | Delete the block and its source lines |
| `Ctrl+B` / `Ctrl+I` / `Ctrl+E` | Bold / italic / inline code |
| `Ctrl+K` | Wrap the selection in a link |
| `Ctrl+click` | Follow a link (plain clicks are inert while editing) |

Pasted content is inserted as plain text, so nothing foreign leaks into the markdown.

### Tables

Put the caret in any table and a floating toolbar appears above it:

- **↑Row / ↓Row / ✕Row** — insert above, insert below, delete
- **←Col / →Col / ✕Col** — insert left, insert right, delete
- **⇤ ⇔ ⇥** — set column alignment (rewrites the `:---:` separator row)

Structural changes re-pad the whole table so the markdown source stays column-aligned.

| Key | Action |
|---|---|
| `Tab` / `Shift+Tab` | Next / previous cell (tabbing past the last cell adds a row) |
| `Enter` / `↓` / `↑` | Move down / up a row |

Toggle the whole feature with the **Edit preview** button in the toolbar.

---

## Markdown support

Hand-rolled CommonMark subset plus the usual GFM extensions:

- **Headings** — ATX (`#`–`######`) and setext, with auto-generated anchor IDs and hover links
- **Emphasis** — `**bold**`, `*italic*`, `***both***`, `~~strike~~`, `==mark==`, `^sup^`
- **Lists** — ordered, unordered, nested, tight/loose, task lists, auto-continuation
- **Tables** — GFM pipe tables with per-column alignment; escaped pipes and code spans in cells
- **Code** — fenced (``` or `~~~`) and 4-space indented, with a hover Copy button
- **Blockquotes** — nested, plus `[!NOTE]` / `[!TIP]` / `[!WARNING]` / `[!CAUTION]` callouts
- **Links** — inline, reference-style, autolinks, bare URLs, email
- **Footnotes** — `[^1]` with bidirectional jump links
- **Front matter** — YAML block rendered as a metadata card
- **Misc** — images, horizontal rules, backslash escapes, hard line breaks

Raw HTML is escaped by default. The **Raw HTML** toggle renders it instead — leave it off
for files you don't trust.

### Syntax highlighting

Regex-based scanner, 16 language rule sets reachable through 60+ aliases: JavaScript/
TypeScript (+JSX/TSX), Python, shell/bash/PowerShell, JSON, YAML, HTML/XML/SVG/Vue,
CSS/SCSS/Less, SQL, Go, Rust, C/C++/Objective-C headers, Java/C#/Kotlin/Scala/Swift,
PHP, Ruby, INI/TOML/env, and diff/patch. Unknown languages fall back to plain text.

### Mermaid diagrams

` ```mermaid ` blocks render as diagrams. This is the one feature that isn't self-contained:
the renderer is fetched from a CDN the first time a diagram appears. Offline, the block
degrades gracefully to its source with a note.

---

## Toolbar

| Control | Description |
|---|---|
| **Open / New / Save** | Uses the File System Access API for true save-in-place; falls back to download |
| **Export** | Standalone HTML (styles inlined), Print/PDF, copy rendered HTML, copy plain text |
| **Split / Source / Preview** | View modes; the divider is draggable (double-click to reset) |
| **Edit preview** | Toggle WYSIWYG editing |
| **Outline** | Slide-out heading navigator |
| **Sync** | Proportional scroll sync between panes |
| **Wrap** | Soft wrap in the source editor |
| **Hi-Src** | Syntax highlighting of the markdown source |
| **Raw HTML** | Render raw HTML instead of escaping it |
| **Light / Dark** | Theme toggle (defaults to your OS preference) |

### Global shortcuts

`Ctrl+O` open · `Ctrl+S` save · `Ctrl+\` toggle preview · `Ctrl+B` / `Ctrl+I` / `Ctrl+E` /
`Ctrl+K` formatting (both panes) · `Tab` indent/outdent in the source editor.

---

## How it works

Everything lives in one HTML file, in three parts:

**1. Parser** — two passes. A pre-pass strips link reference and footnote definitions
while recording original line numbers. The block parser then walks the remaining lines,
handing sub-arrays (plus their line maps) to itself recursively for blockquotes and list
items. Inline parsing escapes HTML first, then converts constructs in a fixed order, each
result stashed behind a placeholder so later passes can't corrupt it.

**2. Highlighter** — a scanner that runs each language's rules from `lastIndex` and takes
the earliest match, with rule order breaking ties (comments and strings before keywords).
The same machinery highlights the markdown source behind the editor textarea.

**3. Editor** — the source pane is a transparent `<textarea>` overlaid on a highlighted
`<pre>` with matched metrics, plus a synced line-number gutter. The preview pane makes
individual blocks `contenteditable` and maps DOM edits back to source lines. Simple text
edits patch the source without re-rendering (so the caret never jumps); structural changes
re-render and restore the caret by source line.

**Performance** — a 122 KB document parses and highlights in roughly 90 ms. Rendering is
debounced (110 ms, 400 ms for very large files) and source highlighting is capped at 400 KB.

---

## Known tradeoffs

- Editing a hard-wrapped paragraph in the preview re-wraps it to approximately the original
  column width rather than preserving your exact line breaks.
- Splitting text in the middle of bold/italic produces two separately formatted halves
  (`**Ope**` / `**n**`), which is what the DOM says happened.
- The parser is a solid CommonMark + GFM subset, not a 100% spec-conformant implementation;
  a handful of pathological nesting cases won't match `cmark` exactly.
- Mermaid needs a connection on first use.
- Undo in the preview pane uses the browser's per-element stack, so it doesn't cross block
  boundaries. The source pane has normal textarea undo.

---

## Changelog

### v2 — two-way editing

- Preview pane is now editable: headings, paragraphs, list items, blockquote and callout
  text, table cells, and code blocks
- Source-line mapping (`data-ls` / `data-le`) on every block, so edits rewrite only the
  lines they touch
- HTML → markdown serializer for inline formatting, with prefix preservation for headings,
  list markers, task boxes and blockquote levels
- Floating table toolbar: add/delete rows and columns, column alignment, automatic source
  re-padding
- Table navigation with `Tab` / `Shift+Tab` / arrows; tabbing past the last cell adds a row
- `Enter` splits blocks and continues lists; `Backspace` deletes emptied blocks
- Clickable task checkboxes that write `[x]` back to the source
- Clipboard fallback for browsers that block the async API on `file://`
- Fixes found in testing: soft line breaks in text nodes were being serialized as real
  newlines; the block-shift heuristic wrongly matched the block immediately before an
  insertion point; the commit debounce only flushed the last-touched block

### v1 — reader

- Dependency-free CommonMark + GFM parser and syntax highlighter
- Split view with line numbers, highlighted source, synced scrolling, draggable divider
- Open / drag-drop / save (File System Access API), export to standalone HTML and PDF
- Outline drawer, light/dark themes, Mermaid support, persisted session

