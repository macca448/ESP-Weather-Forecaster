# ESP32/ESP8266-Weather-Forecaster

ESP8266 or ESP32 Weather Forecaster using 128 x 64 OLED Display

Original author [David Bird](<http://g6ejd.dynu.com/>)

Changes and improvemnts by [Ralph McCleery](https://github.com/macca448)

#### UPDATE 03/06/2022
> Added a second sketch `esp32time_weather_predictor.ino` that is configured with the `BME280` using an `SPI SSD1306 OLED` and will only work on the `ESP32` as it uses the `ESP32Time` library, the native `ESP32 time_t` functions and it has been `dual core threaded`.

> Original sketch `esp_weather_forecaster.ino` uses `<sys/time.h>` and `tmelements` default C++ time libraries and is configured for `SSD1306 I2C OLED` and the `BMP280` weather sensor and will compile one both the `ESP32` and `ESP8266`.

### Changes list:
  1.  NTP time method is in full compliance with NTP ORG "[Terms of Service](https://www.ntppool.org/tos.html)"
  2.  The WiFi connection is only used to update time. At all other times the WiFi is disconnected and the radio is turned Off.
  3.  Screen No.6 has a Time stamp for the last NTP sync so it's easy to know when things aren't working as they should
  4.  Sketch will auto determine your ESP board type (ESP32 or ESP8266)
  5.  The "FLASH / BOOT" button (GPIO0) is used for screen "WAKE" function
  6.  Settings marked with " ! " in the "USER SETTINGS" section of the sketch need your input.
  7.  This sketch is configured to use a BMP280 for Barometric Pressure and Room Temperature.
  8.  Two hardware configurations tested with this sketch: 
      * ESP8266 with I2C SSD1306
      * ESP32 with SPI SSD1309  (Use the SSD1306Spi driver)
  9.  Sketch was tested using:
      * Arduino IDE v1.8.19 and v2.1.0              (v2.1.0 shows some incorrect "unused parameter" warnings. You can ignore them)
      * ESP8266 v3.1.2 (Generic) board profile
      * ESP32   v2.0.9 (Generic) board profile
      * Adafruit BMP280 Library v2.6.6              (Chinese Clones may need you to change the default address to 0x76 in Adafruit_BMP280.h on line:34)
      * ThingPulse v4.4.0 OLED Driver Library
       NOTE: All libraries are available via the Arduino IDE Library Manager

  10. For more information on the "strftime" time string function click [here](https://cplusplus.com/reference/ctime/strftime/)
  11. [Click](https://github.com/nayarsystems/posix_tz_db/blob/master/zones.csv) for NTP Timezone POSIX strings [database](https://github.com/nayarsystems/posix_tz_db/blob/master/zones.csv)


#### ESP8266 and ESP32 OLED Driver from ThingPulse IDE Install Screen
![ThingPulse](https://github.com/macca448/ESP_Weather_Predictor/blob/main/esp_weather_predictor/assets/thingpulse_library.png)


## HOW THIS SKETCH WORKS:
  1. On boot your controller starts two constant timers "millis()" and "micros()".
  2. We connect to WiFi and get an NTP "Unix Epoch" time update.
  3. <sys/time.h> then use's your "Posix" string  to convert the GMT/UDP "Epoch" to your local time and sync's the time with your controllers millis() clock.
  4. If your "Posix" string supports it the time will be auto-corrected for "Daylight Savings Time (DST)" start and end.
  5. The ESP's don't need time to be corrected for drift any shorter than one hour and you'd find doing it daily would be more than enough
  6. A Time re sync only occurs when the screen is OFF. You'll see the on-board LED blinking indicating time update in progress.
  7. As noted above there is a "Last Sync" time stamp on screen 6 under the temperature reading.
  8. To further adhere to NTP ORG's "[Terms of Service](https://www.ntppool.org/tos.html)" a randomness has been employed as per their recommendations.
  9. The screen turns off after 3 minutes. You can adjust this in the "USER SETTINGS" section.
  10. Note that there is a "Duty" period for a shared millis() statement. 50mS provides the de-bounce for the button so to calculate a screen time-out period it is "duration = count * 50" or  3 minutes = "3600 * 50 or 180,000mS". If you want say a 5 minute screen OFF period it would be (5 * 60 * 60 * 1000 /50) = 6000 (#define SCREEN_SLEEP 6000)
  11. To "WAKE" the screen press the "FLASH / BOOT" button on your ESP Dev Board. If the screen is awake and you press this button it resets the timeout count to zero.


### Wiring Guide ESP32 I2C SSD1306
![I2C ESP32 SSD1306](https://github.com/macca448/ESP_Weather_Predictor/blob/main/esp_weather_predictor/assets/ESP32_OLED_I2C.png)


### Wiring Guide ESP32 SPI SSD1309
![SPI ESP32 SSD1309](https://github.com/macca448/ESP_Weather_Predictor/blob/main/esp_weather_predictor/assets/OLED_SPI_BMP_ESP32.png)


### Screens Video
[![Watch the video](https://img.youtube.com/vi/-4ZAevAfWxo/maxresdefault.jpg)](https://youtu.be/-4ZAevAfWxo)
