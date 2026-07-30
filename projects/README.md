# Scientific LaTeX projects

This directory contains project-specific LaTeX documents and source material
shared between them. It is part of the parent `TeX` monorepository, which also
versions the common bibliography.

## Layout

```text
projects/
├── NP/
├── NR_Volkov/
├── THOMSON_project/
├── nuclear_theory/
├── shared_src/
└── preamble-report.tex
```

Individual VS Code workspaces may expose only one project and the shared
sources. Git history and commits belong to the parent `TeX` repository. The
common bibliography is versioned at `../bibliography/main.bib`.
