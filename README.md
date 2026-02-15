# gait-ci-action

GitHub Action for deterministic Gait CI checks.

This action downloads a released `gait` binary, verifies checksums, runs either `gait regress run` or `gait policy test`, and optionally uploads generated artifacts.

## Inputs

- `version`: Gait release tag (for example `v2.7.0`) or `latest`.
- `workdir`: Working directory to execute command in.
- `command`: `regress` or `policy-test`.
- `args`: CLI arguments passed to the selected command.
- `upload_artifacts`: `true`/`false` for artifact upload.
- `artifact_name`: Artifact name when upload is enabled.

## Outputs

- `exit_code`
- `summary_path`
- `artifact_path`

## Usage

```yaml
name: gait-regress
on:
  pull_request:

jobs:
  regress:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Gait regress
        id: gait
        uses: davidahmann/gait-ci-action@v1
        with:
          version: latest
          command: regress
          args: >-
            --json
            --output ./gait-ci/regress_core_result.json
            --junit ./gait-ci/junit.xml

      - name: Print summary path
        run: echo "summary=${{ steps.gait.outputs.summary_path }}"
```

Policy test example:

```yaml
- name: Policy test
  uses: davidahmann/gait-ci-action@v1
  with:
    command: policy-test
    args: >-
      ./policy/base.yaml
      ./policy/intents/intent_delete.json
```

## Artifact Contract

When `upload_artifacts=true`, the action uploads the `gait-ci/` directory from the selected `workdir`.

Typical files include:

- `gait-ci/summary.txt`
- `gait-ci/regress_result.json` and `gait-ci/junit.xml` for `command=regress`
- `gait-ci/policy_test_result.json` for `command=policy-test`

## Release and Tagging

For stable consumers and Marketplace listing:

- create immutable release tags like `v1.0.0`
- keep major tag `v1` updated to latest compatible `v1.x`

## License

Apache-2.0
