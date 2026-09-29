# AGENTS.md — layer-gocryptfs

Standalone candy repo for the `gocryptfs` layer — the gocryptfs FUSE package
that lets charly mount encrypted volumes, landing `/usr/bin/gocryptfs`. The
candy lives in `charly.yml` at the repo root and projects the `gocryptfs` skill
entity (`family: infrastructure`).

Canonical files:

- `charly.yml` — the `gocryptfs:` candy entity and the `gocryptfs-skill:` skill
  entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:gocryptfs` — the owning skill: the package, the
  `charly-enc-*` scope runtime behavior, and the related commands. Load before
  editing or troubleshooting the layer.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, per-distro `distro:` arms, package
  sections). Load before editing any entity field or plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` steps are the functional evidence: the
  `/usr/bin/gocryptfs` file check, the `gocryptfs -version` stdout match, and
  the `package=gocryptfs` check.
- Package-only candy; the actual encrypt/mount lifecycle is owned by charly
  (`charly config mount`), not by this candy's plan.

## Modify this repo

- Edit the `gocryptfs:` candy entity in `charly.yml`; keep the matching
  `gocryptfs-skill:` entity in step with it.
- Keep the `gocryptfs -version` stdout match aligned with the upstream version
  string format.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
