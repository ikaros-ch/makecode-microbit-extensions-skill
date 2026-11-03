# Resources (from awesome-microbit)

Picked from https://github.com/carlosperate/awesome-microbit (CC0) for MakeCode code and extension work. Approved extensions are in [extensions.md](extensions.md); below are tools and other prior art; repos marked (approved) are also in extensions.md.

## Editors and testing

| Tool | Use |
|---|---|
| https://makecode.microbit.org/beta | test against the next editor release before it ships |
| https://makecode.microbit.org/---multi | two editors side by side: test radio code between two simulated micro:bits |
| https://makecode.microbit.org/extensions/build-your-own | official extension guide |
| https://makecode.microbit.org/extensions/extension-gallery | official gallery (same set as extensions.md) |
| https://makecode.com/cli | `pxt` CLI; `mkc` (npm `makecode`) is the lighter one |
| https://github.com/maehw/microbit-pxt-code-extractor | pull MakeCode source back out of a `.hex` |
| https://github.com/microbit-foundation/microbit-universal-hex | combine V1 + V2 hex files |
| https://microbit.org/tools/webusb-hex-download-tool/ | flash a hex over WebUSB |
| https://github.com/bsiever/microbit-usb-grapher | graph serial data over WebUSB |

## Other MakeCode drivers (prior art)

Unmarked ones are not approved; load them by URL. Read before writing a driver for the same chip.

| Chip / device | Repo |
|---|---|
| CCS811 air quality | ADataDate/pxt-airQuality |
| DS1307 RTC | Tinkertanker/pxt-realtimeclock-ds1307 (approved) |
| DS3231 RTC | gbraad/pxt-rtc-ds3231 |
| software RTC | bsiever/microbit-pxt-timeanddate (approved) |
| HT16K33 alphanumeric | Tinkertanker/pxt-alphanumeric-ht16k33 (approved) |
| BMP085 / BMP180 | sabas1080/uBit_BMP085 |
| SHT20/21/25 | Tinkertanker/microDriver_SHT2x (approved) |
| VL53L0X ToF | Tinkertanker/pxt-range-vl53l0x (approved) |
| PCA9685 PWM/servo | Tinkertanker/uDriver_PCA9585 (approved) |
| MLX90614 IR thermometer | DoraLC/pxt-MLX90614 |
| TCS3200 colour | DoraLC/pxt-tcs3200-color-sensor |
| DS18B20 (1-Wire, C++) | bsiever/microbit-dstemp (approved), bsiever/microbit-dstemp-2wire (approved), DFRobot/pxt-ds18b20 |
| MAX31855 thermocouple | bremoran/microDriver_max31855 |
| MCP23017 I/O expander | CaptainCredible/pxt-MCP23017 |
| DFPlayer mini MP3 | lioujj/pxt-mp3 |
| IR tx/rx | lioujj/pxt-IR |
| ESP-01 (AT over serial) | 51bit/esp01 |
| WS2812 matrix | Shorts1999/pxt-smartmatrix |
| Adafruit Motor Shield v1 | vijairaj/pxt-adafruit-motor-driver |
| LEGO Power Functions (IR) | philipphenkel/pxt-powerfunctions (approved) |
| TM1637/TM1650, OLED, LCD1602, AT24XX, DS1302, APDS9930, BH1750, BME280, BMP280 | github.com/makecode-extensions (org, approved) |

Software helpers: robo-technical-group/pxt-microbit-timers (debounce, throttle, background timers), jaustin/pxt-musichelpers.

Bluetooth: kshoji/pxt-bluetooth-keyboard / -mouse / -gamepad (V1 HID, compare with blehid in [ble.md](ble.md)), kshoji/pxt-bluetooth-beacons (iBeacon/AltBeacon), Microsoft/pxt-bluedot.

## Reference drivers in other languages

MicroPython drivers written *for the micro:bit* are the closest port source: same pins, same I2C speed, same memory limits. Check the license before porting; credit in README.

- shaoziyang/microbit-lib: TM1637/TM1650, OLED, LCD1602, AT24XX, DS1302/DS1307/DS3231, APDS9930, BME280, BMP280/BMP180, NeoPixel
- mcauser: HT16K33, MY9221, AM2320, DHT12, TM1637
- fizban99: SSD1306 (I2C and SPI), HC-SR04 via SPI, US-100 via UART, KY038
- CoreElectronics PiicoDev: VEML6030, TMP117, BME280
- Others: PCA9685 (gingemonster), MAX7219 (microbit-playground), DHT11 / MPR121 (rhubarbdog), MCP3008 / HTU21D (ti-nspire), Nokia 5110 / 24LC EEPROM (matneee), ULN2003 (IDWizard)

## Hardware design files

- V1 schematics/BOM: https://github.com/bbcmicrobit/hardware
- V2 schematics/BOM: https://github.com/microbit-foundation/microbit-v2-hardware
- V1 reference design: https://github.com/microbit-foundation/microbit-reference-design
- Edge connector footprints (KiCad): JohnVidler/kicad-microbit-v2-edge-connector, anthonykirby/kicad_microbit_connector
- Open accessory boards to learn from: sparkfun Micro_Bit_Moto_Bit / Weather_Bit / Controller_Bit, mignon-p/NeoBit
- Interface chip (DAPLink): https://tech.microbit.org/firmware/daplink-interface/

## Companion apps and Bluetooth testing

- nRF Connect (Android): scan, inspect GATT services, test UART
- Serial Bluetooth Terminal (Android): talk to the UART service
- Web Bluetooth libraries: thegecko/microbit-web-bluetooth, antefact/microBit.js
- bsiever/microbit-webusb: WebUSB serial from a web page
- Official micro:bit Android/iOS apps: flash over Bluetooth (needs the pairing setup in [ble.md](ble.md))

## Community

- MakeCode forum: https://forum.makecode.com/c/microbit/11 (MakeCode team reads it)
- r/microbit, micro:bit Champions: https://microbit.org/champions/
