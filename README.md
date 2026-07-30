# linksanity-action

A GitHub composite action that scans documentation for broken links with
[linksanity](https://pypi.org/project/linksanity/) and fails the job if any
are found.

Just want the CLI? See [ya8282/linksanity](https://github.com/ya8282/linksanity) for the linksanity CLI itself — this repo is just the GitHub Action wrapper around it.

## Minimal usage

```yaml
- uses: ya8282/linksanity-action@v1
  with:
    paths: docs/
```

## Full usage

```yaml
- uses: ya8282/linksanity-action@v1
  with:
    paths: docs/ README.md
    version: "0.1.0"
    python-version: "3.12"
    check-anchors: "true"
    skip-urls: "https://example.com/flaky-endpoint *.internal.example.com"
    output: linkcheck-results.json
    args: "--check-images"
    upload-results: "true"
```

## Inputs

| Name             | Description                                                             | Required | Default                    |
| ----------------- | ------------------------------------------------------------------------ | -------- | --------------------------- |
| `paths`           | Space-separated paths to scan for links.                                 | false    | `.`                         |
| `version`          | Version of linksanity to install from PyPI. Empty string installs the latest release. | false    | `""`                        |
| `python-version`   | Python version to set up for running linksanity.                         | false    | `3.12`                      |
| `check-anchors`    | Whether to check in-page anchor links (`"true"`/`"false"`).              | false    | `false`                     |
| `skip-urls`        | Space-separated URL patterns to skip.                                    | false    | `""`                        |
| `output`           | Path to write the JSON scan results to.                                  | false    | `linkcheck-results.json`    |
| `args`             | Extra raw arguments passed through to `linksanity scan` as-is.           | false    | `""`                        |
| `upload-results`   | Whether to upload the scan results file as a workflow artifact (`"true"`/`"false"`). | false    | `true`                      |

## Outputs

| Name            | Description                                                    |
| ---------------- | ---------------------------------------------------------------- |
| `broken-count`    | Number of links with status "broken" or "error" found by the scan. |
| `results-file`    | Path to the JSON results file written by the scan.               |

## Self-test

This repo's own `.github/workflows/self-test.yml` exercises the action
against two isolated fixture sets under `test-docs/`: `test-docs/broken/`
(one guaranteed-404 link, expected to fail) and `test-docs/clean/` (only
valid relative links, expected to pass).
