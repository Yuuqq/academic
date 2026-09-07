# Code Review: Yuuqq/academic

This review covers the `Yuuqq/academic` repository, which contains the source for a personal academic website built using Hugo and the Wowchemy Academic Resume theme.

## Findings

### CRITICAL
*(No critical findings identified. No highly sensitive data, keys, or passwords were found in the plain text configuration.)*

### HIGH

- **Asset Management & Repository Bloat**
  - **File(s)**: `static/uploads/CV_Qichang Ma_20230606_en.pdf`, `content/post/jupyter/index.ipynb` (plus generated `.png` assets in source)
  - **Impact**: Large binaries (PDFs, raw Jupyter outputs with inline images) are tracked directly in git. Over time, as CVs are updated or Jupyter outputs change, this causes repository size bloat, increasing clone times and making the repository less maintainable.
  - **Fix Suggestion**: Instead of tracking large binaries in git, use a file hosting service for PDFs (or Git LFS if versioning is necessary), and use standard markdown or stripped python scripts rather than full `.ipynb` files containing large serialized data structures. Alternatively, ensure `preview.png` and other large mockups are optimized or not tracked.

### MEDIUM

- **Outdated Dependencies & Go Version**
  - **File(s)**: `go.mod`
  - **Impact**: The repository enforces Go `1.15` and uses Hugo modules from early 2023 (`wowchemy/v5 v5.7.1...`). Go 1.15 is significantly outdated and lacks modern security/performance enhancements.
  - **Fix Suggestion**: Update `go.mod` to `go 1.19` (or newer, as supported by newer versions of Hugo and Wowchemy). Update the Wowchemy Hugo modules to the latest version via `hugo mod get -u ./...` or equivalent.

- **Unused/Default Template Code**
  - **File(s)**: `content/_index.md`, `content/post/jupyter/index.ipynb`, `content/post/writing-technical-content/`
  - **Impact**: The repository contains boilerplate default files from the Wowchemy template (e.g., example posts about writing technical content, commented-out sections in `_index.md`). This increases technical debt and can cause confusion about what is actual content versus what is dead code.
  - **Fix Suggestion**: Remove unused template files (e.g., `writing-technical-content`, `getting-started` posts) or mark them as `draft: true` if still needed for reference. Delete massive commented-out sections in `content/_index.md` to improve readability.

### LOW

- **Build Output Checked into Git**
  - **File(s)**: `hugo_output.log`
  - **Impact**: Build or debug logs should not be tracked in version control, as they change frequently and introduce noisy diffs.
  - **Fix Suggestion**: Add `*.log` to `.gitignore` and remove `hugo_output.log` from git tracking (`git rm --cached hugo_output.log`).

- **Whitespace in Paths**
  - **File(s)**: `content/publication/conference-paper/Self-Organization Performance.md`, `content/project/User Collaboration in Information Commons/index.md`
  - **Impact**: Whitespaces in file and folder names can cause issues with some scripts and CLI tools, as well as unpredictable behavior in generated URLs if Hugo's path management settings change.
  - **Fix Suggestion**: Rename files and folders to use kebab-case (e.g., `self-organization-performance.md`). The Hugo permalinks system or explicit `url` front-matter will handle the web routing.

- **Missing Developer Documentation**
  - **File(s)**: `README.md`
  - **Impact**: The README describes what the project is, but lacks developer instructions on how to install dependencies (Hugo Extended, Go) and run the local development server.
  - **Fix Suggestion**: Add a "Local Development" section to `README.md` with instructions: `hugo server -D`.

## Summary
The site architecture follows the standard Hugo Wowchemy pattern. Security posture is good as no sensitive secrets were committed. The main areas for improvement revolve around repository hygiene: cleaning up boilerplate content, untracking log files, addressing file paths, and keeping dependencies up to date.
