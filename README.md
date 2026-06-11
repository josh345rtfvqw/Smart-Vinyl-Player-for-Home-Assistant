# Smart Vinyl Player for Home Assistant

I wanted a fun, physical way to play music without going down the expensive rabbit hole of actual vinyl. The idea of dropping a record on a deck to kick off an album is just a nice thing to do. So I built a digital version of it using hardware I already had experience with and software I was already running.

The Smart Vinyl Player uses NFC tags inside 3D printed vinyl records. Drop one on the deck, it spins, and your music starts playing via Music Assistant. Pull it off, it stops. That's it.

It runs on ESPHome, integrates with Home Assistant, and uses Music Assistant to handle the actual media playback. If you're already in the Home Assistant ecosystem, you've got most of this sorted.

---

## How It Works

The deck uses a LOLIN D1 Mini with a 28BYJ-48 stepper motor, a ULN2003 motor driver, and an RDM6300 125kHz NFC RFID reader module. When you place a vinyl record on the deck, two things happen at once.

The ESPHome code spins the motor (giving you about 9.6 hours of spin time) and sends the NFC tag ID to Home Assistant. Home Assistant picks that up as a trigger and fires an automation that tells Music Assistant to play whatever you've assigned to that record, on whichever speaker or media player you've set as the target.

Remove the record and the motor stops. The music keeps playing until you either put a new record on or stop it manually.

Simple, but it works really well in practice.

---

## What You Need

### Parts List

| Part | Notes |
|------|-------|
| LOLIN D1 Mini | The brains of the build |
| 28BYJ-48 Stepper Motor | Drives the spinning deck |
| ULN2003 Stepper Motor Driver | Pairs with the stepper |
| RDM6300 125kHz NFC RFID Reader | Reads the tags in the records |
| NFC RFID Tags (125kHz) | One per vinyl record |
| 3D printed parts | Files included in this listing |

### Software Requirements

1. Home Assistant
2. Music Assistant (add-on, handles media and player management, needs at least one media source added, e.g. Spotify, YouTube Music, or a local library)
3. ESPHome (add-on, used to flash the D1 Mini)

---

## Setting Up ESPHome

Once you've got the D1 Mini wired up, you need to flash it with the ESPHome config included in this listing.

In Home Assistant, go to **Settings > Add-ons > ESPHome** and open the dashboard. Hit **New Device**, give it a name, and choose to edit the configuration manually. Paste in the provided YAML config and hit **Install**.

ESPHome will compile and flash the firmware over USB the first time. After that, all updates are wireless.

Once it's flashed and connected, the device will show up in Home Assistant automatically via the ESPHome integration. The NFC reader and motor control will be ready to go.

---

## Setting Up the Home Assistant Automation

Each vinyl record needs its own automation. This is what ties a specific NFC tag to the music that plays when you drop the record on the deck.

### Before You Start

Make sure you have:

- Music Assistant installed with at least one media source added (Spotify, YouTube Music, a local library, etc.)
- At least one media player added to Music Assistant
- Your NFC tags scanned and showing up under **Settings > Tags** (they register on first scan automatically)

### Creating the Automation

Go to **Settings > Automations & Scenes > Automations** and hit **Create Automation**, then **Create new automation**.

**1. Set the Trigger**

Under **When**, add a trigger and select **Tag** as the type. Pick the tag that matches the record you're setting up.

Every record gets its own automation. 10 records means 10 automations. Once you've done the first one, duplicating and tweaking the rest takes about a minute each.

![Home Assistant automation trigger set to NFC tag scan, with Music Assistant Play Media action targeting Living Room TV for Olivia Dean](screenshot-ha-automation.png)

*Trigger fires on NFC tag scan. Action calls Music Assistant: Play media, targeting the Living Room TV with a library URI and artist name.*

**2. Conditions (Optional)**

Leave the **And if** section blank unless you want to add logic later, like only triggering when you're home or within certain hours. Not needed to get started.

**3. Add the Action**

Under **Then do**, add an action and search for **Music Assistant: Play media**. Here's how to fill it out:

**Targets**
Pick the media player you want the vinyl to play on. Any player added to Music Assistant works, whether that's a Sonos, Chromecast, or a native HA media player entity.

**Media ID(s)**
This is the URI of what you want to play. Music Assistant uses library URIs like:

```
library://artist/2709
library://album/142
library://track/88
library://playlist/5
```

To find the ID, browse to the item in Music Assistant and grab the number from its detail page.

**Media type**
Match this to what you're playing: Artist, Album, Track, or Playlist. You can leave it on auto-detect, but setting it manually is more reliable.

**Artist name** *(optional)*
Only needed if you're playing a track or album by name and want to lock it to a specific artist. If you're using a library URI directly, you can skip it.

**Save and Test**

Give the automation a clear name. I use a format like `Music - 01 - Olivia Dean` so they stay easy to find and sort as the collection grows.

To test it, tap the NFC tag with your phone or hit **Run** in the automation editor. Music should start on your player within a few seconds.

---

## Programming the NFC Tags

Each NFC tag just needs to be scanned by Home Assistant once to register it. After that, the tag ID is the trigger for the automation.

To register a tag, tap it with your phone while the NFC reader is connected and Home Assistant is running. It'll show up under **Settings > Tags** with an auto-generated name. You can rename it there to match the record, which helps keep things organised.

---

## Tips

- **Name automations consistently.** Music - 01, Music - 02, etc. keeps the list manageable once you've got a stack of records.
- **Duplicate, don't rebuild.** After the first automation, duplicate it for each new record and just swap the tag and media ID.
- **Albums and playlists are the most reliable media IDs.** Individual tracks can sometimes resolve to the wrong version if you have multiple library sources.
- **Multiple rooms?** Add more than one target entity to the automation and the vinyl plays across all of them at once.
- **Keep a spreadsheet or note** of which tag ID maps to which record. Makes it much easier if you ever need to rebuild an automation.

---

## Files Included

- 3D printable vinyl record body (with NFC tag recess)
- 3D printable deck and housing
- ESPHome YAML configuration
- Example Home Assistant automation YAML

---

If you build one, I'd love to see it. Drop a photo in the comments.
