# Blog Post Frontmatter Cheatsheet

This reference lists the fields The Tool Store reads from the top of a `.md`
file. For a copy-paste starting point, put a `TEMPLATE.md` next to this file.

## Frontmatter parsing

A post is a markdown file with an optional **frontmatter block** at the top.
The block is a YAML-ish header between two `---` lines:

```markdown
---
title: My Post Title
publishedAt: 2026-05-11
tags: [foo, bar]
---

Markdown body starts here.
```

Rules:

- The opening `---` must sit on the **first line** of the file. Otherwise the
  parser treats the whole file as body and applies the frontmatter defaults.
- The closing `---` must sit on its own line.
- Write each field as `key: value` on one line. The parser doesn't support
  nested objects.
- Arrays use bracket form: `[item1, item2, item3]`. The parser strips quotes
  from quoted items.
- `true` / `false` parse as booleans.
- Plain numbers such as `42` parse as numbers.
- Anything else parses as a string, with surrounding quotes stripped.

## Recognized fields

| Field | Type | Required | Fallback when omitted |
| ----- | ---- | -------- | --------------------- |
| `title` | string | no | First `# heading` in the body, or the filename slug |
| `author` | string | no | The repo owner, gist owner, or "The Tool Store" |
| `publishedAt` | ISO date / `YYYY-MM-DD` | no | The file's creation timestamp |
| `date` | (alias for `publishedAt`) | no | Same as `publishedAt` |
| `updatedAt` | ISO date | no | The file's modification timestamp |
| `categories` | string array | no | `[]` |
| `category` | (alias for `categories`) | no | Same as `categories` |
| `tags` | string array | no | `[]` |
| `keywords` | string array | no | `[]` |
| `excerpt` | string | no | First ~220 chars of the body, code blocks + links stripped |
| `description` | (alias for `excerpt`) | no | Same as `excerpt` |
| `coverImage` | URL string | no | none |
| `featured` | boolean | no | `false` |
| `status` | `published` \| `draft` | no | `published` |
| `readingTimeMinutes` | number | no | Computed from word count (~220 wpm) |

## Field semantics

### `title`

The reader uses the title as the post heading. Cards and list rows show it too.

Fallback chain:
1. Frontmatter `title`.
2. The first `# heading` (or `##`, `###`, …) in the body.
3. The filename without `.md` (e.g. `my-post.md` → `my-post`).

### `author`

The reader shows the author next to the avatar. List and card views show it
below the title. The avatar defaults to the GitHub avatar of the repo or gist
owner. To override it, set `authorAvatar:` to an image URL in the frontmatter.

### `publishedAt` / `date`

The default sort (`Newest first`) uses this date, and the reader displays it.
Both field names accept ISO 8601 (`2026-05-11T13:24:00Z`) or `YYYY-MM-DD`.

If you set both, `publishedAt` wins.

### `updatedAt`

The `Recently updated` sort mode reads this field. If you omit it, The Tool
Store uses the file's mtime.

### `categories`

Each category is a **slug** (lowercase, hyphenated). The Tool Store creates a
`BlogCategory` for each unique slug it finds and gives it a title-cased display
name. Admins can edit the name, description, and color in
`CMS → Blog → Blog Categories`.

```yaml
categories: [news, getting-started, engineering]
```

### `tags`

Free-form lowercase strings. The filter sidebar's Tags section lists them with
a post count for each. Any string works: `tags: [release, beta, v0.1]`.

### `keywords`

The UI doesn't display keywords. Only the sidebar's full-text Keyword search
reads them. Add terms you want readers to find the post by when those words
don't appear in the title or body.

### `excerpt` / `description`

A one- or two-sentence summary. The post list shows it in list and card views,
and the reader shows it under the title. If you omit it, The Tool Store builds
one from the first ~220 characters of the body. A hand-written excerpt reads
better than that cut.

### `coverImage`

Absolute URL to a hero image. The reader shows it above the post, and medium
and large cards in the list show it too. Use a CDN-hosted image: relative
paths won't resolve when The Tool Store loads the post from a remote source.

### `featured`

When `true`, the post gets a `Featured` badge in the list and appears in the
`Featured only` filter. It still sorts by the active sort mode.

Feature a handful of posts at most. If you feature them all, the badge tells
readers nothing.

### `status`

- `published` (default): visible to everyone.
- `draft`: visible to admins only. Use it to stage a post in the repo before
  it goes live.

### `readingTimeMinutes`

Optional override. By default The Tool Store estimates reading time from the
rendered word count at ~220 words per minute. Set this to force a number, for
example on a tutorial with interactive steps.

## Required body content

The body has **no** required content, and an empty body works. For a useful
post, include:

1. A `# Heading` that matches `title`. You can also omit `title` from the
   frontmatter and let the heading set it.
2. A one-paragraph hook.
3. The content, in `##` sections.

## Supported markdown features

The Tool Store renders posts with the `marked` library, GitHub-flavored
markdown enabled:

- Headings `#` through `######`
- **Bold**, *italic*, ***bold italic***, ~~strikethrough~~, `inline code`
- [Links](https://example.com), which open in the user's default browser
- Images via `![alt](url)`
- Fenced code blocks with language hints (\`\`\`ts, \`\`\`python, etc.)
- Bullet and numbered lists, including nested
- Task lists (`- [ ]`, `- [x]`)
- Tables with column alignment
- Blockquotes
- Horizontal rules (`---`)

## Ingesting from a GitHub repo

1. The user adds this repo as a **Blog GitHub Repo Source** in
   `CMS → Blog → Blog GitHub Repo Sources` (owner + repo). Public repos
   **don't** need a token.
2. On each Blog-tab visit, the app calls
   `GET https://api.github.com/repos/{owner}/{repo}/contents/{path}` once
   to list the repo's files.
3. For each `.md` file, the app fetches the raw body from
   `raw.githubusercontent.com`. That CDN has no rate limit and needs no auth
   for public repos.
4. The app parses the frontmatter, keeps the body as the post content, and
   writes the result into the user's local CMS store under a stable ID
   (`gh-{owner}-{repo}-{path}`).
5. Syncs keep the user's `enabled` flag. If you disable a post once, it stays
   disabled after the file refreshes.

## Ingesting from a Gist

Gists work the same way, with `gists/{gistId}` as the source URL. Stable post
IDs take the form `gist-{gistId}-{filename-slug}`, and the app takes the
author from the gist owner in the API response.

## Common gotchas

- **Indented `---`**: the opening and closing fences must start at column 0,
  with no leading spaces.
- **Multi-line strings**: not supported. Keep each frontmatter value on one
  line, long excerpts included.
- **Quoted keys**: not supported. Keys are plain identifiers.
- **Empty arrays**: write `tags: []`. A blank right side parses as an empty
  string.
- **Drafts on public sources**: The Tool Store still fetches `status: draft`
  files from a public repo, and only admins of that install see them in the
  Blog tab. Anyone with the repo URL can read the body, so *do not* put
  secrets in a draft post.

## Minimum viable post

```markdown
---
title: My First Post
publishedAt: 2026-05-11
---

# My First Post

A one-paragraph body meets the minimum.
```

Every other field falls back to the defaults in the Recognized fields table.
