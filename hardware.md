# micro:bit hardware and MakeCode platform

Source of truth: https://tech.microbit.org/hardware/ (edge connector, power, I2C pages) and https://makecode.microbit.org/reference. Re-check there for anything electrical; board revisions change details.

## V1 vs V2

| | V1 (1.3, 1.5) | V2 (2.00, 2.2x) |
|---|---|---|
| MCU | nRF51822, 16 KB RAM, 256 KB flash | nRF52833, 128 KB RAM, 512 KB flash |
| Motion sensor | 1.3: MMA8653 (0x1D) + MAG3110 (0x0E); 1.5: LSM303AGR (0x19, 0x1E) or FXOS8700CQ (0x1F) | LSM303AGR (0x19, 0x1E) or FXOS8700CQ (0x1F); interface chip 0x70, 0x72 |
| I2C buses | one, shared by sensors and edge P19/P20 | separate internal (sensors, interface chip) and external (edge P19/P20) buses |
| Edge connector current (total) | 90 mA | 270 mA (latest revision; less when speaker/mic are busy) |
| Sound | buzzer on P0 only | speaker + P0, microphone, `music.setBuiltInSpeakerEnabled` |
| Extras | | touch logo, sound level, power/sleep APIs, flash data logger |
| PWM | only 3 pins at once | no documented limit |
| Bluetooth + other code | BLE uses most RAM; big programs fail to build | comfortable |

On V1 an edge-connected device at an onboard sensor address (e.g. HMC5883L at 0x1E on a 1.5 LSM303AGR board, or anything at 0x1F on an FXOS8700 board) collides with the onboard chip. On V2 the buses are separate, so it doesn't.

V2-only APIs fail to compile or show an error icon on V1. Mark them in docs, and add `"disablesVariants": ["mbdal"]` to `pxt.json` when the extension is V2-only.

## Edge connector

| Pin | Default use | Notes |
|---|---|---|
| P0, P1, P2 | big rings | analog in, PWM, touch (the only touch pins). P0 also carries sound |
| P3, P4, P10 | LED matrix columns | analog in, but shared with the display. `led.enable(false)` before using |
| P6, P7 | LED matrix columns | `led.enable(false)` first |
| P9 | V1: LED matrix column. V2: free (optional NFC) | |
| P5, P11 | buttons A, B | readable; pressing the button pulls low |
| P8, P16 | free GPIO | P8 optional NFC on V2 |
| P12 | GPIO, reserved for accessibility | avoid in accessories |
| P13, P14, P15 | SPI SCK, MISO, MOSI | free GPIO when SPI is unused |
| P19, P20 | I2C SCL, SDA | pulled up onboard; don't use as GPIO |
| 3V, GND | power | 3.3 V logic; IO tolerates at most VDD + 0.3 V, so not 5 V tolerant. Don't power motors or servos from 3V |

Analog read: `pins.analogReadPin` returns 0-1023 against ~3.3 V. Analog write: 0-1023, change frequency with `pins.analogSetPeriod`. Servos: `pins.servoWritePin` (any PWM pin, needs external power).

## MakeCode platform

- Static TypeScript subset (see SKILL.md overview). Numbers are doubles, but integer math is faster; use `Math.idiv` for integer division.
- Concurrency is fibers: `basic.forever`, `control.inBackground`, event handlers. A fiber only yields at `basic.pause`, waits, and blocking calls. A busy loop without a pause starves everything else, including the display.
- Events: `control.onEvent(src, value, handler)` / `control.raiseEvent`. Pick source ids above 32768 for your own.
- Simulator: no I2C/SPI peripherals, no BLE, no RSSI. Code must not crash there. Real behaviour needs hardware.
- Serial: USB serial at 115200 by default; `serial.redirect` moves it to edge pins, which takes USB logging away.
- Memory: V1 has ~10 KB free for program data. Big arrays, long strings and many fibers run out first on V1.
