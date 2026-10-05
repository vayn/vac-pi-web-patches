# pi-web-patches — an enhancement patch series for pi-web

> Upstream baseline: [@agegr/pi-web](https://github.com/agegr/pi-web) **v0.10.0** (MIT).
> This project is **not** a Pi extension. It is a **patch series** against pi-web
> (ordered `git format-patch` numbering), applied as working-tree state so the
> upstream commit history is never rewritten.

*[中文版 / Chinese version](README.zh-CN.md)*

## 1. What's inside

| # | Patch | Type | Effect |
|---|---|---|---|
| 0001 | `feat-pi-web-compaction` | feat | Collapse compaction messages by default. The title bar becomes a clickable button (`aria-expanded`) showing only a chevron, the summary's first line and the timestamp; expanding reveals the full summary plus the read/modified file list |
| 0002 | `chore-pi-web-pnpm` | chore | Ignore pnpm-generated lockfile (the project keeps the upstream npm layout) |
| 0003 | `fix-pi-web-height-maxHeight` | fix | Long questions no longer squeeze the options area: the dialog gets an explicit height so it resolves to a definite size |
| 0004 | `feat-pi-web-model-caps-rate-badges` | feat | Model capability icons and rate badges: `/api/models` reads a `model-caps.json` sidecar and renders reasoning/image capability icons plus a rate badge in the model selector |
| 0005 | `feat-pi-web-table-zoom` | feat | Markdown table **zoom button and fullscreen view**. The button floats at the table's top-right (always visible on touch); in fullscreen, `<dialog>` cells **wrap automatically**, falling back to horizontal scrolling only when a table has too many columns to fit |

## 2. Why patches instead of pull requests

This series follows a **never-upstream** policy: the patches are for local use and are
**never submitted upstream as PRs**. Upgrading the upstream version is therefore not a
rebase but a **patch re-issue**:

1. Point the upstream checkout at the new tag;
2. Apply this series in order; any failure means a conflict with the new upstream;
3. Realign the patches against the new upstream, keeping the same ordered numbering.

The payoff: the upstream repository stays a **read-only tag**, local changes create no
orphan commits, and clones are reproducible.

## 3. Applying

```bash
# Fetch the upstream baseline
git clone --depth 1 -b v0.10.0 https://github.com/agegr/pi-web
cd pi-web

# Apply in order (the numbering is the order)
for p in /path/to/pi-web-patches/patches/*.patch; do
  git apply --check "$p" && git apply "$p" || { echo "conflict: $p"; break; }
done
```

> Use `git apply`, not `git am`: mailinfo is skipped, which preserves whitespace
> semantics such as CRLF exactly.

Build the upstream project normally afterwards (pi-web is a Next.js app; install
dependencies before building).

## 4. Patches are bound to an upstream version

The patches apply by **line-context**, so they are tightly bound to the upstream
version. `git apply --check` failing against a different baseline is **expected
behaviour, not a defect** — re-issue the patches rather than forcing `--3way`.

## 5. Optional data dependency of 0004

The rate badges and capability icons rendered by 0004 come from two sources:

- **Capabilities** (`reasoning`, image input): declared per model and served to the
  web UI through `/api/models`;
- **Rate**: a `model-caps.json` **sidecar** file (schema 1) in the agent directory.

A sidecar is used instead of a `cost` field because the rate is a **relative multiplier
of the upstream billing factor**, not a currency price; writing it into `cost` would
corrupt that field's semantics. When the sidecar is missing, 0004 **degrades silently** —
no badges are shown and nothing else breaks.

## 6. Desensitization statement

These patches were prepared for public release from a private working tree. Exactly
three classes of text were neutralized, and **nothing else was touched**:

| Class | Example (before → after) |
|---|---|
| Author identity | the private author name and address → `pi-web contributor <contributor@example.com>` |
| Internal references | comments naming a private repository path, a private ledger file and its section numbers → neutral descriptions |
| Third-party product names | private gateway and channel names in comments → "the upstream gateway" / "a mobile table component" |

The series contains **no keys, tokens, passwords or private addresses**. Verified after
sanitization:

- All 5 patches apply cleanly in order to a bare `v0.10.0` tree;
- The resulting tree differs from the pre-sanitization result in **exactly 5
  comment lines**, all of them the neutralized text above, and **zero code lines**.

## 7. Changelog

| Version | Change |
|---|---|
| 1.0.0 | First public release: 5 patches (0001–0005) against pi-web v0.10.0, desensitized |

## 8. License

The upstream code these patches modify is MIT (see the upstream `LICENSE`). This patch
series is likewise offered under **MIT**.
