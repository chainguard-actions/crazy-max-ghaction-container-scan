<!-- markdownlint-disable -->

# Hardening Report: crazy-max--ghaction-container-scan/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **crazy-max--ghaction-container-scan/v4.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ steps.scan.outputs.json }}` and `${{ steps.scan.outputs.sarif }}` expressions (sub-rule a: direct expression interpolation). `steps.*.outputs.*` is a workflow-controllable context that flows through YAML template substitution before the shell processes it. An attacker who can influence the scan output path could inject shell metacharacters. Affected steps:
- ci.yml: `run: cat ${{ steps.scan.outputs.json }}` (image job, tarball job)
- ci.yml: `run: cat ${{ steps.scan.outputs.sarif }}` (sarif job)
- scan.yml: `run: cat ${{ steps.scan.outputs.json }}`

Locations:

- `.github/workflows/ci.yml:46`
- `.github/workflows/ci.yml:65`
- `.github/workflows/ci.yml:86`
- `.github/workflows/scan.yml:34`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable version tags instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved.

ci.yml: `actions/checkout@v6`, `docker/setup-buildx-action@v3`, `docker/build-push-action@v6`, `docker/metadata-action@v5`, `docker/setup-qemu-action@v3`
labels.yml: `actions/checkout@v6`, `crazy-max/ghaction-github-labeler@v5`
scan.yml: `actions/checkout@v6`
test.yml: `actions/checkout@v6`, `docker/bake-action@v6`, `codecov/codecov-action@v5`
trivy-releases-json.yml: `actions/checkout@v6`, `actions/download-artifact@v7`
validate.yml: `actions/checkout@v6`, `docker/bake-action/subaction/list-targets@v6`, `docker/bake-action@v6`

Locations:

- `.github/workflows/ci.yml:37`
- `.github/workflows/ci.yml:53`
- `.github/workflows/ci.yml:56`
- `.github/workflows/ci.yml:134`
- `.github/workflows/ci.yml:138`
- `.github/workflows/ci.yml:140`
- `.github/workflows/ci.yml:144`
- `.github/workflows/labels.yml:34`
- `.github/workflows/labels.yml:37`
- `.github/workflows/scan.yml:24`
- `.github/workflows/test.yml:25`
- `.github/workflows/test.yml:28`
- `.github/workflows/test.yml:33`
- `.github/workflows/trivy-releases-json.yml:39`
- `.github/workflows/trivy-releases-json.yml:42`
- `.github/workflows/validate.yml:27`
- `.github/workflows/validate.yml:31`
- `.github/workflows/validate.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in ci.yml (3 locations: image/JSON result, tarball/JSON result, sarif/SARIF result) and scan.yml (1 location: JSON result) by moving ${{ steps.scan.outputs.json }} and ${{ steps.scan.outputs.sarif }} expressions into step-level env: blocks and referencing them as $SCAN_JSON/$SCAN_SARIF in the shell. Fixed unpinned-uses across all 6 workflow files (ci.yml, labels.yml, scan.yml, test.yml, trivy-releases-json.yml, validate.yml) by replacing all mutable tag references with full 40-character commit SHAs resolved via lookup_action_sha, preserving the original tag as an inline comment.

