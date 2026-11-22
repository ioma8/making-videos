# FilmCraft editing

## Install when missing

First locate the GUI and CLI independently (`/Applications/FilmCraft.app`, `~/Applications/FilmCraft.app`, `command -v filmcraft-cli`, and user-provided locations), and check their versions. A CLI on PATH is not the only possible installation. Reuse a working installation; do not upgrade or replace it merely because a newer version exists. If either required component is missing, discover the current stable release from [FilmCraft releases](https://github.com/storytold/filmcraft/releases) or GitHub's `https://api.github.com/repos/storytold/filmcraft/releases/latest`; fetch assets from that same release, using the same version for both when installing both. Determine the OS and architecture, and inspect current release notes and the [download table](https://github.com/storytold/filmcraft#downloads) rather than pinning a version or assuming asset names.

On macOS, the upstream release currently provides a universal GUI DMG named `filmcraft-<version>-macos-universal.dmg` and a **separate** universal CLI ZIP named `filmcraft-cli-<version>-macos-universal.zip`; the app bundle does not include the CLI. Download missing assets and `SHA256SUMS.txt` into a temporary directory, verify their checksums against the release manifest (`shasum -a 256` on macOS), and stop if verification fails. Mount the DMG read-only with `hdiutil attach -readonly -nobrowse`, inspect its contents and copy the app to `~/Applications` or `/Applications` when writable; detach it afterward. Do not overwrite an existing app or disturb an active project. Unzip the CLI to a stable, versioned user directory such as `~/.local/opt/filmcraft-cli-<version>`; retain companion files included in the archive. Run the extracted binary by its absolute path or add a user-local symlink in `~/.local/bin` if that does not replace an existing command. Confirm the GUI bundle exists and the CLI runs `--version` and `--help`; report the installed paths and any installation failure. Preserve signing and quarantine metadata; do not bypass macOS security checks.

On other platforms, choose the matching official release package and follow its upstream installation instructions; Linux packages and tarballs may already contain both app and CLI, unlike macOS. Avoid system-wide installation or privilege escalation when a user-local option suffices. If no compatible release exists, report the limitation rather than silently using another platform or an unverified build.

## Connect and inspect

Prefer the running app's MCP bridge when the user wants edits visible. Discover available FilmCraft tools; if none are registered, its CLI exposes the same commands. Locate the installed CLI and GUI as described above, and check their current paths and `--help`; command schemas and versions can change.

The app must start with `--control PORT`, and the app and CLI must share a 64-hex-character `FILMCRAFT_CONTROL_TOKEN`. Inspect the running project and unsaved state before restarting; preserve the user's work. For a fresh instance choose a free loopback port and a new secret, and do not print or commit it. If a direct shell background launch does not persist, launching through macOS `open -n ... --args --control PORT` with an environment provided to the app worked. For example, use a private token file, `launchctl setenv FILMCRAFT_CONTROL_TOKEN "$(cat "$TOKEN_FILE")"`, launch the app, and unset the launchd variable after the app inherits it. Reuse an existing token only when authorized and available. Never terminate an app with unsaved work without first preserving it.

Put bridge options before the subcommand. A shell variable for the CLI and an exported matching token keep commands concise:

```sh
"$FC_CLI" --bridge 127.0.0.1:9877 inspect
"$FC_CLI" commands 'timeline.place'
"$FC_CLI" describe effects.setParam
"$FC_CLI" --bridge 127.0.0.1:9877 import /absolute/path/source.mp4
"$FC_CLI" --bridge 127.0.0.1:9877 exec file.newSequence '{"name":"Edit","width":1920,"height":1080,"fps":30}'
```

Import and inspect to discover current item, clip and track IDs; examples from other projects are not stable. Use `timeline.place` to put source video and audio on tracks and give explicit timing. FilmCraft time is 254,016,000,000 ticks per second; the CLI also exposes seconds and frame forms. Integer ticks were reliable for placement time, source in-point and duration in the source workflow; verify the result when using other forms. `run -` accepts JSON lines in order. Save with `file.saveAs` to a project path and retain referenced media there or in stable locations.

For an iteration, save a new project version and use `clip.replaceFromBin` when replacing imported media should preserve timing and motion keyframes; inspect the result. Keep voice, device audio and music on separate tracks where useful, and listen for separation artifacts before accepting processed stems.

## Shorts and demonstrations

For vertical output, choose framing from the actual footage and keep the demonstrated controls and hands visible. Reserve space near edges and the bottom for platform controls; review the export at phone size, especially moving crops and the ends of zooms. Transparent overlays on separate tracks can carry concise labels. For feature demonstrations, establish the subject and benefit early, focus on the requested scenario, and verify control names and claims against the product's documentation.

For emulator demos, capture the actual framebuffer and audio output when available, recording real control events rather than inventing screen states or sounds. Synchronize audio and frames to a shared clock; check the actual source frame rate, timestamps and audible output. Inspect existing connections before changing emulator ports, and restore any temporary changes.

## Edit and review

- Check duration, aspect ratio, frame rate and soundtrack; inspect a contact sheet around the start, performance and ending. The hand reached the lens at about 37 seconds in one recording, while a 33.5-second cut lost several useful seconds: determine the actual cut for each source. Use `timeline.trim` with frame deltas to adjust existing clips and extend associated overlays and audio together.
- Keep layers editable: original footage on V1, score on V2, graphic text on higher tracks, audio on A1. `effects.setParam` on `motion` changes position and scale; `graphics.newText` creates an editable graphic. The graphic starts at the playhead unless `time` is specified. `timeline.move` can reposition it. Add tracks when occupied. Verify that the complete score stays inside the frame, remains legible, and avoids the face, hands and instrument throughout motion. A score that looked safe at one frame obscured the face at another. Full-frame video avoids an unnecessary empty band. Balance readability with obstruction; disclose when the score is too small to read comfortably.
- For text, `graphics.set` and `graphics.setCharStyle` adjust font, color, hierarchy, shadow and background. Inspect rendered text at its delivered size. Respect the user's aesthetic feedback; heavy outlines and shadows may undermine a refined look.
- For audio, compare the opening and performance to the original. A one-pass dynamic `loudnorm` raised a quiet opening from about −72 to −27 dB and made noise prominent. Prefer a measured, conservative treatment suited to the actual recording; static gain, judicious EQ and limiting can preserve quiet passages. Check for clipping and listen for tone, noise and transitions. An external processed audio file, if explicitly allowed, can be imported as an independent timeline track; identify that processing honestly. The FilmCraft sequence can retain original footage while using the processed track.
- For grading in FilmCraft, `effects.apply` with `{"clips":[CLIP_ID],"effect":"lumetri"}` adds an editable effect. Inspect the clip's effect parameters, then use `effects.setParam` for exposure, contrast, highlights, shadows, saturation and temperature. A subtle grade is a starting point, not a preset; compare skin and instrument at multiple times. Apply to footage, not score or title layers.

## Verify and export

Bridge `render` is not supported by the CLI; use MCP `render_frame` in bridge mode, or save and use headless `--project path.fcproj render --seconds 8 --out preview.png`. Inspect multiple frames including cut boundaries and overlays. Headless output may differ from the live monitor; resolve discrepancies before delivery.

Export the active sequence using `--bridge ... export /absolute/path/output.mp4`; wait for completion, check its reported path, frames and `offlineMedia`, then probe duration, streams and dimensions. Scrub the delivered start and end and listen to the audio where available. A job submission or `result: null` after a disconnect is not success; reconnect, inspect the saved project and re-export. Keep each previous output unless the user requests replacement.
