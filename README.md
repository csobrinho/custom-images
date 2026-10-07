# custom-images

Container images that need a custom build (no upstream image, or one compiled for specific
hardware). Each image lives in `images/<name>/` and has its own workflow, triggered only by changes
to that directory, so a Renovate bump rebuilds just that image.

| Image                      | Upstream                                              | Notes                        |
| -------------------------- | ----------------------------------------------------- | ---------------------------- |
| `ghcr.io/csobrinho/strata` | [Niko1221/Strata](https://github.com/Niko1221/Strata) | sm_120 (RTX 5090), no vision |

## Layout

- `images/<name>/image.yaml`: upstream repo, pinned `version` (tracked by Renovate) and
  `buildArgs`.
- `.github/workflows/<name>.yml`: builds the upstream Dockerfile at that version. PRs build only;
  `main` pushes `<version>` and `latest` with a build-provenance attestation.

## Adding an image

1. Add `images/<name>/image.yaml` with the `# renovate:` comment above `version`.
2. Copy `.github/workflows/strata.yml`, rename it, and point `IMAGE` / `SPEC` and `paths` at the
   new image.
3. After the first push, set the GHCR package to public so the cluster can pull it.
