# custom-images

Container images that need a custom build (no upstream image, or one compiled for specific
hardware). Each image lives in `images/<name>/` and has its own workflow, triggered only by changes
to that directory, so a Renovate bump rebuilds just that image.

| Image                                  | Upstream                                                      | Notes                                   |
| -------------------------------------- | ------------------------------------------------------------- | --------------------------------------- |
| `ghcr.io/csobrinho/strata`             | [Niko1221/Strata](https://github.com/Niko1221/Strata)         | sm_120 (RTX 5090), no vision            |
| `ghcr.io/csobrinho/wolf-nvidia-driver` | [games-on-whales/gow](https://github.com/games-on-whales/gow) | tags: Talos release + its NVIDIA driver |

## Layout

- `images/<name>/image.yaml`: upstream repo, pinned `version` (tracked by Renovate), and optionally
  `ref` (defaults to `version`), `context`, `target` and `buildArgs`.
- `.github/workflows/build-image.yml`: the shared build, a reusable workflow. It builds the upstream
  Dockerfile at that ref; PRs build only, `main` pushes `<version>` and `latest` with a
  build-provenance attestation.
- `.github/workflows/<name>.yml`: the image's triggers, calling `build-image.yml`. Steps that only
  one image needs (resolving the Talos driver for `wolf-nvidia-driver`) run as a job before it and
  pass `build-args` / `tags`.

## Adding an image

1. Add `images/<name>/image.yaml` with the `# renovate:` comment above `version`.
2. Copy `.github/workflows/strata.yml`, rename it, and point `paths`, the concurrency group and
   `image` at the new image.
3. After the first push, set the GHCR package to public so the cluster can pull it.
