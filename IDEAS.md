# IDEAS

Scratchpad for Home Assistant. Automations to write, hardware to buy, bigger projects to pursue. Not commitments — sift before promoting to an automation/script in this repo or a hardware purchase.

Same conventions as `infra/IDEAS.md`: dated entries when context matters; rank by "would I actually use this" rather than "is it possible to build."

---

## Automations

Each pitched as: trigger → action → why.

### Audio / Sonos

- **Morning weekday wake-up** — 07:00 Mon-Fri, if `person.brian == home`, play a "morning" Apple Music playlist on the Sonos Move at 30% volume, ramp to 45% over 10 minutes. Why: shifts the from-bed-to-coffee window from "phone alarm + silence" to "music-driven start."
- **Bedtime all-quiet** — 23:00 daily, stop all `media_player.*`, set Sonos Move volume to 25 for next morning. Why: avoids the "speaker blast at 7am because someone left it at 80% yesterday."
- **Returning home → Sonos resumes** — `person.brian` state change from `not_home` → `home`, if last_played < 2h ago, resume on Sonos Move. Why: avoids the "open Sonos app, find the playlist, hit play" friction every time you walk in.
- **Vinyl mode** — when the amp turns on (smart plug power state) → set all Sonos zones to volume 0 and pause anything playing. Why: prevents the room-jukebox-while-listening-to-vinyl conflict.

### Weather (Ecowitt-driven)

- **Heat warning** — if outdoor temp > 90°F sustained 10 min, send Pushover. Why: separate from go-wx's existing alerting; useful for "is now a bad time to go run?"
- **Freeze warning** — if outdoor temp < 32°F at 22:00, send Pushover with "bring in plants / cover the spigot." Why: actionable nightly check.
- **Open-the-windows** — if outdoor temp 65-75°F AND indoor temp > outdoor + 5°F AND humidity < 60%, suggest opening windows via Pushover (max once per 3h). Why: free A/C, but only when conditions actually warrant.
- **Rain accumulation alert** — if Ecowitt rain_today > 1 inch, Pushover. Why: signals real downpour vs ambient drizzle for things like sump-pump check, basement inspection.
- **Wind gust alert** — if wind_gust > 40 mph, Pushover. Why: heads-up for outdoor furniture, trash cans, awnings.

### Presence + status

- **Daily morning brief on Sonos** — 07:15 weekdays, TTS announcement: current outdoor temp, today's high/low, any active rain, calendar events for the day. Why: replaces opening 3 apps to get out the door.
- **Last person leaves → "house away" mode** — when all `person.*` → `not_home`, turn off all media_players, set thermostat (when added) to away setpoint, pulse a status LED somewhere. Why: removes manual checks before leaving.

### Future (needs more hardware)

- **Doorbell → Sonos announcement** — wire a smart doorbell, route press to a TTS "someone's at the door" on all Sonos zones. Why: kills the "missed the doorbell because the music was up" problem.
- **Motion-triggered evening lights** — if motion in hallway AND sun is below horizon AND no lights already on, turn on hallway light to 40%. Auto-off after 10 min no motion. Why: hands-free nighttime navigation.
- **Door-open-too-long** — if any exterior door sensor stays `open` > 5 min, Pushover "you left the door open." Why: classic, prevents AC bills + critter entry.

---

## Hardware

Ranked by "obvious-next-thing" → "interesting-someday." Prices approximate, USD.

### Priority 1 — unlocks Zigbee ecosystem

| Item | Cost | Purpose |
|---|---|---|
| Sonoff Zigbee 3.0 USB Dongle Plus (CC2652P or EFR32MG21) | ~$25 | USB stick that plugs into huron, runs Zigbee2MQTT. Once you have this, every $10-30 sensor is a new HA entity. |
| 2× Aqara Zigbee motion sensors | $30-50 | Hallway + kitchen. First "real" sensor automation in a useful spot. Battery-powered, no wiring. |
| 2× Aqara Zigbee door/window sensors | $30-50 | Front door + back door / patio. Enables door-left-open + house-away. |
| Sonos Era 100 (per the original two-room plan) | $249 | Second Sonos in a fixed room (kitchen?). Stereo-pair-able if you decide to. |

### Priority 2 — useful first additions

| Item | Cost | Purpose |
|---|---|---|
| 4× Aqara Zigbee temp/humidity sensors | $40-60 | Per-room indoor monitoring (bedroom, office, living room, basement). Cheaper than running a single console per room. |
| Aqara water leak sensors (2-3 units) | $30-45 | Under the kitchen sink, bathroom toilet base, water heater pan. The "alert me before $5000 damage" devices. |
| Smart plug (Zigbee — Sonoff or Innr) for the amp/turntable | $15 | Drives the "vinyl mode" automation. Also: power monitoring, lets you alert if amp left on overnight. |

### Priority 3 — bigger projects

| Item | Cost | Purpose |
|---|---|---|
| Aqara FP2 mmWave presence sensor | $80 | Detects actual *presence* (someone sitting still) not just motion. Game-changer for room-based automation. One per main living area. |
| Lutron Caseta starter kit (hub + 2 dimmers) OR equivalent Zigbee no-neutral switches | $150-250 | Smart light switches that don't require a neutral wire (older homes). Caseta is rock-solid but proprietary; Aqara T1 is Zigbee + cheaper. |
| ESPHome BME280 air-quality DIY board (ESP32 + sensor) | $15-25 | Multi-axis indoor air monitoring (CO2, VOC, PM2.5 if you add SGP30/SCD41). DIY route, fun project. |
| Sonos Port (vinyl integration) | $449 | The deferred-from-original-plan vinyl bridge. Connects the amp's tape-out to Sonos zones. |
| Battery-backup UPS (CyberPower CP1500AVRLCD or similar) | $150-200 | Cover huron + detroit + eero through a 10-min power blip. Both servers are SFFs that draw ~30W idle each. |

### Priority 4 — speculative / specific use case

| Item | Cost | Purpose |
|---|---|---|
| Smart thermostat (Ecobee Premium or Honeywell T9) | $200-280 | HVAC scheduling + presence-based setbacks. Worth it only if HVAC is the right kind (24V central) and not already controlled by something modern. |
| Reolink ONVIF cameras (RLC-810A or similar PoE) | $60-100 each | Integrate via HA's ONVIF for motion → record-clip-to-NAS. PoE needs a switch. |
| Aqara G4 doorbell | $130 | Battery + cloud-optional, integrates with HA via local Aqara hub or HomeKit bridge. |
| Wyoming protocol stack: Whisper + Piper + Wyoming Satellite (M5Atom or Raspberry Pi Zero 2 W) | ~$30 per satellite + free SW | Local voice control. "Alexa, but local + no cloud." Each room gets a $30 satellite. Compute (Whisper STT, Piper TTS) runs as Docker containers on huron alongside HA. |

---

## Projects

Bigger initiatives — each a multi-step buildout, not a single purchase.

### 1. Zigbee buildout (Priority 1, ~$100 in hardware to start)

The unlock for everything room-sensor-shaped. Plug the Sonoff dongle into huron, add a `zigbee2mqtt` Docker service alongside HA in compose, pair the first sensors. Once joined to the network, sensors appear as HA entities automatically (via the MQTT broker). Standard HA pattern.

Open questions: Mosquitto MQTT broker as a separate container or use HA's built-in? (Separate is more standard, easier to share with future ESPHome devices.)

### 2. Vinyl-into-Sonos integration

The deferred Phase 7 from the original plan. Three approaches:

| Approach | Cost | Trade-off |
|---|---|---|
| Sonos Port | $449 | Plug into existing amp's tape-out; vinyl streams to all Sonos zones (~75ms latency). Keeps amp. |
| Sonos Amp | $699 | Replaces amp entirely; drives existing passive speakers as a Sonos zone; has phono line-in. Best if amp is dying. |
| Era 100 USB-C line-in | $20 adapter | Cheapest: amp tape-out → Era 100 line-in → other zones. Workable. |

Decide based on where the turntable physically lives and the amp's health.

### 3. Local voice assistant (no cloud)

Wyoming protocol architecture. Three Docker containers alongside HA on huron:

- **Wyoming Whisper** (speech-to-text) — runs `whisper.cpp` or `faster-whisper`, ~1 GB RAM
- **Wyoming Piper** (text-to-speech) — ~500 MB RAM
- **Wyoming Sonos** (optional — uses Sonos as the audio output) OR per-room `Wyoming Satellite` device (M5Atom Echo, ~$30 each)

End state: "Hey Jarvis, what's the temperature outside?" → satellite captures audio → Whisper transcribes → HA intent matches → Piper speaks the answer back through the same speaker. No Amazon / Google / Apple in the loop.

Worth it if: you want HA-as-voice-hub and don't want to keep adding more Echo Dots.

### 4. Energy monitoring

Shelly EM or Iotawatt clamped onto the main panel. HA gets per-circuit power draw, can:
- Notice when heavy loads kick on (HVAC, dryer)
- Generate "your dishwasher just finished" automations
- Track usage patterns for cost analysis

Cost: $80-150 for the device. Wiring is in the breaker panel — comfortable DIY or hire an electrician.

### 5. Per-room dashboards

A dedicated 7-10" touch panel in each room running HA's Lovelace UI. Wallpanel mode + Cast displays. Quick at-a-glance status + tap controls.

Hardware options: cheap Android tablets ($60-100 used), Raspberry Pi + small LCD, dedicated Aqara MagicCube touch.

### 6. Smart-home backup story

Currently nothing is backed up (per the earlier infra audit, /opt/media has no backup, and HA's state — `.storage/`, `home-assistant_v2.db` — is git-ignored so not backed up either).

For HA specifically:
- Enable HA's built-in snapshot via Settings → System → Backups
- Snapshot to `/opt/home-assistant/config/backups/` (git-ignored)
- rsync the backups dir off to a USB drive on chicago (or to the future external HDD per the infra IDEAS entry)

This is small ($0-100 depending on USB drive) and high-value (HA config + history + integrations restored in one click after a disk failure).

### 7. Family-reaching notifications (alerts to non-HA users)

**Surveyed 2026-05-20.** Use case: door-left-open / leak-detected / similar HA alerts need to reach spouse (and eventually other family) in addition to Brian. Spouse doesn't use HA and shouldn't have to. Pushover via secrets.yaml works for Brian only — needs broader fan-out.

**Provisional pick: HA Companion app on spouse's iPhone.** Free, native iOS push, works off-LAN over cellular with no Tailscale on her phone (notifications travel via Apple APNs through HA's free Nabu Casa push proxy — only huron needs outbound internet, which it has). Setup is ~5 min: create a HA user for her, install Home Assistant from App Store, log in once on LAN, allow notifications. From then on she's reachable as `notify.mobile_app_<her_device>` everywhere her phone has internet.

Critically: she does NOT need to "use" HA. The Companion app is just a notification destination. She never opens it again after install.

**Channels surveyed (ranked for this household's spouse-with-iPhone use case):**

| Channel | Recurring cost | Friction for spouse | Off-LAN delivery | HA integration | Why not picked |
|---|---|---|---|---|---|
| **HA Companion (iOS push)** | **$0** | Install app, log in once | ✅ via APNs | First-party | — picked |
| Pushover Family / Delivery Group | $5 one-time per device | Install app, $5 in-app | ✅ via Pushover servers | First-party | App brand unfamiliar; HA Companion is just as good and free |
| Telegram bot + family group | $0 | Install Telegram, accept group invite | ✅ via Telegram servers | First-party, two-way (inline buttons) | Right answer for 3+ recipients on mixed OS; overkill for 1 spouse with iPhone |
| Twilio SMS | ~$15/mo (10DLC reg + number + per-SMS) | None (regular text) | ✅ cellular | First-party | Only worth it if recipient won't install any app (flip phone). Spouse has iPhone. |
| Signal | $0 | Install Signal, register | ✅ via Signal servers | Community (signal-cli daemon on huron) | Heavier setup (~30 min signal-cli + dedicated number registration). Privacy-leaning families only. |
| Discord webhook | $0 | Install Discord, join server | ✅ | Webhook (trivial) | Gamer-branded; spouse unlikely to be in Discord. Useful for tech-team channels. |
| WhatsApp | — | None (already installed by most) | ✅ | Brittle. Official Cloud API needs Meta Business Verification; unofficial (Baileys etc) gets banned. Avoid. |
| iMessage | $0 | None | ✅ | Requires always-on Mac with AppleScript hacks. Don't. |
| ntfy (self-host on huron) | $0 | Install ntfy app, subscribe to topic URL | ✅ | REST | Compelling for technical recipients; overkill for spouse-only. |
| Email-to-SMS carrier gateway | $0 | None | ✅ | SMTP notify | Carriers are killing these (AT&T already partial). Don't build on it. |

**Implementation pattern (whichever channel is picked):** add a second `notify.*` action to the automation alongside the existing `notify.pushover` for Brian. Both fire in parallel; either failing doesn't block the other. Example:

```yaml
action:
  - service: notify.pushover                       # Brian
    data: { title: "...", message: "..." }
  - service: notify.mobile_app_spouse_iphone       # Spouse
    data: { title: "...", message: "..." }
```

**Open questions:**
- Once spouse is wired, is it worth dropping Pushover entirely and switching Brian to `notify.mobile_app_<brians_iphone>` too? Same iOS push UX, free, removes the third-party dependency. Pushover keys would stick around only for the chicago-side rip-dvd Pushover pings (which don't go through HA).
- For "important" alerts (leak detected, smoke detected eventually), is Companion-app push enough? APNs is generally reliable but not 100% — Twilio SMS as a redundant second channel for the truly-critical handful of alerts may be worth ~$15/mo. Revisit when leak sensors land.
- When other family (parent on Android, sibling on iPhone, etc.) needs to be looped in, does the math flip toward Telegram? At 3+ recipients on mixed OS, Telegram's group-chat fan-out + zero per-recipient cost starts winning.

---

## Open questions (not yet decided)

- **Zigbee vs Z-Wave vs Matter for sensors?** Default to Zigbee (cheapest, biggest ecosystem). Z-Wave has better mesh range but pricier; Matter still has device-support gaps as of 2026. Revisit in 12 months.
- **MQTT broker location?** HA's built-in (Mosquitto add-on equivalent), or a separate Docker container? Separate is cleaner for sharing with future ESPHome / Frigate / etc.
- **Voice in addition to or instead of Echo Dot?** The Echo handles "play X on Sonos" today without HA. Local voice via Wyoming would let HA do everything (lights, scenes, complex queries) but requires per-room satellites. Decision when there's a 2nd or 3rd Sonos and the "I want voice in another room" friction becomes real.
- **Storage location for camera recordings, if cameras get added?** detroit's DAS has room (2.6 TB free) but mixing security clips with media is messy. Possibly a separate Z-Wave/RTSP NVR like Frigate with its own bind-mount.
