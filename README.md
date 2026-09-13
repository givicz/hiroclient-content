# Hiro Client Content

This repository is the public content and release index for Hiro Client. It is
kept separate from the Minecraft client source so the launcher can read news
and release metadata without a custom backend service.

## What Hiro Client is

Hiro Client is a Fabric-based Minecraft client focused on a modern Vulkan-only
rendering path, predictable frame pacing and client-side quality-of-life
features. The current release targets Minecraft `26.2` with Fabric Loader
`0.19.3`.

The client combines a shared `core` module with a version-specific Minecraft
integration. Its main technical areas include:

- Vulkan graphics initialization and a custom terrain pipeline.
- Off-thread terrain meshing with bounded work queues and render-thread GPU
  uploads.
- Frustum/occlusion and particle/entity rendering optimizations.
- SDF and ImGui-based HUD and menu interfaces.
- Configurable HUD modules, cosmetics, media integration and profiling tools.
- ViaFabricPlus support for connecting across supported protocol versions.

The source and build project live in the main HiroClient repository. This repo
only contains public metadata and release pointers consumed by the launcher.

## Files

### `manifest.json`

The launcher reads this file from the GitHub raw URL:

`https://raw.githubusercontent.com/givicz/hiroclient-content/master/manifest.json`

It describes available Minecraft versions, Fabric Loader versions and client
artifacts. Every artifact should include:

- `id`: stable artifact identifier.
- `fileName`: local filename used by the launcher.
- `url`: HTTPS download URL for the artifact.
- `sha1`: expected checksum used to verify the download.
- `size`: expected byte size.

The current `v1_21.jar` release is hosted on Dropbox. The launcher streams the
file to a temporary path, verifies its size and checksum, then installs it.

### `news.json`

This is the launcher news feed. Items contain an `id`, `tag`, `title`, `body`
and ISO date (`YYYY-MM-DD`). New items should be added at the beginning of the
array with a new monotonically increasing ID.

## Release process

1. Build and verify the version-specific client JAR in the main source repo.
2. Upload the release artifact to the external file host.
3. Calculate the artifact SHA-1 and byte size.
4. Update the matching artifact in `manifest.json`.
5. Add a concise release note to `news.json` when appropriate.
6. Commit and push this repository.
7. Verify the raw GitHub manifest and the external download before announcing
   the release.

The manifest is the source of truth for what the launcher can install. A
release must not be published with an empty URL, missing checksum or stale
size.

## Current release

- Minecraft: `26.2`
- Fabric Loader: `0.19.3`
- ViaFabricPlus: `4.6.1`
- Artifact: `v1_21.jar`
- Rendering: Vulkan-only
- Distribution: Dropbox artifact referenced by `manifest.json`

## Repository boundaries

There is intentionally no Node/Express backend in this repository. GitHub
serves the small public JSON files, while the large client artifact is stored
on Dropbox. Secrets, access tokens, build output, logs and local Minecraft
worlds must never be committed here.
