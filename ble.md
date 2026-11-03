# Bluetooth patterns

Source: `pxt-microbit/libs/bluetooth`, plus [bsiever/microbit-pxt-blehid](https://github.com/bsiever/microbit-pxt-blehid) as the worked C++ example.

## `bluetooth` package

| Call | What it does |
|------|--------------|
| `startAccelerometerService()`, `startButtonService()`, `startIOPinService()`, `startLEDService()`, `startTemperatureService()`, `startMagnetometerService()` | standard micro:bit firmware services |
| `startUartService()` | Nordic UART service (text/bytes) |
| `onBluetoothConnected(h)`, `onBluetoothDisconnected(h)` | firmware connect/disconnect events |
| `setTransmitPower(0-7)` | clamped to 0-7 |

Start services in `on start`. Eddystone advertising (URL/UID beacons) is deprecated and hidden.

## UART

```typescript
bluetooth.startUartService()
bluetooth.onUartDataReceived(serial.delimiters(Delimiters.NewLine), function () {
    handle(bluetooth.uartReadUntil(serial.delimiters(Delimiters.NewLine)))
})
```

- Each notification carries about 20 bytes. Split longer buffers yourself: `for (let i = 0; i < b.length; i += 20) bluetooth.uartWriteBuffer(b.slice(i, 20))`.
- The package sends `console.log` output to the UART too, so debug logging uses the link.

## Firmware build settings without C++

`pxt.json` `yotta.config` changes firmware build settings. The `bluetooth` package itself uses this:

```json
"yotta": {
    "config": { "microbit-dal": { "bluetooth": { "enabled": 1 } } },
    "optionalConfig": { "microbit-dal": { "stack_size": 1280, "gatt_table_size": "0x700" } },
    "userConfigs": [{ "description": "Disable Bluetooth Event Service",
                      "config": { "microbit-dal": { "bluetooth": { "event_service": 0 } } } }]
}
```

`config` is always applied, `optionalConfig` is a default other packages can override, and `userConfigs` are options the user can switch on.

## Worked example: blehid (C++ extension)

The micro:bit acts as a Bluetooth keyboard, mouse, media keys, gamepad or absolute-position mouse.

TypeScript stubs point at C++ with `shim=`. The C++ function sits in the same namespace and is marked with a bare `//%`:

```typescript
//% blockId="send string" block="send keys | $keys"
//% shim=keyboard::sendString
export function sendString(keys: string): void { return }   // body runs only in the simulator
```

```cpp
namespace keyboard {
    //%
    void sendString(String keys) {
        if (!isInitialized()) return;
        reporter->sendString(keys->ascii.data, keys->ascii.length);
    }
}
```

List every `.cpp` and `.h` in `pxt.json` `files`.

- **One shared service.** A single `HIDService` (Bluetooth HID service `0x1812`) is shared by all the device types. Each one adds its own section to a combined HID report description (200-byte limit, about 3 device types at once) and has its own report characteristic.
- **Rate limiting.** `notifyChrValue` waits (`uBit.sleep`) if reports would go out too fast. `setEventsPerSecond(5-165)` sets the rate, 100 by default.
- **Advertising.** It stops the micro:bit's normal advertising and advertises as an HID device instead. The advertising data has to be `static` because the Bluetooth stack keeps a pointer to it.
- **Special keys in strings.** `\x01`-`\x08` are modifiers (Ctrl, Shift and so on) and `\x10` followed by a scancode is a raw key. So `sendString(ctrl + "s")` sends Ctrl+S.

`pxt.json` settings that matter:

```json
"dependencies": { "core": "*", "bluetooth": "*" },
"disablesVariants": ["mbdal"],
"yotta": { "config": { "microbit-dal": { "bluetooth": {
    "open": 0, "whitelist": 1,
    "security_level": "SECURITY_MODE_ENCRYPTION_NO_MITM",
    "device_info_service": 0 } } } }
```

- `disablesVariants: ["mbdal"]` turns off the V1 build, so the extension is V2 only.
- `open: 0` with `security_level: SECURITY_MODE_ENCRYPTION_NO_MITM` means the host must pair (no passcode) and the link is encrypted. HID hosts require this.

**Pairing problem after reflashing.** A new flash can wipe the micro:bit's stored pairing key while the phone or PC keeps its copy. The host then connects but ignores everything. Fix: make the host forget the micro:bit, then pair again. Small code changes usually keep the key.

| Service | Windows | macOS | Android | iOS |
|---------|:-------:|:-----:|:-------:|:---:|
| Keyboard | ✓ | ✓ | ✓ | ✓ |
| Media keys | ✓ | ✓ | ✓ | |
| Mouse | ✓ | ✓ | ✓ | only with AssistiveTouch |
| Gamepad | ✓ | ✓ | ✓ | |
| Absolute mouse | ✓ | ✓ | | |

On iOS the keyboard service hides the on-screen keyboard. Sending the media "eject" key brings it back.
