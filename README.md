# AppPolicy SDKs & CI

This repo contains the **GitHub Action** and helper scripts to run AppPolicy scanning in CI.

## Usage (GitHub Actions)
```yaml
name: AppPolicy
on: [pull_request]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: apppolicy/copilot-action@v1
        with:
          ios_project: ios/
          android_project: android/
          rules_path: rules/community.yaml
          fail_on: blocking
```

> Replace `rules_path` with the path/URL to your rule pack if you are a Pro customer.
