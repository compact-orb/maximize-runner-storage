# maximize-runner-storage

A GitHub Action that frees disk space on GitHub-hosted runners by removing
pre-installed software and creating a compressed Btrfs filesystem.

## What It Does

1. **Removes pre-installed packages, tools, and data** — Purges APT packages
   not marked `required` or `important` (except `linux*`, `node*`, and `sudo*`),
   deletes pre-installed toolchains, caches, and logs, and removes the `packer`
   user.
2. **Creates and mounts a Btrfs image** — Creates a Btrfs image file
   (`/btrfs.img`) using the free disk space minus 1 GiB, mounts it with `zstd:3`
   compression, and bind-mounts `/usr/local`, `$GITHUB_WORKSPACE`, and any
   paths in `additional-mounts`.

## Usage

Add this action as the **first step** in your job, before checking out code or
installing dependencies:

```yaml
steps:
  - uses: compact-orb/maximize-runner-storage@v4

  - uses: actions/checkout@v7
```

### Additional Mounts

To mount extra directories (such as Docker storage or cache directories) on the
compressed Btrfs filesystem:

```yaml
steps:
  - uses: compact-orb/maximize-runner-storage@v4
    with:
      additional-mounts: /var/lib/docker

  - uses: actions/checkout@v7
```

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| `additional-mounts` | Space- or newline-separated list of extra directories to mount | `''` |
| `create-filesystem` | Create and mount the compressed Btrfs filesystem | `'true'` |
| `mount-usr-local` | Mount `/usr/local` on the Btrfs filesystem | `'true'` |
| `mount-workspace` | Mount `$GITHUB_WORKSPACE` on the Btrfs filesystem | `'true'` |

> [!IMPORTANT]
> This action **must** run before `actions/checkout` because it deletes the
> contents of `$GITHUB_WORKSPACE` before mounting.
