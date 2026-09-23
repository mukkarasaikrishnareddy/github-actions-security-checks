# GitHub Actions Security Checks

A sample **DevSecOps CI/CD security pipeline** built with GitHub Actions.

This project demonstrates how multiple automated security checks can be integrated into a CI/CD workflow to identify exposed secrets, vulnerable dependencies, and insecure coding patterns before changes are merged.

## Overview

The workflow runs security checks automatically on:

* Pushes to the `main` or `develop` branches
* Pull requests
* Manual workflow executions

The pipeline currently integrates three security tools:

| Tool         | Security Check         | Purpose                                                                     |
| ------------ | ---------------------- | --------------------------------------------------------------------------- |
| **Gitleaks** | Secret Scanning        | Detects accidentally committed secrets, credentials, API keys, and tokens   |
| **Trivy**    | Vulnerability Scanning | Detects known vulnerabilities in the repository filesystem and dependencies |
| **Semgrep**  | SAST                   | Detects insecure coding patterns and potential security weaknesses          |

## Security Pipeline

```text
                    Developer
                        │
                        ▼
                Git Push / Pull Request
                        │
                        ▼
              ┌─────────────────────┐
              │   GitHub Actions     │
              │  Security Pipeline   │
              └──────────┬──────────┘
                         │
            ┌────────────┼────────────┐
            │            │            │
            ▼            ▼            ▼
        Gitleaks       Trivy       Semgrep
     Secret Scanning  Vulnerability   SAST
                       Scanning
            │            │            │
            └────────────┼────────────┘
                         ▼
                 Security Results
                         │
                  ┌──────┴──────┐
                  │             │
                  ▼             ▼
                PASS           FAIL
```

## Tools

### 1. Gitleaks

[Gitleaks](https://github.com/gitleaks/gitleaks) scans the Git repository for potentially exposed secrets.

It can detect patterns such as:

* API keys
* Access tokens
* Passwords
* Private keys
* Cloud credentials
* Other sensitive authentication material

The workflow checks the complete Git history to improve secret-detection coverage.

### 2. Trivy

[Trivy](https://github.com/aquasecurity/trivy) is used for filesystem vulnerability scanning.

The workflow is configured to:

* Scan the repository filesystem
* Check for vulnerabilities
* Focus on `HIGH` and `CRITICAL` severity
* Ignore vulnerabilities that do not yet have a fix
* Fail the job when matching vulnerabilities are detected

### 3. Semgrep

[Semgrep](https://github.com/semgrep/semgrep) performs Static Application Security Testing (SAST).

It analyzes source code for security-related patterns without executing the application.

The workflow uses Semgrep's automatic rule configuration.

## GitHub Actions Workflow

The workflow is located at:

```text
.github/workflows/security-checks.yml
```

It contains three independent security jobs:

```yaml
gitleaks
trivy
semgrep
```

Running the checks as separate jobs makes it easier to identify which security control detected a problem.

## Workflow Triggers

The workflow executes on:

```yaml
push:
  branches:
    - main
    - develop

pull_request:

workflow_dispatch:
```

This provides security checks during normal development as well as during pull-request review.

## Security Permissions

The workflow defines explicit GitHub Actions permissions instead of relying on implicit repository defaults.

The goal is to follow the principle of least privilege: workflows should receive only the permissions required for their operations.

## Security Gates

The pipeline can prevent a successful workflow when a configured security check detects a serious issue.

For example, the Trivy job uses:

```yaml
severity: HIGH,CRITICAL
exit-code: 1
```

Therefore, a matching vulnerability can cause the workflow to fail.

This allows security checks to act as CI/CD quality gates rather than merely producing informational output.

## Project Structure

```text
github-actions-security-checks/
│
├── .github/
│   └── workflows/
│       └── security-checks.yml
│
└── README.md
```

## DevSecOps Concepts Demonstrated

This project demonstrates the following concepts:

* DevSecOps
* Shift-left security
* CI/CD security automation
* Secret scanning
* Static Application Security Testing (SAST)
* Dependency vulnerability scanning
* Security quality gates
* GitHub Actions
* Least-privilege workflow permissions
* Automated security validation

## Future Improvements

Potential enhancements include:

* Pin third-party GitHub Actions to full commit SHAs
* Add Dependabot for GitHub Actions updates
* Add GitHub CodeQL analysis
* Add OpenSSF Scorecard
* Generate SARIF security reports
* Upload security reports as workflow artifacts
* Add scheduled security scans
* Add branch protection requiring security checks to pass
* Add container image scanning with Trivy
* Add Infrastructure-as-Code scanning
* Add security notifications

## Learning Objective

The objective of this project is to demonstrate how security controls can be integrated directly into a CI/CD pipeline so that security testing becomes an automated part of the software development lifecycle.

## License

This project is intended for educational and demonstration purposes.
