# File List

This document contains a complete list of all files in the Bitcoin Core repository, totaling **2855** files.

## First 20 Files

```
./.editorconfig
./.gitattributes
./.github/ISSUE_TEMPLATE/bug.yml
./.github/ISSUE_TEMPLATE/config.yml
./.github/ISSUE_TEMPLATE/feature_request.yml
./.github/ISSUE_TEMPLATE/good_first_issue.yml
./.github/ISSUE_TEMPLATE/gui_issue.yml
./.github/PULL_REQUEST_TEMPLATE.md
./.github/actions/configure-docker/action.yml
./.github/actions/configure-environment/action.yml
./.github/actions/restore-caches/action.yml
./.github/actions/save-caches/action.yml
./.github/ci-test-each-commit-exec.py
./.github/workflows/ci.yml
./.gitignore
./.python-version
./.style.yapf
./.tx/config
./CMakeLists.txt
./CMakePresets.json
```

## Reproduction Script

To reproduce this file list and generate SHA256 checksums for all files, run the following bash script:

```bash
find . -type f -not -path "./.git/*" | sort | xargs sha256sum
```
