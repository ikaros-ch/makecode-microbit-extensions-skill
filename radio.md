# Radio patterns

Source: `pxt-common-packages/libs/radio` (`radio.ts`, `radio.cpp`), which `pxt-microbit` reuses.

## Packet layout

Max 32 bytes on air. The receiver adds the RSSI (Int32LE) as 4 extra bytes at the end of the buffer.

| Bytes | Field |
|-------|-------|
| 0 | type: `0` int, `1` name+int, `2` string, `3` buffer, `4` double, `5` name+double |
| 1-4 | sender `control.millis()`, Int32LE |
| 5-8 | sender serial, Int32LE (0 unless `radio.setTransmitSerialNumber(true)`) |
| 9+ | payload |

| Call | Payload | Limit |
|------|---------|-------|
| `sendNumber(v)` | Int32LE, or Float64LE if `v !== (v \| 0)` | picked automatically |
| `sendValue(name, v)` | number, then name length + name | name 8 bytes |
| `sendString(s)` | length + UTF-8 | 19 bytes (≈9 Greek chars) |
| `sendBuffer(b)` | length + bytes | 19 bytes |

Anything too long is cut off without any error. Check lengths yourself if the data matters.

## Behaviour

- One handler per type: calling `onReceivedString` a second time replaces the first handler.
- The radio turns itself on at the first radio call. Defaults: group 0, transmit power 6.
- `setFrequencyBand(n)`: 0-83, which is 2400 + n MHz. Both ends must match the band and the group.
- After `radio.off()`, nothing turns the radio back on (not even sending) until `radio.on()`.
- When the radio wakes the program, the receive loop empties the whole queue, so a burst of packets runs the handlers back to back.

## Broadcast messages

`radio send message` doesn't carry a payload. It sends an event:

```typescript
radio.raiseEvent(2000, msg + 1)          // msg is a hash of the message name
control.onEvent(2000, msg + 1, handler)  // receiver: ordinary event
```

## Your own protocol

Pack fields into a buffer of up to 19 bytes. It's smaller than sending strings and needs no parsing:

```typescript
const b = pins.createBuffer(5)
b[0] = 1                                    // message id
b.setNumber(NumberFormat.Int16LE, 1, x)
b.setNumber(NumberFormat.Int16LE, 3, y)
radio.sendBuffer(b)

radio.onReceivedBuffer(function (r) {
    if (r[0] == 1) move(r.getNumber(NumberFormat.Int16LE, 1), r.getNumber(NumberFormat.Int16LE, 3))
})
```

## Radio and Bluetooth can't be used together

The `radio` package's `pxt.json` sets the firmware build option `"microbit-dal": { "bluetooth": { "enabled": 0 } }` and `bluetooth` sets it to `1`. An extension depends on one or the other, never both. If a user's project needs both, the editor makes them remove one.
