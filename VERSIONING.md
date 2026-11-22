# Versioning and Release Policy

This report is a methodology that licensees, auditors and researchers rely on to produce and check reported numbers. Every published version therefore stays available, unchanged, at its own permanent address, and every change between versions is recorded with its effect on results.

## Version Identifiers

Versions are named `CLEER-Text-MMYY` after the month of release, for example `CLEER-Text-0926` and `CLEER-Text-1226`. The companion Closed AI Emissions Calculation (CAIEC) follows the same policy and release calendar. Each version has

- a git tag and GitHub Release with the same name, with the distributed PDF attached
- a DOI minted by Zenodo from that release (the Zenodo concept DOI always resolves to the latest version)
- a permanent URL, `https://reports.sustainableaigroup.com/CLEER-Tech-Report/MMYY/`
- an entry in `versions.json`, which drives the version switcher and the "superseded" banner on every page

`https://reports.sustainableaigroup.com/CLEER-Tech-Report/` always shows the latest version. Citations should use the permanent URL of the version, never the bare address.

A correction that changes a number and cannot wait for the next scheduled release ships as a point release, `CLEER-Text-MMYY.1`, with its own tag, release, DOI and changelog entry. Typos and broken links wait for the next release.

## What Never Changes

Once a version is released it is not edited. Archived versions live in `archive/MMYY/`, rendered from the release commit, with a `SHA256SUMS` file that the deploy workflow checks on every build. The build fails if any archived file has changed. `archive/0926/PROVENANCE.txt` records the two edits made when the first version was frozen, before this policy existed, and the correction of the reference list on 4 October 2026. CLEER-Text-0926 was also tagged before its archive existed, so its Zenodo deposit contains the source only; from 1226 on, the deposit includes the frozen pages and PDF.

`main` deploys to the live site on every push. Release work is done on a `release/MMYY` branch and merged on release day.

## Change Records

Every change between versions gets an identifier, `CMMYY-NN` (for example `C1226-04`), and one of four types.

| Type | Meaning | Badge in text |
|---|---|---|
| Method | Changes how a quantity is calculated, or the boundary | Yes |
| Data | Updates a parameter value from new evidence | Yes |
| Clarification | Rewords or expands text with no numerical effect | No |
| Editorial | Typos, links, formatting | No |

Each change record states the section, the type, the trigger (the review, disclosure or study that prompted it), the before and after values, and the effect on results. The effect is reported as the change in per-token energy factors (input and output, by model) and as the percentage change in total kgCO₂e on the fixed reference inventory used by the Closed AI Emissions Calculation, so that effects are comparable across releases. Records also say whether the change is likely to be significant enough to prompt recalculation of previously reported periods.

The full record is the Version History annex (Annex B) in the report, written in `_changelog.qmd`, so the PDF carries it. GitHub release notes summarize it and link to the line-by-line comparison between tags (`/compare/CLEER-Text-0926...CLEER-Text-1226`).

Method and Data changes are also marked where they occur in the text with a `.change` block, which renders as a "Changed in MMYY" badge in the web version (with a link to the same section in the previous version) and as a marked note in the PDF. Badges only cover changes since the previous version; older history lives in Annex F.

```markdown
::: {.change #C1226-04 type="data" from="0926"}
New benchmarking round on B300 nodes added to the donor pool. Changes output-token energy for the frontier tier by X%.
:::
```

Section anchors are fixed with explicit IDs (`## 2.1 Section Title {#section-title}`) so that links between versions survive renamed or renumbered headings. Never change an existing ID.

## Compatibility With CLEER Factor Releases

Each version records the CLEER factor release it describes (`cleer_factor_release` in `versions.json`, and the compatibility table in the version history annex). Licensees keep factor releases they have received, so they need to know which methodology version produced which factors.

## Release Checklist

1. Create `release/MMYY` from `main`. Make all changes there.
2. Log every change in `_changelog.qmd` and add `.change` blocks for Method and Data changes. Compute reference-inventory effects.
3. Update the version block at the top of `index.qmd` (version, date, PDF name, permalink, citation month, typst output file); `version`, `publication_date` and the `isIdenticalTo` URL in `.zenodo.json`; `version` and dates in `CITATION.cff`; and add the new entry to `versions.json` with `latest` pointing to it (`doi` stays `null` for now).
4. Open a pull request into `main`, review the preview, and merge on release day. The new version goes live at `/CLEER-Tech-Report/` and `/CLEER-Tech-Report/MMYY/`, and the previous version starts showing the superseded banner.
5. Download the PDF from the live site and run `scripts/freeze-version.sh MMYY path/to/CLEER-Text-MMYY.pdf` on `main`. Add the version to the table in `README.md`. Merge through a pull request.
6. Publish a GitHub Release with the tag `CLEER-Text-MMYY` (check the spelling: it must match the version exactly) on that merge commit, with the PDF attached. The tagged commit then contains the frozen pages and PDF, so the Zenodo deposit does too. Zenodo mints the DOI.
7. Add the DOI to `versions.json`, `CITATION.cff` and `README.md`. Every page picks it up from `versions.json`; nothing in the archive changes.
8. Check that both permanent URLs and PDFs resolve, the DOI resolves, and the previous version shows the superseded banner.
