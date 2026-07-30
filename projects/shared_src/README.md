# Shared LaTeX sources

Reusable LaTeX sections shared by sibling document projects. This directory is
part of the parent `projects` Git monorepository.

Projects that use these files are kept next to this directory, for example:

```text
projects/
├── THOMSON_project/
└── shared_src/
```

The consuming project selects the required sections with `\\input` commands.
