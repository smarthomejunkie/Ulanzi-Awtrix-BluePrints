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

| | Blueprint | Shows or does |
|---|---|---|
| 5 | [Create Notification](#5-create-notification) | A message that interrupts the display, with an optional title and sound |
| 6 | [Create Sensor App](#6-create-sensor-app) | Two lines with sensors, texts or templates next to a large icon |
| 7 | [Weather App](#7-weather-app) | The weather condition above the temperature, wind and humidity, in ten languages |
| 8 | [Rain Forecast](#8-rain-forecast) | The rain for the next 11 hours as a chart |
| 9 | [List Calendar](#9-list-calendar) | Your agenda, with an alert before an event starts |
| 10 | [List ToDo Items](#10-list-todo-items) | The open items from a to-do list |
| 11 | [Countdown Timer](#11-countdown-timer) | A countdown to a date and time, with a start sound |
| 12 | [Show Music Assistant App](#12-show-music-assistant-app) | The song that is playing, with cover and progress bar; the buttons control the music |
| 13 | [Toggle Built-in Apps](#13-toggle-built-in-apps) | Switches the Time app on and off |
| 14 | [Toggle Indicators](#14-toggle-indicators) | The three edge pixels as status lights |
| 15 | [Moodlight](#15-moodlight) | The whole panel in one colour, as a lamp |
| 16 | [Set App Time](#16-set-app-time) | How long each app stays on screen |

**Reference**

17. [Troubleshooting](#17-troubleshooting)
- [Appendix A — Effects, overlays and palettes](#appendix-a--effects-overlays-and-palettes)
- [Appendix B — Moving from the TC001 set](#appendix-b--moving-from-the-tc001-set)
- [Appendix C — Upgrading from earlier versions](#appendix-c--upgrading-from-earlier-versions)
- [Release Notes](#release-notes)

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

The Create Notification, Create Sensor App, Weather App, List Calendar, List ToDo Items, Countdown Timer and Show Music Assistant App blueprints do not use the double-size grid for two lines: they draw an AWTRIX NG **layout** on all 52 × 16 pixels, so they can show a large icon with two lines of text. See [chapter 6](#6-create-sensor-app).

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
- The Create Notification, Create Sensor App, Weather App, List Calendar, List ToDo Items and Countdown Timer have a larger icon space and more icon options — see [chapter 6](#fields--icon).

### Push Icon

What the icon does while the text scrolls past it.

| Option | Behaviour |
|---|---|
| **Icon doesn't move** | Icon stays put, text scrolls past it |
| **Icon moves with text and will not appear again** | Text shoves the icon off screen once; it stays gone |
| **Icon moves with text but appears again when the text starts** | Icon is pushed off, then returns on every scroll cycle *(default)* |

In the Create Notification, Create Sensor App, Weather App, List Calendar, List ToDo Items and Countdown Timer, Push Icon only works for the main icon, in one large line with the clock's Enlarge apps setting on. The small line icons never move. See [chapter 6](#two-lines-or-one-large-line).

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

Only Create Notification has the gradient fields, for its Bottom Line Text. The other blueprints offer Rainbow Colors only. The Create Notification, Create Sensor App, Weather App, List Calendar, List ToDo Items and Countdown Timer also have a **background** gradient — see [chapter 6](#about-the-icons-and-the-gradient).

### Rainbow Colors

Paints the text through the full colour wheel. **Overrides the gradient** if you set both.

### Repeat

How many times scrolling text runs across the screen before the app's turn ends.

- `0` — off *(default)*. The app stays for the Duration instead.
- `1` — long text is shown once, beginning to end, then the rotation moves on.
- `2`, `3`, … — that many full passes.

Repeat does nothing if the text is short enough to fit on screen, because there is nothing to scroll.

### Duration (in seconds)

How long the app stays on screen during each rotation. `0` uses the clock's global app time (7 seconds unless you changed it — see [Set App Time](#16-set-app-time)).

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

**File:** `awtrix_ng_tc002_create_notification.yaml` · **Blueprint name:** *AWTRIX NG TC002 Create Notification* · **Version:** V2.0

### What it does

Interrupts whatever is on screen with a message, for as long as you want. Unlike the app blueprints, a notification is not part of the rotation — it takes over the display immediately.

Turn the toggle helper **on** to show the notification, **off** to dismiss it.

The notification uses the same layout as the [Create Sensor App](#6-create-sensor-app): a large icon on the left, with an optional title on the top line and the message on the bottom line. Both lines can start with an optional small icon.

```
+----------------+-+--------+-+-----------------------+
|                | | top    | |  Doorbell             |
|   Main icon    | | icon   | |                       |
|    16 × 16     | +--------+ +-----------------------+
|                | | bottom | |  Someone at the door! |
|                | | icon   | |                       |
+----------------+-+--------+-+-----------------------+
```

Leave the **Top Line Text** empty and the message is shown on **one large line**, just like in earlier versions.

### When it runs

- Toggle helper goes **on** → the notification is sent, with its sound if Play Alert Tone is on.
- Toggle helper goes **off** → the notification is dismissed.

### Fields — Icon

| Field | What it does |
|---|---|
| **Icon** | The main icon on the left. Empty by default — leave it empty for no icon. |
| **Push Icon**, **Enlarged Icon**, **Icon Width**, **Icon Height** | As in the Create Sensor App — see [Fields — Icon](#fields--icon). Use an 8 × 8 **Enlarged Icon** with a 16 × 16 main Icon, or one large line stays small — see [Why an Enlarged Icon?](#why-an-enlarged-icon). |

### Fields — Top line

| Field | What it does |
|---|---|
| **Top Line Text** | Optional. A title above the message, e.g. `Doorbell`. A plain text or a template — see [Templates](#templates). Leave it empty to show the message on one large line. In one large line with a Top Line Text, the title is shown in front of the message. |
| **Top Line Icon (Optional)** | A small 8 × 8 icon at the front of the top line. |
| **Top Line Font**, **Top Line Color**, **Top Line Background Color**, **Top Line Rainbow Colors** | As in the Create Sensor App. |

The top line has no separate sensor or template field: enter the template in the Top Line Text itself.

### Fields — Bottom line (notification text)

| Field | What it does |
|---|---|
| **Bottom Line Text** | The message itself. A plain text or a template — see [Templates](#templates). This was **Notification Text** in earlier versions. |
| **Bottom Line Icon (Optional)** | A small 8 × 8 icon at the front of the message. Not used in one large line. |
| **Bottom Line Font** | The font of the message. Also used for one large line when the Top Line Text is empty. |
| **Bottom Line Color** | The text colour of the message. This was **Text Color** in earlier versions. |
| **Bottom Line Background Color** | Black *(default)* = no line background. Any other colour gives the bottom line its own background — see [Line backgrounds](#line-backgrounds). |
| **Gradient 1 / Gradient 2** | Paint the message as a gradient from the first colour to the second — see [Gradient 1 / Gradient 2](#gradient-1--gradient-2). Leave both the same for the plain Bottom Line Color. |
| **Rainbow Colors** | Shows the message in rainbow colours. Overrides the gradient. |

There are no threshold colours.

### Layout options

**Display Mode**, **Text Alignment**, **Text Case**, **Background Color**, **Background Gradient** and **Effect** work exactly as in the [Create Sensor App](#fields--layout-options).

- **Two lines** *(default)*: the title on top, the message below. With an empty Top Line Text you always get one large line.
- **One large line**: the title followed by the message, e.g. `Doorbell Someone at the door!`, large or small according to the clock's **Enlarge apps** setting — see [Two lines or one large line](#two-lines-or-one-large-line).

### Advanced options

| Field | What it does |
|---|---|
| **Notification Name** | Optional label. With a name set, switching the toggle off dismisses *exactly this* notification wherever it sits in the queue, instead of whatever happens to be on screen. Use letters, digits, `-` and `_`. Strongly recommended if you have more than one notification automation. |
| **Play Alert Tone** | Plays a sound when the notification appears. Off by default. |
| **Sound File / Track** | Optional. The name of a stored MP3 or melody on the clock (without `.mp3` / `.txt`). Has priority over the Alert Melody. No default. Only used when Play Alert Tone is on. |
| **Alert Melody (RTTTL)** | Played when Play Alert Tone is on and no Sound File / Track is set. Default `alert:d=4,o=5,b=180:8c6,8p,8c6,8p,8c6` (three short beeps). See [Sound File / Track and Alert Melody](#sound-file--track-and-alert-melody-rtttl). |
| **Hold Notification** | On *(default)*: the notification stays until it is dismissed, ignoring Duration and Repeat. Off: it disappears after the Duration. |
| **Repeat**, **Duration** | See [section 4](#repeat). |
| **Stack Notification?** | On *(default)*: queues behind notifications already waiting. Off: replaces whatever is on screen right now, immediately. |
| **Scroll the notification?**, **Scroll Speed Percentage** | See [section 4](#scroll-the-text--scroll-speed-percentage). |

### Templates

The **Top Line Text** and the **Bottom Line Text** both accept a Home Assistant template instead of a plain text. The template is worked out at the moment the notification is sent, so it shows the value at that moment — a notification does not update itself afterwards.

| Field | Template | Shows |
|---|---|---|
| Top Line Text | `{{ state_attr('sensor.front_door_camera', 'friendly_name') }}` | `Front Door Camera` |
| Bottom Line Text | `Washer done at {{ now().strftime('%H:%M') }}` | `Washer done at 14:05` |
| Bottom Line Text | `{{ states('sensor.outdoor_temperature') }} °C outside` | `11.5 °C outside` |

- A template that returns a number is fine: it is turned into text automatically.
- A Bottom Line Text template may also return a list of text parts, each with its own colour, e.g. `{{ [{'text': 'Alarm ', 'color': [255, 0, 0]}, {'text': 'Garage', 'color': [255, 255, 255]}] }}`.
- See [Template examples](#template-examples) in the Create Sensor App for more ideas.

### Example — doorbell

| Field | Value |
|---|---|
| Toggle Helper | `input_boolean.awtrix_doorbell` |
| Icon | a 16 × 16 doorbell icon |
| Enlarged Icon | the 8 × 8 version of that icon |
| Top Line Text | `Doorbell` |
| Top Line Color | Yellow |
| Bottom Line Text | `Someone at the door!` |
| Bottom Line Color | White |
| Notification Name | `doorbell` |
| Play Alert Tone | On |
| Sound File / Track | `dingdong` (an MP3 on the clock) |
| Hold Notification | On |
| Stack Notification? | Off |

Then, from your doorbell automation, turn `input_boolean.awtrix_doorbell` on. The clock drops everything, plays `dingdong.mp3` and shows `Doorbell` above `Someone at the door!` until you turn the helper off again.

### Example — washing machine finished

| Field | Value |
|---|---|
| Bottom Line Text | `Washing machine is done` |
| Top Line Text | *(empty)* |
| Notification Name | `washer` |
| Hold Notification | Off |
| Duration | `15` |
| Stack Notification? | On |
| Rainbow Colors | On |
| Background Gradient | On, dark blue to black |

The message is shown on one large line. It waits politely in the queue and disappears on its own after 15 seconds.

> **Tip:** because both examples use a different **Notification Name**, turning the washer helper off will never dismiss the doorbell message by accident.

> **Upgrading from an earlier version?** See [Appendix C](#create-notification--from-v12).

---

## 6. Create Sensor App

**File:** `awtrix_ng_tc002_create_sensor_app.yaml` · **Blueprint name:** *AWTRIX NG TC002 Create Sensor App* · **Version:** V2.1

### What it does

Puts Home Assistant information into the clock's app rotation and keeps it up to date. It uses the whole 52 × 16 display of the TC002: a large icon on the left, with **two lines of text** next to it. Each line can show a sensor, or a text — a fixed text or a template — and each line can have its own small icon.

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

### When it runs

- Toggle on → the app is created and the clock switches to it.
- Toggle off → the app is deleted.
- The **Top Line Sensor** or **Bottom Line Sensor** changes (while the toggle is on) → the app is updated. A change to `unavailable` or `unknown` is skipped, so the app keeps its last text.
- Every minute, if **Refresh every minute** is on (while the toggle is on) → the app is sent again.

### What a line shows

Each line has two sources. The first one that is filled in wins:

| Priority | Field | Shows |
|---|---|---|
| 1 | **Text** | Exactly the text you typed, or the result of the Home Assistant template you entered — see [Template examples](#template-examples). |
| 2 | **Sensor** | The state of the entity, rounded and with its unit, e.g. `21.5 °C`. |

Both are optional. A line with nothing in it is left out.

**If the Bottom Line Sensor and Text are both empty, the bottom line is ignored** and the app is shown as one large line — see [Two lines or one large line](#two-lines-or-one-large-line).

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
| **Enlarged Icon (Optional)** | An 8 × 8 icon used **instead of the main Icon** when the app is shown at double size (one large line on a clock with Enlarge apps on). The clock then shows it as 16 × 16. Fill this in when your main Icon is a 16 × 16 icon — see [Why an Enlarged Icon?](#why-an-enlarged-icon). Leave it empty to use the main Icon. |
| **Icon Width (Optional)** | Width of the space reserved for the icon, `1`–`50` pixels. Empty (or `0`) = `16`. |
| **Icon Height (Optional)** | Height of the space reserved for the icon, `1`–`16` pixels. Empty (or `0`) = `16`. A lower icon is centred vertically. |

The lines always start 1 pixel to the right of the icon space. A wider icon leaves less room for text: with an icon of 32 pixels wide, each line has 19 pixels left.

> **Push Icon and layouts:** AWTRIX NG only moves an icon with the text in a normal app. Two lines (and one line with Enlarge apps off) are drawn with an AWTRIX NG **layout**, in which every icon has its own fixed box and every text scrolls inside its own box. The icons do not move there, whatever Push Icon is set to.

### Fields — Top line and Bottom line

Both sections have the same fields:

| Field | What it does |
|---|---|
| **Sensor** | The entity to show. The app updates whenever it changes. |
| **Text** | A fixed text, or a Home Assistant template such as `{{ states('sensor.outside_temperature') \| round(1) }} °C`. Wins over the Sensor. |
| **Line Icon (Optional)** | A small icon at the left of the line, before the text, in an 8 × 8 space. The text then starts 1 pixel to the right of it. |
| **Font** | The font of the line. Every font fits in the 8-pixel line: `small` *(default)*, `large`, and the Matrix Light and Matrix Chunky fonts. Try a few — they differ in width, so a text that just fits in one may scroll in another. |
| **Color** | The text colour. |
| **Rainbow Colors** | Paints the line in rainbow colours. Overrides the colours below. |
| **Threshold Value (Optional)** | Enter a number to colour the line by its value. As soon as a number is filled in, the Low-value and High-value colours are used instead of the line's Color. Leave it empty for the normal Color. A decimal comma (`19,5`) works too. |
| **Low-value color** | Used when the value is **below** the threshold. |
| **High-value color** | Used when the value is **at or above** the threshold. |
| **Line Background Color** | The background colour of this line. Black *(default)* means no line background; any other colour gives the line its own background. |

A line icon is skipped when the main icon leaves less than 10 pixels for the line.

> **Note:** with a Text (fixed or a template), the threshold compares the number in the result, stripping out everything that is not a digit, a dot or a minus sign. So `Power: 450W` is compared as `450`.

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
| **Refresh every minute** | Send the app again every minute. Turn this on when a Text template reads entities other than the two selected sensors — changes to those entities do not update the app by themselves. |
| **Scroll the text?** / **Scroll Speed Percentage** | See [section 4](#scroll-the-text--scroll-speed-percentage). Applies to both lines. |

### Two lines or one large line

| Situation | What you see |
|---|---|
| **Two lines** | Main icon, top line and bottom line, each with its optional line icon. Drawn with an AWTRIX NG layout on the full 52 × 16 pixels. |
| **One large line**, clock's *Enlarge apps* **on** | Main icon and top line, drawn at **double size**: an 8 × 8 icon becomes 16 × 16 and the text is twice as tall. Push Icon works here. |
| **One large line**, clock's *Enlarge apps* **off** | Main icon and top line at normal size, with the top line using the full height of the display. The Top Line Icon sits next to it. |

You get one large line when **Display Mode** is *One large line*, **or** when the Bottom Line Sensor and Text are both empty.

**Enlarge apps** is the clock's own setting under **Display → Text** in its web interface (`enlargeApps`). The blueprint reads it from the clock every time it sends the app, so you never have to set it twice. If the clock does not answer within 2 seconds, the blueprint assumes it is on, which is the firmware default.

In one large line with Enlarge apps on, the clock places the icon itself, so **Icon Width** and **Icon Height** are not used. The **Enlarged Icon** is used instead of the main Icon when you set one, and the **Top Line Icon** only when no main Icon is set. The bottom line fields are not used, and the gradient has 8 steps instead of 16. A main icon taller than 8 pixels makes the clock draw the app at normal size — see below.

#### Why an Enlarged Icon?

AWTRIX NG only draws an app at double size when its icon is at most 8 pixels high. With a 16 × 16 icon — which looks best in two lines — the clock draws the whole app at normal size, so the text stays small even with Enlarge apps on. The **Enlarged Icon** solves this: in two lines the app uses your 16 × 16 main Icon, and in the enlarged one large line it uses the 8 × 8 Enlarged Icon, which the clock shows as 16 × 16. Use the 8 × 8 version of the same picture and the two look alike.

### About the icons and the gradient

- **An icon stored on the clock keeps its own size.** An 8 × 8 icon in the 16 × 16 space sits small in the middle of it. For a full-size icon in two lines, upload a 16 × 16 icon.
- **A PNG or JPEG from a web address is resized** to fill its space, pixel by pixel, so pixel art stays sharp. These pictures do not animate. **Animated GIFs** play at their own size.
- A layout holds at most four icons; this blueprint uses at most three.
- **The gradient** is drawn by the blueprint, one line per row of the display, behind the icons and the text. Black pixels in an icon stay black.

### Line backgrounds

Pick a colour other than black in **Top Line Background Color** or **Bottom Line Background Color** to give that line its own background. Black, the default, means no line background. It fills the line from directly after the main icon to the right edge, lining up with the gradient, and **overrides the Background Color, the Background Gradient and the Effect** for that line. The main icon keeps the general background.

- In **one large line**, the top line background fills the full height next to the icon. With Enlarge apps on, the whole app is drawn as one line, so the top line background becomes the background of the whole app.
- The bottom line background is not used in one large line.

### Template examples

Enter a template in the **Top Line Text** or **Bottom Line Text** field — there are no separate template fields. Each line has its own text, so you can combine two values in one app:

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
| Top Line Sensor | `sensor.bedroom_co2` (so the app updates when the value changes) |
| Top Line Text | `{{ states('sensor.bedroom_co2') \| int }}ppm` |
| Top Line - Threshold Value | `1000` |
| Top Line - Low-value color | Green |
| Top Line - High-value color | Red |
| *(bottom line empty)* | |
| Lifetime | `1800` |
| On lifetime expiry | Mark the app as stale |

With the bottom line empty, the app is one large line: a big icon and a big number, green below 1000 ppm and red from 1000 up.

> **Showing what's playing?** Use the [Show Music Assistant App](#12-show-music-assistant-app) for Music Assistant players.

### App naming

You choose the name of the app on the clock yourself, in the required **App Name** field. It is the name the app has on the **Apps** tab of the clock's web interface, where you can drag it to the slot you want. The clock remembers that slot.

- **Give every automation its own App Name.** Two automations with the same name, on the same clock, overwrite each other's app.
- **Allowed characters:** letters, digits, `-` and `_`, up to 32 characters. Anything else, such as a space, is replaced by `_`, and a longer name is cut to 32 characters. `Living Room Climate` becomes the app `Living_Room_Climate`.
- **Changing the App Name** of an existing automation creates a new app on the clock. Switch the toggle helper off first, so the app with the old name is removed; then change the name and switch the toggle back on.
- The name does not depend on a sensor or on the automation, so you can rename the automation, or swap the sensors, without the app changing.

> **Upgrading from an earlier version?** See [Appendix C](#create-sensor-app--from-v20-to-v21).

---

## 7. Weather App

**File:** `awtrix_ng_tc002_weather_app.yaml` · **Blueprint name:** *AWTRIX NG TC002 Weather App* · **Version:** V2.0

### What it does

Shows the current weather from any Home Assistant `weather` entity. It uses the same layout as the [Create Sensor App](#6-create-sensor-app): the weather icon on the left, the **condition** on the top line, and the **temperature, wind speed and humidity** on the bottom line, in the language you choose. Both lines can start with an optional small icon.

```
+----------------+-+--------+-+-----------------------+
|                | | top    | |  halfbewolkt          |
|  Weather icon  | | icon   | |                       |
|    16 × 16     | +--------+ +-----------------------+
|                | | bottom | |  12.4°C - 8km/h - 71%  |
|                | | icon   | |                       |
+----------------+-+--------+-+-----------------------+
```

Choose **One large line** for the look of earlier versions: the icon and one line, `halfbewolkt 12.4°C - 8.6km/h - 71%`.

### When it runs

Toggle on → the app is created and the clock switches to it. Toggle off → the app is deleted. The weather changes → the app is updated (a change to `unavailable` or `unknown` is skipped).

### Fields — Weather

| Field | What it does |
|---|---|
| **Sensor** | Your weather entity (Met.no, KNMI, OpenWeatherMap — anything in the `weather` domain). Also the app name on the clock: `weather.knmi` becomes `weather_knmi`. |
| **Show Weather Text** | Show the condition as words ("partly cloudy") on the top line. |
| **Language** | Translates the condition into English, Danish, Dutch, French, German, Italian, Polish, Portuguese, Russian or Spanish. The conditions `exceptional`, `lightning-rainy` and `windy-variant` have no translation and are shown as they are. |
| **Show temperature**, **Show Wind Speed**, **Show Humidity** | Which values the bottom line shows, joined with ` - `. |

Switch off the condition text, or all three values, and the app is always **one large line** with what is left.

### The icon is automatic

This blueprint has **no Icon field**. It uses the weather condition itself as the icon name, so the clock looks for an icon named after the current condition. Upload icons with these exact names on the **Icons** tab of the clock's web interface:

`sunny` · `clear-night` · `cloudy` · `partlycloudy` · `rainy` · `pouring` · `snowy` · `snowy-rainy` · `fog` · `hail` · `lightning` · `lightning-rainy` · `windy` · `windy-variant` · `exceptional`

The clock refuses a whole layout when one of its icons is missing. The blueprint listens for the clock's answer and then sends the app again **without icons**, so the weather is still shown — just without the picture.

| Field | What it does |
|---|---|
| **Push Icon** | As in the Create Sensor App: only works in one large line on a clock with Enlarge apps on — see [Push Icon](#push-icon). |
| **Icon Width (Optional)**, **Icon Height (Optional)** | The size of the space for the weather icon. Empty = 16 × 16. An 8 × 8 icon sits small in the middle of the 16 × 16 space; upload 16 × 16 icons for a full-size picture in two lines. |

In one large line with Enlarge apps on, an 8 × 8 weather icon is drawn at 16 × 16. A 16 × 16 icon keeps the app at normal size there — see [Why an Enlarged Icon?](#why-an-enlarged-icon); the Weather App has no Enlarged Icon field.

### Fields — Top line (condition) and Bottom line (values)

| Field | What it does |
|---|---|
| **Top Line Icon**, **Bottom Line Icon** (Optional) | A small 8 × 8 icon at the front of the line, e.g. a thermometer before the values. The Bottom Line Icon is not used in one large line. |
| **Top Line Font**, **Bottom Line Font** | The font of each line. The Top Line Font is also used for one large line. |
| **Top Line Color** | The colour of the condition. |
| **Bottom Line Color** | The colour of the values, and of the whole line in one large line without the condition. This was **Text Color** in earlier versions. |
| **Top / Bottom Line Background Color** | Black *(default)* = no line background — see [Line backgrounds](#line-backgrounds). |
| **Top Line Rainbow Colors**, **Bottom Line Rainbow Colors** | Rainbow colours per line. Bottom Line Rainbow Colors was **Rainbow Colors** in earlier versions. |

### Layout and Advanced options

**Display Mode**, **Text Alignment**, **Text Case**, **Background Color**, **Background Gradient**, **Effect**, **Repeat**, **Duration**, **Lifetime**, **Switch to app on value change** and **Scroll** work exactly as in the [Create Sensor App](#fields--layout-options).

**Weather Overlay** draws animated rain, snow, drizzle, storm, thunder or frost *on top of* the app — see [Appendix A](#weather-overlays). Leave it on *Follow device setting* to use the clock's own overlay.

### Example — Dutch weather with rain overlay

| Field | Value |
|---|---|
| Sensor | `weather.knmi` |
| Language | Dutch |
| Show Wind Speed | Off |
| Show Humidity | Off |
| Top Line Color | Yellow |
| Background Gradient | On, dark blue to black |
| Weather Overlay | Rain |
| Duration | `10` |

The weather icon, `halfbewolkt` above `12.4°C`, with rain falling over it.

> **Upgrading from an earlier version?** See [Appendix C](#weather-app--from-v11).

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

Each of the 11 bars is one hour ahead, left to right. The chart shows *when* it will rain and *roughly how hard*, not how many millimetres. Leave Autoscale on: with it off, a normal shower is a single pixel high.

---

## 9. List Calendar

**File:** `awtrix_ng_tc002_list_calendar.yaml` · **Blueprint name:** *AWTRIX NG TC002 List Calendar* · **Version:** V2.0

### What it does

Two things at once:

1. **A rotating app** that shows your upcoming appointments. It uses the same layout as the [Create Sensor App](#6-create-sensor-app): a large icon on the left, with the **Calendar Name** on the top line and the **events** on the bottom line. Both lines can start with an optional small icon.
2. **An alert** a number of minutes before an event starts — you choose how many — with an optional alert tone. The alert is always shown on its own, on **one line**.

Either half can be switched off.

```
+----------------+-+--------+-+-----------------------+
|                | | top    | |  Family               |
|   Main icon    | | icon   | |                       |
|    16 × 16     | +--------+ +-----------------------+
|                | | bottom | |  Today 14:00 Dentist  |
|                | | icon   | |  // 19:30 Football    |
+----------------+-+--------+-+-----------------------+
```

### When it runs

- Toggle on → the app is created and the clock switches to it.
- Toggle off → the app is deleted.
- **Every minute** → the agenda is re-read and the app refreshed. With **Switch to app on value change** on, the clock also switches to the app every minute.
- **Every minute** → the blueprint looks for events that start in **Alert Minutes Before Start** minutes, and shows an alert for each of them (if Show Alerts is on).

### Fields — Calendar

| Field | What it does |
|---|---|
| **Calendar** | The calendar entity to read. |
| **Hours ahead** | How far into the future to look. Default `24`. |
| **Show Timeline?** | On: the app lists all upcoming events. Off: no app is shown at all and you only get the alerts. |
| **Show Whole Day Events?** | Include all-day events (the ones with no start time). |
| **Custom Text** | Shown on the bottom line when there is nothing in the agenda. Default: `Yay! You have no appointments!`. |
| **Show Empty Calendar** | Off: the custom text is not shown when there are no events. The app is not removed straight away: it keeps its last text until its **Lifetime** runs out. Set a Lifetime of `120` or more when you switch this off, so the app disappears about two minutes after the last event. |

### Fields — Icon

| Field | What it does |
|---|---|
| **Icon** | The main icon on the left. Empty by default — leave it empty for no icon. |
| **Push Icon**, **Enlarged Icon**, **Icon Width**, **Icon Height** | As in the Create Sensor App — see [Fields — Icon](#fields--icon). Use an 8 × 8 **Enlarged Icon** with a 16 × 16 main Icon, or one large line and the alerts stay small — see [Why an Enlarged Icon?](#why-an-enlarged-icon). |

### Fields — Alerts

| Field | What it does |
|---|---|
| **Show Alerts?** | Show an alert before an event starts. It reads `Event <name> starts at <time>`. That text is fixed and in English. Alerts also work when the toggle helper is off or Show Timeline is off. |
| **Alert Minutes Before Start** | How many minutes before the start of an event the alert is shown. Default `15`. `0` shows it at the start of the event. |
| **Alert Repeat** | How many times the alert text is shown — scrolls through the display — before the alert ends. Default `1`. |
| **Play Alert Tone** | Add a sound to the alert. |
| **Sound File / Track**, **Alert Melody (RTTTL)** | The sound to play — see [Sound File / Track and Alert Melody](#sound-file--track-and-alert-melody-rtttl). The sound file has priority over the melody. |
| **Stack Notification?** | On: queue behind other notifications. Off: replace whatever is on screen. |

### Fields — Top line

| Field | What it does |
|---|---|
| **Calendar Name (Top Line Text)** | The name on the top line, e.g. `Family` — useful when you run this blueprint several times for different calendars. In one large line it is shown in front of the events. Leave it empty for no top line text. This was the **Prefix** field in earlier versions. |
| **Top Line Icon (Optional)** | A small 8 × 8 icon at the front of the top line. |
| **Top Line Font**, **Top Line Color**, **Top Line Background Color**, **Top Line Rainbow Colors** | As in the Create Sensor App. The Top Line Font is also used for one large line and for the alerts. |

The top line has no sensor or template: it always shows the Calendar Name you typed.

### Fields — Bottom line (events)

| Field | What it does |
|---|---|
| **Today Text** | Label placed before today's events, e.g. `Today:`. Leave empty for none. |
| **Tomorrow Text** | Label placed before events on later days, e.g. `Tomorrow:`. Leave empty for none. |
| **Bottom Line Icon (Optional)** | A small 8 × 8 icon at the front of the events. Not used in one large line. |
| **Bottom Line Font** | The font of the events. |
| **Bottom Line Color** | The text colour of the events and of the alerts. This was **Text Color** in earlier versions. |
| **Bottom Line Background Color** | Black *(default)* = no line background. Any other colour gives the bottom line its own background — see [Line backgrounds](#line-backgrounds). |
| **Bottom Line Rainbow Colors** | Shows the events and the alerts in rainbow colours. This was **Rainbow Colors** in earlier versions. |

Events are joined with ` // `, and timed events are prefixed with their start time — exactly as in earlier versions. There are no threshold colours.

### Layout and Advanced options

**Display Mode**, **Text Alignment**, **Text Case**, **Background Color**, **Background Gradient**, **Effect**, **Repeat**, **Duration**, **Lifetime**, **Switch to app on value change** and **Scroll** work exactly as in the [Create Sensor App](#fields--layout-options).

In **one large line**, the line shows the Calendar Name followed by the events, e.g. `Family Today 14:00 Dentist // 19:30 Football`, as in earlier versions. The Calendar Name keeps the Top Line Color and the events keep the Bottom Line Color.

### Alerts on one line

An alert is a notification that interrupts whatever is on screen. It is always shown on its own, on one line next to the main icon, and follows the clock's **Enlarge apps** setting, read from the clock each time — see [Two lines or one large line](#two-lines-or-one-large-line):

- **Enlarge apps on:** the alert and the icon are drawn at **double size**. Use an 8 × 8 Enlarged Icon if your main Icon is 16 × 16.
- **Enlarge apps off:** the alert uses the full 16-pixel height next to the icon, at normal size.

The alert uses the Bottom Line Color (or Rainbow Colors), the Top Line Font, and the general background, gradient or effect. **Alert Repeat** decides how many times the alert text runs through the display before the alert ends. A text short enough to stand still does not scroll, so it stays for the **Duration** instead.

How the alert time works: Home Assistant does not allow a calendar trigger's offset to be a blueprint field, so the blueprint checks every minute which events start in **Alert Minutes Before Start** minutes. Every event gets one alert. If Home Assistant is restarting at exactly that minute, that alert is skipped.

### Example — family calendar

| Field | Value |
|---|---|
| Calendar | `calendar.family` |
| Hours ahead | `36` |
| Calendar Name (Top Line Text) | `Family` |
| Today Text | `Today` |
| Tomorrow Text | `Tomorrow` |
| Show Whole Day Events? | On |
| Show Alerts? | On |
| Alert Minutes Before Start | `15` |
| Alert Repeat | `2` |
| Play Alert Tone | On |
| Icon | `11899` |
| Top Line Color | Yellow |
| Custom Text | `Nothing planned` |

On screen, two lines next to the calendar icon:

```
Family
Today 14:00 Dentist // 19:30 Football // Tomorrow 09:00 Deliver parcel
```

At 13:45 an alert interrupts the display with three beeps: `Event Dentist starts at 14:00`, on one line, shown twice.

### Example — alerts only

| Field | Value |
|---|---|
| Calendar | `calendar.work` |
| Show Timeline? | **Off** |
| Show Alerts? | On |
| Alert Minutes Before Start | `5` |
| Play Alert Tone | On |

No app in the rotation, just a heads-up 5 minutes before each meeting.

> **Upgrading from an earlier version?** See [Appendix C](#list-calendar--from-v12).

---

## 10. List ToDo Items

**File:** `awtrix_ng_tc002_list_todo_items.yaml` · **Blueprint name:** *AWTRIX NG TC002 List ToDo Items* · **Version:** V2.0

### What it does

Shows the open items from a Home Assistant to-do list on the clock. Completed items are ignored. It uses the same layout as the [Create Sensor App](#6-create-sensor-app): a large icon on the left, with the **Top Line Text** above and the **to-do items** below. The top line can start with an optional small **Top Line Icon**, in front of the Top Line Text.

```
+----------------+-+--------+-+-----------------------+
|                | | top    | |  Top Line Text        |
|   Main icon    | | icon   | |  (e.g. To Do:)        |
|    16 × 16     | +--------+-+-----------------------+
|                | |  Milk // Bread // Coffee  ...    |
+----------------+-+----------------------------------+
```

Both icons are optional. Without a Top Line Icon, the Top Line Text starts directly after the main icon; without a main icon, both lines start at the left edge.

Choose **One large line** to show the icon with the Top Line Text and the items on a single line, like the earlier version did.

### When it runs

- Toggle on → the app is created.
- Toggle off → the app is deleted.
- The to-do list changes (while the toggle is on) → the app is updated immediately.

With items on the list, the clock switches to the app every time it is sent. With an empty list, it only switches when **Switch to app on value change** is on.

### Fields — General

| Field | What it does |
|---|---|
| **ToDo List** | The `todo` entity to read. |
| **AWTRIX Displays**, **Extra MQTT prefixes**, **Toggle Helper** | See [section 4](#4-fields-you-will-see-in-almost-every-blueprint). |
| **Icon** | The main icon on the left. An icon on the clock, or the web address of a PNG or JPEG picture. Empty by default — leave it empty for no icon. |
| **Push Icon**, **Enlarged Icon**, **Icon Width**, **Icon Height** | As in the Create Sensor App — see [Fields — Icon](#fields--icon). Use an 8 × 8 **Enlarged Icon** with a 16 × 16 main Icon, or one large line stays small — see [Why an Enlarged Icon?](#why-an-enlarged-icon). |

### Fields — Top line

| Field | What it does |
|---|---|
| **Top Line Text** | The text on the top line, e.g. the name of the list. Default `To Do:`. In one large line it is shown in front of the items. Leave it empty for no top line text. This was the **Prefix** field in earlier versions. |
| **Top Line Icon (Optional)** | A small 8 × 8 icon at the front of the top line, before the Top Line Text. An icon on the clock, or the web address of a PNG or JPEG picture. The text then starts 1 pixel to the right of it. Leave it empty for no icon. In one large line it sits in front of the whole line; with Enlarge apps on it is only used when no main Icon is set. |
| **Top Line Font** | The font of the top line. In one large line it is used for the whole line. |
| **Top Line Color** | The text colour of the top line. |
| **Top Line Background Color** | Black *(default)* = no line background. Any other colour gives the top line its own background — see [Line backgrounds](#line-backgrounds). |
| **Top Line Rainbow Colors** | Shows the top line in rainbow colours. |

The top line has no sensor or template: it always shows the optional Top Line Icon followed by the text you typed.

### Fields — Bottom line (to-do items)

| Field | What it does |
|---|---|
| **Custom Text** | Shown on the bottom line when the list is empty. Default `Relax! You have nothing to do!`. |
| **Show Empty ToDo List** | Off: the app is removed from the clock while the list is empty, instead of showing the Custom Text. |
| **Bottom Line Font** | The font of the to-do items. |
| **Bottom Line Color** | The text colour of the to-do items. |
| **Bottom Line Background Color** | Black *(default)* = no line background. Any other colour gives the bottom line its own background. |
| **Bottom Line Rainbow Colors** | Shows the to-do items in rainbow colours. |

The items are joined with ` // `. The bottom line has no icon and no threshold colours.

### Layout and Advanced options

**Display Mode**, **Text Alignment**, **Text Case**, **Background Color**, **Background Gradient**, **Effect**, **Repeat**, **Duration**, **Lifetime**, **Switch to app on value change** and **Scroll** work exactly as in the [Create Sensor App](#fields--layout-options). One large line follows the clock's **Enlarge apps** setting in the same way — see [Two lines or one large line](#two-lines-or-one-large-line).

In **one large line**:

- The line shows the Top Line Text followed by the items, e.g. `Shopping: Milk // Bread // Coffee`, in the **Top Line Font**.
- The Top Line Text keeps the Top Line Color and the items keep the Bottom Line Color. With Rainbow Colors on either line, the whole line is drawn in rainbow colours.
- The Top Line Background Color, when set, becomes the background of the whole line.

### App naming

The app is named after the to-do list: `todo.shopping_list` becomes the app `todo_shopping_list`. One automation per list.

### Example — shopping list

| Field | Value |
|---|---|
| ToDo List | `todo.shopping_list` |
| Icon | `29644` |
| Top Line Text | `Shopping:` |
| Top Line Icon | a small shopping-cart icon |
| Top Line Color | Yellow |
| Custom Text | `Nothing to buy` |
| Show Empty ToDo List | Off |
| Background Gradient | On, dark blue to black |
| Scroll Speed Percentage | `90` |

On screen, two lines next to the checklist icon, with the cart icon in front of `Shopping:`:

```
[cart] Shopping:
Milk // Bread // Coffee // Apples
```

With **Show Empty ToDo List** off, the app disappears from the rotation entirely once you have ticked everything off — no wasted screen time.

> **Upgrading from an earlier version?** See [Appendix C](#list-todo-items--from-v11).

---

## 11. Countdown Timer

**File:** `awtrix_ng_tc002_countdown_timer.yaml` · **Blueprint name:** *AWTRIX NG TC002 Countdown Timer* · **Version:** V2.0

### What it does

Counts down to a date and time — a birthday, a holiday, New Year — and updates once a minute. It uses the same layout as the [Create Sensor App](#6-create-sensor-app): a large icon on the left, with a label on the top line and the countdown on the bottom line. Both lines can start with an optional small icon.

```
+----------------+-+--------+-+-----------------------+
|                | | top    | |  Vacation             |
|   Main icon    | | icon   | |                       |
|    16 × 16     | +--------+ +-----------------------+
|                | | bottom | |  47d 12h 30m          |
|                | | icon   | |                       |
+----------------+-+--------+-+-----------------------+
```

When the event starts, the countdown makes way for the **Event Started Message** or the **Event Reached Text**. These are always shown on their own, on **one line**, and a start sound can play.

### When it runs

- Toggle on → the app is created and the clock switches to it.
- Toggle off → the app is deleted.
- **Every minute** → the countdown is recalculated and sent again. With **Switch to app on update** on, the clock also switches to the app every minute.
- **The minute the event starts** → the start sound plays once (see [Start sound](#start-sound)).

### Fields — Event

| Field | What it does |
|---|---|
| **Event Name** | Short name for the event. Shown on the top line, and doubles as the app name on the clock: the event name in lowercase, with spaces and anything else turned into underscores. `Summer Vacation` becomes the app `summer_vacation`. |
| **Event Date** | Target date, `YYYY-MM-DD`. |
| **Event Time** | Target time, `HH:MM:SS`, 24-hour. |
| **Show Event Name on Clock** | On: the Event Name is shown on the top line when the Top Line Text is empty. Off: no event name. |
| **Show Seconds** | Include seconds. On by default. The app only refreshes every minute, so these are approximate. |
| **Event Started Message** | Shown from the moment the event starts until midnight that day. Leave empty to skip straight to the next field. |
| **Event Reached Text** | Shown once the event day is over. Also used on the day itself if the field above is empty. The default is `Now!`. **Clear this field and leave the one above empty, and the app removes itself automatically after the event.** |
| **Sound File / Track** | Optional. Played once when the event starts: the name of a stored MP3 or melody on the clock (without `.mp3` / `.txt`). Has priority over the Alert Melody. No default. |
| **Alert Melody (RTTTL)** | Played once when the event starts, when no Sound File / Track is set. Default `alert:d=4,o=5,b=180:8c6,8p,8c6,8p,8c6` (three short beeps). Leave both sound fields empty for no sound. |

The countdown scales itself: days are dropped once there are none left, then hours, so you never see `0d 0h 5m`.

### Fields — Icon

| Field | What it does |
|---|---|
| **Icon** | The main icon on the left. Empty by default — leave it empty for no icon. |
| **Push Icon**, **Enlarged Icon**, **Icon Width**, **Icon Height** | As in the Create Sensor App — see [Fields — Icon](#fields--icon). Use an 8 × 8 **Enlarged Icon** with a 16 × 16 main Icon, or the large messages stay small — see [Why an Enlarged Icon?](#why-an-enlarged-icon). |

### Fields — Top line

| Field | What it does |
|---|---|
| **Top Line Text** | Optional. The text on the top line. Leave it empty to show the **Event Name** there (when Show Event Name on Clock is on). |
| **Top Line Icon (Optional)** | A small 8 × 8 icon at the front of the top line. |
| **Top Line Font**, **Top Line Color**, **Top Line Background Color**, **Top Line Rainbow Colors** | As in the Create Sensor App. The Top Line Font is also used for the one-line views. |

The top line has no sensor or template. Once the event has started, the top line is not shown — only the message is.

### Fields — Bottom line (countdown)

| Field | What it does |
|---|---|
| **Bottom Line Icon (Optional)** | A small 8 × 8 icon at the front of the countdown, e.g. an hourglass. Not used in one large line. |
| **Bottom Line Font** | The font of the countdown. |
| **Bottom Line Color** | The text colour of the countdown and of the messages. This was **Text Color** in earlier versions. |
| **Bottom Line Background Color** | Black *(default)* = no line background. Any other colour gives the bottom line its own background — see [Line backgrounds](#line-backgrounds). |
| **Bottom Line Rainbow Colors** | Shows the countdown and the messages in rainbow colours. This was **Rainbow Colors** in earlier versions. |

There are no threshold colours.

### Layout and Advanced options

**Display Mode**, **Text Alignment**, **Text Case**, **Background Color**, **Background Gradient**, **Effect**, **Repeat**, **Duration**, **Lifetime** and **Scroll** work exactly as in the [Create Sensor App](#fields--layout-options). **Switch to app on update** switches the clock to the countdown on every minute update.

### What you see, and when

| Moment | What you see |
|---|---|
| **Counting down**, Display Mode *Two lines* | The label (Top Line Text or Event Name) on top, the countdown below, each with its optional icon. |
| **Counting down**, Display Mode *One large line* | One line with the label followed by the countdown, e.g. `Vacation 47d 12h 30m`. The label keeps the Top Line Color, the countdown the Bottom Line Color. |
| **Event started**, until midnight | The **Event Started Message** on its own, on one line. |
| **Event day over** (or no Event Started Message) | The **Event Reached Text** on its own, on one line. |
| **Event over, both messages empty** | The app is removed from the clock. |

Every one-line view follows the clock's **Enlarge apps** setting, read from the clock each time the app is sent — see [Two lines or one large line](#two-lines-or-one-large-line):

- **Enlarge apps on:** the line and the icon are drawn at **double size**. Use an 8 × 8 Enlarged Icon if your main Icon is 16 × 16.
- **Enlarge apps off:** the line uses the full 16-pixel height next to the icon, at normal size.

### Start sound

When the event starts, the clock plays the **Sound File / Track**, or the **Alert Melody** when no sound file is set. The sound goes out once, with the first minute update at or after the Event Time, and only while the toggle helper is on.

- It also plays when the app removes itself because both messages are empty.
- It does not play when Home Assistant was restarting at that moment, or when the toggle is switched on later.
- See [Sound File / Track and Alert Melody](#sound-file--track-and-alert-melody-rtttl) and [No sound](#no-sound) if you hear nothing.

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
| Bottom Line Icon | a small hourglass icon |
| Top Line Color | Yellow |
| Background Gradient | On, dark blue to black |

Shows `Vacation` above `47d 12h 30m` counting down, switches to a large `Bon voyage!` on the day with three beeps, then removes itself from the clock at midnight.

### Example — New Year

| Field | Value |
|---|---|
| Event Name | `NewYear` |
| Event Date | `2027-01-01` |
| Event Time | `00:00:00` |
| Top Line Text | `New Year in` |
| Show Seconds | On |
| Event Started Message | `Happy New Year!` |
| Event Reached Text | `Happy New Year!` |
| Icon | a 16 × 16 fireworks icon |
| Enlarged Icon | the 8 × 8 version of that fireworks icon |
| Sound File / Track | `newyear` (an MP3 on the clock) |
| Bottom Line Rainbow Colors | On |
| Effect | Fireworks |

At midnight the clock plays `newyear.mp3` and shows `Happy New Year!` large, next to the fireworks icon.

> **Note:** emoji do not render on the matrix — the font has no glyphs for them and they come out as `?`. Stick to plain text in these fields.

> **Upgrading from an earlier version?** See [Appendix C](#countdown-timer--from-v11).

## 12. Show Music Assistant App

**File:** `awtrix_ng_tc002_show_musicassistant_app.yaml` · **Blueprint name:** *AWTRIX NG TC002 Show Music Assistant App* · **Version:** V1.1

### What it does

Shows the song that a **Music Assistant** player is playing: the album cover on the left, the artist on the top line, and the song title with a thin progress bar on the bottom line. While the app is on screen, the clock's buttons control the music.

```
+----------------+-+-----------------------------------+
|                | |  Van Halen                        |
|  Album cover   | +-----------------------------------+
|    16 × 16     | |  Panama (2015 Remaster)           |
|                | |  ==========-------------- (1 px)  |
+----------------+-+-----------------------------------+
```

| Button | While the app is on screen |
|---|---|
| **Right** | Next song |
| **Left** | Previous song |
| **Middle** | Play / pause |

### When it runs

- Toggle on → the app is sent and the clock switches to it, when music is playing (or paused, with Show when paused on).
- Toggle off → the app is removed and the clock's buttons work as usual again.
- A new song, artist, cover or play state → the app is updated.
- Every **Progress Update Interval** (5 seconds by default) → the progress bar moves on. A long title keeps scrolling smoothly: the clock only restarts a text that changed.
- The music has stopped for **10 seconds** → the app is removed. A short stop while skipping to the next song keeps it on screen.
- With **Stop app rotation while playing** on: the music starts → the clock switches to the app and stops rotating; the music has stopped or paused for 10 seconds → the rotation starts again. See [Stopping the rotation while playing](#stopping-the-rotation-while-playing).

The app has a fixed lifetime of 2 minutes, so it disappears by itself if Home Assistant stops sending it.

### Fields — General

| Field | What it does |
|---|---|
| **AWTRIX Displays**, **Extra MQTT prefixes**, **Toggle Helper** | See [section 4](#4-fields-you-will-see-in-almost-every-blueprint). |
| **Music Assistant Player** | The Music Assistant `media_player` to follow. |
| **App Name** | The name of the app on the clock. Default `music_assistant`. Use a different name for every automation — see [App naming](#app-naming). |
| **Home Assistant URL** | The address the clock uses to reach Home Assistant, e.g. `http://192.168.1.10:8123`, without a `/` at the end. Needed for covers that Home Assistant serves itself — see [Album covers](#album-covers). |
| **Icon without cover (Optional)** | An icon on the clock, shown in the cover space when a song has no cover. Leave it empty and the text uses the full width. |

### Fields — Top line (artist) and Bottom line (song title)

| Field | What it does |
|---|---|
| **Font**, **Color**, **Rainbow Colors** | Per line, as in the [Create Sensor App](#fields--top-line-and-bottom-line). |
| **Show Progress Bar** | A 1-pixel line under the title that fills up while the song plays. On by default. Not shown for streams without a length, such as radio. |
| **Progress Bar Color** | The part that has been played. Default light blue. |
| **Progress Bar Track Color** | The part still to come. Default dark grey; black hides it. |

The top line shows the artist, or — when there is none — the album artist, the album or the player's name. With the progress bar on, the title line is 7 pixels high: the 8-pixel fonts (*Matrix … 8*) lose their bottom row there, so use Small, Large or a 6-pixel font.

### Layout and Advanced options

**Text Alignment**, **Text Case**, **Background Color**, **Background Gradient**, **Effect**, **Duration** and **Scroll** work as in the [Create Sensor App](#fields--layout-options). There is no Display Mode: the app always shows two lines.

| Field | What it does |
|---|---|
| **Use the clock's buttons** | On *(default)*: the buttons control the music while this app is on screen — see [The clock's buttons](#the-clocks-buttons). Off: the buttons work as usual. |
| **Stop app rotation while playing** | Off *(default)*: the app takes its turn in the normal rotation. On: the clock stays on this app while music plays — see [Stopping the rotation while playing](#stopping-the-rotation-while-playing). |
| **Show when paused** | On *(default)*: the app stays while the music is paused, with the progress bar standing still. Off: the app is removed 10 seconds after you pause, and comes back when the music plays again. |
| **Progress Update Interval** | How often the app is sent while it is shown: every 5 *(default)*, 10, 15, 20 or 30 seconds. Shorter moves the bar more smoothly but sends more messages. |
| **Switch to app on new song** | Jump to this app whenever a new song starts. |

### The clock's buttons

Normally the left and right buttons switch apps. While this app is on screen, the blueprint switches the clock's **Block buttons** setting on (**Display → App rotation**), so the buttons only control the music. As soon as another app comes on screen, it switches the setting off again. While the app is shown, this means:

- A double press on the middle button does not switch the display off, and holding it does not open the menu. A double press pauses and plays again.
- The app still moves on with the normal rotation, after its Duration.
- The clock does not report when a notification is on screen. With a notification on top of the music app, the middle button dismisses it **and** pauses or plays the music.
- The blueprint takes over the Block buttons setting: if you keep it on yourself, it is switched off as soon as another app is shown. The setting is only sent when it has to change, because the clock saves it every time.

### Stopping the rotation while playing

With **Stop app rotation while playing** on, the blueprint switches the clock's automatic rotation — the **Transition** setting, `autoTransition` — off while music plays, so the clock shows only the Music Assistant app:

| Moment | What happens |
|---|---|
| Music starts, or the toggle goes on while music plays | The clock switches to the app and the rotation is switched **off** |
| You skip to another song | Nothing changes: the short stop while skipping does not count |
| The music has stopped or paused for **10 seconds** | The rotation is switched **on** again and the other apps come back |
| The toggle goes off | The app is removed and the rotation is switched **on** again |

- The rotation is always switched back **on**, also when you normally keep it off on the clock.
- With **Show when paused** on, a paused song stays in the rotation after the 10 seconds; it just no longer holds the clock.
- Like Block buttons, the setting is only sent when it has to change, because the clock saves its settings every time.
- Notifications still interrupt the app as usual.

### Album covers

Music Assistant gives each song a cover in one of two ways:

- **From a streaming service** — a full `https://` address. The clock downloads it directly.
- **Through Home Assistant** — an address that starts with `/api/media_player_proxy/…`. The blueprint puts the **Home Assistant URL** in front of it. Without that field, the cover space shows the Icon without cover.

The clock downloads each cover once, crops it to a square and shrinks it to 16 × 16. Home Assistant changes the access code in its cover addresses every few minutes, so the clock may download the same cover again and show an empty cover space for a moment. For `https`, the clock checks the certificate: a self-signed certificate fails.

### Example — kitchen speaker

| Field | Value |
|---|---|
| AWTRIX Displays | your TC002 |
| Toggle Helper | `input_boolean.awtrix_music` |
| Music Assistant Player | `media_player.kitchen` |
| Home Assistant URL | `http://192.168.1.10:8123` |
| Icon without cover | a music-note icon |
| Text Alignment | Center |
| Switch to app on new song | On |

Turn the helper on and the clock jumps to the song that is playing. Press right to skip it; every new song brings the clock back to the app.


---

## 13. Toggle Built-in Apps

**File:** `awtrix_ng_tc002_toggle_builtin_apps.yaml` · Needs AWTRIX NG **1.2.2** or newer.

### What it does

Switches built-in apps on and off from toggle helpers. Your rotation order, pushed apps and scripts are left exactly as they are.

**On the TC002 only the Time toggle does something.** The TC002 has just two built-in apps, **Time** and **Status**; the date is part of its clock faces, and it has no Temperature, Humidity or Battery app. The blueprint keeps five fields so it matches the TC001 version, and all five need a helper. Switch the Status app on the **Apps** tab of the clock's web interface.

### When it runs

- One of the toggle helpers changes → the switch of that one app is sent, e.g. `awtrixNG/cmd/apps/Time/enabled` `false`.
- Home Assistant starts → all switches are sent again (**Apply on Home Assistant start**, on by default). The clock saves every switch itself, so this is only a safety net.

A switched-off app stays installed and keeps its slot. A helper that is unavailable or unknown is skipped. There is no rotation-order field: arrange your apps on the **Apps** tab of the clock's web interface.

> **Do not use `<prefix>/cmd/apps/order` to switch single apps.** Its `disabled` list is the *complete* off-list: every app it does not name is switched on, including apps you switched off on purpose.

---

## 14. Toggle Indicators

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

---

## 15. Moodlight

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

---

## 16. Set App Time

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

The clock's own default is 7 seconds. The blueprint converts the seconds to the milliseconds AWTRIX NG stores (`appDurationMs`).

---

## 17. Troubleshooting

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

Something published to `<prefix>/cmd/apps/order`, which sets the whole arrangement at once. The current [Toggle Built-in Apps](#13-toggle-built-in-apps) never uses it; look for an older copy of that blueprint, or another automation or script. Put things right on the **Apps** tab of the clock's web interface.

### The clock reboots or misbehaves

Almost always **Switch to app on value change** enabled on a fast-changing sensor. Turn it off.

### Text shows `?` characters

The matrix font has no glyph for that character. Emoji and other unusual symbols come out as `?`. Latin accents, Greek, Cyrillic, `°`, `€` and the other common currency signs are fine on AWTRIX NG 1.2.2 or newer.

### No sound

- The clock's **volume** (or **alertVolume**) is `0`. Commands succeed silently.
- The clock runs firmware older than **1.2.2**, which does not understand the new sound format.
- The sound file does not exist in `/MP3` or `/MELODIES`.
- The RTTTL string has a typo — test it in the clock's web interface on the **Audio** tab first.

### A layout app does not show up, or shows the old content

This covers the Create Notification, Create Sensor App, Weather App, List Calendar, List ToDo Items, Countdown Timer and Show Music Assistant App.


- The clock rejects a whole layout when one part is wrong, and keeps showing what it had. Check `<prefix>/cmd/apps/pushed/+/result` for the reason. `"outside the display"` means a box is too big — check **Icon Width** and **Icon Height**. An `icon could not be decoded` error means an icon name or web address is wrong.
- Shows only one line? In the Create Notification the Top Line Text is empty. In the Create Sensor App the Bottom Line Sensor and Text are both empty, or Display Mode is *One large line*. The Countdown Timer always shows the Event Started Message and the Event Reached Text on one line.
- Icon and text not enlarged in one large line? The clock's **Enlarge apps** setting is off, or the icon is taller than 8 pixels. Set an 8 × 8 **Enlarged Icon** — see [Why an Enlarged Icon?](#why-an-enlarged-icon).
- A template that reads other entities does not update? Turn on **Refresh every minute**.
- A line that used a template is suddenly empty or shows the sensor? Since Create Sensor App V2.1 the template goes in the **Text** field — see [Create Sensor App — from V2.0 to V2.1](#create-sensor-app--from-v20-to-v21).

### The Show Music Assistant App

- **No album cover?** Fill in the **Home Assistant URL**, with `http://`, and without a `/` at the end. A cover over `https` needs a real certificate; a self-signed one fails. The clock's **Log** tab shows a line starting with `picture from` when a cover cannot be loaded.
- **The buttons still switch apps?** Turn **Use the clock's buttons** on, and check that the clock is selected under AWTRIX Displays (or in Extra MQTT prefixes).
- **The buttons stay blocked after you removed the automation?** Switch **Block buttons** off on the clock, under **Display → App rotation**.
- **The apps no longer rotate?** With **Stop app rotation while playing** on, the rotation starts again 10 seconds after the music stops. Removed the automation while music was playing? Switch **Transition** back on in the clock's web interface, or with the *Transition* switch of the clock in Home Assistant.

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

For most blueprints the fields are the same as before. The **Create Notification**, **Create Sensor App**, **Weather App**, **List Calendar**, **List ToDo Items** and **Countdown Timer** are rebuilt for the TC002 with the two-line layout and have new fields — see [Appendix C](#appendix-c--upgrading-from-earlier-versions).

The TC002 has never run AWTRIX 3. If you are moving a TC001 from AWTRIX 3 to AWTRIX NG, see Appendix B of the [TC001 manual](AWTRIX-NG-TC001-MANUAL.md#appendix-b--coming-from-awtrix-3).

Full firmware documentation: **[ang.blueforcer.de/tc002](https://ang.blueforcer.de/tc002/)**

---

## Appendix C — Upgrading from earlier versions

After updating a blueprint, open each automation made with it once and check the points below.

### Create Notification — from V1.2

Existing automations keep working after you update the blueprint: Notification Text (now Bottom Line Text), Icon, Push Icon, Text Color (now Bottom Line Color), Gradient 1 / Gradient 2, Rainbow Colors, Background Color, Text Case, Effect and all advanced options carry over. With the Top Line Text empty, the notification looks like before: the icon and one line. Things to check:

- The main icon no longer has a default icon. Fill in **Icon** if you relied on it.
- Using a 16 × 16 icon? Add an 8 × 8 **Enlarged Icon**, or the text stays small on a clock with Enlarge apps on.
- Want a title? Fill in the new **Top Line Text**.

### Create Sensor App — from V2.0 to V2.1

> **Breaking change:** V2.1 removes the **Top Line Template** and **Bottom Line Template** fields. The **Top Line Text** and **Bottom Line Text** fields now accept template code.
>
> Did you use a Template field? Copy the template code to the text field before you upgrade! Or open the automation after updating the blueprint, and **re-enter the template code in the Text field of the same line**. Home Assistant does not move it for you: until you do, that line shows its Sensor, or nothing at all.
>
> Automations that only use the Sensor and Text fields keep working as before.

### Create Sensor App — from V1.1

V2.0 has new fields, so an automation made with V1.1 (or with the shared `awtrix_ng_create_sensor_app.yaml`) needs its fields filled in again:

| V1.1 field | V2.0 field |
|---|---|
| *(the app was named after the sensor)* | **App Name** — required. Enter the old name (e.g. `sensor_bedroom_co2`) to keep the app's slot on the clock |
| Sensor | Top Line Sensor |
| Template (Optional) | Top Line Text — paste the template there |
| Text Color | Top Line Color |
| Use Threshold value? / Threshold Value / Low / High colour | Top Line - Threshold Value / Low / High colour. There is no on/off switch any more: entering a Threshold Value turns the threshold colours on |
| Rainbow Colors | Top Line Rainbow Colors |
| Gradient 1 / Gradient 2 | gone. Use Rainbow Colors, or the Background Gradient |
| Push Icon | Push Icon (one large line with Enlarge apps on only) |
| Background Color, Effect, Text Case | moved to **Layout options** |

### Weather App — from V1.1

Existing automations keep working after you update the blueprint: the weather entity, Show switches, Language, Weather Overlay, Push Icon, Text Color (now Bottom Line Color), Rainbow Colors (now Bottom Line Rainbow Colors), Background Color, Text Case, Effect and the advanced options carry over. Things to check:

- The weather now shows **two lines**: the condition above the values. Set **Display Mode** to *One large line* for the old look.
- With Rainbow Colors on, only the values are in rainbow colours now. Turn on **Top Line Rainbow Colors** for the condition too.
- A missing condition icon still shows the weather, without the picture.

### List Calendar — from V1.2

Existing automations keep working after you update the blueprint: the Prefix becomes the Calendar Name, and the calendar settings, alert settings, Icon, Push Icon, Text Color (now Bottom Line Color), Rainbow Colors (now Bottom Line Rainbow Colors), Background Color, Text Case and Effect carry over. Things to check:

- The app now shows **two lines** by default. Set **Display Mode** to *One large line* for the old look.
- The alert time is **15 minutes** by default, as before. Change **Alert Minutes Before Start** to choose another time — no YAML editing needed any more.
- The alert is now shown on one line, large or small according to Enlarge apps.

### List ToDo Items — from V1.1

Existing automations keep working after you update the blueprint: the Prefix becomes the Top Line Text, and the icon, Push Icon, Text Color (now Bottom Line Color), Rainbow Colors (now Bottom Line Rainbow Colors), Custom Text, Background Color and Effect carry over. Two things to check:

- The app now shows **two lines** by default. Set **Display Mode** to *One large line* for the old look.
- The **Icon** no longer defaults to `29644`. If your automation never changed the icon, enter `29644` again.

> **Tip:** this pairs beautifully with a voice assistant. "Add coffee to the shopping list" and it is on the clock a second later.

### Countdown Timer — from V1.1

Existing automations keep working after you update the blueprint: the event fields, Icon, Push Icon, Text Color (now Bottom Line Color), Rainbow Colors (now Bottom Line Rainbow Colors), Background Color, Text Case and Effect carry over. Things to check:

- The countdown now shows **two lines**: the Event Name above the countdown. Set **Display Mode** to *One large line* for the old look.
- **Gradient 1 / Gradient 2** are gone. Use Rainbow Colors, or the Background Gradient.
- The start sound is new and plays the Alert Melody by default. Clear the Alert Melody if you want a silent start.

---

# Release Notes
**V6.11**
* Weather App V2.0 (TC002): rebuilt with the same layout as the Create Sensor App — see [Weather App](#7-weather-app)
  * The weather icon on the left, the condition on the top line and the temperature, wind speed and humidity on the bottom line
  * Optional 8 × 8 Top Line Icon and Bottom Line Icon, adjustable icon size and Push Icon
  * Per-line font, colour, background colour and rainbow colours; background colour, vertical gradient, effect and weather overlay
  * Display Mode: two lines, or one large line as in earlier versions, following the clock's Enlarge apps setting
  * The icon is still the weather condition. When the clock does not have that icon, the app is sent again without icons, so the weather is still shown
  * The weather handling is unchanged: same entity, conditions, ten languages, Show switches and app name

**V6.10**
* Show Music Assistant App V1.1 (TC002): new option **Stop app rotation while playing**. While music plays, the clock's automatic rotation is switched off and the clock stays on the Music Assistant app; 10 seconds after the music stops or pauses, or when the toggle goes off, the rotation is switched on again so the other apps are shown — see [Stopping the rotation while playing](#stopping-the-rotation-while-playing)

**V6.9**
* New: Show Music Assistant App V1.0 (TC002) — see [Show Music Assistant App](#12-show-music-assistant-app)
  * The album cover (16 × 16) on the left, the artist on the top line, and the song title with a 1-pixel progress bar on the bottom line
  * While the app is on screen, the clock's right button skips to the next song, the left button to the previous song and the middle button plays or pauses
  * Show when paused, Progress Update Interval, Switch to app on new song, and the usual fonts, colours, background colour, gradient and effect
* Manual: shorter and easier to read. The contents list says what every blueprint does, the upgrade notes are collected in [Appendix C](#appendix-c--upgrading-from-earlier-versions), and the Toggle Built-in Apps, Set App Time and Create Sensor App chapters are tightened

**V6.8**
* Create Sensor App V2.1 (TC002): the **Top Line Template** and **Bottom Line Template** fields are gone. The **Top Line Text** and **Bottom Line Text** fields now accept template code as well as a fixed text, and still have priority over the line's Sensor — see [What a line shows](#what-a-line-shows)
  * **Breaking change:** if you used a Template field, copy the template code to the text field before you upgrade! Or re-enter the template code in the Text field of the same line after updating the blueprint. Until then that line shows its Sensor, or nothing — see [Create Sensor App — from V2.0 to V2.1](#create-sensor-app--from-v20-to-v21)

**V6.7**
* Create Notification V2.0 (TC002): rebuilt with the same layout as the Create Sensor App — see [Create Notification](#5-create-notification)
  * A 16 × 16 main icon with an optional title — the new **Top Line Text** — on the top line and the message on the bottom line
  * **Notification Text** is now **Bottom Line Text**, in the Bottom line section; **Text Color** is now **Bottom Line Color**
  * Optional 8 × 8 Top Line Icon and Bottom Line Icon, adjustable icon size, Push Icon and Enlarged Icon
  * Per-line font, colour, background colour and rainbow colours; background colour, vertical gradient and effect. Gradient 1 / Gradient 2 still colour the message
  * Display Mode: two lines, or one large line with the title and the message together, following the clock's Enlarge apps setting. With an empty Top Line Text the notification is one large line, as before
  * The main icon no longer has a default icon
  * The notification handling is unchanged: Notification Name and dismissing, Play Alert Tone with Sound File / Track and Alert Melody, Hold, Stack, Repeat, Duration and Scroll

**V6.6**
* List Calendar V2.0 (TC002): rebuilt with the same layout as the Create Sensor App — see [List Calendar](#9-list-calendar)
  * A 16 × 16 main icon with the **Calendar Name** (the former **Prefix**) on the top line and the events on the bottom line
  * Optional 8 × 8 Top Line Icon and Bottom Line Icon, adjustable icon size, Push Icon and Enlarged Icon
  * Per-line font, colour, background colour and rainbow colours; background colour, vertical gradient and effect
  * Display Mode: two lines, or one large line with the Calendar Name and the events together, following the clock's Enlarge apps setting
  * New **Alert Minutes Before Start** field: choose how many minutes before an event the alert is shown (default 15, as before)
  * The alert is always shown on its own single line, large or small according to the clock's Enlarge apps setting
  * New **Alert Repeat** field: how many times the alert text is shown (default 1)
  * The main icon no longer has a default icon
  * The calendar handling is unchanged: same events, Today / Tomorrow texts, whole-day events, Custom Text, Show Empty Calendar, alert text and sounds, and app name

**V6.5**
* Countdown Timer V2.0 (TC002): rebuilt with the same layout as the Create Sensor App — see [Countdown Timer](#11-countdown-timer)
  * A 16 × 16 main icon with a label on the top line — the new **Top Line Text**, or the Event Name — and the countdown on the bottom line
  * Optional 8 × 8 Top Line Icon and Bottom Line Icon, adjustable icon size and Push Icon
  * Per-line font, colour, background colour and rainbow colours; background colour, vertical gradient and effect
  * Display Mode: two lines, or one large line with the label and the countdown together, following the clock's Enlarge apps setting
  * The **Event Started Message** and the **Event Reached Text** are always shown on their own single line, large or small according to the clock's Enlarge apps setting
  * New **Sound File / Track** and **Alert Melody (RTTTL)** fields: a sound plays once when the event starts. The sound file has priority over the melody
  * The main icon no longer has a default icon. Gradient 1 / Gradient 2 are replaced by the Background Gradient
  * The countdown itself is unchanged: same calculation, messages, automatic removal and app name
* Create Sensor App, List ToDo Items and Countdown Timer (TC002): new optional **Enlarged Icon**. AWTRIX NG does not enlarge an app whose icon is taller than 8 pixels, so with a 16 × 16 main Icon one large line stayed small. An 8 × 8 Enlarged Icon is used instead in the enlarged view — see [Why an Enlarged Icon?](#why-an-enlarged-icon)

**V6.4**
* List ToDo Items V2.0 (TC002): rebuilt with the same layout as the Create Sensor App — see [List ToDo Items](#10-list-todo-items)
  * A 16 × 16 main icon with the **Top Line Text** (the former **Prefix**) on the top line and the to-do items on the bottom line
  * Optional 8 × 8 Top Line Icon, adjustable icon size and Push Icon
  * Per-line font, colour, background colour and rainbow colours
  * Background colour, vertical gradient and effect, as in the Create Sensor App
  * Display Mode: two lines, or one large line with the Top Line Text and the items together, following the clock's Enlarge apps setting
  * The main icon no longer has a default icon
  * The to-do list handling is unchanged: same items, Custom Text, Show Empty ToDo List and app name
* Create Sensor App and List ToDo Items (TC002): the **Top Line Background** and **Bottom Line Background** switches are gone. A line gets its own background as soon as its **Background Color** is set to anything other than black; black (the default) means no line background — see [Line backgrounds](#line-backgrounds)

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
