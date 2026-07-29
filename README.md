# Simple-Markdown-Editor-Reader
A single stand alone HTML page to open, read, create, save, export and edit Markdown files.

# Markdown Reader

Open a `.md` file with **Open**, or just drag one onto this window. The left pane is a
live editor; the right pane renders as you type. Everything runs locally in this one file.

## What it handles

| Feature | Syntax | Notes |
|---|:--|--:|
| Headings | `# … ######` | ATX + setext, auto anchors |
| Emphasis | `**bold**`, `*italic*`, `~~strike~~`, `==mark==` | nesting works |
| Tables | `\| a \| b \|` | alignment row supported |
| Footnotes | `[^1]` | collected at the bottom[^1] |
| Task lists | `- [x] done` | see below |

- [x] Split view with synced scrolling
- [x] Syntax-highlighted code blocks
- [ ] Anything else you want added
  - nested items work too

> [!NOTE]
> Blockquote callouts (`NOTE`, `TIP`, `WARNING`, `CAUTION`) get styled automatically.

## Code

```python
def fib(n: int) -> list[int]:
    """First n Fibonacci numbers."""
    a, b, out = 0, 1, []
    for _ in range(n):
        out.append(a)
        a, b = b, a + b
    return out  # 0, 1, 1, 2, 3, 5 …
```

```sql
SELECT user_id, COUNT(*) AS n
FROM events WHERE created_at > NOW() - INTERVAL '7 days'
GROUP BY 1 ORDER BY n DESC LIMIT 10;
```

## Shortcuts

<kbd>Ctrl</kbd>+<kbd>O</kbd> open · <kbd>Ctrl</kbd>+<kbd>S</kbd> save · <kbd>Ctrl</kbd>+<kbd>B</kbd> bold ·
<kbd>Ctrl</kbd>+<kbd>I</kbd> italic · <kbd>Ctrl</kbd>+<kbd>K</kbd> link · <kbd>Ctrl</kbd>+<kbd>\</kbd> toggle preview

[^1]: Footnotes link both ways — click the arrow to jump back.
