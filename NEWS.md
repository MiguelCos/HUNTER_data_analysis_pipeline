# HUNTER Data Analysis Pipeline News

## 2026-06-02

- Updated the HUNTER exploratory and inferential workflows to rely on `TermineR::annotate_neo_termini()` for TargetP2-derived processing annotations.
- Removed the manual `.targetp2` loading, joining, and post-processing logic from `HUNTER_inferential_analysis_refined.qmd`.
- Updated exploratory missingness plots to facet by the consolidated `processing_type` annotation rather than UniProt-only processing type.
- Documented the required workflow inputs and clarified that TargetP2 annotation is now provided by the current GitHub version of `MiguelCos/TermineR` when available for the selected organism.
