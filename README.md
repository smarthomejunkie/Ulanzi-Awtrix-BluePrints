# Ulanzi-Awtrix-BluePrints
Manual for the Ulanzi Clock - Awtrix Firmware BluePrints

## Where to get?
You can download the Home Assistant Blueprints by sponsoring me on [this Ko-Fi page](https://ko-fi.com/s/0d1e4419bd)

## How to install
1. Upload the smarthomejunkie folder into the /config/blueprints/automations folder in Home Assistant
2. After uploading the Blueprints, go to Developer Tools > YAML tab and click AUTOMATIONS

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
