# Working instructions for the THOMSON project

Before making structural or build-related changes, read `ARCHITECTURE.md`.
Keep that document synchronized when a change affects the documented layout,
entry point, profiles, shared paths, preamble, bibliography, or build process.

## Scope and ownership

- Treat `main.tex` as the only root document for this project.
- Keep potentially reusable scientific document-body sources under
  `../shared_src`; do not classify sources as permanently project-specific.
- Treat `../preamble-report.tex` as the common preamble used by sibling
  projects. Changes to it can affect more than this project.
- Preserve unrelated user edits, including changes in `../shared_src`.

## Profiles

- Keep document selections under `profiles/` as `.inc` files.
- Select the active profile explicitly in `main.tex`.
- A profile may contain structural commands such as `\part`, `\chapter`, and
  `\input`.
- Do not assume that `profiles/all.inc` is the active profile; inspect
  `main.tex` first.

## LaTeX conventions

- Use `\input` for shared source fragments and profile files.
- Keep `\documentclass`, `\begin{document}`, the bibliography setup, and
  `\end{document}` in `main.tex`.
- Prefer globally distinctive label prefixes in reusable sources to prevent
  collisions when several fragments are combined.
- Use the semantic draft commands from the common preamble: `\missing`,
  `\todo`, `\tocheck`, `\question`, and `\draftnote`. Use `warningbox` for
  larger annotations.
- Do not fix unrelated typesetting or duplicate-label warnings unless asked.

## Verification

- Build from this directory with `latexmk main.tex` after changing LaTeX
  sources or build configuration.
- Report compilation errors separately from existing warnings.
- Check `git diff --check` after editing tracked text files.
