# THOMSON project

This directory contains the main LaTeX document for the Thomson-scattering
project. It is part of the parent `projects` Git monorepository, alongside the
reusable source sections in `../shared_src`.

## Required directory layout

Keep the project and shared files in this relative layout:

```text
work/TeX/
├── bibliography/
│   └── main.bib
└── projects/
    ├── THOMSON_project/
    ├── shared_src/
    └── preamble-report.tex
```

`THOMSON_project/main.tex` loads sections from `../shared_src`, uses
`../preamble-report.tex` through the tracked `preamble-report.tex` symlink,
and reads the bibliography from `../../bibliography/main`.

Git commands issued here operate on the parent `projects` repository.

## Build

From this repository directory, run:

```sh
latexmk main.tex
```

The included `latexmkrc` selects pdfLaTeX.
