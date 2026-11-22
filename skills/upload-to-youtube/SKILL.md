---
name: upload-to-youtube
description: Upload a finished video to YouTube Studio or update its YouTube details using a headless browser.
---

# Upload to YouTube

Read [references/youtube.md](references/youtube.md) for channel selection, headless Studio access and upload verification. Uploading does not imply public publication.

Before accessing browser credentials for an upload, ask which browser and, if needed, profile contains the signed-in YouTube account, unless the user already specified them. Use the selected session. Then list accessible YouTube channels and ask which to use unless the user already selected one for this upload; wait for that choice. Verify the selected channel immediately before uploading.

Identify the intended final video and thumbnail, distinguishing older exports. Reconcile artist, song, title and credits across the video, thumbnail and metadata; reconcile on-screen calls to action with actual description links. Leave links absent until a real destination is supplied. Verify the upload, thumbnail, visibility, checks and final Studio confirmation, then report the video link and actual visibility.

If a thumbnail needs to be created or refined for the upload, use the `make-youtube-thumbnail` skill before uploading.
