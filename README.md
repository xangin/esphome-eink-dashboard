# ESPHome E-ink Dashboard
感謝[Madelena](https://github.com/Madelena/esphome-weatherman-dashboard/)提供本專案硬體架構與程式碼的發想

本專案的E-ink資訊板放在玄關處，當左上方的Zigbee人體感應器偵測到有人時且在指定的時段內，會自動更新顯示內容

<img src="https://user-images.githubusercontent.com/56766371/184292623-32fe29f9-7cd7-4407-84ca-c2a5a2b17f17.jpg" width="66%" alt="Context"/>


資訊板顯示內容包括:
- 今天日期
- 各房間溫溼度
- 當下天氣預報
- 未來四小時天氣預報

以下將說明硬體架構、ESPHome yaml code與Home assistant yaml code


## Hardware 硬體架構

- [微雪 7.5吋黑白墨水屏裸屏](https://detail.tmall.com/item.htm?id=606005913066) - 不帶外殼
- [微雪 墨水屏裸屏 SPI驅動板 ESP32](https://detail.tmall.com/item.htm?id=605757128869) - wifi+藍牙版本(ESP32)
- [IKEA RIBBA 相框 13x18公分](https://www.ikea.com.tw/zh/products/wall-decoration/frames/ribba-art-40378415) - 外框有黑白兩色，**注意裱框紙開孔較小，需要挖大**

## 安裝方式

1. 將`/fonts`資料夾及`esp32-eink-dashboard.yaml`放到HA/config/esphome的資料夾內
2. 將`/package`內的`eink_dashboard.yaml`放到HA/config/package內
3. 將`esp32-eink-dashboard.yaml`及`eink_dashboard.yaml`的內容修改成自己HA裡的實體ID，**解說在下方**
4. HA檢查YAML code有無錯誤
    1. 開發工具>YAML>檢查設定內容，確認左下角通知沒有出現錯誤
    2. YAML 設定新載入中>模板實體
    3. 開發工具>狀態>檢查sensor.eink_sensors有確實出現，以及內容是自己想要的
5. 在ESPhome將`esp32-eink-dashboard.yaml`燒錄至ESP32模組
6. 完成!

## ESPHome yaml 說明

以下說明幾個程式中的重點地方

### 


## References
- https://github.com/Madelena/esphome-weatherman-dashboard/

