# ESPHome E-ink Dashboard
感謝[Madelena](https://github.com/Madelena/esphome-weatherman-dashboard/)提供本專案硬體架構與程式碼的發想

本專案的E-ink資訊板放在玄關處，在指定的時段內每15分鐘，會自動更新顯示內容

<img src="https://user-images.githubusercontent.com/56766371/184495447-3c9c7ea9-0f64-442d-b695-d47738ba2d2d.jpg" width="66%" alt="Context"/>


資訊板顯示內容包括:
- 今天日期
- 各房間溫溼度
- 當下天氣預報
- 未來四小時天氣預報

<img src="https://user-images.githubusercontent.com/56766371/184495454-a9595013-f24a-4c55-b7f8-b29cdee5af32.jpg" width="66%" alt="Context"/>

以下將說明硬體架構、ESPHome yaml code與Home assistant yaml code


## Hardware 硬體架構

- [微雪 7.5吋黑白墨水屏裸屏](https://detail.tmall.com/item.htm?id=606005913066) - 不帶外殼
- [微雪 墨水屏裸屏 SPI驅動板 ESP32](https://detail.tmall.com/item.htm?id=605757128869) - wifi+藍牙版本(ESP32)
- [IKEA RIBBA 相框 13x18公分](https://www.ikea.com.tw/zh/products/wall-decoration/frames/ribba-art-40378415) - 外框有黑白兩色，**注意裱框紙開孔較小，需要挖大**

## Installation 安裝方式

1. 將`/fonts`資料夾及`esp32-eink-dashboard.yaml`放到HA/config/esphome的資料夾內
2. 將`/package`內的`eink_dashboard.yaml`放到HA/config/package內
3. 將`esp32-eink-dashboard.yaml`及`eink_dashboard_sensor.yaml`的內容修改成自己HA裡的實體ID，**解說在下方**
4. HA檢查YAML code有無錯誤
    1. 開發工具>YAML>檢查設定內容，確認左下角通知沒有出現錯誤
    2. YAML 設定新載入中>模板實體
    3. 開發工具>狀態>檢查sensor.eink_sensors有確實出現，以及內容是自己想要的
5. 在ESPhome將`esp32-eink-dashboard.yaml`燒錄至ESP32模組
6. 完成!

## ESPHome yaml 說明

### 在HA內手動更新面板

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


### 將HA的時間帶進ESPHome

```YAML
time:
  - platform: homeassistant
    id: ha_time
```


### 根據來自HA的binary sensor來決定現在是否要更新面板，晚上睡覺無人時就不用更新以延長面板壽命

```YAML
binary_sensor:
  - platform: homeassistant
    id: 'eink_refresh'
    entity_id: 'binary_sensor.eink_refresh_time'
```


### 面板更新時機

因為預設螢幕不會自動更新`update_interval: never`，是當資料都差不多收到後就執行腳本來更新內容，流程如下:

#### 1. 等這個sensor有接收到資料後就執行腳本:  

```YAML
  - platform: homeassistant
    entity_id: sensor.eink_sensors
    attribute: forecast_temperature_4
    id: forecast_temperature_4
    on_value: 
      then:
        - script.execute: all_data_received 
```

#### 2. 執行all_data_received腳本，每15分鐘會重複執行此腳本以更新面板

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

## HA template sensor 說明




## References
- https://github.com/Madelena/esphome-weatherman-dashboard/


