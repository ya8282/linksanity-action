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
    version: "0.2.0"
    python-version: "3.12"
    check-anchors: "true"
    skip-urls: "https://example.com/flaky-endpoint *.internal.example.com"
    output: linkcheck-results.json
    baseline: .linksanity-baseline.json
    args: "--check-images"
    upload-results: "true"
```

## Inputs

| Name             | Description                                                             | Required | Default                    |
| ----------------- | ------------------------------------------------------------------------ | -------- | --------------------------- |
| `paths`           | Space-separated paths to scan for links.                                 | false    | `.`                         |
| `version`          | Version of linksanity to install from PyPI, pinned to the release this action is tested against. Pass `""` to track latest instead — see [Versioning](#versioning) for what that trusts. | false    | `0.2.0`                     |
| `python-version`   | Python version to set up for running linksanity.                         | false    | `3.12`                      |
| `check-anchors`    | Whether to check in-page anchor links (`"true"`/`"false"`).              | false    | `false`                     |
| `skip-urls`        | Space-separated URL patterns to skip.                                    | false    | `""`                        |
| `output`           | Path to write the JSON scan results to.                                  | false    | `linkcheck-results.json`    |
| `baseline`         | Path to a previous JSON results file; only links that are newly broken relative to it fail the job. | false    | `""`                        |
| `args`             | Extra raw arguments passed through to `linksanity scan` as-is.           | false    | `""`                        |
| `upload-results`   | Whether to upload the scan results file as a workflow artifact (`"true"`/`"false"`). | false    | `true`                      |
| `artifact-name`    | Name of the workflow artifact to upload the scan results as. Set a distinct value per job when a workflow calls this action more than once. | false    | `linksanity-results`        |
| `browser`          | Whether to install the Playwright browser extra, required for `--js-domains` (`"true"`/`"false"`). Adds a Chromium download to the run. | false    | `false`                     |
| `fail-on-redirect-loop` | Whether a redirect loop (`too_many_redirects`) fails the job and is counted in `broken-count` (`"true"`/`"false"`). When `"false"` it is still surfaced as a `::warning::` instead of being silently dropped. | false    | `true`                      |

`--js-domains` passed via `args` requires `browser: true`; otherwise the action fails fast with a clear error instead of installing Playwright unconditionally on every run.

`baseline` must contain no whitespace and must not start with `-` (the value is passed unquoted, same constraint as `paths`); the action fails with an error rather than proceeding. If set and the named file does not exist, the action also fails — it does not silently fall back to checking everything.

### Versioning

This action pins `version` to a known-good linksanity release by default, so upgrading the action (via its tag) is what upgrades linksanity; the CLI is not left to float on its own. Pass `version: ""` to track the latest linksanity release instead; a CLI change (e.g. a renamed or removed flag) can then break the action without warning. Pass an explicit `version: "X.Y.Z"` to pin to a different release.

This action's exit-code handling assumes the CLI's exit-code contract (0 and 1 are link-status verdicts, 2 and above are operational failures), verified for the pinned default only; linksanity 0.4.0+ also uses 3 for a failed --github-issue report; 0.3.0 and earlier, including the pinned default (0.2.0), report a reporter failure as 1 instead. Tracking latest means trusting a version this action has not verified against either contract.

## Outputs

| Name            | Description                                                    |
| ---------------- | ---------------------------------------------------------------- |
| `broken-count`    | Number of links found by the scan with a failing status (broken, error, or too_many_redirects — a redirect loop counts as a failure even though it isn't literally "broken"). |
| `results-file`    | Path to the JSON results file written by the scan.               |

## Self-test

This repo's own `.github/workflows/self-test.yml` exercises the action
against two isolated fixture sets under `test-docs/`: `test-docs/broken/`
(one guaranteed-404 link, expected to fail) and `test-docs/clean/` (only
valid relative links, expected to pass).
