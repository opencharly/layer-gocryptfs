# gocryptfs

The `gocryptfs` encrypted filesystem, so charly can mount encrypted volumes.

`gocryptfs` installs the gocryptfs FUSE package — the same name on rpm (Fedora),
pac (Arch), and deb (Debian/Ubuntu) — so charly can mount encrypted volumes. The
candy's whole deliverable is the `gocryptfs` binary at `/usr/bin/gocryptfs`; the
observable proof it is present and runnable is that the binary exists and
`gocryptfs -version` reports a version string. charly drives the actual
encrypt/mount lifecycle, each mount running in a per-volume
`charly-enc-<image>-<volume>` systemd `--user` scope. It is usually composed
through the `charly` candy rather than named directly.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `gocryptfs` |
| Distro | all — `gocryptfs` (rpm/pac/deb) |
| Binary | `gocryptfs` at `/usr/bin/gocryptfs` |
| Service / port | none |

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-image:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-gocryptfs:v2026.239.1626'
```

Then, inside the built image:

```bash
gocryptfs -version        # gocryptfs vX.Y.Z
```

## Runtime behavior

When `charly config mount` or `charly start` mounts encrypted volumes, each
gocryptfs daemon runs inside a `systemd-run --scope --user
--unit=charly-enc-<image>-<volume>` scope unit. This decouples the FUSE mount
lifecycle from the container service — mounts survive container stop/restart and
remain browsable on the host. The `-allow_other` flag is always passed (required
for rootless podman with `--userns=keep-id`); gocryptfs auto-enables
`default_permissions`, so kernel UNIX permission checks still apply.

## Layout

- `charly.yml` — the `gocryptfs:` candy entity: the package and the `check:` steps.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Family skill: `/charly-infrastructure:gocryptfs`
- `/charly-automation:enc` — full encrypted-volume operations documentation
- `/charly-infrastructure:virtualization`, `/charly-infrastructure:socat` — the other members of the `charly` candy's toolchain
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
