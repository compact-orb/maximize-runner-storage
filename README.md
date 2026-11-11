# maximize-runner-storage

A GitHub Action that reclaims storage space on GitHub-hosted runners by removing
non-essential software and adding a compressed Btrfs filesystem.

## What It Does

1. **Removes non-essential packages, programs, and data** — Purges APT packages,
   build tools, SDKs, and cached data that are pre-installed on the runner.
2. **Creates and mounts a Btrfs image** — Allocates the freed space into a loop
   Btrfs image with transparent `zstd:3` compression, mounted over `/usr/local`,
   `$GITHUB_WORKSPACE`, and any configured additional paths.

## Usage

Add the action as the **first step** in your job, before checking out code or
installing dependencies:

```yaml
steps:
  - uses: compact-orb/maximize-runner-storage@v3

  - uses: actions/checkout@v7
```

### Additional Mounts

To bind-mount additional directories (such as Docker storage or cache paths)
onto the compressed Btrfs filesystem:

```yaml
steps:
  - uses: compact-orb/maximize-runner-storage@v3
    with:
      additional-mounts: /var/lib/docker

  - uses: actions/checkout@v7
```

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| `additional-mounts` | Newline-separated list of paths to mount | `''` |
| `create-filesystem` | Whether to create and mount Btrfs | `'true'` |
| `mount-usr-local` | Whether to mount `/usr/local` on Btrfs | `'true'` |
| `mount-workspace` | Whether to mount `$GITHUB_WORKSPACE` on Btrfs | `'true'` |

> [!IMPORTANT]
> This action **must** run before `actions/checkout` because it initializes
> `$GITHUB_WORKSPACE`.
