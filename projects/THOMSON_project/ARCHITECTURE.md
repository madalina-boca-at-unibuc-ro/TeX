# THOMSON project architecture

## Purpose

THOMSON is a LaTeX driver project for a larger collection of material on
classical radiation scattering and electron--laser interaction. It assembles
reusable scientific source fragments into different document profiles, so a
small working document can be compiled without rebuilding the complete work.

This file records the current architecture and the decisions behind it. Update
it when those facts change.

## Repository context

The project is part of a larger `TeX` repository with this relevant layout:

```text
TeX/
├── bibliography/
│   └── main.bib
└── projects/
    ├── preamble-report.tex
    ├── shared_src/
    └── THOMSON_project/
        ├── AGENTS.md
        ├── ARCHITECTURE.md
        ├── README.md
        ├── THOMSON_project.code-workspace
        ├── latexmkrc
        ├── main.tex
        ├── preamble-report.tex -> ../preamble-report.tex
        └── profiles/
            ├── all.inc
            └── lpwa.inc
```

Git commands run in this directory operate on the parent `projects`
repository. The bibliography is versioned separately under the parent `TeX`
repository layout.

## Document entry point

`main.tex` is the only root document for this project. It is responsible for:

1. selecting the document class;
2. defining `\srcpath` as `../shared_src`;
3. loading the common preamble through the local symbolic link;
4. creating the title and table of contents;
5. selecting one document profile;
6. loading the shared bibliography.

Keep project-root concerns in `main.tex`; keep content selection in profiles.

## Profiles

Files under `profiles/` are ordinary LaTeX fragments with the `.inc`
extension. They contain the parts, chapters, and `\input` commands that make up
a particular build.

Current profiles:

- `profiles/all.inc`: the complete THOMSON document structure.
- `profiles/lpwa.inc`: the smaller LPWA/application-focused build.

The active profile is currently selected in `main.tex` with:

```latex
\input{profiles/lpwa.inc}
```

To change builds, comment the current selection and enable another profile.
Only one profile should normally be active.

## Shared scientific sources

Reusable document-body fragments live under `../shared_src`. The present
top-level subject areas are:

```text
shared_src/
├── NR_Volkov/
├── classical_motion/
├── coherent_thomson/
├── elm_el_sc/
└── elm_field/
```

Sources are kept here even if only one project uses them today, because they
may be reused later. These files should normally contain document-body
material rather than a document class, preamble, or `document` environment.

Profiles access them through `\srcpath`, for example:

```latex
\input{\srcpath/coherent_thomson/lpwa-lg-circular_motion.tex}
```

## Common preamble

The common preamble is `../preamble-report.tex`. The local
`preamble-report.tex` is a symbolic link to that file, allowing `main.tex` to
load it simply with:

```latex
\input{preamble-report.tex}
```

Because this preamble can be used by sibling projects, changes to it should be
checked for cross-project consequences.

### Document conventions

The common preamble establishes several conventions that affect every source
loaded after it:

- The document language is English (`babel`) and the font encoding is T1.
- KP Fonts provide the main document fonts; Latin Modern and Euler Fraktur are
  also available.
- Equations are numbered within sections.
- Chapter numbers include the part number, for example `II.3`. Hyperlink
  chapter identifiers use the same part-qualified form.
- The table of contents has enlarged number and page-number widths to
  accommodate part-qualified chapter and section numbers.
- The bibliography is added to the table of contents at chapter level, while
  the table of contents itself is not listed there.
- Appendix headings are integrated with the table of contents.
- Paragraph indentation begins immediately after section headings.
- Empty verso pages are kept visually empty.
- The page is A4 with a configured text area of 7.5 by 10.5 inches.

The preamble provides the usual AMS mathematics and theorem facilities, bold
and slashed mathematical symbols, `empheq`, line-spacing controls, graphics,
floats, captions and subcaptions, professional and flexible tables, hyperlinks
and clever cross-references, TikZ including 3-D plotting, and breakable
`tcolorbox` environments. It also loads `import` and `subfiles`, although this
project currently assembles its profiles with `\input`.

The `ulem` package is loaded with its default options. Consequently, it changes
the usual behavior of `\emph` to underlining; this is a global preamble choice
to remember when writing shared material.

### Custom author command

`\chapterauthor{...}` places a small-caps author attribution beneath a chapter
heading with custom vertical spacing:

```latex
\chapter{Chapter title}
\chapterauthor{Author name}
```

### Content boxes

The preamble defines two semantic boxes for finished document content:

| Environment | Intended use | Visual style |
|---|---|---|
| `importantbox` | Important conclusions or principal results | Pale navy background, dark navy rounded frame, compact display-math spacing |
| `highlightbox` | Intermediate results or material deserving lighter emphasis | Pale pink background with a dark pink left border |

Both accept optional `tcolorbox` settings, so a particular instance can be
adjusted without defining a new environment:

```latex
\begin{importantbox}
The principal result, including displayed mathematics if needed.
\end{importantbox}

\begin{highlightbox}[title={Intermediate result}]
A useful step in the derivation.
\end{highlightbox}
```

`importantbox` reduces the normal space above and below `equation`, `align`,
and other display mathematics. This prevents a display at the beginning of a
box from looking like it is preceded by an empty line while retaining the
box's explicit top and bottom padding.

These boxes are part of the published-content style. They are distinct from
`warningbox`, which marks unfinished work.

### Draft annotations

The preamble includes semantic draft annotations:

| Command or environment | Meaning |
|---|---|
| `\missing{...}` | Known missing scientific content |
| `\todo{...}` | Concrete work still to perform |
| `\tocheck{...}` | Existing content requiring verification |
| `\question{...}` | Unresolved conceptual decision |
| `\draftnote{...}` | Contextual reminder |
| `warningbox` | Breakable block-level draft annotation |

Draft annotations are controlled globally with `\draftnotestrue` or
`\draftnotesfalse` in the common preamble. Inline annotations disappear when
draft notes are disabled. The `warningbox` environment is not currently
conditional and remains visible; remove or comment out such blocks for a clean
build.

The block environment takes a required title and an optional category color;
its default is `missingcolor`:

```latex
\begin{warningbox}{MISSING}
A larger description of content that has not yet been written.
\end{warningbox}

\begin{warningbox}[checkcolor]{CHECK}
Material that needs verification.
\end{warningbox}
```

The defined category colors are `missingcolor` (red), `todocolor` (orange),
`checkcolor` (blue), `questioncolor` (purple), and `notecolor` (gray). Labels
remain visible in the annotation text so that meaning does not depend only on
color.

## Bibliography

The project uses BibTeX with the `plain` bibliography style. `main.tex` loads:

```latex
\bibliography{../../bibliography/main}
```

which resolves to `TeX/bibliography/main.bib`.

## Build and editor integration

Build from `THOMSON_project/` with:

```sh
latexmk main.tex
```

The local `latexmkrc` selects pdfLaTeX. The VS Code multi-root workspace
exposes this project, `../shared_src`, and `../../bibliography` while keeping
this project's `main.tex` as the compilation root.

## Known issues

- Some currently selected source combinations produce multiply defined LaTeX
  labels.
- The build reports existing PDF-string and overfull/underfull box warnings.
- These warnings are independent of the profile architecture and should be
  addressed separately rather than during unrelated structural changes.
