# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A LaTeX `book`-class document ("The Mott problem") on time-dependent perturbation theory for a projectile scattering off one or two model atoms. `main.tex` is the entry point; all actual content lives outside this directory, in the sibling `shared_src/mott_problem/` tree, and is pulled in through a single "profile" include.

This directory is part of a larger monorepo (`~/Dropbox/work/TeX`) that is versioned as a whole — `git` commands run here resolve to the repo root two levels up, not to a repo scoped to this project.

## Build

```bash
latexmk -pdf -interaction=nonstopmode -halt-on-error main.tex   # build main.pdf
latexmk -C                                                      # clean build artifacts
```

Run from this directory. Bibliography is `../../bibliography/main.bib` (BibTeX, `plain` style), rebuilt automatically by `latexmk`. There is no separate lint/test step; correctness is "it compiles with no `undefined reference` / `Multiply defined` warnings in `main.log`" — grep for those after a build:

```bash
grep -n "undefined\|Multiply defined" main.log
```

`preamble-report.tex` here is a symlink to `../preamble-report.tex`, shared by all sibling projects — edit the target, not the link, if a package/macro change should apply repo-wide. Project-only macro changes belong in `main.tex` itself, after the `\input{preamble-report.tex}` line.

## Architecture

**Content is data, structure is code.** Every physics/derivation file lives in `../shared_src/mott_problem/` (a sibling directory, referenced via `\srcpath`) as a bare fragment — usually starting straight from `\section{...}`, with no `\documentclass` or preamble of its own. `main.tex` never includes these fragments directly; it includes `profiles/mott.inc`, which is the actual table of contents: an ordered sequence of `\part`/`\chapter` headings each followed by one or more `\input{\srcpath/mott_problem/...}` lines. To change what the document contains or in what order, edit `profiles/mott.inc`; to change the physics, edit the corresponding file under `shared_src/mott_problem/`.

`profiles/` currently has one active profile (`mott.inc`); `main.tex` has a commented-out `\input{profiles/all.inc}` line as a hook for an alternate profile that doesn't exist yet — leave it as-is unless asked to add one.

The document is organized into four parts, mirrored by subdirectories under `shared_src/mott_problem/`, each part's chapter numbering reset to 1 via `\setcounter{chapter}{0}` in `mott.inc`:

- **`part1_general/`** — Part I, general TDPT results (Dyson series, first/second-order transition amplitudes, the projectile's initial wave packet). Everything here is independent of how the atom is modeled and is referenced by, not duplicated in, the later parts. `\label{sec:proj-wavepacket}` (in `projectile_wavepacket.tex`) and the boxed results `eq:proj-CP` / `eq:proj-CP-approx` are the canonical wave-packet references used throughout Parts II–III — if a Part II/III file needs $C(P)$, it should `\eqref{eq:proj-CP}`, never restate the formula under its own label (a restated-with-the-same-label copy is a hard "multiply defined" error waiting for the next build; a restated-with-a-new-label copy is just a maintenance trap).
- **`part2_continuous_atoms/`** — Part II, the atom modeled as a 1D harmonic oscillator (continuous position). Oscillator properties/matrix elements, then the one-absorber and two-absorber systems (description → perturbative → numerical, as separate files combined under one `\chapter` in `mott.inc`).
- **`part3_discrete_atoms/`** — Part III, the atom modeled as a discrete few-level system, in active development. `two-level-atom_description.tex` sets up the two-level free Hamiltonian and the interaction Hamiltonian's two blocks: a diagonal elastic term $W_n(a)$ (Gaussian coupling, same in both internal states by default, with a free momentum-selectivity parameter left for numerical choice) and an off-diagonal excitation/de-excitation term $V(a)$ (Gaussian coupling modulated to favor a chosen momentum transfer, so the atom's energy gap shows up as a preferred momentum kick rather than through Hermite/Laguerre structure). `one-absorber-description.tex` and `one-absorber-perturbative.tex` are building the first-order one-absorber chapter on top of that; two-absorber content hasn't been started. Since Part III deliberately mirrors Part II's chapter shape (same generic titles: "description", "perturbative", "one-absorber system", "two-absorber system"), watch for label and content collisions between the two — see the labeling convention below.
- **`part4_appendix/`** — Part IV, `\appendix`: generic math identities and the numerically-stable (sinc-based) forms of the energy factors used in the Part I amplitude formulas.

Cross-references (`\ref`/`\eqref`) resolve globally across all `\input`-ed files regardless of directory, since it's one LaTeX document — a file's physical location only controls where it renders, not whether its labels are reachable. When relocating or splitting content, grep the whole `shared_src/mott_problem/` tree for a label before deleting/renaming it, since it may be `\eqref`'d from a file in a different part.

`shared_src/mott_problem/md_helpers/` holds Markdown specs (e.g. `perturbation_theory_implementation_spec.md`) for external numerical/Python work derived from this document's formulas — not part of the LaTeX build.

## Conventions in the LaTeX source

- Boxed/highlighted results use custom `tcolorbox` environments from `preamble-report.tex`: `importantbox` for a finished boxed result, `highlightbox` for an intermediate one, `warningbox`/`\todo`/`\missing`/`\tocheck`/`\question`/`\draftnote` for draft annotations (toggled off document-wide by flipping `\draftnotestrue`/`\draftnotesfalse` there).
- `\thechapter` is rendered as `\thepart.\arabic{chapter}` (e.g. `II.3`) — this is why `mott.inc` resets the chapter counter at the start of each `\part`.
- **Labels follow `type:model-topic`**: `sec:`/`eq:`/`part:` for the kind of thing, then a model tag — `gen`/`proj` for Part I (general TDPT / projectile wave packet), `osc` for Part II (oscillator), `tls` for Part III (two-level system), `app` for Part IV — then a short topic, e.g. `sec:tls-elastic`, `eq:osc-V-n1n2`. The model tag exists specifically because Part III mirrors Part II's structure, so terse names like `sec:one-absorber` would otherwise collide or silently drift apart (a `\ref` typo to a near-miss name doesn't error — LaTeX only catches exact duplicates — so it's easy to end up with a silently-broken cross-reference). Before adding a new label, `grep -rn "\\\\label{" ../../shared_src/mott_problem | sort` to check the namespace and follow the existing tag for whichever part you're in.
- Interaction-width and momentum symbols are reserved and should not be reused for something else: $\sigma_V$ is the interaction Gaussian's width (never bare $\sigma$, except as a generic placeholder in a general derivation before it's specialized), $\sigma_\alpha$ is the projectile wave packet's width, and $P_\alpha$ is the projectile's characteristic momentum (never $P_0$ — freed up precisely so model-specific momentum scales, e.g. Part III's coupling momenta, can use $P_0$-shaped names like $P_c$, $P_e$ without colliding).
