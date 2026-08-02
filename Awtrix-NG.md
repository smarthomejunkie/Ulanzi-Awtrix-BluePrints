# Ulanzi-Awtrix-NG-BluePrints
Manual for the Awtrix NG Firmware BluePrints (and the Ulanzi Desktop Clock)

[![This is the BEST MATRIX DISPLAY CLOCK for Home Assistant!](https://img.youtube.com/vi/N0NKPJzGHuA/maxresdefault.jpg)](https://www.youtube.com/watch?v=N0NKPJzGHuA)
Click the image to watch the video. (It will open in this browser window)

# Table of Contents:
- [Ulanzi-Awtrix-BluePrints](#ulanzi-awtrix-ng-blueprints)
- [Where to get the Blueprints and firmware?](#where-to-get-the-blueprints-and-firmware)
- [How to install or update](#how-to-install-or-update)
- [How to use](#how-to-use)
  * [1. Awtrix Create Notification](#1-awtrix-create-notification)
  * [2. Awtrix Create Sensor App](#2-awtrix-create-sensor-app)
  * [3. Awtrix Rain Forecast](#3-awtrix-rain-forecast)
  * [4. Awtrix Set Transition Time](#4-awtrix-set-transition-time)
  * [5. Awtrix Toggle Stock App](#5-awtrix-toggle-stock-app)
  * [6. Toggle Indicators](#6-toggle-indicators)
  * [7. Awtrix Weather App](#7-awtrix-weather-app)
  * [8. Awtrix Moodlight](#8-awtrix-moodlight)
  * [9. Awtrix List Calendar](#9-awtrix-list-calendar)
  * [10. Awtrix List ToDo Items](#10-awtrix-list-todo-items)
  * [11. Awtrix Countdown timer](#11-awtrix-countdown-timer)
- [FAQ](#faq)
- [Release Notes](#release-notes)

# Where to get the Blueprints and firmware?
**1. You can download the Home Assistant Blueprints by sponsoring me on [this Ko-Fi page](https://ko-fi.com/s/0d1e4419bd)**.
You will get lifetime updates, and you can download new versions by logging in to Ko-Fi when a new update is released.

**2. You can get the Awtrix NG firmware [on this site](https://github.com/Blueforcer/awtrixng#readme)**.

**3. You can [find the Awtrix NG Repository here](https://github.com/Blueforcer/awtrixng)**.

**4. Check [the manual of Awtrix NG here](https://ang.blueforcer.de/)**.

# How to install or update
1. Upload (or overwrite) the smarthomejunkie folder into the /config/blueprints/automation/awtrix_ng folder in Home Assistant.
2. After uploading the Blueprints, go to Developer Tools > YAML tab and click AUTOMATIONS. Your existing automations will still work. 
3. You can upload the weather icons in the ICONS folder using the Icons tab in the Awtrix NG web interface.
3. You can upload the notification file alert.txt in the Sounds tab using the file manager in the Awtrix NG web interface.

# How to use
There are 11 Blueprints to control your Ulanzi Desktop Clock using Awtrix NG. Each automation that you create with a Blueprint needs a toggle helper to turn the notification or app on or off. Don't forget to create a toggle (or number) helper for each automation that you create with these Blueprints!


## 1. Awtrix Create Notification
With this Blueprint, you can send a notification to the clock. It's also possible to use template code in the message

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Notification Text|string||**Examples:**<br />Dinner is Ready!<br/>The electricity price is now {{ states('sensor.electricity_price',rounded= True,with_unit=True) }}|Enter the notification text. Template code is allowed.|
|Icon|string||1234|Enter the Icon Name or ID of the icon that you like to show.|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Gradient 1|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the first Gradient color if you want to use a gradient color for the text. This will be ignored if both gradient colors are the same.|
|Gradient 2|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the second Gradient color if you want to use a gradient color for the text. This will be ignored if both gradient colors are the same.|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors? This overwrites the Gradient setting.|
|Play Alert Tone|boolean|false|true|Should an alert tone be played?<br /> Make sure you have copied the alerts.txt file into the MELODIES folder in Awtrix|
|Hold Notification|boolean|true|false|Should the notification stay on the display until it's manually dismissed? (Overrides Repeat & Duration)|
|Repeat|number|-1|5|Sets how many times the text should be scrolled through the matrix before the app ends. If the value is -1, the duration will be taken into account instead.|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|
|Stack|boolean|true|false|Defines if the notification will be stacked. False will immediately replace the current notification.|
|Scroll|boolean|true|false|Enables text scrolling.|
|Scroll Speed Percentage|number|100|50|Modifies the scrollspeed. You need to enter a percentage value.|
|Effect|string|None|Ripple|Shows a background effect. If you only want to show the effect in a notification, then simply do not enter a text in the notification.|

## 2. Awtrix Create Sensor App
With this Blueprint, you can create a sensor App on the clock that is part of the app cycle.

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Sensor|dropdown||sensor.netto_power|Select the Sensor, Text helper, or Media Player for which you want to show the state on the Ulanzi clock. The app value will change when the value of this sensor changes|
|Template (Optional)|string||{{ state_attr('media_player.chromecast_audio','media_artist') }} - {{ state_attr('media_player.chromecast_audio','media_title') }}|Enter a template to format your sensor the way you like it. (Advanced mode)|
|Icon|string||1234|Enter the Icon Name or ID of the icon that you like to show.|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Use Threshold value|boolean|false|true|If set to true, the color of your text will adapt based on whether the value of your sensor is below or above the threshold value.|
|Threshold Value|number|0|200|Enter a value that can be used as a threshold to switch colors|
|Low-value Text color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the color when the value of the sensor is lower than the threshold value|
|High-value Text color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the color when the value of the sensor is equal to or higher than the threshold value|
|Gradient 1|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the first Gradient color if you want to use a gradient color for the text. This will be ignored if both gradient colors are the same.|
|Gradient 2|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the second Gradient color if you want to use a gradient color for the text. This will be ignored if both gradient colors are the same.|
|Rainbow Colors|boolean|false|true|Should the text be shown in Rainbow colors? This overwrites the Gradient setting.|
|Repeat|number|-1|5|Sets how many times the text should be scrolled through the matrix before the app ends. If the value is -1, the duration will be taken into account instead.|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time.|
|Lifetime (in seconds)|number|0|30|Sets how long the app should stay alive before it gets removed from the app cycle automatically. 0 is infinite lifetime. This only works if App cycling is enabled|
|Switch to app on value change|boolean|true|false|Should the clock switch to the app immediately when the value of the sensor changes? **BEWARE**: Setting this to On for a sensor that changes very frequently might flood your clock with MQTT messages and might cause reboots of the clock!|
|Scroll|boolean|true|false|Enables text scrolling.|
|Scroll Speed Percentage|number|100|50|Modifies the scrollspeed. You need to enter a percentage value.|
|Effect|string|None|Ripple|Shows a background effect.|

## 3. Awtrix Rain Forecast
With this Blueprint, you can create a bar or line chart that shows the rain forecast

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Sensor|dropdown||sensor.netto_power|Select your Weather Sensor|
|Graph Type|dropdown|bar|Select bar chart or line chart|
|Graph Color|color_rgb|[255, 255, 255]|[255, 255, 0]|Select the Graph color|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Autoscale|boolean|true|false|Enables or disables autoscaling for bar and linechart|
|Custom Text|string|No rain expected|There will be no rain|Text to show when no rain is expected|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|
|Lifetime (in seconds)|number|0|30|Sets how long the app should stay alive before it gets removed from the app cycle automatically. 0 is infinite lifetime. This only works if App cycling is enabled|
|Switch to app on value change|boolean|true|false|Should the clock switch to the app immediately when the value of the sensor changes?|

## 4. Awtrix Set Transition Time
With this Blueprint, you can set the global time that each app is visible in seconds.

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|App Time Number Helper|dropdown||input_number.app_time|Select the App Time Number Helper that stores the App time. The number helper should be set in seconds.|

## 5. Awtrix Toggle Stock App
With this Blueprint, you can toggle the stock apps Time, Clock, Temperature, Humidity, Battery. Eyes

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_battery|Select the Toggle Helper that will toggle the App on or off|
|Sensor|radiobutton||Battery|Select the stock app that you'd like to toggle|

## 6. Toggle Indicators
With this Blueprint, you can set up the two indicators to indicate a certain event.

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Right Top Indicator Toggle Helper|dropdown||input_boolean.indicator_1|Select the Toggle Helper that will toggle the right top indicator|
|Right Top Indicator color|color_rgb|[255, 255, 255]|[255, 255, 0]|Select the Right Top Indicator color|
|Blink Speed Top Indicator|number|0|1000|Select the blink speed in milliseconds. (0 = no blinking)|
|Right Bottom Indicator Toggle Helper|dropdown||input_boolean.indicator_1|Select the Toggle Helper that will toggle the right bottom indicator|
|Right Bottom Indicator color|color_rgb|[255, 255, 255]|[255, 255, 0]|Select the Right Bottom Indicator color|
|Blink Speed Bottom Indicator|number|0|1000|Select the blink speed in milliseconds. (0 = no blinking)|

## 7. Awtrix Weather App
With this Blueprint, you can set up the weather app

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Sensor|dropdown||weather.openweathermap|Select your Weather Sensor|
|Show Weather Text|boolean|true|false|Should the weather condition be shown as text?|
|Language|dropdown|English|Dutch|Show the weather condition in this language|
|Show temperature|boolean|true|false|Should the temperature be shown?|
|Show windspeed|boolean|true|false|Should the windspeed be shown?|
|Show Humidity|boolean|true|false|Should the humidity be shown?|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Repeat|number|-1|5|Sets how many times the text should be scrolled through the matrix before the app ends. If the value is -1, the duration will be taken into account instead.|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|
|Lifetime (in seconds)|number|0|30|Sets how long the app should stay alive before it gets removed from the app cycle automatically. 0 is infinite lifetime. This only works if App cycling is enabled|
|Switch to app on value change|boolean|true|false|Should the clock switch to the app immediately when the value of the sensor changes?|
|Scroll|boolean|true|false|Enables text scrolling.|
|Scroll Speed Percentage|number|100|50|Modifies the scrollspeed. You need to enter a percentage value.|
|Effect|string|None|Ripple|Shows a background effect.|

## 8. Awtrix Moodlight
With this Blueprint, you can turn your Awtrix clock into a moodlight

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the moodlight on or off|
|Brightness|number|100|200|**WARNING**: This function causes much higher current draw and heat, because every pixel is lit. Keep the brightness value low (between 0 and 200). The clock may turn off if the brightness level is too high when running on battery power!|
|Color|color_rgb|[255, 255, 255]|[255, 255, 0]|Select the Moodlight color|
|Kelvin value|number|0|4000|Enter a Kelvin value. Typically choose a value between 1000 and 10000. A value of 0 disables Kelvin. Kelvin overwrites the color.|

## 9. Awtrix List Calendar
With this Blueprint, you can create a calendar App on the clock that is part of the app cycle. It shows the calendar events for the next 24 hours of a chosen calendar

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Calendar|dropdown||yourname@gmail.com|Select the calendar for which you want to show the items on the Ulanzi clock. The app value will change when the value of this sensor changes|
|Hours ahead|number|24|8|How many hours in advance should agenda items be shown? (default 24).|
|Show Timeline|boolean|true|false|Do you want to cycle the whole timeline for the upcoming hours?|
|Show Whole Day Events|boolean|true|false|Do you want to show events that last the whole day?|
|Show Alerts|boolean|true|false|Do you want to show an alert when the calendar event starts|
|Play Alert Tone|boolean|false|true|Should an alert tone be played 15 minutes before the event starts?|
|Stack|boolean|true|false|Defines if the notification will be stacked. False will immediately replace the current notification.|
|Icon|string||1234|Enter the Icon Name or ID of the icon that you like to show.|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Custom Text|string|Yay! You have no appointments!|No events|Text to show when there are no upcoming calendar events|
|Show Empty Calendar|boolean|true|false|Show the custom text when there are no events. If set to false, the calendar will not show when there are no events after the lifetime has ended. This might take a minute or two to take effect.|
|Prefix|string|<empty>|Ed's Calendar:|Enter an optional prefix text. For instance, show the Calendar name if you use multiple calendars in front of the list of events.|
|Today Text|string|<empty>|Text to show for events that occur today.|
|Tomorrow Text|string|<empty>|Text to show for events that occur tomorrow.|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Repeat|number|-1|5|Sets how many times the text should be scrolled through the matrix before the app ends. If the value is -1, the duration will be taken into account instead.|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|
|Lifetime (in seconds)|number|0|30|Sets how long the app should stay alive before it gets removed from the app cycle automatically. 0 is infinite lifetime. This only works if App cycling is enabled|
|Switch to app on value change|boolean|true|false|Should the clock switch to the app immediately when the value of the sensor changes? **BEWARE**: Setting this to On for a sensor that changes very frequently might flood your clock with MQTT messages and might cause reboots of the clock!|
|Scroll|boolean|true|false|Enables text scrolling.|
|Scroll Speed Percentage|number|100|50|Modifies the scrollspeed. You need to enter a percentage value.|
|Effect|string|None|Ripple|Shows a background effect.|

## 10. Awtrix List ToDo Items
With this Blueprint, you can create a ToDo List App on the clock that is part of the app cycle. It shows the to-do items for the chosen to-do list

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|ToDo List|dropdown||My ToDo List|Select your todo list|
|Icon|string|29644|1234|Enter the Icon Name or ID of the icon that you like to show.|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Custom Text|string|Relax! You have nothing to do!|No events|Text to show when there are no to-do items|
|Prefix|string|To Do:|My To-Do list:|Enter a prefix text.|
|Show Empty ToDo List|boolean|true|false|Show the custom text when there are no items.|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Repeat|number|-1|5|Sets how many times the text should be scrolled through the matrix before the app ends. If the value is -1, the duration will be taken into account instead.|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|
|Lifetime (in seconds)|number|0|30|Sets how long the app should stay alive before it gets removed from the app cycle automatically. 0 is infinite lifetime. This only works if App cycling is enabled|
|Switch to app on value change|boolean|true|false|Should the clock switch to the app immediately when the value of the sensor changes? **BEWARE**: Setting this to On for a sensor that changes very frequently might flood your clock with MQTT messages and might cause reboots of the clock!|
|Scroll|boolean|true|false|Enables text scrolling.|
|Scroll Speed Percentage|number|100|50|Modifies the scrollspeed. You need to enter a percentage value.|
|Effect|string|None|Ripple|Shows a background effect.|

## 11. Awtrix Countdown Timer
With this Blueprint, you can create countdown timers for specific events. This timer counts down to a date. It's not possible to let it count down in seconds because of the refresh interval.

**Fields:**

|Name|Type|Default|Example|Description|
|---|---|---|---|---|
|Awtrix Displays|dropdown||awtrix_d6b064|Select the target Awtrix displays|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Event Name|text|Countdown|New Year|Enter a short name for your event. This is used as the app identifier on the clock and shown as a label (e.g. "Birthday", "Vacation", "New Year").|
|Event Date|text|2025-12-31|2027-01-01|Enter the target date in YYYY-MM-DD format (e.g. 2025-12-31).|
|Event Time|text|00:00:00|00:00:00|Enter the target time in HH:MM:SS format using 24-hour notation (e.g. 00:00:00).|
|Show Event Name on Clock|boolean|true|false|If enabled, the event name will be shown as a prefix before the countdown (e.g. "Vacation 3d 4h 20m 10s"). Disable to show only the countdown value.|
|Show Seconds|boolean|true|false|If enabled, seconds will be included in the countdown display. Note: the automation updates every minute, so seconds are approximate.|
|Event Started Message|text|empty|New year has started!|Message to display from the moment the event starts until midnight of the same day (e.g. "🎂 Happy Birthday!" or "✈️ Bon Voyage!"). Leave empty to skip straight to the Event Reached Text below.|
|Event Reached Text|text|🎉 Now!|It's New Years Day|Fallback text to display once the event day has ended (i.e. from midnight onward). Also used on the event day if Event Started Message is left empty. Leave empty to hide the app automatically after the event.|
|Icon|string|29644|1234|Enter the Icon Name or ID of the icon that you like to show.|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Custom Text|string|Relax! You have nothing to do!|No events|Text to show when there are no to-do items|
|Prefix|string|To Do:|My To-Do list:|Enter a prefix text.|
|Show Empty ToDo List|boolean|true|false|Show the custom text when there are no items.|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Repeat|number|-1|5|Sets how many times the text should be scrolled through the matrix before the app ends. If the value is -1, the duration will be taken into account instead.|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|
|Lifetime (in seconds)|number|0|30|Sets how long the app should stay alive before it gets removed from the app cycle automatically. 0 is infinite lifetime. This only works if App cycling is enabled|
|Switch to app on value change|boolean|true|false|Should the clock switch to the app immediately when the value of the sensor changes? **BEWARE**: Setting this to On for a sensor that changes very frequently might flood your clock with MQTT messages and might cause reboots of the clock!|
|Scroll|boolean|true|false|Enables text scrolling.|
|Scroll Speed Percentage|number|100|50|Modifies the scrollspeed. You need to enter a percentage value.|
|Effect|string|None|Ripple|Shows a background effect.|

# FAQ

<details>
 <summary><b>What's the password for the Access Point when the clock is in AP Mode?</b></summary>

 The AP Mode password is: 12345678
</details>

<details>
 <summary><b>I cannot see my clock in Home Assistant</b></summary>

 Flash your clock again and make sure you tick the "erase" checkbox. This should hard reset the clock. If this didn't work, then go to [Blueforcer's Discord server](https://discord.gg/Wn7TWDzPY4) to ask for support about this issue.
</details>

<details>
 <summary><b>My display does not show notifications or apps as soon as I trigger them</b></summary>

 Make sure the MQTT prefix in Awtrix 3 is exactly the same as the name of the device in Home Assistant. Don't use spaces in the name. 
</details>

<details>
 <summary><b>Will my existing automations still work after I've updated the Blueprints to a new version?</b></summary>

 Yes
</details>

<details>
 <summary><b>Will the display update as soon as a sensor value updates?</b></summary>

 If you enable the "Switch to app on value change" option, changes will take effect immediately. Conversely, if the option is disabled, the updated value will be displayed when the app appears in the cycle.
</details>

<details>
 <summary><b>How can I toggle a notification when a sensor value changes? For instance when a door sensor gets opened?</b></summary>

 Set the toggle helper for your Awtrix notification to "on" in the automation that detects if the door gets opened. This way, the Awtrix notification automation that you've created will also be triggered. I call this technique "daisy-chaining" of automations and explain this in this video: https://youtu.be/sNmonuw4EHo
</details>

<details>
 <summary><b>How can I automatically turn off a notification toggle helper, so that I can turn it on again after a while?</b></summary>

 When you've set the notification to turn off after a delay time in the blueprint, the toggle helper will not turn off automatically because Home Assistant cannot see the status of the notification. So, if you use a notification in an automation, you have to switch off the toggle helper after a duration in the automation that triggers that toggle helper to on. 
Alternatively, you can use the sensor app blueprint instead. You can enter a custom message in the template field so that it behaves a lot like the notification app. 

</details>

<details>
 <summary><b>How many custom apps can I run simultaneously on this clock?</b></summary>

 You can run up to 20 custom apps simultaneously. 
 </details>

 <details>
 <summary><b>I only see one app on the clock, even when I configured more than one. How can I fix this?</b></summary>

 You probably entered a high number for the transition time. This time is in seconds nowadays. In the past, it was in milliseconds.
 </details>

<details>
 <summary><b>The icons are not showing on the display.</b></summary>

 Upload the icons to your clock first using the Awtrix interface. Go to your Ulanzi URL, Icons tab, fill in the icon ID, and click on Preview to check it. Use the download button to upload the icon to the Ulanzi clock.
</details>


<details>
 <summary><b>How can I show the Artist and Song every time a new song starts playing?</b></summary>

 Follow these steps:
  1. Create an automation with the Awtrix Create Sensor App Blueprint.
  2. Select a toggle helper that you've created for the Awtrix media player app.
  3. Select the Media Player as the Sensor.
  4. Add code to the template field to show the artist and title. For example: {{ state_attr('media_player.chromecast_audio','media_artist') }} - {{ state_attr('media_player.chromecast_audio','media_title') }}
  5. Set the lifetime to 5 minutes
  6. Make sure the toggle helper is set to on.
</details>

<details>
 <summary><b>How can I disable the stock apps so that they do not reappear after a reboot of the clock?</b></summary>

 You can turn them off on the device itself. Long press the middle button to get into the menu and disable them one by one in the menu-option APPS.
</details>

<details>
 <summary><b>How can I change the order of the apps?</b></summary>

 The apps show in the order that you activate them.
</details>

<details>
 <summary><b>How can I download an updated version of the Blueprints for free?</b></summary>

 * If you've ordered the Blueprints before, log in to Ko-Fi and go to the menu.
 * Select "Payments History" and go to your previous payment.
 * You can download the new version by clicking on "View Details > View Content > Download".
 * Or, open the e-mail that you got at the time of the purchase and click the link in the e-mail to download the latest version.
</details>
<details>
 <summary><b>Where can I ask questions or report a bug?</b></summary>

 Join my Discord server to ask questions in the Ulanzi-display channel: [https://discord.gg/pP53uDEgUw](https://discord.gg/pP53uDEgUw)
</details>
<details>
 <summary><b>My Ulanzi clock dies after a couple of hours. What can I do to fix this?</b></summary>

 Unfortunately, there has been a bad hardware batch released by Ulanzi. They pinpointed the issue and fixed it in new batches. Contact your seller and/or Ulanzi and they will send you a new device. Be sure not to tell you've flashed the device, although Ulanzi promotes flashing it on their website.
</details>
<details>
 <summary><b>The colors on the clock do not look the same as the colors that I've selected in the Blueprint</b></summary>

 The colors on the clock are determined by the color code and the brightness setting of the clock. The clock has a color correction function to correct the colors. Mostly bright colors work best. "In-between" colors might not work well.
</details>
<details>
 <summary><b>The update button in Home Assistant does not work. How can I update the firmware?</b></summary>

 There are two ways: 
 1. Long press the middle button on the clock to access the clock menu on the clock itself. You can navigate to the update menu item using the clock's left and right buttons. Short press the middle button on the clock to enter the menu item of your choice.
 2. Download the iPhone or Android App for Awtrix 3 and update your clock using that app.
</details>
<details>
 <summary><b>How can I change the Language of the Weather Forecast Blueprint?</b></summary>

 You can create a template sensor that translates weather state names and use it in the Custom App blueprint.
</details>
<details>
 <summary><b>How can I show a different icon based on the value of a sensor?</b></summary>

 You can add template code in the icon field like this:

```{% if states('sensor.yoursensor') | int < 0 %}55738{% else %}55742{% endif %}```

Replace the <yoursensor> entity with your own, and replace the icon numbers with those of your choice.
</details>
<details>
 <summary><b>How do I make sure that my calendar is not shown when there are no events scheduled for the upcoming hours?</b></summary>

 1. Set the Show Empty Calendar option to off.
 2. Set the Lifetime (in seconds) option to a number higher than 60. For instance: 120.
</details>
<details>
 <summary><b>How should I interpret the weather forecast graph?</b></summary>

 The weather forecast graph displays the expected precipitation provided by your weather provider. Each bar represents the time interval during which your provider delivers. In the case of OpenWeatherMap, this is three hours. You have to check for yourself what the expected precipitation time interval is for the weather integration you use.   
</details>
<details>
 <summary><b>Can the countdown timer count down in seconds?</b></summary>

 It does show the seconds, but it can't count down in seconds because of the refresh interval. The clock should be refreshed every second in that case, and that would kill the app cycle on the clock. (And I guess even the clock itself, because it refreshes too often).
 There's an option to turn off the display of seconds.
</details>


# Release Notes
**V1.0**
* Blueprints for the new Awtrix NG firmware

