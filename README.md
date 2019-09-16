# Cấu hình và cài đặt công tắc wifi cảm ứng

## 1. Định nghĩa

  * **wtls-dev**: Bộ công tắc cảm ứng gắn tường
  * **wtls-app**: App điều khiển bộ công tắc cảm ứng gắn tường
  * **wtls-protocol-pubsub**: Giao thức điều khiển thiết bị thông qua aws-iot cloud

## 2. Tổng quan
  2.1 Hardware platform
  * Chip điều khiển: esp8266 hoặc esp32 của espressif
  * Hỗ trợ tối đa 4 công tắc
  * Điều khiển: 4 nut cảm ứng + 1 nút vật lý
  * Chỉ thị: 4 led RGB

  2.2 Cloud platform
  * Hỗ trợ aws-iot cloud platform

  2.3 App platform
  * Hỗ trợ COAP trong mạng local với esp8266 - TODO
  * Hỗ trợ Homekit trong mạng local với esp32 - TODO

  2.4 Cài đặt
  * Certificate
    * Hỗ trợ **Just-in-Time Registration of Device Certificates**
    * Thiết bị **wtls-dev** tự đọng generate certificate và đăng kí với cloud
    * App **wtls-app** làm nhiệm vụ kích hoạt **wtls-dev** và liên kết user

  * Wifi
    * Sử dụng giao thức [ESP-TOUCH](https://www.espressif.com/en/products/software/esp-touch/overview)

## 3. Phần dành cho người sử dụng & cài đặt

  3.1 Cài đặt thiết bị **wtls-dev** mới:
  * Cài đặt wifi:
    * Nhấn giữ 1 phím cảm ứng bất kì trong vòng 5s thiết bị sẽ vào trạng thái cài đặt wifi
    * Tất cả các đèn chỉ thị nhấp nháy trong quá trình cài đặt wifi
    * Sử dụng **wtls-app** để cài đặt wifi

  * Đưa thiết bị về trạng thái khi xuất xưởng - factory reset
    * Nhấn giữ phím vật lý trong vòng 5s
    * Sử dụng **wtls-app** để kích hoạt **wtls-dev** và liên kết user

## 4. Phần dành cho **wtls-app** developer

  4.1 **wtls-aws-iot-thing-shadow**

  * **wtls-dev** đã implement aws-iot-thing-shadow để report state của thiết bị
  * Thing shadow Json example
  ```JSON
  "endpoints": {
      "1": {
        "onOff": 0,
        "onTime": 0
      },
      "2": {
        "onOff": 0,
        "onTime": 0
      },
      "3": {
        "ep": 3,
        "onOff": 0,
        "onTime": 0
      },
      "4": {
        "onOff": 0,
        "onTime": 0
      }
  }
  ```

  4.2 **wtls-protocol-pubsub**

  * Được kế thừa lại một phần của giao thức **[mht-zigbee-gateway-pubsub](https://github.com/vinhlq/mht-zigbee-docs/blob/master/pubsub.md)**

  * Device control
    * Topic
      > mht/${DeviceUid}/action
    * Payload: Cần tối ưu payload vì thiết bị không có khả năng buffer payload quá lớn (< 256 bytes)
    * OnOff control

      ```JSON
      {
        "type":"lighton",
        "deviceEndpoint": {
          "endpoint": 1
        }
      }
      ```

      ```JSON
      {
        "type":"lightoff",
        "deviceEndpoint": {
          "endpoint": 1
        }
      }
      ```

      ```JSON
      {
        "type":"lighttoggle",
        "deviceEndpoint": {
          "endpoint": 1
        }
      }
      ```
    * Brightness control

      ```JSON
      {
        "type":"setlightlevel",
        "level": number,
        "deviceEndpoint": {
          "endpoint": 1
        }
      }
      ```

    * Color control

      ```JSON
      {
        "type":"setlighthuesat",
        "hue": number,
        "sat": number,
        "deviceEndpoint": {
          "endpoint": 1
        }
      }
      ```

    * Color temp control

        ```JSON
        {
          "type":"setlightcolortemp",
          "colorTemp": number,
          "deviceEndpoint": {
            "endpoint": 1
          }
        }
        ```

    * OTA upgrade

        ```JSON
        {
          "type":"otasetupgrade",
          "upgrade": true
        }
        ```

  4.3 **wtls-aws-iot-certificate**

  * [Tham khảo](https://github.com/vinhlq/mht-zigbee-docs/blob/master/aws-iot-cert.md)

  4.4 [ESP-TOUCH](https://www.espressif.com/en/products/software/esp-touch/overview)

  * Đưa thiết bị về chế độ cài đặt wifi: xem mục 3.1

  * [EsptouchForAndroid](https://github.com/EspressifApp/EsptouchForAndroid)

  * [EsptouchForIOS](https://github.com/EspressifApp/EsptouchForIOS)
