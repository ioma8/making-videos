# YouTube Studio through headless Firefox

Use a connected YouTube tool if it fulfills the request; otherwise, when the user requests a headless browser, this tested local fallback controls YouTube Studio through Selenium. UI language, selectors, custom-thumbnail availability and upload checks change; inspect the current DOM. In November 2026 the installed Studio UI was Czech. 

An upload request authorizes the upload; use the requested visibility. If none was specified, save privately and report that state. Publish publicly only when requested. Verify the chosen title, artist/song relationship and video asset from the user, not inferred filenames. If a download URL is missing, ask or omit it, and mention any in-video promise that still refers to the absent URL. Disclose generated or altered content in Studio as appropriate to the actual video and thumbnail; do not fabricate facts.

## Choose the channel before uploading

Before starting an upload, inspect the signed-in YouTube account menu and its account/channel switcher, or use an available connected YouTube tool, to enumerate channels accessible in the current session. Present the channel names and any distinguishing account or channel identifiers the interface shows, then ask the user which channel to use. If the user has already explicitly selected a channel for this upload, treat that as their choice; otherwise wait for their answer before sending the video or creating an upload. A single visible channel is not an implicit choice. Do independent preparation while waiting.

Switch to the selected channel and verify its name and, when available, channel ID in Studio immediately before uploading. Account-switcher layouts and labels vary; inspect the live interface rather than relying on a fixed selector. List only channels actually visible, without exposing credentials or unrelated private account details. If the session cannot enumerate every account or channel, say what is visible and ask the user to choose from those or sign in to the missing account. Never guess that an unavailable channel is accessible.

## Temporary session

On the tested Mac, Firefox was `/Applications/Firefox.app/Contents/MacOS/firefox`; geckodriver and Selenium were installed locally. Discover the signed-in profile via `~/Library/Application Support/Firefox/profiles.ini`. Set up a virtual environment if necessary. This example copies only Google/YouTube cookies into a temporary private profile and always deletes it. The live database and WAL copied separately are not an atomic snapshot; if inconsistent, use a clean sign-in or another authorized session rather than modifying the live profile. Cookies are credentials: never print or transmit them.

```python
import os, tempfile, sqlite3, shutil
from selenium import webdriver
from selenium.webdriver.firefox.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait

profile = tempfile.mkdtemp(prefix='yt-upload-')
os.chmod(profile, 0o700)
source = os.path.expanduser('~/Library/Application Support/Firefox/Profiles/PROFILE/cookies.sqlite')
driver = None
try:
    shutil.copyfile(source, profile + '/cookies.sqlite')
    if os.path.exists(source + '-wal'):
        shutil.copyfile(source + '-wal', profile + '/cookies.sqlite-wal')
    db = sqlite3.connect(profile + '/cookies.sqlite')
    try:
        with db:
            db.execute("DELETE FROM moz_cookies WHERE host NOT LIKE '%youtube.com' AND host NOT LIKE '%google.com' AND host NOT LIKE '%google.cz'")
    finally:
        db.close()
    options = Options()
    options.add_argument('-headless')
    options.add_argument('-profile')
    options.add_argument(profile)
    options.binary_location = '/Applications/Firefox.app/Contents/MacOS/firefox'
    driver = webdriver.Firefox(options=options)
    driver.set_window_size(1440, 1100)
    driver.get('https://studio.youtube.com/')
    # Inspect channel and current UI; use explicit waits for each action.
finally:
    try:
        if driver is not None:
            driver.quit()
    finally:
        shutil.rmtree(profile, ignore_errors=True)
```

## Observed Studio flow

1. After the user chooses a channel, switch to it and verify its name and channel ID where available. On Czech Studio, `button[aria-label="Vytvořit"]` → `Nahrávání videí` opens upload. Send the absolute video path to `input[type=file]` and wait for the Details page and completion.
2. The first two `[contenteditable=true]` fields held title and description. Replace existing title with Command-A and enter accurate metadata. Upload the user's exact thumbnail via `ytcp-thumbnail-uploader input[type=file]`, then verify the thumbnail preview appears. Set the audience truthfully; the tested general-audience performance used `tp-yt-paper-radio-button[name=VIDEO_MADE_FOR_KIDS_NOT_MFK]`. Expand additional options when relevant.
3. Use `#next-button` to traverse video elements, checks and visibility. Wait for copyright and community checks and report restrictions or pending checks. In the tested case the upload completed and both checks showed no issues. Do not claim completion merely because the upload reached 100%.
4. At visibility, check the radio's `aria-checked`, select the authorized state, and click `#done-button`. Confirm the saved/private or published outcome and capture the displayed video URL. A visible confirmation, not the click alone, establishes success.
5. For a later title edit, the tested Studio page was `https://studio.youtube.com/video/VIDEO_ID/edit`; edit the first contenteditable field, click `#save`, and verify Studio says changes saved. Preserve visibility and all other metadata.

Close the driver and remove the temporary profile even after failures. Report the channel, link, actual visibility, check status, selected thumbnail and any missing user-supplied link.
