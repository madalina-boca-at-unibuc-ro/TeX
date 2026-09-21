# THOMSON project

A LaTeX project assembling reusable sources on classical radiation scattering
and electron–laser interaction. This file is the single project guide and
working-instructions document; keep it current when the layout or build changes.
Physics discussions and numerical implementation notes live separately in
`md_helpers/` and `theory/` and are not loaded by LaTeX. Preserve those notes.

## Layout and profiles

- `main.tex` is the only root document. Keep the document class, preamble,
  document environment, title, profile selection, and bibliography setup there.
- `../shared_src/` holds reusable scientific document-body fragments, accessed
  through `\srcpath`. Keep new scientific sources there even if only this
  project uses them today.
- `preamble-report.tex` is a symlink to `../preamble-report.tex`, the common
  preamble used by sibling projects. Changes can affect those projects too.
- `../../bibliography/main.bib` is the shared bibliography, used with BibTeX
  and the `plain` style.
- `profiles/*.inc` select content using `\part`, `\chapter`, and `\input`.
  Enable one profile in `main.tex`; always inspect that file for the active one.
  Available profiles are `all.inc` (complete document), `lpwa.inc`
  (LPWA/applications), and `angular_momentum_paper.inc` (fields, motion,
  Fourier transforms, and energy, Poynting-vector, linear-momentum, and
  angular-momentum observables, including densities and flux densities).
- `THOMSON_project.code-workspace` exposes this project, the shared sources,
  and the bibliography in VS Code, with `main.tex` as the compilation root.

## Build and verification

Run from this directory:

```sh
latexmk main.tex
```

`latexmkrc` selects pdfLaTeX. Rebuild after changing LaTeX sources or build
configuration. Report compilation errors separately from existing duplicate-label,
PDF-string, and overfull/underfull box warnings; do not fix unrelated warnings.
Run `git diff --check` after editing tracked text files. Preserve unrelated user
edits, including those in shared sources.

## Writing conventions

- Use `\input` for profiles and shared fragments, and globally distinctive
  label prefixes to avoid collisions when combining sources.
- Use `importantbox` for principal results and `highlightbox` for intermediate
  results; both accept optional `tcolorbox` settings.
- Use `\missing`, `\todo`, `\tocheck`, `\question`, and `\draftnote` for inline
  draft annotations. The common preamble controls them with `\draftnotestrue`
  or `\draftnotesfalse`.
- For larger annotations, use `\begin{warningbox}{MISSING} ... \end{warningbox}`
  or, for example, `\begin{warningbox}[checkcolor]{CHECK} ... \end{warningbox}`.
  These boxes remain visible even when inline draft notes are disabled.
- `\chapterauthor{...}` adds attribution below a chapter heading. Equations
  are numbered within sections, and chapter numbers include the part number.
- The preamble loads `ulem`, so `\emph` produces underlining.
