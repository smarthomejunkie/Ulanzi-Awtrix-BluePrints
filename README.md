# Ulanzi Desktop Clock — Home Assistant Blueprints

Home Assistant Blueprints for the **Ulanzi TC001 Desktop Clock**, for both the **AWTRIX 3** and the **AWTRIX NG** firmware.

By [Smart Home Junkie](https://www.youtube.com/@SmartHomeJunkie).

[![This is the BEST MATRIX DISPLAY CLOCK for Home Assistant!](https://img.youtube.com/vi/N0NKPJzGHuA/maxresdefault.jpg)](https://www.youtube.com/watch?v=N0NKPJzGHuA)

*Click the image to watch the video.*

---

## 📖 Documentation

The manuals live on their own pages. Pick the one that matches the firmware on your clock:

### ➡️ **[AWTRIX 3 Blueprints — Manual](AWTRIX-3-MANUAL.md)**

The original set of blueprints for the AWTRIX 3 firmware. Mature, widely used, and still fully supported.

### ➡️ **[AWTRIX NG Blueprints — Manual](AWTRIX-NG-MANUAL.md)**

The rebuilt set for the new AWTRIX NG firmware. Same ideas, new firmware API, plus a few things AWTRIX 3 could not do.

**Not sure which one you need?** See [Which firmware am I running?](#which-firmware-am-i-running) below.

---

## What is the Ulanzi Desktop Clock?

The Ulanzi TC001 is a small desk clock built around a **32 × 8 RGB LED matrix** — 256 individually addressable pixels behind a smoked front panel. Out of the box it is a pleasant but fairly closed little gadget.

Inside, it is an **ESP32**. That is the interesting part, because it means the stock firmware can be replaced with something far more capable.

The hardware you get to play with:

| | |
|---|---|
| **Display** | 32 × 8 RGB LED matrix |
| **Indicators** | Three separate pixels along the right-hand edge |
| **Sensors** | Temperature, humidity, and an ambient light sensor for automatic brightness |
| **Buttons** | Three — left, select, right |
| **Sound** | Built-in buzzer (and a DFPlayer module on modified units) |
| **Power** | USB-C, with an internal battery so it keeps running unplugged |

---

## What is AWTRIX?

**AWTRIX** is open-source replacement firmware by [Blueforcer](https://github.com/Blueforcer) that turns the clock into a proper smart home display. It rotates through a loop of small "apps" — the time, the date, the temperature — and, crucially, lets *you* push your own apps into that loop over Wi-Fi.

That is where these blueprints come in. They connect Home Assistant to the clock over MQTT, so anything Home Assistant knows can end up on your desk: energy prices, the next train, CO₂ levels, your calendar, the shopping list, a countdown to your holiday.

No YAML, no MQTT topics, no JSON. You pick a sensor from a dropdown, choose some colours, and it appears on the clock.

### AWTRIX 3 and AWTRIX NG

**AWTRIX 3** is the established version — the one most clocks are running today.

**AWTRIX NG** is the next generation. It keeps the same idea but rewrites how the clock is controlled: new addresses for the messages, new names for every setting, and much stricter checking, so a mistake tells you exactly what is wrong instead of silently doing nothing. It also adds things AWTRIX 3 never had — richer scrolling modes, 16-stop colour palettes, weather overlays drawn on top of any app, and small programs that run on the clock itself.

> ⚠️ **The two are not compatible.** AWTRIX 3 blueprints do not work on AWTRIX NG, and vice versa. That is not a bug in the blueprints — the firmware genuinely changed the way it is spoken to. Use the set that matches your firmware.

### Which firmware am I running?

Open the clock's web interface in a browser. AWTRIX NG shows a modern dashboard with tabs for Apps, Display, Sounds and Files; AWTRIX 3 looks noticeably older.

In Home Assistant, look at the device page under **Settings → Devices & Services → MQTT**. If you see a diagnostic sensor called **MQTT prefix**, you are on AWTRIX NG.

---

## What the Blueprints can do

Both sets cover roughly the same ground:

| Blueprint | What it does |
|---|---|
| **Create Notification** | Interrupt the display with a message, with sound and hold-until-dismissed |
| **Create Sensor App** | Put any Home Assistant sensor into the app rotation, with threshold colours |
| **Weather App** | Current conditions, temperature, wind and humidity, in ten languages |
| **Rain Forecast** | The next hours of rainfall as a bar or line chart |
| **List Calendar** | Scroll your agenda, and get a warning before an event starts |
| **List ToDo Items** | Scroll the open items from a to-do list |
| **Countdown Timer** | Count down to a birthday, a holiday, New Year |
| **Toggle Indicators** | Use the three edge pixels as silent status lights |
| **Moodlight** | Flood the whole panel with one colour and use the clock as a lamp |
| **Set App Time** | Change how long each app stays on screen, from a slider |
| **Toggle apps** | Switch the built-in Time, Date, Temperature, Humidity and Battery apps on and off |

**AWTRIX NG adds:** stale-data marking (an app gets a red frame when its sensor stops reporting), animated weather overlays, named notifications you can dismiss individually, and full rotation ordering — so you decide exactly which apps run and in what order.

Each automation you create from a blueprint needs its own **toggle helper** to switch it on and off. Both manuals walk you through that.

---

## Where to get things

| What | Where |
|---|---|
| **The Blueprints** | [Sponsor on Ko-Fi](https://ko-fi.com/s/0d1e4419bd) — lifetime updates, re-download whenever a new version is released |
| **AWTRIX 3 firmware** | [github.com/Blueforcer/awtrix3](https://github.com/Blueforcer/awtrix3) |
| **AWTRIX 3 documentation** | [blueforcer.github.io/awtrix3](https://blueforcer.github.io/awtrix3/#/README) |
| **AWTRIX NG firmware** | [github.com/Blueforcer/awtrix-ng](https://github.com/Blueforcer/awtrix-ng) |
| **AWTRIX NG documentation** | [blueforcer.github.io/awtrix-ng/](https://blueforcer.github.io/awtrix-ng/) |
| **Icons** | [developer.lametric.com/icons](https://developer.lametric.com/icons) |

---

## Quick start

1. **Flash AWTRIX** onto the clock and connect it to your Wi-Fi.
2. **Enable MQTT** on the clock, pointed at the same broker Home Assistant uses. On AWTRIX NG, also switch on **Home Assistant discovery**.
3. **Copy the blueprints** into `config/blueprints/automation/smarthomejunkie/` in Home Assistant.
4. **Reload** — *Developer Tools → YAML → Reload Blueprints*, or restart Home Assistant.
5. **Upload the icons** you want into the `/ICONS` folder using the file manager in the clock's web interface, and the alert melody into `/MELODIES`.
6. **Create a toggle helper**, then create your first automation from a blueprint.

The full details, field by field, are in the two manuals linked at the top.

---

## Support

- **Video guides:** [Smart Home Junkie on YouTube](https://www.youtube.com/@SmartHomeJunkie)
- **Website:** [smarthomejunkie.net](https://smarthomejunkie.net)
- **Firmware problems** — bugs in AWTRIX itself — belong in Blueforcer's repositories, not here.

---

*© 2026 Smart Home Junkie. You may not copy, reproduce, distribute, transmit, modify, create derivative works, or in any other way exploit any part of this copyrighted material without prior written permission from Smart Home Junkie.*
