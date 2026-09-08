# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A LaTeX driver project that assembles reusable scientific-writing fragments
(classical radiation scattering, electron–laser interaction, Thomson
scattering) into a single book document. `main.tex` is the only root
document; it never contains scientific content itself — content lives in
`../shared_src` and is selected via a `profiles/*.inc` file.

Two docs already carry the detailed rules — read them before any structural
or build-related change, and keep them in sync when such a change lands:

- `AGENTS.md` — working instructions and scope/ownership rules.
- `ARCHITECTURE.md` — full repository layout, preamble conventions, box/draft
  commands, bibliography wiring, known issues.

## Repository layout

This project is one part of a larger `TeX` tree; git commands here operate on
the parent `projects` repository (the bibliography is a separate repo):

```text
TeX/
├── bibliography/main.bib
└── projects/
    ├── preamble-report.tex        # common preamble, shared by sibling projects
    ├── shared_src/                # reusable document-body fragments (by subject)
    └── THOMSON_project/
        ├── main.tex                # documentclass, preamble, title, profile select, bib
        ├── preamble-report.tex -> ../preamble-report.tex   (symlink)
        └── profiles/
            ├── all.inc             # complete THOMSON structure
            └── lpwa.inc            # smaller LPWA/application-focused build
```

`shared_src` subject areas: `NR_Volkov/`, `classical_motion/`,
`coherent_thomson/`, `elm_el_sc/`, `elm_field/`.

## Build

```sh
latexmk main.tex
```

Run from `THOMSON_project/`. `latexmkrc` forces `$pdf_mode = 1` (pdfLaTeX,
required by kpfonts) even if the system default is LuaLaTeX. After editing
LaTeX sources or build config, rebuild and report compilation errors
separately from pre-existing warnings (see Known issues below).

Sanity-check text edits with `git diff --check` before considering a change
done.

## Key rules (see AGENTS.md for the full list)

- Treat `main.tex` as the only root document; keep `\documentclass`,
  `\begin{document}`, bibliography setup, and `\end{document}` there.
- The active profile is whichever `\input{profiles/....inc}` line is
  uncommented in `main.tex` — do not assume it is `all.inc`, check first.
- New reusable scientific content goes under `../shared_src`, not inside this
  project; don't treat sources as permanently project-specific.
- Give reusable sources globally distinctive label prefixes to avoid
  collisions when fragments are combined into different profiles.
- Changes to `../preamble-report.tex` affect sibling projects — check for
  cross-project consequences.
- Use the semantic draft/content commands from the common preamble rather
  than ad hoc formatting: `\missing`, `\todo`, `\tocheck`, `\question`,
  `\draftnote`, `warningbox` (draft annotations); `importantbox`,
  `highlightbox` (finished-content boxes). Full semantics in
  `ARCHITECTURE.md`.
- Note: the preamble loads `ulem`, so `\emph` renders as underline, not
  italics, project-wide.
- Do not fix unrelated typesetting or duplicate-label warnings unless asked —
  `profiles/all.inc` is known to combine sources that produce multiply
  defined labels; this is a pre-existing issue, not a regression to chase.

## Editor integration

The VS Code multi-root workspace (`THOMSON_project.code-workspace`) exposes
this project, `../shared_src`, and `../../bibliography` together, with this
project's `main.tex` as the compilation root.
