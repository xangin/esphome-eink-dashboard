# ESPHome E-ink Dashboard

<a href="https://www.buymeacoffee.com/xangin" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="30" width="130"></a>

English | [正體中文](https://github.com/xangin/esphome-eink-dashboard/blob/main/README_zh-tw.md)

Thanks to [Madelena](https://github.com/Madelena/esphome-weatherman-dashboard/) for inspiring this project and providing the source code for weather data.

This E-ink dashboard is placed at the entrance and updates automatically every 15 minutes during the specified time period.

There is also a vertical version that only displays date and weather information, which can be referred to at [E-ink weather board](https://github.com/xangin/eink-weather-board).

<img src="https://user-images.githubusercontent.com/56766371/184495447-3c9c7ea9-0f64-442d-b695-d47738ba2d2d.jpg" width="66%" alt="Context"/>

The information board displays:
- Today's date
- Temperature and humidity of each room
- Current weather forecast
- Weather forecast for the next four hours

<img src="https://user-images.githubusercontent.com/56766371/184495454-a9595013-f24a-4c55-b7f8-b29cdee5af32.jpg" width="66%" alt="Context"/>

Below are the hardware structure, ESPHome yaml code, and Home assistant yaml code.

## Hardware

- [Waveshare 7.5-inch black and white E-ink screen](https://detail.tmall.com/item.htm?id=606005913066) - bare screen without casing
- [Waveshare E-ink screen SPI driver board ESP32](https://detail.tmall.com/item.htm?id=605757128869) - WiFi + Bluetooth version (ESP32)
- [IKEA RÖDALM Frame 13x18 cm](https://www.ikea.com.tw/zh/products/wall-decoration/frames/rodalm-art-50550033) - available in black, white, and wooden color. Note: The mat opening is small and needs to be enlarged

## Installation

1. Place the files in the `/fonts` folder and `esp32-eink-dashboard.yaml`in the HA/config/esphome folder.
2. Place `eink_dashboard_sensor.yaml` in the HA/config/packages folder.
3. Modify `e-ink-weather-board.yaml` and `eink_dashboard_sensor.yaml` to match your HA entity IDs. **Instructions are below**
4. Check for YAML code errors in HA.
   1. Developer Tools > YAML > Check Configuration, ensure no errors in the bottom left notification.
   2. YAML Configuration Reloading > Template Entities
   3. Developer Tools > States > Check `sensor.eink_sensors` to ensure it appears and contains the desired content.
5. Flash `esp32-eink-dashboard.yaml` to the ESP32 module in ESPHome.
6. Done!

## ESPHome YAML Explanation

### Manually update the panel in HA

```YAML
button:
  - platform: template
    name: '${devicename} Refresh'
    icon: 'mdi:update'
    on_press:
      then:
        - component.update: 'my_display'
    internal: false
```


### Pass HA time to ESPHome

```YAML
time:
  - platform: homeassistant
    id: ha_time
```


### Decide whether to update the panel based on a binary sensor from HA, to extend panel life by not updating during sleep time

```YAML
binary_sensor:
  - platform: homeassistant
    id: 'eink_refresh'
    entity_id: 'binary_sensor.eink_refresh_time'
```


### Panel update timing

The screen does not automatically update by default update_interval: never. It updates when data is received and the script is executed, as follows:

#### 1. Execute the script when this sensor receives data: 

```YAML
  - platform: homeassistant
    entity_id: sensor.eink_sensors
    attribute: forecast_temperature_4
    id: forecast_temperature_4
    on_value: 
      then:
        - script.execute: all_data_received 
```

#### 2. Execute the all_data_received script, which runs every 15 minutes and updates the panel only when the binary sensor is on

```YAML
script:
  - id: all_data_received
    mode: single
    then:
      - if:
          condition:
            binary_sensor.is_on: 'eink_refresh'
          then:
            - component.update: 'my_display'
      - delay: 15min
      - script.execute: all_data_received
```

## HA Template Sensor Explanation

**Ensure the following code is written in `configuration.yaml` for the `eink_dashboard_sensor.yaml` file to take effect**

![](https://user-images.githubusercontent.com/56766371/184566430-d2dff49b-38cd-4ddd-a775-eaadf7099fc1.png)


Since the weather forecast is of type weather and not sensor, it cannot be directly processed by ESPHome, so we use this template sensor.

Format the desired data and send it to ESPHome for display. weather.myhome is my weather forecast entity name, remember to replace it with your own ID.

By default, it shows hourly forecasts. Ensure your current weather integration supports hourly forecasts (built-in met.no does).

The following YAML indicates that the hourly weather forecast service is called at 1 minute past each hour, updating the content in `sensor.eink_sensors`.

Ensure the panel update timing is after the weather forecast update to avoid showing the previous hour's forecast.

```YAML

  - trigger:
      - platform: time_pattern     
        hours: "/1" 
        minutes: 1
    action:
      - service: weather.get_forecasts
        target:
          entity_id: weather.myhome #replace with your weather forecast entity id
        data:
          type: hourly
        response_variable: hourly  

```

`attributes` splits out the information to be used from the weather forecast:
- Temperature of this hour:  `today_temperature`
- Forecast times for the next four hours: `forecast_weekday_1`, `forecast_weekday_2`, `forecast_weekday_3`, `forecast_weekday_4`
- Weather icons for the next four hours:  `forecast_condition_1`, `forecast_condition_2`, `forecast_condition_3`, `forecast_condition_4`
- Temperatures for the next four hours:   `forecast_temperature_1`, `forecast_temperature_2`, `forecast_temperature_3`, `forecast_temperature_4`


## References
- https://github.com/Madelena/esphome-weatherman-dashboard/


