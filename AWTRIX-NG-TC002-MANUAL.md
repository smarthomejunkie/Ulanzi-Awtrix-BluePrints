[← Back to overview](README.md)

# AWTRIX NG Blueprints for the Ulanzi TC002 — User Manual

Blueprints by **Smart Home Junkie** for the **AWTRIX NG** firmware on the **Ulanzi TC002**.

**⬇️ Download the blueprints:** sponsor on **[Ko-fi](https://ko-fi.com/s/0d1e4419bd)** — lifetime updates, re-download whenever a new version is released.

[![
Install Awtrix NG on the Ulanzi TC002: Turn It Into a Home Assistant Voice Display!](https://img.youtube.com/vi/b_JTOygN-Y0/maxresdefault.jpg)](https://www.youtube.com/watch?v=b_JTOygN-Y0)

*Click the image to watch the video.*

> **Have a Ulanzi TC001?** Use the **[AWTRIX NG TC001 manual](AWTRIX-NG-TC001-MANUAL.md)** instead. Since V6.1 there are two separate sets of AWTRIX NG blueprints:
>
> | Set | Files | Blueprint names | Manual |
> |---|---|---|---|
> | **TC001** | `awtrix_ng_*.yaml` | *AWTRIX NG …* | [TC001 manual](AWTRIX-NG-TC001-MANUAL.md) |
> | **TC002** | `awtrix_ng_tc002_*.yaml` | *AWTRIX NG TC002 …* | this manual |
>
> The TC002 set is made specifically for the 52 × 16 display of the TC002. If you used the `awtrix_ng_*.yaml` blueprints on your TC002 before, read [Appendix B](#appendix-b--moving-from-the-tc001-set) first.

---

## Contents

**Getting started**

1. [What you need](#1-what-you-need)
2. [Installing a blueprint](#2-installing-a-blueprint)
3. [Creating the helpers](#3-creating-the-helpers)
4. [Fields you will see in almost every blueprint](#4-fields-you-will-see-in-almost-every-blueprint)

**The blueprints**

5. [Create Notification](#5-create-notification)
6. [Create Sensor App](#6-create-sensor-app)
7. [Weather App](#7-weather-app)
8. [Rain Forecast](#8-rain-forecast)
9. [List Calendar](#9-list-calendar)
10. [List ToDo Items](#10-list-todo-items)
11. [Countdown Timer](#11-countdown-timer)
12. [Toggle Built-in Apps](#12-toggle-built-in-apps)
13. [Toggle Indicators](#13-toggle-indicators)
14. [Moodlight](#14-moodlight)
15. [Set App Time](#15-set-app-time)

**Reference**

16. [Troubleshooting](#16-troubleshooting)
- [Appendix A — Effects, overlays and palettes](#appendix-a--effects-overlays-and-palettes)
- [Appendix B — Moving from the TC001 set](#appendix-b--moving-from-the-tc001-set)

---

## 1. What you need

| | |
|---|---|
| **Firmware** | AWTRIX NG **1.2.2 or newer** on your clock |
| **Home Assistant** | 2025.2.0 or newer |
| **Integration** | The MQTT integration, connected to the same broker as your clock |
| **On the clock** | MQTT enabled, and **Home Assistant discovery** switched on |

### Why Home Assistant discovery matters

Every blueprint needs to know your clock's **MQTT topic prefix** — the piece of text at the front of every message it listens to. With HA discovery on, AWTRIX NG creates a sensor called **MQTT prefix** on the device, and the blueprints read the prefix straight out of it. That is the whole reason you can just pick your clock from a dropdown instead of typing topics.

To check: go to **Settings → Devices & Services → MQTT → your AWTRIX device**. You should see a diagnostic sensor named *MQTT prefix* with a value like `awtrixNG` or `a4cf12ab34cd`.

If discovery is off, or you have a second clock that is not in Home Assistant, use the **Extra MQTT prefixes** field instead — see [section 4](#awtrix-displays--extra-mqtt-prefixes).

### About the TC002

| | Ulanzi TC002 |
|---|---|
| **Display** | 52 × 16 pixels. Normal apps and notifications are drawn at double size on a 26 × 8 grid, so they fill the display (the clock's **Enlarge apps** setting) |
| **Text that fits without scrolling** | About 6 characters, about 4 next to an icon. With Enlarge apps off, about 13 |
| **Built-in apps** | Time and Status |
| **Sound** | MP3 files and melodies on the speaker. No DFPlayer |
| **Indicators** | Three small groups of pixels on the right-hand edge |
| **Light sensor** | No, so automatic brightness does nothing |

The **Create Sensor App** blueprint does not use the double-size grid for two lines: it draws an AWTRIX NG **layout** on all 52 × 16 pixels, so it can show an icon with two lines of text. See [chapter 6](#6-create-sensor-app).

---

## 2. Installing a blueprint

Download the blueprints from **[Ko-fi](https://ko-fi.com/s/0d1e4419bd)**. Use the `awtrix_ng_tc002_*.yaml` files for the TC002.

1. In Home Assistant, go to **Settings → Automations & Scenes → Blueprints**.
2. Click **Import Blueprint**, or copy the `.yaml` file into `config/blueprints/automation/smarthomejunkie/`.
3. If you copied files manually, go to **Settings → Tools → YAML → Automations** (or restart Home Assistant).
4. Back on the Blueprints page, click the blueprint and choose **Create Automation**. The TC002 blueprints are the ones whose name starts with **AWTRIX NG TC002**.

Each blueprint can be used **as many times as you like**. One automation per sensor, per calendar, per countdown. They do not interfere with each other as long as each one targets a different app name. The one exception is [Rain Forecast](#8-rain-forecast), which always uses the same app name — use it once per clock.

---

## 3. Creating the helpers

Nearly every blueprint is driven by a **toggle helper** — a simple on/off switch you create in Home Assistant. The blueprint watches it: flip it on, the app appears on the clock; flip it off, the app is removed.

**To create one:** **Settings → Devices & Services → Helpers → Create Helper → Toggle**. Give it a clear name like `AWTRIX Living Room Temp App`.

You then get an entity such as `input_boolean.awtrix_living_room_temp_app`, which you can put on a dashboard, flip from another automation, or control by voice.

> **Tip:** name your helpers after the app they control. Once you have ten of them, `input_boolean.awtrix_toggle_3` will mean nothing to you.

The **Set App Time** blueprint uses a **Number** helper instead of a toggle, and **Toggle Built-in Apps** needs five toggle helpers — one per built-in app.

---

## 4. Fields you will see in almost every blueprint

Rather than repeat these in every chapter, they are explained once here. Each blueprint chapter then only covers what is unique to it.

### AWTRIX Displays / Extra MQTT prefixes

**AWTRIX Displays** — pick one or more clocks. The list is filtered to MQTT devices made by *Blueforcer*, so only AWTRIX hardware shows up. Select several and the same app is pushed to all of them.

**Extra MQTT prefixes** — for clocks that are *not* in Home Assistant. Type the prefix exactly as it appears in the clock's MQTT settings (`mqttPrefix`), or the 12-character device ID if that field is empty. One entry per line.

You can use either field, or both. Leave *Extra MQTT prefixes* empty if you do not need it — most people never touch it.

### Toggle Helper

The on/off switch that controls the app. See [section 3](#3-creating-the-helpers).

- **Helper turns on** → the app is created and shown on the clock.
- **Helper turns off** → the app is deleted from the clock.

The app also updates by itself whenever its source data changes, as long as the helper is on.

### Icon

The 8×8 image shown to the left of the text. Enter an **icon ID** (a number, e.g. `2422`) or a **file name** of an icon stored on the clock.

- Browse and download icons at **[developer.lametric.com/icons](https://developer.lametric.com/icons)**.
- Upload them in the AWTRIX web interface on the **Icons** tab (**Icons → Add**). The file name without its extension is the icon ID: `2422.gif` is icon `2422`.
- The same tab installs icons from the [AWTRIX Hub](https://awtrix.de/icons). Those are used by their name.
- Leave the field empty for no icon — the text then uses the full width of the display.
- Animated GIFs work. A GIF that is as wide as the app (26 pixels, because apps are drawn at double size) becomes a background image instead of an icon.
- The Create Sensor App has a larger icon space and more icon options — see [chapter 6](#fields--icon).

### Push Icon

What the icon does while the text scrolls past it.

| Option | Behaviour |
|---|---|
| **Icon doesn't move** | Icon stays put, text scrolls past it |
| **Icon moves with text and will not appear again** | Text shoves the icon off screen once; it stays gone |
| **Icon moves with text but appears again when the text starts** | Icon is pushed off, then returns on every scroll cycle *(default)* |

In the Create Sensor App, Push Icon only works in one large line with the clock's Enlarge apps setting on. See [chapter 6](#two-lines-or-one-large-line).

### Text Case

| Option | Result |
|---|---|
| **Use global setting** | Follows the clock's own uppercase setting *(default)* |
| **Force Uppercase** | `hello` → `HELLO` |
| **Show as you entered it** | Text is drawn exactly as typed |

There is no lowercase option — the matrix font has no lowercase-only mode.

### Background Color / Text Color

Standard Home Assistant colour pickers. Background is black by default; text is white.

Background colour is **ignored when you set an Effect** — an effect owns the whole screen.

### Gradient 1 / Gradient 2

Set these to **two different colours** to paint the text as a gradient from the first colour to the second. Leave both the same (the default) and the plain Text Color is used.

Example: Gradient 1 = yellow, Gradient 2 = red gives a "heat" look that runs across the whole string.

Only Create Notification and Countdown Timer have the gradient fields. The other blueprints offer Rainbow Colors only. The Create Sensor App has a **background** gradient instead — see [chapter 6](#about-the-background-gradient).

### Rainbow Colors

Paints the text through the full colour wheel. **Overrides the gradient** if you set both.

### Repeat

How many times scrolling text runs across the screen before the app's turn ends.

- `0` — off *(default)*. The app stays for the Duration instead.
- `1` — long text is shown once, beginning to end, then the rotation moves on.
- `2`, `3`, … — that many full passes.

Repeat does nothing if the text is short enough to fit on screen, because there is nothing to scroll.

> **Changed from AWTRIX 3:** this field used to be `-1` for "off". In AWTRIX NG it is `0`. If you type `-1` here it will not work.

### Duration (in seconds)

How long the app stays on screen during each rotation. `0` uses the clock's global app time (7 seconds unless you changed it — see [Set App Time](#15-set-app-time)).

### Lifetime (in seconds) / On lifetime expiry

**Lifetime** is a dead-man's switch. If the app is not refreshed within this many seconds, the clock acts on it. `0` means the app lives forever.

**On lifetime expiry** decides what "acts on it" means:

| Option | Result |
|---|---|
| **Remove the app** | The app disappears from the rotation *(default)* |
| **Mark the app as stale** | The app stays, with a thin dark-red frame drawn around it |

This is how you spot a dead sensor. Set Lifetime to a bit more than your sensor's normal update interval — say `600` for a sensor that reports every 5 minutes — and the app will quietly vanish (or get a red frame) if the sensor stops reporting.

### Switch to app on value change

When on, the clock jumps straight to this app the moment its value changes, instead of waiting for its turn in the rotation. In the Countdown Timer this field is called **Switch to app on update**.

> **Careful:** for a sensor that changes every few seconds, this makes the clock unusable and floods your MQTT broker. Use it for doorbells and alarms, not for power meters.

### Scroll the text? / Scroll Speed Percentage

**Scroll** off = the text sits still and anything too long is cut off. **Scroll** on = long text moves across the screen.

About 6 characters fit before the text has to move, about 4 next to an icon. See [About the TC002](#about-the-tc002).

**Scroll Speed** is a percentage of the clock's normal speed. `100` is normal, `50` is half speed, `200` is double. `0` freezes the text where it starts.

### Effect

An animated background drawn behind the text. See [Appendix A](#appendix-a--effects-overlays-and-palettes) for the full list of 19 and what each one looks like. Choose **None** for a plain background.

### Sound File / Track and Alert Melody (RTTTL)

Two ways to make a noise, used by the blueprints that can play an alert:

- **Sound File / Track** — the name of a sound stored on the clock, without its extension: an MP3 (`/MP3`, without the `.mp3`) or a melody file (`/MELODIES`, without the `.txt`). The TC002 has a speaker and no DFPlayer.
- **Alert Melody (RTTTL)** — a melody written inline, in the old Nokia ringtone format. Used only when the Sound File field is empty.

The default melody, `alert:d=4,o=5,b=180:8c6,8p,8c6,8p,8c6`, is three short beeps. You can test and store melodies in the clock's web interface on the **Audio** tab before pasting them in.

Sound is silent if the clock's **volume** is set to `0` — no error, just no noise.

The blueprints send sound in the format introduced with AWTRIX NG **1.2.2**, so the clock needs that firmware or newer.

---

## 5. Create Notification

**File:** `awtrix_ng_tc002_create_notification.yaml`

### What it does

Interrupts whatever is on screen with a message, for as long as you want. Unlike the app blueprints, a notification is not part of the rotation — it takes over the display immediately.

Turn the toggle helper **on** to show the notification, **off** to dismiss it.

### When it runs

- Toggle helper goes **on** → notification is sent.
- Toggle helper goes **off** → notification is dismissed.

### Fields unique to this blueprint

| Field | What it does |
|---|---|
| **Notification Text** | The message itself. |
| **Notification Name** | Optional label. With a name set, switching the toggle off dismisses *exactly this* notification wherever it sits in the queue, instead of whatever happens to be on screen. Use letters, digits, `-` and `_`. Strongly recommended if you have more than one notification automation. |
| **Play Alert Tone** | Plays a sound when the notification appears. |
| **Hold Notification** | On *(default)*: the notification stays until it is dismissed, ignoring Duration and Repeat. Off: it disappears after the Duration. |
| **Stack Notification?** | On *(default)*: queues behind notifications already waiting. Off: replaces whatever is on screen right now, immediately. |

All other fields are the shared ones from [section 4](#4-fields-you-will-see-in-almost-every-blueprint).

### Example — doorbell

| Field | Value |
|---|---|
| Toggle Helper | `input_boolean.awtrix_doorbell` |
| Notification Text | `Someone at the door!` |
| Notification Name | `doorbell` |
| Icon | `1234` |
| Play Alert Tone | On |
| Hold Notification | On |
| Stack Notification? | Off |
| Text Color | Red |

Then, from your doorbell automation, turn `input_boolean.awtrix_doorbell` on. The clock drops everything and shows the message until you turn the helper off again.

### Example — washing machine finished

| Field | Value |
|---|---|
| Notification Text | `Washing machine is done` |
| Notification Name | `washer` |
| Hold Notification | Off |
| Duration | `15` |
| Stack Notification? | On |
| Rainbow Colors | On |

This one waits politely in the queue and disappears on its own after 15 seconds.

> **Tip:** because both examples use a different **Notification Name**, turning the washer helper off will never dismiss the doorbell message by accident.

---

## 6. Create Sensor App

**File:** `awtrix_ng_tc002_create_sensor_app.yaml` · **Blueprint name:** *AWTRIX NG TC002 Create Sensor App* · **Version:** V2.0

### What it does

Puts Home Assistant information into the clock's app rotation and keeps it up to date. It uses the whole 52 × 16 display of the TC002: a large icon on the left, with **two lines of text** next to it. Each line can show a sensor, a template or a fixed text, and each line can have its own small icon.

```
+----------------+-+--------+-+--------------------------+
|                | | top    | |  Top line                |
|   Main icon    | | icon   | |                          |
|    16 × 16     | +--------+ +--------------------------+
|                | | bottom | |  Bottom line             |
|                | | icon   | |                          |
+----------------+-+--------+-+--------------------------+
```

Need only one value? Leave the bottom line empty, or choose **One large line**, and the app shows the icon and the top line at full height. With the clock's **Enlarge apps** setting on, the icon and the text are then drawn at double size.

This is the workhorse blueprint — most people end up with several copies of it.

### When it runs

- Toggle on → the app is created and the clock switches to it.
- Toggle off → the app is deleted.
- The **Top Line Sensor** or **Bottom Line Sensor** changes (while the toggle is on) → the app is updated. A change to `unavailable` or `unknown` is skipped, so the app keeps its last text.
- Every minute, if **Refresh every minute** is on (while the toggle is on) → the app is sent again.

### What a line shows

Each line has three sources. The first one that is filled in wins:

| Priority | Field | Shows |
|---|---|---|
| 1 | **Text** | Exactly the text you typed. |
| 2 | **Template** | The result of your Home Assistant template. |
| 3 | **Sensor** | The state of the entity, rounded and with its unit, e.g. `21.5 °C`. |

All three are optional. A line with nothing in it is left out.

**If the Bottom Line Sensor, Template and Text are all empty, the bottom line is ignored** and the app is shown as one large line — see [Two lines or one large line](#two-lines-or-one-large-line).

The Sensor field accepts `sensor`, `binary_sensor`, `media_player`, `input_text`, `input_number` and `input_select` entities.

### Fields — App

| Field | What it does |
|---|---|
| **App Name** | **Required.** The name of this app on the clock. See [App naming](#app-naming). |
| **AWTRIX Displays**, **Extra MQTT prefixes**, **Toggle Helper** | See [section 4](#4-fields-you-will-see-in-almost-every-blueprint). |

### Fields — Icon

| Field | What it does |
|---|---|
| **Icon** | The main icon on the left. An icon ID or file name of an icon on the clock, or the web address of a PNG or JPEG picture. Leave it empty for no icon — both lines then use the full width. |
| **Push Icon** | What the icon does while the text scrolls. See [Push Icon](#push-icon). Only works in **one large line** on a clock with **Enlarge apps** on — see the note below. |
| **Icon Width (Optional)** | Width of the space reserved for the icon, `1`–`50` pixels. Empty (or `0`) = `16`. |
| **Icon Height (Optional)** | Height of the space reserved for the icon, `1`–`16` pixels. Empty (or `0`) = `16`. A lower icon is centred vertically. |

The lines always start 1 pixel to the right of the icon space. A wider icon leaves less room for text: with an icon of 32 pixels wide, each line has 19 pixels left.

> **Push Icon and layouts:** AWTRIX NG only moves an icon with the text in a normal app. Two lines (and one line with Enlarge apps off) are drawn with an AWTRIX NG **layout**, in which every icon has its own fixed box and every text scrolls inside its own box. The icons do not move there, whatever Push Icon is set to.

### Fields — Top line and Bottom line

Both sections have the same fields:

| Field | What it does |
|---|---|
| **Sensor** | The entity to show. The app updates whenever it changes. |
| **Template** | A Home Assistant template. Wins over the Sensor. |
| **Text** | A fixed text. Wins over the Template and the Sensor. |
| **Line Icon (Optional)** | A small icon at the left of the line, before the text, in an 8 × 8 space. The text then starts 1 pixel to the right of it. |
| **Font** | The font of the line. Every font fits in the 8-pixel line: `small` *(default)*, `large`, and the Matrix Light and Matrix Chunky fonts. Try a few — they differ in width, so a text that just fits in one may scroll in another. |
| **Color** | The text colour. |
| **Rainbow Colors** | Paints the line in rainbow colours. Overrides the colours below. |
| **Threshold Value (Optional)** | Enter a number to colour the line by its value. As soon as a number is filled in, the Low-value and High-value colours are used instead of the line's Color. Leave it empty for the normal Color. A decimal comma (`19,5`) works too. |
| **Low-value color** | Used when the value is **below** the threshold. |
| **High-value color** | Used when the value is **at or above** the threshold. |
| **Line Background** | Gives this line its own background colour. Off by default. |
| **Line Background Color** | The background colour of this line, used when Line Background is on. Default dark grey. |

A line icon is skipped when the main icon leaves less than 10 pixels for the line.

> **Note:** with a Template or Text, the threshold compares the number in the result, stripping out everything that is not a digit, a dot or a minus sign. So `Power: 450W` is compared as `450`.

### Fields — Layout options

| Field | What it does |
|---|---|
| **Display Mode** | **Two lines** *(default)* or **One large line**. See below. |
| **Text Alignment** | Left *(default)*, Center or Right. Only while the text stands still; scrolling text always starts at the left. |
| **Text Case** | See [Text Case](#text-case). |
| **Background Color** | Plain background colour. Ignored when the Background Gradient is on or an Effect is selected. |
| **Background Gradient** | Fills the background with a vertical gradient. |
| **Gradient Top Color** | Colour of the top row of the gradient. Default dark blue. |
| **Gradient Bottom Color** | Colour of the bottom row of the gradient. Default black. |
| **Effect** | An animated background. See [Appendix A](#appendix-a--effects-overlays-and-palettes). Wins over the gradient and the background colour. |

### Fields — Advanced options

| Field | What it does |
|---|---|
| **Repeat** | How many times a scrolling line runs through its box before the app's turn ends. `0` = off, the Duration is used. |
| **Duration**, **Lifetime**, **On lifetime expiry** | See [section 4](#duration-in-seconds). |
| **Switch to app on value change** | Jump to this app when a sensor changes. See [section 4](#switch-to-app-on-value-change). |
| **Refresh every minute** | Send the app again every minute. Turn this on when a template reads entities other than the two selected sensors — changes to those entities do not update the app by themselves. |
| **Scroll the text?** / **Scroll Speed Percentage** | See [section 4](#scroll-the-text--scroll-speed-percentage). Applies to both lines. |

### Two lines or one large line

| Situation | What you see |
|---|---|
| **Two lines** | Main icon, top line and bottom line, each with its optional line icon. Drawn with an AWTRIX NG layout on the full 52 × 16 pixels. |
| **One large line**, clock's *Enlarge apps* **on** | Main icon and top line, drawn at **double size**: an 8 × 8 icon becomes 16 × 16 and the text is twice as tall. Push Icon works here. |
| **One large line**, clock's *Enlarge apps* **off** | Main icon and top line at normal size, with the top line using the full height of the display. The Top Line Icon sits next to it. |

You get one large line when **Display Mode** is *One large line*, **or** when the Bottom Line Sensor, Template and Text are all empty.

**Enlarge apps** is the clock's own setting under **Display → Text** in its web interface (`enlargeApps`). The blueprint reads it from the clock every time it sends the app, so you never have to set it twice. If the clock does not answer within 2 seconds, the blueprint assumes it is on, which is the firmware default.

In one large line with Enlarge apps on:

- **Icon Width** and **Icon Height** are not used — the clock places the icon itself.
- The **Top Line Icon** is used as the icon only when the main Icon is empty.
- The bottom line, the Bottom Line Icon and the bottom line fields are not used.
- The gradient has 8 steps instead of 16, because the app is drawn on a 26 × 8 grid.
- A main icon taller than 8 pixels (such as a 16 × 16 icon) makes the clock draw the app at normal size instead.

### About the icons

- **An icon stored on the clock keeps its own size.** An 8 × 8 icon in the 16 × 16 space sits small in the middle of it. For a full-size icon in two-lines mode, upload a 16 × 16 icon, or use one large line with Enlarge apps on.
- **A PNG or JPEG from a web address is resized** by the clock to fill its space, pixel by pixel, so pixel art stays sharp. The clock needs internet access for this, and these pictures do not animate.
- **Animated GIFs** play at their own size and are never resized.
- A layout can hold at most four icons. This blueprint uses at most three: the main icon and the two line icons.

### Line backgrounds

Each line can have its own background colour (**Top Line Background** / **Bottom Line Background**). It fills the line from directly after the main icon (including the 1-pixel gap before the text) to the right edge of the display, so it lines up exactly with the gradient, behind the line icon and the text, and **overrides the general Background Color, the Background Gradient and the Effect** for that line. The main icon keeps the general background.

- In **one large line**, the top line background fills the full height next to the icon. With Enlarge apps on, the whole app is drawn as one line, so the top line background becomes the background of the whole app.
- The bottom line background is not used in one large line.

### About the background gradient

AWTRIX NG has no gradient background of its own, so the blueprint draws one: one line per row of the display, each in a colour blended between the top and bottom colour, behind the icons and the text. The text and icon boxes do not paint their own background, so the gradient shows behind them too. Black pixels in an icon stay black.

### Template examples

Each line has its own template, so you can combine two values in one app:

```jinja
{{ states('sensor.living_room_temperature') | round(1) }}°C
```

```jinja
{{ states('sensor.living_room_humidity') | int }}%
```

```jinja
{{ state_attr('media_player.spotify','media_artist') }}
```

```jinja
{{ state_attr('media_player.spotify','media_title') }}
```

### Example — living room climate on two lines

| Field | Value |
|---|---|
| App Name | `living_room_climate` |
| Icon | `2422` (or a 16 × 16 thermometer icon) |
| Top Line Sensor | `sensor.living_room_temperature` |
| Top Line Color | White |
| Bottom Line Sensor | `sensor.living_room_humidity` |
| Bottom Line Icon | `2056` |
| Bottom Line Color | Light blue |
| Background Gradient | On, dark blue to black |

The temperature on top, the humidity with a small drop icon below it.

### Example — CO₂ as one large line

| Field | Value |
|---|---|
| App Name | `bedroom_co2` |
| Icon | `12160` |
| Top Line Template | `{{ states('sensor.bedroom_co2') \| int }}ppm` |
| Top Line - Threshold Value | `1000` |
| Top Line - Low-value color | Green |
| Top Line - High-value color | Red |
| *(bottom line empty)* | |
| Lifetime | `1800` |
| On lifetime expiry | Mark the app as stale |

With the bottom line empty, the app is one large line: a big icon and a big number, green below 1000 ppm and red from 1000 up.

### Example — now playing

| Field | Value |
|---|---|
| App Name | `now_playing` |
| Top Line Sensor | `media_player.spotify` |
| Top Line Template | `{{ state_attr('media_player.spotify','media_title') }}` |
| Bottom Line Template | `{{ state_attr('media_player.spotify','media_artist') }}` |
| Bottom Line Font | `matrix-light6` |
| Icon | the web address of an album-art PNG, or a music icon |

The Top Line Sensor makes the app update on every track change; the templates choose what is shown.

### App naming

You choose the name of the app on the clock yourself, in the required **App Name** field. It is the name the app has on the **Apps** tab of the clock's web interface, where you can drag it to the slot you want. The clock remembers that slot.

- **Give every automation its own App Name.** Two automations with the same name, on the same clock, overwrite each other's app.
- **Allowed characters:** letters, digits, `-` and `_`, up to 32 characters. Anything else, such as a space, is replaced by `_`, and a longer name is cut to 32 characters. `Living Room Climate` becomes the app `Living_Room_Climate`.
- **Changing the App Name** of an existing automation creates a new app on the clock. Switch the toggle helper off first, so the app with the old name is removed; then change the name and switch the toggle back on.
- The name does not depend on a sensor or on the automation, so you can rename the automation, or swap the sensors, without the app changing.

### Upgrading from the earlier version (V1.1)

V2.0 has new fields, so an automation made with V1.1 (or with the shared `awtrix_ng_create_sensor_app.yaml`) needs its fields filled in again:

| V1.1 field | V2.0 field |
|---|---|
| *(the app was named after the sensor)* | **App Name** — required. Enter the old name (e.g. `sensor_bedroom_co2`) to keep the app's slot on the clock |
| Sensor | Top Line Sensor |
| Template (Optional) | Top Line Template |
| Text Color | Top Line Color |
| Use Threshold value? / Threshold Value / Low / High colour | Top Line - Threshold Value / Low / High colour. There is no on/off switch any more: entering a Threshold Value turns the threshold colours on |
| Rainbow Colors | Top Line Rainbow Colors |
| Gradient 1 / Gradient 2 | gone. Use Rainbow Colors, or the Background Gradient |
| Push Icon | Push Icon (one large line with Enlarge apps on only) |
| Background Color, Effect, Text Case | moved to **Layout options** |

---

## 7. Weather App

**File:** `awtrix_ng_tc002_weather_app.yaml`

### What it does

Shows the current weather from any Home Assistant `weather` entity: condition, temperature, wind speed and humidity, in the language you choose.

### When it runs

Toggle on → app created. Toggle off → app deleted. Weather changes → app updated.

### Fields unique to this blueprint

| Field | What it does |
|---|---|
| **Sensor** | Your weather entity (Met.no, KNMI, OpenWeatherMap — anything in the `weather` domain). |
| **Show Weather Text** | Include the condition as words ("partly cloudy"). |
| **Language** | Translates the condition into English, Danish, Dutch, French, German, Italian, Polish, Portuguese, Russian or Spanish. The conditions `exceptional`, `lightning-rainy` and `windy-variant` have no translation and are shown as they are. |
| **Show temperature** | Append the temperature with its unit. |
| **Show Wind Speed** | Append the wind speed with its unit. |
| **Show Humidity** | Append the humidity as a percentage. |
| **Weather Overlay** | Draws animated rain, snow, drizzle, storm, thunder or frost *on top of* the app. See [Appendix A](#appendix-a--effects-overlays-and-palettes). Leave on *Follow device setting* to use whatever the clock is set to globally. |

The four "show" switches build one line of text, joined with dashes. With all four on, in Dutch, you get something like:

```
halfbewolkt 12.4°C - 8.6km/h - 71%
```

### The icon is automatic

This blueprint does **not** have an Icon field. It uses the weather condition itself as the icon name — so the clock looks for an icon file named after the current condition.

Upload icons with these exact names to `/ICONS` on your clock:

`sunny` · `clear-night` · `cloudy` · `partlycloudy` · `rainy` · `pouring` · `snowy` · `snowy-rainy` · `fog` · `hail` · `lightning` · `lightning-rainy` · `windy` · `windy-variant` · `exceptional`

If an icon is missing, the app simply draws without one — no error, just a wider text area.

### Example — Dutch weather with rain overlay

| Field | Value |
|---|---|
| Sensor | `weather.knmi` |
| Show Weather Text | On |
| Language | Dutch |
| Show temperature | On |
| Show Wind Speed | Off |
| Show Humidity | Off |
| Weather Overlay | Rain |
| Scroll Speed Percentage | `80` |
| Duration | `10` |

> **Tip:** turn Wind Speed and Humidity off unless you like long scrolling text. Condition plus temperature reads well at a glance; all four together takes about eight seconds to scroll past.

---

## 8. Rain Forecast

**File:** `awtrix_ng_tc002_rain_forecast.yaml`

### What it does

Draws the next 11 hours of expected rainfall as a bar or line chart across the display. When no rain is expected at all, it shows a text message instead.

### When it runs

Toggle on → app created. Toggle off → app deleted. Weather entity changes (and stays changed for 2 seconds) → chart redrawn.

### Fields unique to this blueprint

| Field | What it does |
|---|---|
| **Sensor** | Your weather entity. It must provide an **hourly forecast** — most do. |
| **Graph Type** | **Bar Chart** (columns growing from the bottom) or **Line Chart** (a connected line). |
| **Graph Color** | Colour of the bars or the line. |
| **Autoscale** | On *(default)*: the chart scales to the highest value in the data, so a light drizzle still fills the screen. Off: the scale is fixed at 0–8, so small amounts of rain barely register. |
| **Custom Text** | Shown when the forecast is completely dry. Default: `No rain expected`. |

This blueprint has no Icon, Text Color, Gradient, Scroll, Repeat or Effect fields — a chart fills the screen, so they would have nothing to do. The icon is taken from the weather condition, exactly as in the [Weather App](#the-icon-is-automatic).

It does have **Text Case** and **Rainbow Colors**. Text Case applies to the Custom Text, which is drawn in the Graph Color. Rainbow Colors paints the chart and the Custom Text in rainbow colours instead of the Graph Color.

### One rain forecast per clock

The app is always called `rain_forecast`. A second automation from this blueprint that targets the same clock overwrites the first one, so use it once per clock.

### Example — bar chart, blue

| Field | Value |
|---|---|
| Sensor | `weather.forecast_home` |
| Graph Type | Bar Chart |
| Graph Color | Blue |
| Autoscale | On |
| Custom Text | `Dry for now` |
| Duration | `10` |

### Reading the chart

Each of the 11 bars is one hour ahead, left to right. Values are precipitation in millimetres, multiplied by 100 and rounded, so the chart shows *relative* rainfall rather than an exact figure — it tells you *when* it will rain and *roughly how hard*, not how many millimetres.

> **Tip:** leave Autoscale on. With it off, a normal shower is a single pixel high and you will think the blueprint is broken.

---

## 9. List Calendar

**File:** `awtrix_ng_tc002_list_calendar.yaml`

### What it does

Two things at once:

1. **A rotating app** that scrolls through your upcoming appointments.
2. **A notification** 15 minutes before an event starts, with an optional alert tone.

Either half can be switched off.

### When it runs

- Toggle on → app created.
- Toggle off → app deleted.
- **Every minute** → the agenda is re-read and the app refreshed.
- **15 minutes before any event** → notification sent (if Show Alerts is on).

### Fields unique to this blueprint

| Field | What it does |
|---|---|
| **Calendar** | The calendar entity to read. |
| **Hours ahead** | How far into the future to look. Default `24`. |
| **Show Timeline?** | On: the app lists all upcoming events. Off: no app is shown at all and you only get the notifications. |
| **Show Whole Day Events?** | Include all-day events (the ones with no start time). |
| **Show Alerts?** | Send a notification 15 minutes before an event starts. It reads `Event <name> starts at <time>`. That text is fixed and in English. |
| **Play Alert Tone** | Add a sound to that notification. |
| **Stack Notification?** | On: queue behind other notifications. Off: replace whatever is on screen. |
| **Custom Text** | Shown when there is nothing in the agenda. Default: `Yay! You have no appointments!`. |
| **Show Empty Calendar** | Off: the custom text is not shown when there are no events. The app is not removed straight away: it keeps its last text until its **Lifetime** runs out. Set a Lifetime of `120` or more when you switch this off, so the app disappears about two minutes after the last event. |
| **Prefix** | Text placed in front of the whole list — useful when you run this blueprint several times for different calendars. |
| **Today Text** | Label placed before today's events, e.g. `Today:`. Leave empty for none. |
| **Tomorrow Text** | Label placed before events on later days, e.g. `Tomorrow:`. Leave empty for none. |

Events are joined with ` // `, and timed events are prefixed with their start time.

### Example — family calendar

| Field | Value |
|---|---|
| Calendar | `calendar.family` |
| Hours ahead | `36` |
| Prefix | `Family: ` |
| Today Text | `Today` |
| Tomorrow Text | `Tomorrow` |
| Show Whole Day Events? | On |
| Show Alerts? | On |
| Play Alert Tone | On |
| Icon | `11899` |
| Custom Text | `Nothing planned` |

On screen you get something like:

```
Family: Today 14:00 Dentist // 19:30 Football // Tomorrow 09:00 Deliver parcel
```

### Example — notifications only

| Field | Value |
|---|---|
| Calendar | `calendar.work` |
| Show Timeline? | **Off** |
| Show Alerts? | On |
| Play Alert Tone | On |

No app in the rotation, just a heads-up 15 minutes before each meeting.

> **Note:** the 15-minute warning time is fixed. Home Assistant does not currently allow a calendar trigger offset to be a blueprint input, so changing it means editing the automation's YAML after you create it.

---

## 10. List ToDo Items

**File:** `awtrix_ng_tc002_list_todo_items.yaml`

### What it does

Scrolls the open items from a Home Assistant to-do list across the clock. Completed items are ignored.

### When it runs

Toggle on → app created. Toggle off → app deleted. The to-do list changes → app updated immediately.

### Fields unique to this blueprint

| Field | What it does |
|---|---|
| **ToDo List** | The `todo` entity to read. |
| **Prefix** | Text in front of the list. Default `To Do:`. |
| **Custom Text** | Shown when the list is empty. Default `Relax! You have nothing to do!`. |
| **Show Empty ToDo List** | Off: the app is removed entirely when the list is empty, rather than showing the custom text. |

The default icon is `29644` — a checklist.

### Example — shopping list

| Field | Value |
|---|---|
| ToDo List | `todo.shopping_list` |
| Prefix | `Shopping:` |
| Icon | `29644` |
| Custom Text | `Nothing to buy` |
| Show Empty ToDo List | Off |
| Scroll Speed Percentage | `90` |

On screen:

```
Shopping: Milk // Bread // Coffee // Apples
```

With **Show Empty ToDo List** off, the app disappears from the rotation entirely once you have ticked everything off — no wasted screen time.

> **Tip:** this pairs beautifully with a voice assistant. "Add coffee to the shopping list" and it is on the clock a second later.

---

## 11. Countdown Timer

**File:** `awtrix_ng_tc002_countdown_timer.yaml`

### What it does

Counts down to a date and time — a birthday, a holiday, New Year — and updates once a minute.

### When it runs

Toggle on → app created. Toggle off → app deleted. **Every minute** → countdown recalculated.

### Fields unique to this blueprint

| Field | What it does |
|---|---|
| **Event Name** | Short name for the event. Doubles as the app name on the clock, so stick to letters, digits, `-` and `_`. The app name is the event name in lowercase, with spaces and anything else turned into underscores: `Summer Vacation` becomes the app `summer_vacation`. |
| **Event Date** | Target date, `YYYY-MM-DD`. |
| **Event Time** | Target time, `HH:MM:SS`, 24-hour. |
| **Show Event Name on Clock** | On: the name is shown in front of the countdown (`Vacation 3d 4h 20m`). Off: only the numbers. |
| **Show Seconds** | Include seconds. On by default. The app only refreshes every minute, so these are approximate. |
| **Event Started Message** | Shown from the moment the event starts until midnight that day. Leave empty to skip straight to the next field. |
| **Event Reached Text** | Shown once the event day is over. Also used on the day itself if the field above is empty. The default is `Now!`. **Clear this field and leave the one above empty, and the app removes itself automatically after the event.** |

The countdown scales itself: days are dropped once there are none left, then hours, so you never see `0d 0h 5m`.

### Example — holiday countdown

| Field | Value |
|---|---|
| Event Name | `Vacation` |
| Event Date | `2027-07-17` |
| Event Time | `09:00:00` |
| Show Event Name on Clock | On |
| Show Seconds | Off |
| Event Started Message | `Bon voyage!` |
| Event Reached Text | *(empty)* |
| Icon | `52` |
| Gradient 1 | Yellow |
| Gradient 2 | Orange |

Shows `Vacation 47d 12h 30m` counting down, switches to `Bon voyage!` on the day, then removes itself from the clock at midnight.

### Example — New Year

| Field | Value |
|---|---|
| Event Name | `NewYear` |
| Event Date | `2027-01-01` |
| Event Time | `00:00:00` |
| Show Seconds | On |
| Event Started Message | `Happy New Year!` |
| Event Reached Text | `Happy New Year!` |
| Rainbow Colors | On |
| Effect | Fireworks |

> **Note:** emoji do not render on the matrix — the font has no glyphs for them and they come out as `?`. Stick to plain text in these fields.

---

## 12. Toggle Built-in Apps

**File:** `awtrix_ng_tc002_toggle_builtin_apps.yaml`

### What it does

Switches built-in apps on and off from toggle helpers. Your rotation order, pushed apps and scripts are left exactly as they are.

> **On the TC002 only the Time toggle does something** — see [On the Ulanzi TC002](#on-the-ulanzi-tc002) below. The blueprint keeps five fields so it matches the TC001 version.

Needs AWTRIX NG **1.2.2** or newer.

### How it works

Every app on the clock has its own on/off switch. The blueprint publishes `true` or `false` to `<prefix>/cmd/apps/<app>/enabled`, for example:

```
awtrixNG/cmd/apps/Date/enabled      false
```

- **Only the named app changes.** Your pushed apps, your scripts and any app you switched off on the clock yourself are never touched.
- A switched-off app stays installed. It keeps appearing in the clock's app list with `enabled: false`, and switching it back on returns it to the slot it had.
- The clock saves every switch, so it survives a reboot.

> **Do not use `<prefix>/cmd/apps/order` to switch single apps.** Its `disabled` list is the *complete* off-list: every app it does not name is switched on, including apps you switched off on purpose.

### When it runs

- One of the five toggle helpers changes → the switch of that one app is sent.
- Home Assistant starts → all five switches are sent (can be switched off).

### Fields

| Field | What it does |
|---|---|
| **Time / Date / Temperature / Humidity / Battery app** | One toggle helper per built-in app. On = the app rotates, off = it is switched off. All five fields need a helper. |
| **Apply on Home Assistant start** | Send all five switches when HA starts, so the clock matches your toggles again after a restart. The clock saves every switch itself, so this is only a safety net. On by default. |

That's it — there is no rotation-order field. Arrange your apps on the **Apps** tab of the clock's web interface; this blueprint never sends or overwrites the order.

### Example

Five helpers. You switch the Temperature helper off, and the blueprint publishes one message:

```
<prefix>/cmd/apps/Temperature/enabled      false
```

The Temperature app leaves the rotation. Time, Date, Humidity and Battery, your pushed apps (a sensor app, the rain forecast, a countdown) and your scripts carry on exactly as before. Switch the helper back on and `true` goes to the same topic.

### On the Ulanzi TC002

The TC002 has only two built-in apps: **Time** and **Status**. The date is part of its clock faces, and it has no Temperature, Humidity or Battery app.

- Only the **Time** toggle does something on a TC002. The clock ignores the other four.
- The **Status** app has no toggle in this blueprint. Switch it on the **Apps** tab of the clock's web interface.
- The blueprint still needs a helper in all five fields.

### Notes

- **Temperature**, **Humidity** and **Battery** only exist on hardware that has the sensor or a battery pin. On a board without them the toggle simply does nothing.
- A helper that is **unavailable or unknown** is skipped. Its app keeps whatever it has on the clock.
- The switches are stored on the clock and survive a reboot.

---

## 13. Toggle Indicators

**File:** `awtrix_ng_tc002_toggle_indicators.yaml`

### What it does

Controls the three small indicator pixels on the right-hand edge of the panel — top, middle and bottom — each from its own toggle helper. They can be any colour, can blink, and can play a sound when they come on.

On the TC002 each indicator is a small group of pixels: a corner at the top and at the bottom, and a short bar in the middle.

Indicators are drawn on top of everything, so they are visible no matter which app is showing. They are ideal for silent status signals: a window left open, the washing machine running, an alarm armed.

### When it runs

Any of the three toggle helpers goes on or off.

### Fields

For **each** of the three indicators:

| Field | What it does |
|---|---|
| **Toggle Helper** | The on/off switch for this indicator. |
| **Indicator color** | Its colour when on. |
| **Blink Speed** | Blink period in milliseconds, `0`–`5000`. `0` means a steady light. `500` is a brisk blink, `1000` a calm pulse. |
| **Play Alert Tone** | Play a sound when this indicator comes on. |

Plus a shared **Alert sound** section with the **Sound File / Track** and **Alert Melody (RTTTL)** fields described in [section 4](#sound-file--track-and-alert-melody-rtttl). All three indicators use the same sound.

The defaults for the three toggle helper fields point at `input_boolean.awtrix_display_indicator_1`, `_2` and `_3`. If you do not have helpers with those names, pick your own — the defaults are only a suggestion.

### Example

| Indicator | Meaning | Colour | Blink | Sound |
|---|---|---|---|---|
| Top | Front door unlocked | Red | `500` | On |
| Middle | Washing machine running | Blue | `0` | Off |
| Bottom | Someone home | Green | `0` | Off |

Then point each toggle helper at a template switch, or flip them from your existing automations.

> **Tip:** reserve blinking for things you actually want to be nagged about. A steady light reads as information, a blinking one reads as a problem.

---

## 14. Moodlight

**File:** `awtrix_ng_tc002_moodlight.yaml`

### What it does

Floods the entire panel with one colour, turning the clock into a small lamp. All apps are hidden while the moodlight is on.

### When it runs

Toggle on → moodlight on. Toggle off → moodlight off, apps come back.

### Fields

| Field | What it does |
|---|---|
| **Brightness** | `0`–`255`. See the warning below. |
| **Color** | The colour to flood the panel with. |
| **Kelvin value** | A colour temperature instead of a colour. `0` disables it. **When Kelvin is anything other than 0, the Color field is ignored.** Typical values: `2200` candlelight, `2700` warm white, `4000` neutral, `6500` daylight. |

### About brightness

⚠️ **Every pixel is lit at once.** That draws far more current and generates far more heat than a normal app. Keep the brightness low — under 200, and lower still on battery. A clock running on battery may switch itself off if you push the brightness too high.

Start at `100`. It is brighter than you expect in a dark room.

### Example — warm evening lamp

| Field | Value |
|---|---|
| Toggle Helper | `input_boolean.awtrix_moodlight` |
| Brightness | `80` |
| Color | *(ignored)* |
| Kelvin value | `2700` |

### Example — red night light

| Field | Value |
|---|---|
| Brightness | `40` |
| Color | Deep red |
| Kelvin value | `0` |

> **Tip:** drive the toggle helper from a sunset trigger and turn it off at bedtime, and the clock becomes an ambient lamp that costs you nothing extra.

---

## 15. Set App Time

**File:** `awtrix_ng_tc002_set_transition_time.yaml`

### What it does

Changes how long each app stays on screen before the clock rotates to the next one, from a Number helper. This lets you put a slider on your dashboard and adjust the pace without opening the clock's web interface.

The setting applies to **every** app on the clock, and it is what a Duration of `0` in the other blueprints falls back to.

### When it runs

The Number helper changes.

### Fields

| Field | What it does |
|---|---|
| **App Time Number Helper** | An `input_number` holding the app time **in seconds**. |

That is the whole blueprint. Everything else is the shared display selection.

### Creating the helper

**Settings → Devices & Services → Helpers → Create Helper → Number**

| Setting | Suggested value |
|---|---|
| Name | `AWTRIX App Time` |
| Minimum | `3` |
| Maximum | `30` |
| Display mode | Slider |
| Step size | `1` |
| Unit of measurement | `s` |

### Example

Set the helper to `5` and each app gets five seconds. Set it to `15` and the clock slows right down.

The clock's own default is 7 seconds.

> **Note:** the seconds you enter are converted to milliseconds for you — AWTRIX NG stores this as `appDurationMs`. You never need to think about that.

---

## 16. Troubleshooting

### Nothing appears on the clock

Work through these in order:

1. **Is the automation running?** Open it and check **Traces**. If it never triggered, the problem is your toggle helper, not the clock.
2. **Did it find your clock?** In the trace, look at the `targets` variable. It should contain your MQTT prefix (e.g. `["awtrixNG"]`). If it is empty, HA discovery is off or the *MQTT prefix* sensor is unavailable — see [section 1](#why-home-assistant-discovery-matters). Fill in **Extra MQTT prefixes** as a workaround.
3. **Is the clock listening?** Subscribe to `<prefix>/cmd/apps/pushed/+/result` in an MQTT client. AWTRIX NG replies to every command it understands.

### The clock replies with an error

AWTRIX NG answers every command on a `/result` topic. `{"ok":true}` means it worked. A failure looks like:

```json
{"ok":false,"error":{"code":"validationFailed","message":"unknown key","field":"pushIcon"}}
```

The `field` names the culprit. If you see an old AWTRIX 3 key name there, you are running an old blueprint — replace it with the NG version.

### The app appears but disappears again

Your **Lifetime** is shorter than the interval at which the source data updates. Raise it, or set it to `0` for an app that should never expire.

### Apps switched themselves on or off

Something published to `<prefix>/cmd/apps/order`. That topic sets the whole arrangement at once: its `disabled` list is the complete off-list, so every app it names is switched off and every app it does not name is switched on.

The [Toggle Built-in Apps](#12-toggle-built-in-apps) blueprint cannot cause this: it only uses the switch of one app at a time and never publishes to this topic. If you still run an older copy of that blueprint, replace it with the current one. Otherwise look at any other automation or script that publishes to this topic.

To put things right, open the **Apps** tab of the clock's web interface and set the switches and the order the way you want them.

### The clock reboots or misbehaves

Almost always **Switch to app on value change** enabled on a fast-changing sensor. Turn it off.

### Text shows `?` characters

The matrix font has no glyph for that character. Emoji and other unusual symbols come out as `?`. Latin accents, Greek, Cyrillic, `°`, `€` and the other common currency signs are fine on AWTRIX NG 1.2.2 or newer.

### No sound

- The clock's **volume** (or **alertVolume**) is `0`. Commands succeed silently.
- The clock runs firmware older than **1.2.2**, which does not understand the new sound format.
- The sound file does not exist in `/MP3` or `/MELODIES`.
- The RTTTL string has a typo — test it in the clock's web interface on the **Audio** tab first.

### The Create Sensor App does not show up, or shows the old content

- The clock rejects a whole layout when one part is wrong, and keeps showing what it had. Check `<prefix>/cmd/apps/pushed/+/result` for the reason. `"outside the display"` means a box is too big — check **Icon Width** and **Icon Height**. An `icon could not be decoded` error means an icon name or web address is wrong.
- Shows only the top line? The Bottom Line Sensor, Template and Text are all empty, or Display Mode is *One large line*.
- Icon not enlarged in one large line? The clock's **Enlarge apps** setting is off, or the icon is taller than 8 pixels.
- A template that reads other entities does not update? Turn on **Refresh every minute**.

### The app name looks mangled

That is deliberate. AWTRIX NG only accepts `A–Z`, `a–z`, `0–9`, `-` and `_` in app names, up to 32 characters, so the blueprints replace anything else with an underscore. `sensor.living_room_temperature` becomes `sensor_living_room_temperature`. In the Create Sensor App you enter the name yourself in **App Name**; a name like `Living Room` becomes `Living_Room`.

---

## Appendix A — Effects, overlays and palettes

### Background effects

Drawn *behind* the text, filling the whole panel. Set one and the Background Color is ignored.

| Effect | What it looks like |
|---|---|
| `BrickBreaker` | Breakout that plays itself: a ball clears rows of bricks, then the wall is rebuilt |
| `Checkerboard` | A checkerboard in two colours that drift through the palette |
| `ColorWaves` | Colours spreading across the display, sliding sideways |
| `Fade` | Horizontal colour bands, slowly cycling through the colours |
| `Fireworks` | Rockets that rise and burst into sparks |
| `LookingEyes` | Two eyes that look around and blink |
| `Matrix` | Falling green trails, one per column |
| `MovingLine` | A full-width line bouncing up and down, with a short glow behind it |
| `Pacifica` | Blue-teal ocean waves |
| `PingPong` | Pong that plays itself: two paddles and a red ball |
| `Plasma` | Flowing plasma through the whole hue wheel |
| `PlasmaCloud` | Soft drifting clouds in a few colours at a time |
| `Radar` | A beam sweeping round the centre with a fading afterglow |
| `Ripple` | Rings spreading from random points and fading out |
| `Snake` | Snake that plays itself: it hunts a red apple and grows |
| `SwirlIn` | A rainbow spiral turning inwards |
| `SwirlOut` | A rainbow spiral turning outwards |
| `TheaterChase` | Every third column lit, marching sideways |
| `TwinklingStars` | Stars that flare up, fade and come back somewhere else |

`PingPong`, `Matrix` and `LookingEyes` have fixed colours and ignore palettes.

> **Tip:** effects are eye-catching but they compete with your text. Use them on apps you *want* people to look at, and leave the everyday sensors plain.

### Weather overlays

Drawn *on top of* the finished page, so text and icons stay visible underneath. Only the Weather App blueprint exposes this.

| Overlay | What it looks like |
|---|---|
| `rain` | Scattered blue drops falling straight down |
| `snow` | Grey flakes swaying as they fall |
| `drizzle` | Fine, sparse, lighter blue mist |
| `storm` | Dense wind-slanted streaks with long tails |
| `thunder` | Storm, plus a lightning bolt every few seconds while the sky lights up |
| `frost` | A softly shimmering icy border along the top and bottom edges |

You can also set an overlay for the *whole clock* in its web interface. A per-app overlay always wins over the global one.

### Palettes

The blueprints expose palettes through the **Gradient 1 / Gradient 2** and **Rainbow Colors** fields. Under the surface AWTRIX NG supports eight named palettes — `Cloud`, `Lava`, `Ocean`, `Forest`, `Stripe`, `Party`, `Heat`, `Rainbow` — and you can upload your own to `/PALETTES` on the clock.

### Icons

Icons come from **[developer.lametric.com/icons](https://developer.lametric.com/icons)**. Note the ID number, download the file, and upload it on the **Icons** tab of the clock's web interface (**Icons → Add**). Both static images and animated GIFs work.

Some handy ones to start with:

| ID | Icon |
|---|---|
| `2422` | Thermometer |
| `2056` | Humidity drop |
| `1817` | Lightning bolt / power |
| `11899` | Calendar |
| `29644` | Checklist |
| `12160` | Air quality |
| `1234` | Doorbell |

---

## Appendix B — Moving from the TC001 set

Up to V6.0 there was one set of AWTRIX NG blueprints for both clocks. Since V6.1 the TC002 has its own set:

| | Before (shared) | Now, for the TC002 |
|---|---|---|
| **Files** | `awtrix_ng_*.yaml` | `awtrix_ng_tc002_*.yaml` |
| **Blueprint names** | *AWTRIX NG …* | *AWTRIX NG TC002 …* |

The `awtrix_ng_*.yaml` files still exist and are now meant specifically for the TC001. Your existing automations keep running — Home Assistant does not change them. To move an automation to the TC002 set:

1. Copy the `awtrix_ng_tc002_*.yaml` files to `config/blueprints/automation/smarthomejunkie/` and reload, as in [section 2](#2-installing-a-blueprint).
2. Switch the automation's toggle helper **off**, so its app is removed from the clock.
3. Create a new automation from the matching **AWTRIX NG TC002** blueprint, with the same toggle helper and settings.
4. Delete the old automation.

For most blueprints the fields are the same as before. The **Create Sensor App** is rebuilt for the TC002 and has new fields — see [Upgrading from the earlier version](#upgrading-from-the-earlier-version-v11).

The TC002 has never run AWTRIX 3. If you are moving a TC001 from AWTRIX 3 to AWTRIX NG, see Appendix B of the [TC001 manual](AWTRIX-NG-TC001-MANUAL.md#appendix-b--coming-from-awtrix-3).

Full firmware documentation: **[ang.blueforcer.de/tc002](https://ang.blueforcer.de/tc002/)**

---

# Release Notes
**V6.3**
* Create Sensor App (TC002): new **Top Line Background** and **Bottom Line Background** options. Each line can now have its own background colour, which overrides the general Background Color, the Background Gradient and the Effect for that line. The line background starts directly after the main icon, so it lines up exactly with the gradient. In one large line, the top line background fills the whole app — see [Line backgrounds](#line-backgrounds)
* Create Sensor App (TC002): threshold colours are easier to use. The **Use Threshold value?** switches are gone: the Low-value and High-value colours of a line are used as soon as its **Threshold Value** contains a number. Leave the Threshold Value empty for the normal line colour. A decimal comma (`19,5`) is accepted too. Existing automations with a Threshold Value filled in now use their threshold colours automatically

**V6.2**
* Create Sensor App (TC002): new required **App Name** field. You now choose the name of the app on the clock yourself, instead of the blueprint taking it from the automation. Existing automations need an App Name filled in after updating the blueprint — see [App naming](#app-naming)

**V6.1**
* New: a separate set of blueprints for the TC002, `awtrix_ng_tc002_*.yaml`, named *AWTRIX NG TC002 …*. The `awtrix_ng_*.yaml` files are now the TC001 set
* Create Sensor App V2.0 for the TC002: rebuilt with AWTRIX NG layouts
  * A 16 × 16 icon with two lines of text, each line showing a sensor, a template or a text (Text > Template > Sensor)
  * Optional 8 × 8 icon before each line, and an adjustable icon size
  * Per-line font, colour, rainbow and threshold colours
  * Vertical background gradient
  * Display Mode: two lines or one large line; an empty bottom line is ignored automatically
  * One large line follows the clock's Enlarge apps setting
  * Refresh every minute, for templates that read other entities
  * The app is named after the automation

**V6.0**
* Made blueprints compatible with the new Ulanzi TC002 Desktop Clock
* Toggle Built-in Apps now switches each app with its own switch, so every other app stays as it is. Needs AWTRIX NG 1.2.2 or newer
* Alert sounds use the sound format of AWTRIX NG 1.2.2

**V5.3**
* Fixed template rendering for all blueprints, including rendering of icon templates

**V5.2**
* Fixed an error in the template rendering for the sensor app blueprint

**V5.0**
* Blueprints for the new Awtrix NG firmware

---

*Blueprints and manual © 2026 Smart Home Junkie. You may not copy, reproduce, distribute, transmit, modify, create derivative works, or in any other way exploit any part of this copyrighted material without prior written permission from Smart Home Junkie.*
