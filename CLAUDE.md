# OpenGrid XR (Jason's fork): notes for Claude

This is **optimuspine82/OpenGridXR**, a fork of DSuzuki35/OpenGridXR (upstream author: Dan Suzuki). Jason runs builds of this fork on his rig (Windows 11, Pimax Dream Air SE, iRacing).

## Rules
- **Never open pull requests, issues or comments on DSuzuki35/OpenGridXR.** Work only in this fork. (One was opened by accident before; Jason closed it.)
- Jason isn't a terminal expert: give click-by-click GitHub steps and one copy-paste block for any command he runs.
- Nothing here runs Windows, a headset or iRacing. Say what was tested (Linux unit tests, mingw compile) and what wasn't.

## Branches
- `main`: upstream 1.2.0 (0bf9b36) + Jason's changes: the RaceLab-style edit mode, the button-learning fix and the installer build. This is what he installs.
- `rig-edit-mode`: the edit mode commits, kept fast-forwarded to `main`.
- The earlier wheel-LED work (`rig-integration`) was dropped and is not in this repo.

## Building the installer (GitHub Actions)
- Workflow **Build and test** (`.github/workflows/build.yml`) runs on pushes to `main`, `develop` and `rig-*`, and on demand. On this fork it makes the NSIS installer and uploads it as the **OpenGridXR-installer** artifact (30 days). Upstream's build doesn't do that; keep the fork's version.
- In practice only pushes to **`main`** reliably started builds here; tell Jason to download from the **main** run.
- The Actions API isn't reachable from this session: ask Jason for a screenshot of the run, or of the red step if it fails.
- The **benchmark-ui** check (`tests/benchmark-ui.cjs`) fails now and then (CSV not written in time). It's unrelated to our changes; re-run rather than "fix".
- A Node.js 20 deprecation warning from the pinned actions is expected; leave the action versions alone (they're upstream's).
- The installer is unsigned (Windows SmartScreen: More info, then Run anyway). Tell Jason to decline OpenGrid's in-app update offers, which would replace this build with upstream.

## Checking changes here
- `npm test` runs `node --test tests/*.test.mjs`. This clone has no `node_modules`: install with `npm ci` or link one in, and remove it again before committing.
- On Linux a few tests always fail or are cancelled (Windows-only paths). Compare against that baseline instead of expecting all green.
- Native code is Windows C++17 (MSVC in CI). Sanity-compile new helpers with `x86_64-w64-mingw32-g++-posix -std=c++17 -Wall ... -luser32`; it can't be run here (no Wine).
- Add each new native self-test to the `ctest -R` list in build.yml.

## Things we learned the hard way
- Edit mode input (`native/src/edit_input.cpp`):
  - Measure mouse moves from the current cursor position, not from where editing started: iRacing re-centres and clips the cursor itself.
  - The helper must be per-monitor DPI aware. Hook coordinates are physical pixels, and display scaling otherwise reads as constant movement.
- Bavarian Simtec wheels (Omega Pro V2) keep one button held in every multi-position rotary position (button 104 here). Button learning now ignores inputs already held when learning starts (`desktop/input-controller.cjs`).
- Edit mode overview: `CHANGELOG.md` (Unreleased) and the Project doc `claude/OpenGridXR/edit-mode.md`.
