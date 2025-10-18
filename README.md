<<<<<<< HEAD
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
=======
# AppPolicy Copilot — GitHub Action

Run the AppPolicy scanner in CI and fail PRs on policy risks. Supports both **community rules** (YAML file) and **signed Pro rule packs**.

## Inputs

| Input             | Required | Default | Description |
|------------------ |----------|---------|-------------|
| `ios_project`     | no       | `""`    | Path to your iOS project root (if scanning iOS). |
| `android_project` | no       | `""`    | Path to your Android project root (if scanning Android). |
| `rules_path`      | either this **or** `rules_pack_url` | `""` | Path to a **community** YAML rules file in your repo. |
| `rules_pack_url`  | either this **or** `rules_path` | `""` | HTTPS URL or local file path to a **signed Pro rules pack** (`.tar.gz`). |
| `public_key_hex`  | when using `rules_pack_url`        | `""` | Trusted **Ed25519 public key (hex)** for verifying rule packs. |
| `fail_on`         | no       | `blocking` | Threshold to fail the job (`blocking`, `advisory`, `none`). |

> The Action installs `apppolicy-scanner[pro]` from PyPI (>= **1.0.3**).

---

## Usage — Community rules
>>>>>>> 584adae (Update README)

```yaml
name: AppPolicy (community)
on: [pull_request]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
<<<<<<< HEAD
=======

      - name: Create community rules (inline)
        run: |
          mkdir -p rules
          cat > rules/community.yaml <<'RULES'
          version: "0.1-community"
          rules:
            - id: android.target_sdk.minimum
              platform: android
              severity: blocking
              when: { all: [ { android.targetsdk.lt_policy_min: 34 } ] }
              because:
                source: google.play.policies
                section: "Target API level"
                url: "https://developer.android.com/google/play/requirements/target-sdk"
              then:
                policy_min: 34
                require: []
          RULES

>>>>>>> 584adae (Update README)
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

<<<<<<< HEAD
### B) Pro rules pack (signed, verified)

Add your **trusted public key (hex)** as a repo/org secret (e.g., `APPPOLICY_PUBKEY_HEX`).
=======
---

## Usage — Pro rule pack (signed)
>>>>>>> 584adae (Update README)

```yaml
name: AppPolicy (pro pack)
on: [pull_request]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
<<<<<<< HEAD
=======

      # Option A: pack already in workspace (rules-pack.tar.gz)
      # Option B: reference a direct HTTPS URL below

>>>>>>> 584adae (Update README)
      - uses: <ORG>/apppolicy-sdks@v1
        with:
          ios_project: ios/
          android_project: android/
<<<<<<< HEAD
          rules_pack_url: https://private.example.com/rules-pack-2025.10.12.tar.gz
          public_key_hex: ${{ secrets.APPPOLICY_PUBKEY_HEX }}
          fail_on: blocking
```

## Notes
- Installs `apppolicy-scanner[pro]` from PyPI and runs the CLI.
- Uploads `report.json` as a job artifact.
- If PyPI is not yet published, temporarily replace the install step with a git URL in your fork.
=======
          rules_pack_url: rules-pack.tar.gz       # or https://...
          public_key_hex: ${{ secrets.APPPOLICY_PUBKEY_HEX }}
          fail_on: blocking

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

**Secrets**
- `APPPOLICY_PUBKEY_HEX` — trusted public key (hex) for verifying signed rule packs.
- For private release downloads, use a fine-grained PAT as `RULES_DOWNLOAD_TOKEN` in an earlier download step.

---

## Notes

- **Fail threshold:**  
  - `blocking` → fail if any blocking finding  
  - `advisory` → fail if any advisory or worse  
  - `none` → never fail (CI stays green)

- **Rendering HTML:**  
  You can always render locally:
  ```bash
  apppolicy html --report report.json --out report.html
  ```

- **Troubleshooting:**  
  - Signature failures → check `APPPOLICY_PUBKEY_HEX` matches the key that signed the pack.  
  - 404 asset → verify tag/asset names and token permissions.  
  - Empty findings → confirm project paths and rule inputs.
>>>>>>> 584adae (Update README)
