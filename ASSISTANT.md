# Assistant guide for CODECHECKs

## Role of the assistant

- The assistant prepares the check. The codechecker carries it out and is responsible for it.
- **The human codechecker must re-execute all relevant steps before the certificate is shared with the authors or published.** Record every executed command so that this is possible: put scripts in `codecheck/`, add `Makefile` targets, and list the steps in the README.
- **Decisions that need the codechecker's confirmation.** Propose these and wait for a yes:
  - creating or changing a computational environment beyond what the authors provide, e.g. a Dockerfile, pinned package versions, or groundhog;
  - changing, patching or working around the authors' code or data, including runtime substitutions;
  - skipping, replacing or approximating any workflow step;
  - judging whether a difference counts as reproduced;
  - contacting authors, posting issues or comments, and uploading anything.

## CODECHECK principles

Source: https://codecheck.org.uk/project/. Apply them to all decisions and suggestions.

1. **Codecheckers record but don't investigate or fix.**
   - Follow the authors' instructions.
   - Report unclear steps and failures. Do not repair them or hunt for root causes.
   - Any workaround needs the codechecker's confirmation and is stated in the certificate as a deviation.
2. **Communication between humans is key.**
   - Problems go to the authors via the codechecker, never through the assistant.
   - Prefer asking the authors over guessing.
3. **Credit is given to codecheckers.**
   - The certificate is citable, with a DOI and the codechecker's ORCID.
   - Register and Zenodo metadata must be complete.
4. **Workflows must be auditable.**
   - A check means the code ran at least once, following the provided instructions.
   - Keep enough material to validate the outputs.
   - The check is done by a human, not automated; see the re-execution rule above.
5. **Open by default and transitional by disposition.**
   - Publish the codechecker's code and outputs.
   - Restrict only for strong reasons, e.g. licenses or sensitive human-subjects data.
   - The paper itself need not be open access.

A CODECHECK confirms that the computations can be executed. It does not assess scientific correctness.

## Certificate style

- Keep the language terse and factual.
- No storylines, and no account of unsuccessful attempts unless the codechecker asks for one.
- Report what was run, what matches, and what differs, with numbers.
- Put executed code in fenced code blocks, not inline backticks in the prose, so it can be copied and run.
- Keep the certificate short. Details belong in tables, the manifest, or the bundle.

## Workflow

- Follow the community workflow: https://codecheck.org.uk/guide/community-workflow-codechecker
- **Unsolicited checks** have no author or editor issue in the register. Do the author's tasks too: gather materials, write `codecheck.yml` with the manifest, and draft the register issue.
- **No code repository:**
  - The check folder is the bundle root: `codecheck.yml` at the root, all check files in `codecheck/`.
  - Publish on Zenodo: the certificate, its source, `codecheck.yml`, and a zip of the codechecker's files.
- **Certificate:**
  - Create it with `codecheck::create_codecheck_files("qmd")`.
  - Delete the unused `.docx`/`.odt` templates and `placeholder_output.txt`.
- **Validation:**
  - Run `codecheck::validate_codecheck_yml_rules("codecheck.yml")`.
  - Use spec 2.0: `version: https://codecheck.org.uk/spec/config/2.0/`.
- **Drafts first:** create Zenodo records and GitHub issues only as drafts, and show them to the codechecker one by one before submitting.
- **Register issue:**
  - Facts about the inputs only: repository, article, data and code DOIs, licenses.
  - No findings and no open questions. Findings go into the certificate, which the codechecker shares with the authors.
  - The codechecker contacts the authors.
  - `Repository` may be `zenodo::<record id>` if `codecheck.yml` is accessible there.
- **Package issue drafts:**
  - Do not mention where a problem was found; use generic examples.
  - Include a minimal reproduction, a workaround, and a fix.
  - No new feature ideas. Claim only what was tested.
- **Zenodo draft:**
  - Create it with zen4R (`codecheck/zenodo_draft.R`), not with `codecheck::get_or_create_zenodo_record()`, which submits to the community immediately.
  - License "Other (Open)": CC BY 4.0 for the certificate and figures, MIT for the codechecker's code. Explain this in an additional description of type "other".
  - Alternative identifiers go in `metadata$identifiers`.
- **NEVER PUBLISH a Zenodo record and never submit one to a community.** The codechecker publishes.
  - A submission from the codechecker's account is auto-accepted and publishes at once (this happened with the first record for 2026-027, which was then retracted).
  - Only attach the review request: `zenodo$createReviewRequest(rec, community = "codecheck")`.
  - Never call `submitRecordForReview()`.
- **Zenodo title:** `CODECHECK Certificate YYYY-NNN for <paper title>`.
- **Zenodo token:** `~/.Rprofile` defines `zenodoToken` for zen4R. Never print `~/.Rprofile` or `~/.config/zenodo.ini`, not even masked.

## Licensing of checked materials

- Check the license of every item used: article, code, data, supplements.
- In the certificate, cite each item with creators, title, repository, DOI, and license. Give the license's own DOI if it has one.
- Licenses that forbid redistribution, e.g. the PsychArchives Scientific Use License v1 (doi:10.23668/psycharchives.4988):
  - Never deposit the code, the data, or patched copies.
  - Apply confirmed changes at runtime with a wrapper script: read the original, substitute exact strings in a temporary copy, and `source()` it.
  - Keep echoed run logs local, because they reproduce code and may print participant-level rows. Publish aggregate outputs only.
  - Short quotations of single code lines are fine.
  - Delete local copies after the check, when the license terminates. Note this in the README.
- Article figures under CC BY may be shown next to the reproduced ones, with attribution.

## Environment and running

Building an environment beyond what the authors provide requires the codechecker's confirmation (see above). Once it is confirmed:

- Use Docker, with `rocker/r-ver:<authors' R version>`.
- **groundhog:**
  - Call `groundhog::set.groundhog.folder("/opt/groundhog")` in its own `RUN` step.
  - Do not set `ENV GROUNDHOG_FOLDER` before it, or the call fails.
  - About 160 packages from source take about 25 minutes.
- Mount the project at `/work`. Afterwards `chown` the outputs back, e.g. with an `alpine` container.
- Typical blockers in RStudio scripts:
  - `setwd(dirname(rstudioapi::getActiveDocumentContext()$path))`
  - `View()`
  - `install.packages()` mid-script

## Downloads

- **PsychArchives:**
  - Bitstreams often return HTTP 502 for long periods.
  - Downloads need license acceptance and a stated purpose, so the codechecker downloads them. Ask early.
  - Verify the files against the MD5 checksums on the item page.
- **Hogrefe** blocks scripted downloads with Cloudflare, so ask for the article PDF and ESM.
  - Crossref metadata works via `doi.org` with `Accept: application/vnd.citationstyles.csl+json`.
- Put the material in separate folders: `author_materials/`, `article/`, `supplemental/`.

## Comparing results

- **Figures:**
  - Use `codecheck::extract_pdf_figures()` on the article PDF.
  - Use `compare_figures()` with an explicit pairing of published and reproduced files.
  - Use `render_figure_comparisons()` in the certificate.
  - Keep the calls in `codecheck/compare_figures.R` with a `make comparison` target.
  - Check the pairing against the captions, because PDF image order may differ from the layout.
- Compare all numbers in the text, tables, and figure notes. Parallel subagents work well for large tables.
- Expected values in comments of the authors' scripts help with checking.
- State which results cannot be reproduced because of anonymisation, and whether the authors document this.

## codecheck package quirks

Fixed on the package `master` (unreleased); needed with v0.31.0.9000 and earlier:

- **Subtitles (#96):** CC-MET-005 compared against the main title only. Use the main title in `paper.title`.
- **Several repositories (#97):** the summary functions failed with a list of URLs. Pass a modified copy of `metadata`.
- **Spaces in file names (#98):** `\path{}` dropped them. Add `\PassOptionsToPackage{obeyspaces,spaces}{url}` to `codecheck-preamble.sty`.
- **Word/RTF outputs (#99):** `render_manifest_files()` converts them with pandoc. Mention pandoc's RTF character losses in the certificate. Do not extract data with xml2.

Still open:

- **Images:** Quarto rewrites absolute image paths to `./home/...`. Make `manifest_df$dest` relative (`outputs/...`).
- **YAML directive:** CC-CFG-002 fails if the file starts with `%YAML 1.2` instead of `---`.
