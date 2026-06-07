# maximize-runner-storage

A GitHub Action that reclaims storage space on GitHub-hosted runners by removing non-essential software and adding a compressed Btrfs filesystem.

## What it does

1. **Removes non-essential packages, programs, and data** — Purges APT packages, build tools, SDKs, and cached data that are pre-installed on the runner.

2. **Creates and mounts a Btrfs image** — Allocates the freed space into a Btrfs image with `zstd` compression, then bind-mounts it over `/usr/local`, `/opt`, and `$GITHUB_WORKSPACE` for transparent compression.

## Usage

Add the action as the **first step** in your job, before checking out code or installing dependencies:

```yaml
steps:
  - uses: compact-orb/maximize-runner-storage@v1

  - uses: actions/checkout@v6
```

> [!IMPORTANT]
> This action **must** run before `actions/checkout` because it clears `$GITHUB_WORKSPACE`.
