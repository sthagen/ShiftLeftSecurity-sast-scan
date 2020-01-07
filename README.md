# Introduction

This repo builds `appthreat/sast-scan` (and `quay.io/appthreat/sast-scan`), a container image with a number of bundled open-source static analysis tools. This is like a Swiss Army knife for DevSecOps engineers. RedHat's `ubi8/ubi-minimal` is used as a base image instead of the usual alpine to help with enterprise adoption of this tool.

[![Docker Repository on Quay](https://quay.io/repository/appthreat/sast-scan/status "Docker Repository on Quay")](https://quay.io/repository/appthreat/sast-scan)

## Bundled tools

| Programming Language | Tools                                 |
| -------------------- | ------------------------------------- |
| ansible              | ansible-lint                          |
| aws                  | cfn-lint, cfn_nag                     |
| bash                 | shellcheck                            |
| bom                  | cdxgen                                |
| Credential scanning  | gitleaks                              |
| golang               | gosec, staticcheck                    |
| java                 | cdxgen, gradle, pmd, dependency-check |
| json                 | jq, jsondiff, jsonschema              |
| kotlin               | detekt                                |
| kubernetes           | kube-score                            |
| node.js              | cdxgen, retire, eslint, yarn          |
| puppet               | puppet-lint                           |
| python               | bandit, cdxgen, ossaudit, pipenv      |
| ruby                 | railroader, cyclonedx-ruby            |
| rust                 | cargo-audit                           |
| terraform            | tfsec                                 |
| yaml                 | yamllint                              |

## Bundled languages/runtime

- jq
- Python 3.6
- OpenJDK 11 (jre)
- Ruby 2.5.5
- Rust
- Node.js 10
- Yarnpkg

## Usage

### Invoking built-in tools

Bandit

```bash
docker run --rm -v <source path>:/app appthreat/sast-scan bandit -r /app
```

dependency-check

```bash
docker run --rm --tmpfs /tmp -v <source path>:/app appthreat/sast-scan /opt/dependency-check/bin/dependency-check.sh -s /app
```

Retire.js

```bash
docker run --rm --tmpfs /tmp -v <source path>:/app appthreat/sast-scan retire -p --path /app
```

### Using custom scan script

Scan python project

```bash
docker run --rm --tmpfs /tmp -v <source path>:/app appthreat/sast-scan scan --src /app --type python --out_dir /app
```

Scan node.js project

```bash
docker run --rm --tmpfs /tmp -v <source path>:/app appthreat/sast-scan scan --src /app --type nodejs --out_dir /app
```

Scan java project

```bash
docker run --rm --tmpfs /tmp -v ~/.m2:/.m2 -v <source path>:/app appthreat/sast-scan scan --src /app --type java --out_dir /app

# For gradle project
docker run --rm --tmpfs /tmp -v ~/.gradle:/.gradle -v <source path>:/app appthreat/sast-scan scan --src /app --type java --out_dir /app
```

## Integration with GitHub action

This tool can be used with GitHub actions using this [action](https://github.com/marketplace/actions/sast-scan). All the supported languages can be used.

## Alternatives

GitLab [SAST](https://docs.gitlab.com/ee/user/application_security/sast/) uses numerous single purpose [analyzers](https://gitlab.com/gitlab-org/security-products/analyzers) and GoLang based convertors to produce a custom json format. This model has the downside of increasing build times since multiple container images should get downloaded and hence is not suitable for CI environments such as Azure Pipelines, CodeBuild and Google CloudBuild. Plus the license used by GitLab is not opensource even though the analyzers merely wrap existing oss tools!
