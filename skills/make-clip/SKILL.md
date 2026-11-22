---
name: make-clip
description: Edit and export video in FilmCraft.
---

# Make a clip

For FilmCraft editing, read [references/filmcraft.md](references/filmcraft.md). Editing does not imply a YouTube upload.

1. If no video file or path is provided, ask whether the user wants to use an existing recording or capture an app or website. For a capture, ask which app or website and which scenarios to demonstrate; for an existing recording, identify the source file. If the output format is not specified, ask whether to make a classic 16:9 landscape video or a vertical Shorts video. If the user has not supplied a video title, ask for one. Bundle missing details into one request when practical. Use details already supplied and continue independent preparation while waiting. Inspect the source, user instructions, destination, and existing project. Preserve source media and editable project assets.
2. For a website screencast, use a headless browser to capture the requested scenarios and import the recording into FilmCraft. For a silent screencast, try to add suitable public-domain background music unless the user asks for silence; verify the recording’s public-domain status and retain its source and rights information. If no suitable verified track is available, keep it silent and report that.
3. Build and verify the requested edit. Inspect representative rendered frames including the beginning and ending; check the face, hands, instrument and overlays throughout. Confirm cut points against the actual gesture and preserve the performance.
4. Watch and listen to the entire exported file, including the final note and cut. For audio changes, compare the opening and a loud passage against the original at matched volume for noise, bass definition, clipping and changes in tone. Report any check that could not be performed.
5. Identify the current final export and editable project, distinguishing superseded versions. Reconcile artist, song, title and credits within the video; flag on-screen calls to action that lack a destination.

If a thumbnail is requested, use the `make-youtube-thumbnail` skill. If the user also requests a YouTube upload, use `upload-to-youtube` after the final export is verified.
