# AppPolicy Copilot — GitHub Action (v1)

Run the AppPolicy scanner in CI and fail PRs on policy risks.

## Inputs

| Input            | Required | Default    | Description |
|------------------|----------|------------|-------------|
| `ios_project`    | no       | `""`       | Path to your iOS project root (if scanning iOS). |
| `android_project`| no       | `""`       | Path to your Android project root (if scanning Android). |
| `rules_path`     | **either this or `rules_pack_url`** | `""` | Path to a **community** YAML rules file in your repo. |
| `rules_pack_url` | **either this or `rules_path`** | `""` | HTTPS URL to a **signed Pro rules pack** (`.tar.gz`). |
| `public_key_hex` | when using `rules_pack_url` | `""` | Trusted **Ed25519 public key (hex)** for verifying rule packs. |
| `fail_on`        | no       | `blocking` | Threshold to fail the job (`blocking`, `advisory`, `none`). |

> Provide **one** of `rules_path` or `rules_pack_url`.  
> With Pro packs, pass the trusted public key (`public_key_hex`).

## Usage

### A) Community rules (file in your repo)

```yaml
name: AppPolicy (community)
on: [pull_request]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: <ORG>/apppolicy-sdks@v1
        with:
          ios_project: ios/
          android_project: android/
          rules_path: rules/community.yaml
          fail_on: none

      - name: Render HTML report
        run: python -m apcop.cli html --report report.json --out report.html

      - name: Upload reports
        uses: actions/upload-artifact@v4
        with:
          name: apppolicy-report
          path: |
            report.json
            report.html
```

### B) Pro rules pack (signed, verified)

Add your **trusted public key (hex)** as a repo/org secret (e.g., `APPPOLICY_PUBKEY_HEX`).

```yaml
name: AppPolicy (pro pack)
on: [pull_request]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: <ORG>/apppolicy-sdks@v1
        with:
          ios_project: ios/
          android_project: android/
          rules_pack_url: https://private.example.com/rules-pack-2025.10.12.tar.gz
          public_key_hex: ${{ secrets.APPPOLICY_PUBKEY_HEX }}
          fail_on: blocking
```

## Notes
- Installs `apppolicy-scanner[pro]` from PyPI and runs the CLI.
- Uploads `report.json` as a job artifact.
- If PyPI is not yet published, temporarily replace the install step with a git URL in your fork.
