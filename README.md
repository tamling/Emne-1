# Emne 1 - course reader (Quarto)

Quarto book project for the Emne 1 course reader (Cybersecurity, networks,
Linux and scripting - Gokstad Akademiet), set up like the
[SKY2100](https://github.com/tamling/SKY2100) course script.

```
_quarto.yml         project config: book structure (4 module parts, 26 chapters)
index.qmd           book landing page (about, module table, version history)
chapters/           one .qmd per lecture (NN-name.qmd), converted from the
                    LaTeX reader chapters (chNN.tex / e1reader.sty)
chapters/figures/   pre-built SVG figures, referenced by the chapters
_tikz/              TikZ sources for the figures + build.sh → chapters/figures/*.svg
theme-light.scss    light theme tweaks (source tags, bridges, output blocks)
theme-dark.scss     dark theme tweaks (same, plus a light backing card for figures)
```

Render: `quarto render`. Publishing runs from `.github/workflows/publish.yml`
(GitHub Pages on push to `main`).

## Dark mode

Every page carries a light/dark toggle (top right). The two themes are
configured in `_quarto.yml`:

```yaml
theme:
  light: [cosmo, theme-light.scss]
  dark: [darkly, theme-dark.scss]
```

The figures are SVGs with dark ink on transparent background;
`theme-dark.scss` gives them a white backing card so they stay readable in
dark mode. Neither theme loads Google Fonts (`$web-font-path: false`) so the
toggle never stalls on a blocked font request.

## Conventions carried over from the LaTeX reader

| LaTeX (e1reader.sty) | Site |
|:---------------------|:-----|
| `defbox` (grey definition box) | `callout-note` |
| `thinkbox` (orange question box) | `callout-warning` |
| `casebox` (blue Nordvik AS box) | `callout-tip` |
| `keybox` (red key-idea box) | `callout-important` |
| `\chaptermeta{…}` | simple callout under the chapter title |
| `\src{…}` / `\srcp{…}` | `[…]{.src}` inline source tag |
| `\bridge{…}` | `[→ …]{.bridge}` forward pointer |
| `lstlisting[style=out]` | fenced block with `.code-out` |

## Password gate

Every page loads `includes/password-gate.html` (via `include-in-header`):
an overlay asks for the course password before the page becomes visible,
and a correct entry is remembered in the browser (localStorage). The
current password is **Nordvik2026**; to change it, compute
`python3 -c "import hashlib; print(hashlib.sha256('emne1|NEWPASSWORD'.encode()).hexdigest())"`
and put the hash into `E1_HASH` in that file (bump the storage key to
force re-entry).

This is client-side only - it keeps the reader from being casually
opened, but it is not real access control: the content still reaches the
browser and lives in this repository. For real protection the site would
need an authenticating host (e.g. Cloudflare Access) and a private repo.

## Interactive exercises

Chapters can embed self-contained HTML/JS widgets in a ```` ```{=html} ````
block - Quarto passes them straight through to the page. The first one is
the **CIA-triad drag-and-drop** in chapter 1 (`chapters/01-…qmd`, look for
`cia-triad-ex`): six terms, three drop zones on a triangle, auto-check when
all corners are filled, with a hint when authenticity is misplaced. It
supports mouse drag, tap-to-place on touch screens, and keyboard (Tab +
Enter), and styles itself for both themes via `body.quarto-dark`. Use it as
the template for further exercises: scope all CSS/JS under one widget class,
give the container a unique id, and keep state in plain JS (no libraries).

## Rebuilding the figures

`_tikz/build.sh` renders every `_tikz/chNN-figM.tikz` to
`chapters/figures/chNN-figM.svg`. It needs `pdflatex` with TikZ
(`texlive-pictures`, `standalone` from `texlive-latex-extra`) and
`pdftocairo` (`poppler-utils`). The SVGs are committed, so the site build
itself needs no LaTeX.
