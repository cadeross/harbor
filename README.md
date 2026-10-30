# ⛵ Harbor

A tiny Liquid Glass menu bar app for macOS that keeps your project folders close and your localhost servers in check.

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="docs/screenshot-dark.png">
    <img src="docs/screenshot-light.png" alt="The Harbor panel with four pinned folders, two of them running localhost servers" width="456">
  </picture>
</p>

- **Pin folders** and copy their full path with a single click. Press and hold a folder to drag it into a new order.
- **See what's running**: Harbor spots dev servers listening on localhost and matches them to your pinned projects
- **Stop servers** right from the menu bar, no hunting for the right terminal tab
- **Open in your tools**: reveal in Finder, or open in your editor (Cursor, Zed, VS Code) or terminal (Ghostty, iTerm, Warp, Terminal)
- The menu bar icon shows how many servers are running, and the panel notices servers outside your pinned folders too
- **Keep your Mac awake** with one switch, lid open or closed, so long-running work (agents, builds, downloads) carries on while you're away

Built with SwiftUI and the macOS Liquid Glass APIs. No dependencies.

## Download

Get the latest `Harbor-x.y.zip` from [Releases](https://github.com/cadeross/Harbor/releases/latest), unzip it, and drag Harbor.app into Applications. It runs on Apple silicon and Intel Macs with macOS 26 or later.

Harbor isn't notarized yet, so the first time you open it macOS says it can't check it for malicious software. Click **Done**, open **System Settings → Privacy & Security**, scroll down, and click **Open Anyway** next to Harbor. You only need to do this once. (Or run `xattr -dr com.apple.quarantine /Applications/Harbor.app` in Terminal.)

## Requirements

- macOS 26 or later
- Xcode 26 or later and [XcodeGen](https://github.com/yonaskolb/XcodeGen) (`brew install xcodegen`) to build

## Building

```sh
xcodegen generate
xcodebuild -scheme Harbor -configuration Release -derivedDataPath build build
cp -R build/Build/Products/Release/Harbor.app /Applications/
```

The Xcode project is generated from `project.yml` and isn't checked in.

## How it works

Harbor runs `lsof` to find TCP ports that are listening, then reads each process's working directory with `libproc`. A server belongs to a pinned folder when its working directory is inside that folder. **Stop** sends `SIGTERM` and follows up with `SIGKILL` if the process is still running after two seconds.

**Keep Mac awake** holds two power assertions (no idle sleep for the system or the display) and asks the power-management root domain not to sleep when the lid closes. Nothing needs admin rights, and everything is released when you switch it off or quit Harbor. A closed, awake laptop still uses its battery and makes heat, so give it some air rather than zipping it into a bag.

Because it inspects and stops other processes, Harbor runs outside the App Sandbox.

## Development tips

- `open Harbor.app --args -HarborPreview YES` opens the panel in a standalone window over a backdrop, for screenshots. Add `-HarborAppearance light` or `dark` to force a theme. Preview mode never saves pins, so you can pass demo pins as a launch argument (`-pinnedFolders "<hex-encoded JSON>"`) without touching your real ones.
- `swift scripts/make-icon.swift icon.png` re-renders the app icon.

## License

MIT. See [LICENSE](LICENSE).
