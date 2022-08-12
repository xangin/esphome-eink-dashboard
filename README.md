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


## Hardware 硬體

- [微雪 7.5吋黑白墨水屏裸屏](https://detail.tmall.com/item.htm?id=606005913066) - 不帶外殼
- [微雪 墨水屏裸屏 SPI驅動板 ESP32](https://detail.tmall.com/item.htm?id=605757128869) - wifi+藍牙版本(ESP32)
- [IKEA RIBBA 相框 13x18公分](https://www.ikea.com.tw/zh/products/wall-decoration/frames/ribba-art-40378415) - 外框有黑白兩色，*注意裱框紙開孔較小，需要再挖大一點*

## References
- https://github.com/Madelena/esphome-weatherman-dashboard/

