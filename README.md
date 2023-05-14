# Ulanzi-Awtrix-BluePrints
Manual for the Ulanzi Clock - Awtrix Firmware BluePrints

[![This is the BEST MATRIX DISPLAY CLOCK for Home Assistant!](https://img.youtube.com/vi/N0NKPJzGHuA/maxresdefault.jpg)](https://www.youtube.com/watch?v=N0NKPJzGHuA)
Click the image to watch the video. (It will open in this browser window)

# Table of Contents:
- [Ulanzi-Awtrix-BluePrints](#ulanzi-awtrix-blueprints)
- [Where to get the Blueprints and firmware?](#where-to-get-the-blueprints-and-firmware)
- [How to install](#how-to-install)
- [How to use](#how-to-use)
  * [1. Awtrix Create Notification](#1-awtrix-create-notification)
  * [2. Awtrix Create Sensor App](#2-awtrix-create-sensor-app)
  * [3. Awtrix Rain Forecast](#3-awtrix-rain-forecast)
  * [4. Awtrix Set Transition Time](#4-awtrix-set-transition-time)
  * [5. Awtrix Toggle Stock App](#5-awtrix-toggle-stock-app)
  * [6. Toggle Indicators](#6-toggle-indicators)
  * [7. Awtrix Weather App](#7-awtrix-weather-app)
  * [8. Awtrix Moodlight](#8-awtrix-moodlight)
- [FAQ](#faq)
- [Release Notes](#release-notes)

# Where to get the Blueprints and firmware?
**1. You can download the Home Assistant Blueprints by sponsoring me on [this Ko-Fi page](https://ko-fi.com/s/0d1e4419bd)**.
You will get lifetime updates, and you can download new versions by logging in to Ko-Fi when a new update is released.

**2. You can get the Awtrix Light firmware [on this site](https://blueforcer.github.io/awtrix-light/#/README)**.

**3. You can [find the Awtrix Light Repository here](https://github.com/Blueforcer/awtrix-light)**.

# How to install
1. Upload (or overwrite) the smarthomejunkie folder into the /config/blueprints/automations folder in Home Assistant.
2. After uploading the Blueprints, go to Developer Tools > YAML tab and click AUTOMATIONS. Your existing automations will still work. 
3. You can upload the icons in the ICONS folder using the file manager in the Awtrix web interface.
3. You can upload the notification file alert.txt in the MELODIES folder using the file manager in the Awtrix web interface.

# How to use
There are 7 Blueprints to control your Ulanzi Desktop Clock using Awtrix. Each automation that you create with a Blueprint needs a toggle helper to turn the notification or app on or off. Don't forget to create a toggle (or number) helper for each automation that you create with these Blueprints!


## 1. Awtrix Create Notification
With this Blueprint, you can send a notification to the clock. It's also possible to use template code in the message

**Fields:**

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
|Clock Number|dropdown|Clock Number 1|Clock Number 3|Select your clock in your house if you have multiple clocks. This is needed to support the middle button to dismiss a notification. Supports up to 5 clocks.|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Notification Text|string||**Examples:**<br />Dinner is Ready!<br/>The electricity price is now {{ states('sensor.electricity_price',rounded= True,with_unit=True) }}|Enter the notification text. Template code is allowed.|
|Icon|string||1234|Enter the Icon Name or ID of the icon that you like to show.|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Play Alert Tone|boolean|false|true|Should an alert tone be played?<br /> Make sure you have copied the alerts.txt file into the MELODIES folder in Awtrix|
|Hold Notification|boolean|true|false|Should the notification stay on the display until it's manually dismissed? (Overrides Duration)|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|

## 2. Awtrix Create Sensor App
With this Blueprint, you can create a sensor App on the clock that is part of the app cycle.

**Fields:**

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Sensor|dropdown||sensor.netto_power|Select the Sensor or Media Player for which you want to show the state on the Ulanzi clock. The app value will change when the value of this sensor changes|
|Template (Optional)|string||{{ state_attr('media_player.chromecast_audio','media_artist') }} - {{ state_attr('media_player.chromecast_audio','media_title') }}|Enter a template to format your sensor the way you like it. (Advanced mode)|
|Icon|string||1234|Enter the Icon Name or ID of the icon that you like to show.|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|
|Lifetime (in seconds)|number|0|30|Sets how long the app should stay alive before it gets removed from the app cycle automatically. 0 is infinite lifetime. This only works if App cycling is enabled|
|Switch to app on value change|boolean|true|false|Should the clock switch to the app immediately when the value of the sensor changes? **BEWARE**: Setting this to On for a sensor that changes very frequently might flood your clock with MQTT messages and might cause reboots of the clock!|

## 3. Awtrix Rain Forecast
With this Blueprint, you can create a bar or line chart that shows the rain forecast

**Fields:**

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
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

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
|App Time Number Helper|dropdown||input_number.app_time|Select the App Time Number Helper that stores the App time. The number helper should be set in seconds.|

## 5. Awtrix Toggle Stock App
With this Blueprint, you can toggle the stock apps Time, Clock, Temperature, Humidity, Battery. Eyes

**Fields:**

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
|Toggle Helper|dropdown||input_boolean.display_battery|Select the Toggle Helper that will toggle the App on or off|
|Sensor|radiobutton||Battery|Select the stock app that you'd like to toggle|

## 6. Toggle Indicators
With this Blueprint, you can set up the two indicators to indicate a certain event.

**Fields:**

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
|Right Top Indicator Toggle Helper|dropdown||input_boolean.indicator_1|Select the Toggle Helper that will toggle the right top indicator|
|Right Top Indicator color|color_rgb|[255, 255, 255]|[255, 255, 0]|Select the Right Top Indicator color|
|Blink Speed Top Indicator|number|0|1000|Select the blink speed in milliseconds. (0 = no blinking)|
|Right Bottom Indicator Toggle Helper|dropdown||input_boolean.indicator_1|Select the Toggle Helper that will toggle the right bottom indicator|
|Right Bottom Indicator color|color_rgb|[255, 255, 255]|[255, 255, 0]|Select the Right Bottom Indicator color|
|Blink Speed Bottom Indicator|number|0|1000|Select the blink speed in milliseconds. (0 = no blinking)|

## 7. Awtrix Weather App
With this Blueprint, you can set up the weather app

**Fields:**

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Sensor|dropdown||weather.openweathermap|Select your Weather Sensor|
|Show temperature|boolean|true|false|Should the temperature be shown?|
|Show windspeed|boolean|false|true|Should the windspeed be shown?|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|
|Lifetime (in seconds)|number|0|30|Sets how long the app should stay alive before it gets removed from the app cycle automatically. 0 is infinite lifetime. This only works if App cycling is enabled|
|Switch to app on value change|boolean|true|false|Should the clock switch to the app immediately when the value of the sensor changes?|

## 8. Awtrix Moodlight
With this Blueprint, you can turn your Awtrix clock into a moodlight

**Fields:**

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
|Clock Number|dropdown|Clock Number 1|Clock Number 3|Select your clock in your house if you have multiple clocks. This is needed to support the middle button to dismiss a notification. Supports up to 5 clocks.|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Brightness|number|100|200|**WARNING**: This function causes much higher current draw and heat, because every pixel is lit. Keep the brightness value low (between 0 and 200). The clock may turn off if the brightness level is too high when running on battery power!|
|Color|color_rgb|[255, 255, 255]|[255, 255, 0]|Select the Moodlight color|
|Kelvin value|number|0|4000|Enter a Kelvin value. Typically choose a value between 1000 and 10000. A value of 0 disables Kelvin. Kelvin overwrites the color.|

# FAQ

**Q: What's the password for the Access Point when the clock is in AP Mode?**

A: The AP Mode password is: 12345678

**Q: My display does not show notifications or apps as soon as I trigger them.**

A: Make sure the MQTT prefix in Awtrix Light is exactly the same as the name of the device in Home Assistant. Don't use spaces in the name. 

**Q: Will my existing automations still work after I've updated the Blueprints to a new version?**

A: Yes

**Q: Will the display update as soon as a sensor value updates?**

A: Yes, it will update immediately.

**Q: How can I toggle a notification when a sensor value changes. For instance when a door sensor gets opened?**

A: Set the toggle helper for your Awtrix notification to "on" in the automation that detects if the door gets opened. This way, the Awtrix notification automation that you've created will also be triggered. I call this technique "daisy-chaining" of automations and explain this in this video: https://youtu.be/sNmonuw4EHo

**Q: How many custom apps can I run simultaneously on this clock?**

A: You can run up to 20 custom apps simultaneously. 

**Q: How can I show the Artist and Song every time when a new song starts playing?**

A: Follow these steps:
  1. Create an automation with the Awtrix Create Sensor App Blueprint.
  2. Select a toggle helper that you've created for the Awtrix media player app.
  3. Select the Media Player as the Sensor.
  4. Add code to the template field to show the artist and title. For example: {{ state_attr('media_player.chromecast_audio','media_artist') }} - {{ state_attr('media_player.chromecast_audio','media_title') }}
  5. Set the lifetime to 5 minutes
  6. Make sure the toggle helper is set to on.

**Q: How can I disable the stock apps so that they do not reappear after a reboot of the clock?**

A: You can turn them off on the device itself. Long press the middle button to get into the menu and disable them one by one in the menu-option APPS.

**Q: How can I change the order of the apps?**

A: The apps show in the order that you activate them.

**Q: How can I download an updated version of the Blueprints for free?**

A: If you've ordered the Blueprints before, log in to Ko-Fi and go to the menu. Select "Payments History" and go to your previous payment. You can download the new version by clicking on "View Details > View Content > Download". Or, open the e-mail that you got at the time of the purchase and click the link in the e-mail to download the latest version.

**Q: Where can I ask questions?**

A: Join my Discord server to ask questions in the Ulanzi-display channel: [https://discord.gg/pP53uDEgUw](https://discord.gg/pP53uDEgUw)

**Q: My Ulanzi clock dies after a couple of hours. What can I do to fix this?**

A: Unfortunately, there has been a bad hardware batch released by Ulanzi. They pinpointed the issue and fixed it in new batches. Contact your seller and/or Ulanzi and they will send you a new device. Be sure not to tell you've flashed the device, although Ulanzi promotes flashing it on their website.

**Q: The colors on the clock do not look the same as the colors that I've selected in the Blueprint**

A: The colors on the clock are determined by the color code and the brightness setting of the clock. The clock has a color correction function to correct the colors. Mostly bright colors work best. "In-between" colors do not work well.

**Q: The update button in Home Assistant does not work. How can I update the firmware?**

A: Long press the middle button on the clock to access the clock menu on the clock itself. You can navigate to the update menu item using the left and right buttons on the clock. Short press the middle button on the clock to enter the menu item of your choice.


# Release Notes

**V1.61:**
* The new stock App Eyes can now be enabled and disabled using the Toggle Stock App Blueprint

**V1.60:**
* Added a blink speed number selector for the indicators in the indicator blueprint. Supports Awtrix firmware 0.62 or higher.

**V1.59:**
* Added a new Blueprint to convert your Awtrix Clock into a moodlight. Awtrix Light Firmware 0.61 or higher is needed for this to work.

**V1.58:**
* If you have multiple clocks in your house, you can target a specific clock in the notification Blueprint, so that the middle button for that specific clock will dismiss the notifcation when pressed. This fix is created to prevent tools as Watchman to report errors for non used entity ids.
* BREAKING CHANGE: If you already had multiple clocks in your house, the middle button of the extra clocks will not work automatically anymore. You have to go through all the notification automations for each extra clock to select that specific clock to make it work again.

**V1.57:**
* Added a toggle to the Indicators Blueprint to toggle an alert sound on and off. Make sure you've uploaded the alert.txt file to the MELODIES folder to make this work.

**V1.56:**
* Re-added the Lifetime option. This option resulted in errors in older Awtrix firmware. Make sure you have at least Awtrix Light firmware version 0.58 installed to let this work properly.
* Up to 3 Ulanzi displays now support the middle button to dismiss a notification for you people with multiple clocks in your home.
* Streamlined some code to make it future-proof.

**V1.55:**
* The middle button of the Ulanzi clock now dismisses a notification and sets the corresponding toggle helper to Off.
NOTE: This uses the default entity ID for the middle button of the Ulanzi clock which is binary_sensor.button_select. If you renamed this entity ID, this function will not work!

**V1.54:**
* Removed the lifetime setting from the Blueprints. This setting causes the device to reboot every now and then.

**V1.53:**
* Added the option to toggle if the clock should switch to the app immediately when the value of the sensor changes.

**V1.52:**
* Fixed bug in Forecast Blueprint that pointed hardcoded to OpenWeatherMap.

**V1.51:**
* An alert tone can now be played when a notification is triggered. Make sure you copy the provided alert.txt file into the MELODIES folder of Awtrix Light to make this work.

**V1.50:**
* Added the possibility to select media players in the Awtrix Create Sensor App. It's now possible to show the artist and song on the clock when a new song starts playing on the selected media player.
* Added a Template field. You can now format your sensors so that they look exactly like you want them to look!

**V1.43:**
* Renaming the prefix in Awtrix Light and in Home Assistant to the same name will not break the apps anymore.

**V1.42:**
* Added option to select a background color for notifications and custom apps.
* Fixed rain forecast bug that in some cases did not show a graph.

**V1.41:**
* Fixed new App Time setting so that it matches the new API variable.

**V1.4:**
* Added Blueprint for the Rain Forecast Graph. It will show a bar chart or line chart for the Rain Forecast. If no rain is expected in the next couple of hours it will show a custom message instead.

**V1.3:**
* Added app lifetime option to sensor app and weather app Blueprints. This removes the custom app when there is no update after the given time in seconds.

**V1.2:**
* Some textual changes in the indicator blueprint.

**V1.1:** 
* It's now possible to select to show temperature and windspeed optionally in Weather app. After uploading the new weather blueprint, go to Developer Tools > YAML and refresh the automations.
* Added new Blueprint to toggle indicators.
