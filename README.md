# Chip on the Desk

The demo page and slides from my XtremeJS 2026 talk, **Making Web Bluetooth Work: A Field Report from a Chip on the Desk** (10 November 2026).

**Live demo:** https://ifedayoagboola.github.io/chip-on-the-desk/ (Chrome or Edge on Windows, macOS, ChromeOS or Android; Web Bluetooth needs HTTPS and a click)

**Slides:** [PDF](slides-xtremejs-2026.pdf) · [PowerPoint](slides-xtremejs-2026.pptx) (Inter and Cascadia Mono, both SIL OFL, give the intended rendering)

## What the page shows

One HTML file, plain JavaScript, no build step. It connects the browser to an NXP QN9090 development kit (the DK6) running NXP's Proximity Reporter firmware, and shows the parts the seven-line Web Bluetooth example leaves out:

- a GATT operation queue, with a toggle so you can watch `NetworkError: GATT operation already in progress` happen without it
- a 10-second timeout on every `connect()`
- a reconnect policy with exponential backoff (1, 2, 4, 8, 16 s) that tells an intentional disconnect from a dropped link and rediscovers services after every reconnect
- feature detection: `navigator.bluetooth`, `getAvailability()`, `getDevices()` when exposed, permission state
- reads as `DataView`, writes as `Uint8Array`, strings through `TextDecoder`
- `forget()` so a user can revoke the page's access
- a `watchAdvertisements()` probe, so you can see what your browser says about signal strength
- a "Show every device nearby" button that opens the chooser with no filter, for diagnosing permission problems

## Run it locally

```
python3 -m http.server 8000
```

Open http://localhost:8000/ and press **Find the chip**. Any peripheral advertising the Immediate Alert service (0x1802) appears in the chooser; the page also uses Link Loss (0x1803), TX Power (0x1804), Battery (0x180F) and Device Information (0x180A) when present.

## The device

NXP's Proximity Reporter example from the QN9090 SDK, loaded with LinkServer:

```
LinkServer flash QN9090:QN9090DK6 load qn9090dk6_proximity_reporter_freertos.axf
```

Press SW1 on the board to start advertising. A mild alert turns LED2 on, a high alert makes it flash, and the chip flashes its own LEDs when the link is lost.

## Platform notes, November 2026

Chrome and Edge ship the GATT API without flags on Windows 10+, macOS, ChromeOS and Android 6+. Linux, `getDevices()`, `watchAdvertisements()`, scanning and persistent permissions sit behind `chrome://flags/#enable-experimental-web-platform-features`. Firefox and Safari have stated no plan to implement, so there is nothing on iPhone or iPad except wrapper apps such as Bluefy.

## Licence

MIT. Ifedayo Agboola, Belfast. [dev.to/blackscripts](https://dev.to/blackscripts)
