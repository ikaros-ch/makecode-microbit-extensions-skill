# I2C patterns

Addresses are 7-bit. Datasheets that give an 8-bit "write address" (e.g. `0x48`) mean `0x48 >> 1` = `0x24`.

## Two kinds of device

| Kind | Examples | Write | Read |
|------|----------|-------|------|
| Register | INA219, BME280, MLX90615, HMC5883L | `[reg, value...]` | point at `reg` with `repeat=true`, then read |
| Command packet | Nezha, Wukong, Nezha V2 (all `0x10`) | fixed-length frame | send the frame with STOP, wait, then `i2cReadBuffer` |

Register read (INA219). `repeat=true` sends a repeated start instead of a STOP, which SMBus parts like MLX90615 require:

```typescript
function readU16(reg: number): number {
    pins.i2cWriteNumber(ADDR, reg, NumberFormat.UInt8BE, true)
    return pins.i2cReadNumber(ADDR, NumberFormat.UInt16BE, false)
}
```

Always set the pointer before a multi-byte read. HMC5883L code that calls `i2cReadBuffer(0x1E, 6)` with no pointer write works only because the chip wraps its pointer back to `0x03`; any other write in between breaks it:

```typescript
pins.i2cWriteNumber(0x1E, 0x03, NumberFormat.UInt8BE, true)
const d = pins.i2cReadBuffer(0x1E, 6)
```

## Command-packet frames

Nezha / Wukong: 4 bytes, write only.

| Byte | Motor | Servo |
|------|-------|-------|
| 0 | channel `0x01`-`0x04` | channel (Nezha `0x10`-`0x13`, Wukong `0x03`-`0x09`, S7 `0x10`) |
| 1 | `0x01` forward, `0x02` backward | angle 0-180 (270°/360° servos are mapped down to 0-180) |
| 2 | speed 0-100 (clamp first: one byte) | 0 |
| 3 | 0 | 0 |

Nezha V2: 8 bytes, `FF F9 motor dir CMD valHi mode valLo` (value bytes are split). CMD `0x70` move, `0x5D` absolute angle, `0x60` start, `0x5F` stop, `0x1D` zero, `0x77` speed (motor 0 = all), `0x46` read angle (4 bytes LE, 0.1°), `0x47` read speed (2 bytes), `0x88` version (3 bytes).

```typescript
// Nezha V2 read: frame + STOP, let the MCU prepare the reply, then read
buf[2] = motor; buf[4] = 0x46
pins.i2cWriteBuffer(0x10, buf)
delayMs(4)
const a = pins.i2cReadBuffer(0x10, 4)
return (a[3] << 24) | (a[2] << 16) | (a[1] << 8) | a[0]
```

## Waiting between write and read

`basic.pause` yields, so another fiber (`control.inBackground`, `basic.forever`, an event handler) can put its own I2C traffic on the bus between your write and read. When the gap is a few ms and the device must not be interrupted, busy-wait (Nezha V2 does this):

```typescript
function delayMs(ms: number) {
    const end = input.runningTime() + ms
    while (input.runningTime() < end) { }
}
```

Use `basic.pause` for long waits (resets, conversions >10 ms) where blocking every fiber is worse.

## Errors don't throw

A missing or NACKing device does not raise an exception; `try/catch` around `pins.i2c*` catches nothing. Reads just return junk. Validate the data instead:

```typescript
const b = pins.i2cReadBuffer(ADDR, 2)
if ((b[0] == 0xFF && b[1] == 0xFF) || (b[0] == 0 && b[1] == 0)) return NaN // can be real readings; prefer checking an ID register if the chip has one
```

## Signed values and endianness

Match `NumberFormat` to the datasheet: MSB first = `BE`, LSB first = `LE` (BME280 calibration and MLX90615 are `LE`; INA219 and HMC5883L are `BE`). `Int16BE`/`Int16LE` sign-extend for you. When assembling from a buffer, sign-extend by hand:

```typescript
let v = (d[0] << 8) | d[1]
if (v > 0x7FFF) v -= 0x10000
```

## Boot delay and lazy init

Peripherals powered with the micro:bit can miss commands sent before their reset finishes. Init on the first block call, waiting until the board has been up long enough (Grove LCD v4):

```typescript
let inited = false
function init() {
    if (inited) return
    basic.pause(Math.max(50, 400 - control.millis()))
    // datasheet init sequence...
    inited = true
}
```
