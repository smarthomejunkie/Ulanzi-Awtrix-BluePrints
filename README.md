# Ulanzi-Awtrix-BluePrints
Manual for the Ulanzi Clock - Awtrix Firmware BluePrints

[![This is the BEST MATRIX DISPLAY CLOCK for Home Assistant!](https://img.youtube.com/vi/N0NKPJzGHuA/maxresdefault.jpg)](https://www.youtube.com/watch?v=N0NKPJzGHuA)
Click the image to watch the video. (It will open in this browser window)

## Where to get?
You can download the Home Assistant Blueprints by sponsoring me on [this Ko-Fi page](https://ko-fi.com/s/0d1e4419bd)

## How to install
1. Upload the smarthomejunkie folder into the /config/blueprints/automations folder in Home Assistant
2. After uploading the Blueprints, go to Developer Tools > YAML tab and click AUTOMATIONS

## How to use
There are 7 Blueprints to control your Ulanzi Desktop Clock using Awtrix.

### 1. Create Notification
With this Blueprint, you can send a notification to the clock.

*Fields:*

|Name|Type|Default|Example|Decription|
|---|---|---|---|---|
|Awtrix Display|dropdown||awtrix_d6b064|Select the target Awtrix display|
|Toggle Helper|dropdown||input_boolean.display_power|Select the Toggle Helper that will toggle the notification on or off|
|Notification Text|string||This is a notification|Enter the notification text|
|Icon|string||1234|Enter the Icon Name or ID of the icon that you like to show.|
|Push Icon|dropdown|2|0=Icon doesn't move<br />1=Icon moves with text and will not appear again<br />2=Icon moves with text but appears again when the text starts|Icon behavior|
|Text Case|dropdown|0|0=Use global setting<br />1=Force Uppercase<br />2=Show as you entered it|Select how you would like your text to display|
|Background Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Background color|
|Text Color|color_rgb|[0, 0, 0]|[255, 255, 0]|Select the Text color|
|Rainbow Colors|boolean|false|true|Should the notification be shown in Rainbow colors?|
|Hold Notification|boolean|true|false|Should the notification stay on the display until it's manually dismissed? (Overrides Duration)|
|Duration (in seconds)|number|0|30|Sets how long the app should be displayed. 0 is global app time|


## Updates
V1.1: 
* It's now possible to select to show temperature and windspeed optionally in Weather app. After uploading the new weather blueprint, go to Developer Tools > YAML and refresh the automations.
* Added new Blueprint to toggle indicators.

V1.2:
* Some textual changes in the indicator blueprint.

V1.3:
* Added app lifetime option to sensor app and weather app Blueprints. This removes the custom app when there is no update after the given time in seconds.

V1.4:
* Added Blueprint for the Rain Forecast Graph. It will show a bar chart or line chart for the Rain Forecast. If no rain is expected in the next couple of hours it will show a custom message instead.

V1.41:
* Fixed new App Time setting so that it matches the new API variable.

V1.42:
* Added option to select a background color for notifications and custom apps.
* Fixed rain forecast bug that in some cases did not show a graph.

V1.43:
* Renaming the prefix in Awtrix Light and in Home Assistant to the same name will not break the apps anymore.
