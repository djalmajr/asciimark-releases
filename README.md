# asciimark-releases

Public distribution channel for [**AsciiMark**](https://asciimark.djalmajr.dev/) — an AsciiDoc and Markdown viewer with diagrams, math, table of contents, search, and live preview.

This repository hosts **two products**:

1. The **release artifacts** — installers and signed update bundles for the desktop app.
2. The **public website** — published from this repo via GitHub Pages at
   <https://djalmajr.github.io/asciimark/> (or the custom domain mirror,
   <https://asciimark.djalmajr.dev/>).

The application source lives in a separate private repository
(`djalmajr/asciimark`). Only public-facing assets land here.

---

## Downloads

Every tagged release publishes binaries for macOS, Linux, and Windows. Latest:

- 🍎 **macOS Apple Silicon** — [`AsciiMark-macos-arm64.dmg`](https://github.com/djalmajr/asciimark-releases/releases/latest/download/AsciiMark-macos-arm64.dmg)
- 🍎 **macOS Intel** — [`AsciiMark-macos-x64.dmg`](https://github.com/djalmajr/asciimark-releases/releases/latest/download/AsciiMark-macos-x64.dmg)
- 🐧 **Linux** — [`AsciiMark-linux-x64.AppImage`](https://github.com/djalmajr/asciimark-releases/releases/latest/download/AsciiMark-linux-x64.AppImage) · [`.deb`](https://github.com/djalmajr/asciimark-releases/releases/latest/download/AsciiMark-linux-x64.deb)
- 🪟 **Windows** — [`AsciiMark-windows-x64.msi`](https://github.com/djalmajr/asciimark-releases/releases/latest/download/AsciiMark-windows-x64.msi) · [`-setup.exe`](https://github.com/djalmajr/asciimark-releases/releases/latest/download/AsciiMark-windows-x64.exe)

Or browse all releases: <https://github.com/djalmajr/asciimark-releases/releases>.

### macOS first-launch note

macOS may block unsigned apps on first launch. Run this once after installing:

```bash
xattr -cr /Applications/AsciiMark.app
```

### Windows first-launch note

SmartScreen may show a "Windows protected your PC" dialog. Click **More info → Run anyway**.

---

## Browser extension

For an in-browser preview (without installing anything), the **AsciiMark Chrome
extension** auto-renders `.adoc` and `.md` files when you open them as URLs
(`file://` or `https://`). It shares the same UI as the desktop app in a smaller
window — sidebar tree, tabs, TOC, and the same shortcuts.

Install at the [Chrome Web Store](https://chromewebstore.google.com/) (search
"AsciiMark"). Source ships from the private app repository; the extension's
zip artifact is uploaded manually with each release.

---

## Auto-update (desktop)

The desktop app uses [`tauri-plugin-updater`](https://v2.tauri.app/plugin/updater/)
to check for new releases on startup. The check fetches a single signed JSON
manifest from this repo:

```
https://github.com/djalmajr/asciimark-releases/releases/latest/download/latest.json
```

Each platform asset has a sibling `.sig` file (ed25519 / `minisign`). The
client refuses any update without a valid signature, so the public key
embedded in the app and the private key on the build pipeline are the only
way to ship a trusted update. Rotating the public key would break
auto-update for every existing client — that key is stable for the lifetime
of the app.

A "Check for updates" item is also exposed in the desktop app's hamburger
menu for manual checks.

---

## Release artifacts (per platform)

Each release contains both an **installer** (first install) and an **update
bundle** (auto-updater). Tauri's updater protocol mandates the latter as a
specific archive shape:

| Platform     | Update asset (auto-update) | Installer (first install) |
|--------------|----------------------------|---------------------------|
| macOS arm64  | `*.app.tar.gz` + `.sig`    | `*.dmg`                   |
| macOS x64    | `*.app.tar.gz` + `.sig`    | `*.dmg`                   |
| Linux x64    | `*.AppImage.tar.gz` + `.sig` | `*.AppImage` or `*.deb` |
| Windows x64  | `*.nsis.zip` + `.sig`      | `*.msi` or `*-setup.exe`  |

`latest.json` ties these together: it lists the version, publish date,
release notes, and per-platform `signature` + `url` for the update bundle.

---

## How releases are produced

The build pipeline lives in the private app repo
(`djalmajr/asciimark/.github/workflows/build-desktop.yml`). On a `v*` tag
push it:

1. Builds Tauri bundles for macOS arm64/x64, Linux, and Windows in parallel.
2. Generates release notes from conventional-commit messages.
3. Normalizes asset names and constructs `latest.json` with signatures.
4. Publishes a GitHub Release here (`asciimark-releases`).

Pre-release versions (e.g. `v0.8.0-rc.0`) are tagged as such and excluded
from the auto-updater's "latest" channel.

---

## Public site

The marketing + docs site is built from `apps/site/` in the source repo
(SolidJS + TanStack Router) and deployed to this repository's GitHub Pages
on every push to `main` that touches site code. The deploy workflow is
`djalmajr/asciimark/.github/workflows/deploy-site.yml`.

- 🏠 Home: <https://asciimark.djalmajr.dev/>
- 📖 Guide: <https://asciimark.djalmajr.dev/guide>
- 🔒 Privacy: <https://asciimark.djalmajr.dev/privacy>

The site does not collect analytics or telemetry. See the privacy page for
the full disclosure.

---

## Privacy

AsciiMark is local-first: file content stays on your machine. The only
external requests at runtime are:

- **Kroki diagrams** (`https://kroki.io`) — only when the document contains
  Kroki diagram blocks (PlantUML, Graphviz, etc.). Plain text source is
  POSTed; an SVG comes back. No JavaScript is fetched.
- **Auto-updater** (desktop only) — fetches `latest.json` from this repo
  on startup, then the signed update artifact if the user accepts the
  prompt. No document content is sent.

The full privacy policy:
<https://asciimark.djalmajr.dev/privacy>

---

## Issues and feedback

For bugs, feature requests, or privacy questions, please open an issue:
<https://github.com/djalmajr/asciimark-releases/issues>.

The application source repository is private. All public discussion happens
here.

---

## License

Releases are distributed under the terms documented inside each release
artifact. The site source under `apps/site/` (in the source repo) is
distributed under MIT.
