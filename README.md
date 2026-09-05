# Emne 1 — course reader (Quarto)

Quarto book project for the Emne 1 course reader (Cybersecurity, networks,
Linux and scripting — Gokstad Akademiet), set up like the
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

## Rebuilding the figures

`_tikz/build.sh` renders every `_tikz/chNN-figM.tikz` to
`chapters/figures/chNN-figM.svg`. It needs `pdflatex` with TikZ
(`texlive-pictures`, `standalone` from `texlive-latex-extra`) and
`pdftocairo` (`poppler-utils`). The SVGs are committed, so the site build
itself needs no LaTeX.
