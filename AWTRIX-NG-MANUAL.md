[← Back to overview](README.md)

# AWTRIX NG Blueprints — User Manual

Blueprints by **Smart Home Junkie** for the **AWTRIX NG** firmware (Ulanzi TC001, TC002 and compatible LED matrix clocks).

These are the AWTRIX 3 blueprints, rebuilt for AWTRIX NG. If you are coming from AWTRIX 3, read [Appendix B](#appendix-b--coming-from-awtrix-3) first — the firmware renamed almost everything, so your old automations will not work until you swap them for these.

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
- [Appendix B — Coming from AWTRIX 3](#appendix-b--coming-from-awtrix-3)

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

### TC001 or TC002?

The blueprints work on both clocks, and an automation built for one runs unchanged on the other. Both show up in the same **AWTRIX Displays** dropdown. A few things look or behave differently, and the chapters below point them out where they matter:

| | Ulanzi TC001 | Ulanzi TC002 |
|---|---|---|
| **Display** | 32 × 8 pixels | 52 × 16 pixels. Apps and notifications are drawn at double size on a 26 × 8 grid, so they fill the display |
| **Text that fits without scrolling** | About 8 characters, fewer next to an icon | About 6 characters, about 4 next to an icon |
| **Built-in apps** | Time, Date, Temperature, Humidity, Battery | Time and Status |
| **Sound** | Melodies on the buzzer, DFPlayer tracks on modified units | MP3 files and melodies on the speaker. No DFPlayer |
| **Indicators** | Three pixels on the right-hand edge | Three small groups of pixels on the right-hand edge |
| **Light sensor** | Yes | No, so automatic brightness does nothing |

Because less text fits on the TC002, your apps scroll sooner there. If you would rather have more text than big text, switch off the clock's `enlargeApps` setting: apps and notifications are then drawn at normal size on the full 52 × 16 pixels, and about 13 characters fit.

---

## 2. Installing a blueprint

1. In Home Assistant, go to **Settings → Automations & Scenes → Blueprints**.
2. Click **Import Blueprint**, or copy the `.yaml` file into `config/blueprints/automation/smarthomejunkie/`.
3. If you copied files manually, go to **Settings → Tools → YAML → Automations** (or restart Home Assistant).
4. Back on the Blueprints page, click the blueprint and choose **Create Automation**.

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
- Animated GIFs work. A GIF that is as wide as the app becomes a background image instead of an icon: 32 pixels wide on the TC001, 26 pixels wide on the TC002.

### Push Icon

What the icon does while the text scrolls past it.

| Option | Behaviour |
|---|---|
| **Icon doesn't move** | Icon stays put, text scrolls past it |
| **Icon moves with text and will not appear again** | Text shoves the icon off screen once; it stays gone |
| **Icon moves with text but appears again when the text starts** | Icon is pushed off, then returns on every scroll cycle *(default)* |

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

Only Create Notification, Create Sensor App and Countdown Timer have the gradient fields. The other blueprints offer Rainbow Colors only.

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

How much fits before the text has to move depends on the clock: about 8 characters on a TC001 and about 6 on a TC002, fewer next to an icon. See [TC001 or TC002?](#tc001-or-tc002)

**Scroll Speed** is a percentage of the clock's normal speed. `100` is normal, `50` is half speed, `200` is double. `0` freezes the text where it starts.

### Effect

An animated background drawn behind the text. See [Appendix A](#appendix-a--effects-overlays-and-palettes) for the full list of 19 and what each one looks like. Choose **None** for a plain background.

### Sound File / Track and Alert Melody (RTTTL)

Two ways to make a noise, used by the blueprints that can play an alert:

- **Sound File / Track** — the name of a sound stored on the clock, without its extension. What you can store depends on the clock:
  - **TC001:** a melody file (`/MELODIES`, without the `.txt`). It has a buzzer and cannot play MP3 files. On a unit with a DFPlayer module, enter the track number instead.
  - **TC002:** an MP3 (`/MP3`, without the `.mp3`) or a melody file. It has a speaker and no DFPlayer.
- **Alert Melody (RTTTL)** — a melody written inline, in the old Nokia ringtone format. Used only when the Sound File field is empty. It works on both clocks.

The default melody, `alert:d=4,o=5,b=180:8c6,8p,8c6,8p,8c6`, is three short beeps. You can test and store melodies in the clock's web interface on the **Audio** tab before pasting them in.

Sound is silent if the clock's **volume** is set to `0` — no error, just no noise.

The blueprints send sound in the format introduced with AWTRIX NG **1.2.2**, so the clock needs that firmware or newer.

---

## 5. Create Notification

**File:** `awtrix_ng_create_notification.yaml`

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

**File:** `awtrix_ng_create_sensor_app.yaml`

### What it does

Puts any Home Assistant sensor into the clock's app rotation, and keeps it up to date. This is the workhorse blueprint — most people end up with several copies of it.

Works with `sensor`, `media_player` and `input_text` entities.

### When it runs

- Toggle on → app is created.
- Toggle off → app is deleted.
- The sensor changes (while the toggle is on) → app text is updated.

The app is **not** updated while the entity is `off`, `idle`, `unavailable` or `unknown` — it keeps the last text it had. For a media player this means the last track stays on the clock after playback stops.

### Fields unique to this blueprint

| Field | What it does |
|---|---|
| **Sensor** | The entity to display. Its state, rounded and with its unit, becomes the text. |
| **Template (Optional)** | Format the value yourself. Overrides the plain state — see the examples below. |
| **Use Threshold value?** | Turns on colour-by-value. |
| **Threshold Value** | The number the sensor is compared against. |
| **Low-value Text color** | Colour used when the value is **below** the threshold. |
| **High-value Text color** | Colour used when the value is **at or above** the threshold. |

### About the Template field

Leave it empty and the app shows the sensor's state with its unit — for example `21.5 °C`.

Fill it in and you get full control. Some useful patterns:

```jinja
{{ states('sensor.living_room_temperature') | round(1) }}C
```

```jinja
Power: {{ states('sensor.house_power') | int }}W
```

```jinja
{{ state_attr('media_player.spotify','media_artist') }} - {{ state_attr('media_player.spotify','media_title') }}
```

```jinja
{{ states('sensor.next_train') }} to {{ state_attr('sensor.next_train','destination') }}
```

> **Note:** the threshold comparison also reads from the template when you use one, stripping out everything that is not a digit, a dot or a minus sign. So `Power: 450W` is compared as `450`.

### Example — CO₂ warning

| Field | Value |
|---|---|
| Sensor | `sensor.bedroom_co2` |
| Template | `{{ states('sensor.bedroom_co2') | int }}ppm` |
| Icon | `12160` |
| Use Threshold value? | On |
| Threshold Value | `1000` |
| Low-value Text color | Green |
| High-value Text color | Red |
| Lifetime | `1800` |
| On lifetime expiry | Mark the app as stale |

Below 1000 ppm the number is green; at 1000 and above it turns red. If the sensor stops reporting for half an hour, the app gets a red frame so you know the reading is stale.

### Example — energy price

| Field | Value |
|---|---|
| Sensor | `sensor.current_electricity_price` |
| Template | `{{ states('sensor.current_electricity_price') | round(2) }} EUR` |
| Icon | `1817` |
| Duration | `8` |
| Scroll the text? | Off |

### App naming

Each copy of this blueprint creates an app on the clock named after the sensor entity, with dots turned into underscores and trimmed to 32 characters. `sensor.bedroom_co2` becomes the app `sensor_bedroom_co2`.

Two consequences worth knowing:

- **One app per sensor.** Two automations pointing at the same sensor will fight over the same app.
- This is the name the app has on the **Apps** tab of the clock's web interface, where you can drag it to the slot you want. The clock remembers that slot.

---

## 7. Weather App

**File:** `awtrix_ng_weather_app.yaml`

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

**File:** `awtrix_ng_rain_forecast.yaml`

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

**File:** `awtrix_ng_list_calendar.yaml`

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

**File:** `awtrix_ng_list_todo_items.yaml`

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

**File:** `awtrix_ng_countdown_timer.yaml`

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

**File:** `awtrix_ng_toggle_builtin_apps.yaml`

### What it does

Switches the five built-in apps — **Time, Date, Temperature, Humidity, Battery** — on and off from five toggle helpers. Your rotation order, pushed apps and scripts are left exactly as they are.

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

**File:** `awtrix_ng_toggle_indicators.yaml`

### What it does

Controls the three small indicator pixels on the right-hand edge of the panel — top, middle and bottom — each from its own toggle helper. They can be any colour, can blink, and can play a sound when they come on.

On the TC002 each indicator is a small group of pixels instead of a single pixel: a corner at the top and at the bottom, and a short bar in the middle.

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

**File:** `awtrix_ng_moodlight.yaml`

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

**File:** `awtrix_ng_set_transition_time.yaml`

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
- You entered the name of an MP3 on a TC001. It has a buzzer and only plays melodies.
- The RTTTL string has a typo — test it in the clock's web interface on the **Audio** tab first.

### The app name looks mangled

That is deliberate. AWTRIX NG only accepts `A–Z`, `a–z`, `0–9`, `-` and `_` in app names, up to 32 characters, so the blueprints replace anything else with an underscore. `sensor.living_room_temperature` becomes `sensor_living_room_temperature`.

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

## Appendix B — Coming from AWTRIX 3

AWTRIX NG is not a drop-in upgrade. It changed the MQTT topics, renamed nearly every payload key, and — most importantly — became **strict**: a key it does not recognise causes the *whole* message to be rejected, where AWTRIX 3 would quietly ignore it.

That is why your old blueprints go silent rather than half-working. These NG blueprints handle all of it for you; this appendix is here so the changed *fields* make sense.

### What changed in the blueprint fields

| Field | AWTRIX 3 | AWTRIX NG |
|---|---|---|
| **Repeat** | `-1` meant "off" | **`0` means off.** `-1` no longer works |
| **Duration / Lifetime** | seconds | still entered in seconds; converted to milliseconds for you |
| **Push Icon** | `0` / `1` / `2` | `fixed` / `pushOnce` / `push` |
| **Text Case** | `0` / `1` / `2` | `inherit` / `upper` / `asTyped` |
| **Scroll + Scroll Speed** | two separate keys | one combined scroll setting |
| **Gradient / Rainbow** | two competing options | one palette mechanism; Rainbow still wins over Gradient |
| **On lifetime expiry** | not available | new: remove the app, or mark it stale with a red frame |
| **Weather Overlay** | not available | new on the Weather App |

### What disappeared

| Gone | Why, and what to do instead |
|---|---|
| **`save`** — apps surviving a reboot | Pushed apps are deliberately memory-only now, so they come back with *fresh* data instead of a stale copy. Your automation re-pushes on Home Assistant start. For content that needs no outside data, write a script on the clock. |
| **`pos`** — placing an app at a position | No blueprint field for this. Set the order on the **Apps** tab of the clock's web interface — it is stored there and survives reboots. |
| **Per-app show/hide** | Replaced by a switch per app: publish `true` or `false` to `<prefix>/cmd/apps/<name>/enabled` (AWTRIX NG 1.2.2 or newer) — see [Toggle Built-in Apps](#12-toggle-built-in-apps). Every other app, and the order, is left untouched. |
| **`topText`** | No equivalent. |
| **`clients`** | Gone — but the blueprints already send to every display you select. |

### What stayed the same

Icon IDs and icon files carry over unchanged. Colours accept the same formats. `hold`, `stack` and `wakeup` still mean what they meant.

### Where the messages go now

For the curious, this is the whole mapping the blueprints implement:

| Action | AWTRIX 3 topic | AWTRIX NG topic |
|---|---|---|
| Create / update an app | `<prefix>/custom/name` | `<prefix>/cmd/apps/pushed/name` |
| Delete an app | empty payload, same topic | empty payload, same topic |
| Notification | `<prefix>/notify` | `<prefix>/cmd/notify` |
| Dismiss notification | `<prefix>/notify/dismiss` | `<prefix>/cmd/notify/dismiss` (or `.../dismiss/<name>`) |
| Switch to an app | `<prefix>/switch` | `<prefix>/cmd/apps/switch` |
| Show / hide apps | `<prefix>/apps` | `<prefix>/cmd/apps/<name>/enabled` |
| Settings | `<prefix>/settings` | `<prefix>/cmd/settings` |
| Moodlight | `<prefix>/moodlight` | `<prefix>/cmd/display/moodlight` |
| Indicators | `<prefix>/indicator1..3` | `<prefix>/cmd/indicators/1..3` |
| Play a sound | `<prefix>/sound` | `<prefix>/cmd/audio/play` |

Full firmware documentation: **[ang.blueforcer.de](https://ang.blueforcer.de/)**

---

# Release Notes
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
