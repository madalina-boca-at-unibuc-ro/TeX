# Scientific LaTeX collection

This monorepository contains related LaTeX projects, reusable source material,
and their shared bibliography.

## Versioned layout

```text
TeX/
├── bibliography/
│   └── main.bib
└── projects/
    ├── NP/
    ├── NR_Volkov/
    ├── THOMSON_project/
    ├── nuclear_theory/
    ├── shared_src/
    └── preamble-report.tex
```

`Books/` and `fast_wrt/` are intentionally excluded from this repository.
Individual VS Code workspaces can expose only the active project and shared
folders; Git commands issued inside them resolve to this parent repository.

