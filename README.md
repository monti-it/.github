# .github
Org-wide defaults for monti-it: community health files, reusable security workflows

## Usage

Three reusable [Trivy](https://trivy.dev) workflows gate a repo's CI on security findings. Each one:

- fails the job on a finding at or above `severity` (default `CRITICAL,HIGH`); pass `severity: CRITICAL` to let HIGH findings through;
- ignores vulnerabilities that have no fixed version yet (dependency and container scans);
- honours a `.trivyignore` at the root of the calling repo (one finding ID per line, e.g. `CVE-2024-12345`);
- requests only `contents: read` and takes no secrets.

Call them from a job in your own workflow, pinned to the major tag `@v1`:

### Dependency scan

Scans lockfiles (`trivy fs`).

```yaml
jobs:
  dependency-scan:
    uses: monti-it/.github/.github/workflows/dependency-scan.yml@v1
    with:
      scan-ref: .              # optional, default "."
      severity: CRITICAL,HIGH  # optional, default "CRITICAL,HIGH"
```

### Container scan

Builds the image with `docker build` (on the runner only, never pushed), then scans it (`trivy image`).

```yaml
jobs:
  container-scan:
    uses: monti-it/.github/.github/workflows/container-scan.yml@v1
    with:
      context: ./api           # optional, default "."
      dockerfile: Dockerfile   # optional, default "Dockerfile", relative to context
      target: prod             # optional, default none (last stage)
      severity: CRITICAL,HIGH  # optional, default "CRITICAL,HIGH"
```

### IaC scan

Scans Dockerfiles, Kubernetes manifests, Terraform, Helm charts... for misconfigurations (`trivy config`).

```yaml
jobs:
  iac-scan:
    uses: monti-it/.github/.github/workflows/iac-scan.yml@v1
    with:
      scan-ref: .              # optional, default "."
      severity: CRITICAL,HIGH  # optional, default "CRITICAL,HIGH"
```

### Versioning

Callers pin to a major tag (`@v1`), which moves to the latest `v1.x.y` release. A breaking input change (an input renamed or removed, a default made stricter, a new required input) means a new major version, `v2`; `v1` keeps working until callers move.

The `Self-test` workflow calls all three through their local path against the fixtures in `tests/fixtures/` on every pull request.
