# screenshots/

Store listing screenshot **candidates** for `io.deepiks.livia`, captured from a
real device rather than a mockup. Nothing here has been uploaded to Play
Console yet — pick from these, or reshoot at a higher resolution first (see
[Resolution](#resolution-the-one-blocker) below).

## What is here

Captured 23 September 2026 from the Galaxy Tab A9 (`SM-X130`), Android 16, French locale, care receiver mode, on the build
that targets API 36.

| File | Screen |
| --- | --- |
| `tablet-7in/landscape/01-home-screen.png` | Livia as the launcher: date, clock, the Talk / Calendar / Photos / Morning brief tiles |
| `tablet-7in/landscape/02-calendar-month.png` | Calendar, month view, today highlighted |
| `tablet-7in/landscape/03-calendar-day.png` | Calendar, day view |
| `tablet-7in/landscape/04-grocery-list.png` | Grocery list with items and the add field |
| `tablet-7in/landscape/06-settings-modes.png` | Settings: the caregiver / care receiver switch and the language picker |
| `tablet-7in/portrait/01-calendar-month.png` | Calendar, month view, portrait |

A devices screenshot was captured too and is **deliberately not committed**:
the plug's name is `shellyplugmg3-70af09e05c94`, which is its MAC address, and
this repo and the store listing are both public. Reshoot it with the plug
renamed if the Devices screen needs to be in the listing.

`tablet-7in` because the Tab A9 is an 8.7" device, which lands in Play
Console's **7-inch tablet** slot. There are no phone screenshots yet — the app
has only been run on the tablet, and the phone layout differs (a bottom
navigation bar rather than the side rail), so phone shots have to be captured
on a phone rather than cropped from these.

## Play's requirements

From [Add preview assets](https://support.google.com/googleplay/android-developer/answer/9866151):

| | Requirement | These files |
| --- | --- | --- |
| Format | JPEG or **24-bit PNG, no alpha** | ✅ 24-bit RGB (flattened from `adb screencap`, which emits RGBA that Play rejects) |
| Size per side | 320 – 3840 px | ✅ 800 / 1340 px |
| Aspect | longest side ≤ 2 × shortest | ✅ 1340 ≤ 1600 |
| Count | ≥ 2 to publish, ≤ 8 per device type | ✅ 6 landscape, 1 portrait |
| File size | ≤ 8 MB each | ✅ 63 – 96 KB |

### Resolution: the one blocker

These are **valid to upload** but will **not** qualify the listing for
large-screen recommendations on Play. That tier wants at least **four**
screenshots with a shortest side of **1080 px**, at 16:9 landscape or 9:16
portrait. These are 800 px on the short side, at 1.675:1.

That is the tablet's own panel — the Tab A9 is 1340×800, so no capture from it
can clear 1080 px. To qualify, shoot on a higher-resolution device, or run an
API 36 emulator sized to 1920×1080.

Everything else about them is fine, so this only costs the recommendation
placement, not the listing itself.

## Deliberately left out

Other screens were captured during the API 36 sweep and are not here:

- **Account menu, caregiver band and caregiver menu** — they show a real
  account address and a real name. Not suitable for a public listing, and not
  worth keeping in a repo either.
- **Photos feed** — the thumbnail had not loaded, so it reads *Image
  indisponible*.
- **Web tab** — showed Google's cookie consent sheet rather than the app.
- **Paywall** — prices come from the test offering (`Consumable · 0,99 $US`),
  which is not what a buyer would see.

Reshoot these with seeded demo data if the listing needs them.
