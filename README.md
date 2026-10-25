# Apollo PoE mmWave Multisensor (R_PRO-1)

## Description

The Apollo Automation R_PRO-1 is a PoE mmWave multisensor with the following features:

- PoE, Ethernet or WiFi, all in one firmware. The board can be powered from PoE through the vertical RJ45, which also provides a rock solid wired connection. It can also be powered from USB-C and use Ethernet or WiFi; Ethernet is used whenever a cable is plugged in and WiFi takes over automatically when it is not
- ld2412 mmWave - This mmWave has great still detection and is lightning fast. It is fully configurable from Home Assistant and has a auto calibrate feature.
- ld2450 mmWave - This mmWave can track the x/y coordinates of 2 targets in a room. Meaning you don't just know someone is in the room, you know if they are on the couch or near the TV.
- ltr390 - Provides both LUX and UV values. This will let you know how bright the room is and if there is sunlight or not.
- Expansion port on the back for additional sensors such as CO2, GPIO, or other
- RGB Pixels: Notify you of the status
- Buzzer: Can play beeps, and RTTTL tones when outside a specified temperature range
- Bluetooth tracking and Bluetooth proxy

## Mounting


The R_PRO-1 has been designed to fit both in recessed ceiling mounts and wall gang boxes. This provides a clean and non intrusive way to place the sensor in a room.

## Quickstart

1. Power the R_PRO-1 from PoE, or from USB-C.
2. **Ethernet:** plug in the cable; the device gets its address from your network and shows up in Home Assistant.
3. **WiFi:** with no Ethernet cable connected (power from USB-C), connect to the "Apollo R PRO 1 Hotspot" and enter your WiFi credentials (Improv via Bluetooth or USB works too). The hotspot and Bluetooth setup are off while Ethernet is connected, and after a wired boot the hotspot can take about a minute to appear.
4. In Home Assistant, look at discovered devices.

## Connectivity

One firmware covers both WiFi and Ethernet, so there is no need to pick a variant or reflash to switch:

- **Ethernet plugged in:** traffic moves to Ethernet. If the device is also joined to your WiFi, that connection stays up alongside, so the device stays online if the cable is unplugged. Home Assistant keeps using the address it found the device on; if that is the Ethernet address, it can show the device unavailable while the cable is out. If WiFi has no network to join, it turns off; the setup hotspot is never active while Ethernet is connected.
- **No Ethernet:** WiFi works as usual. The device rejoins its saved network, or opens the hotspot for setup if none is configured. Its log then shows an Ethernet "Connecting failed" warning every 15 seconds, which is normal.

Use the on-device **Network Connection** sensor (Diagnostics) to see which connection is active.

### Migrating from the separate WiFi / Ethernet firmware

Existing devices update to the unified firmware through their normal "Firmware Update" entity and keep their current name and Home Assistant entry. After the update:

- The **Firmware Type** select is gone; the device no longer needs it.
- The **IP Address** sensor stays; it shows the Ethernet address while the cable is connected, otherwise the WiFi address. **Ethernet IP Address**, **WiFi IP Address** and **Network Connection** are new.
- **SCD40 Automatic Self Calibration** is replaced by **CO2 Auto Calibration**, which starts on. If you had turned self-calibration off, turn it off again. **Combined Presence** is new.
- A WiFi device gains Ethernet: plug in a cable and traffic moves to it, while WiFi stays joined.
- An Ethernet device keeps WiFi off while its cable is connected. If Ethernet is lost, or isn't up about a minute after boot, it falls back to WiFi: the network it was set up on before it switched to the Ethernet firmware, if any, or else the open setup hotspot and Improv BLE. The hotspot and Improv turn off again when Ethernet returns; a joined WiFi network stays up alongside. To keep WiFi off entirely, see `wifi_fallback` below.
- Update existing devices with the "Firmware Update" entity. Reinstalling from the web installer instead switches the device to the new `apollo-r-pro-1` name.

### Customizing the firmware ("Take Control" in ESPHome)

If you adopt the device in the ESPHome dashboard and add your own YAML:

- Add to `on_boot` as a list of `- priority: ...` entries, and to `on_connect` and `on_disconnect` as a list of actions (`- logger.log: ...`). The single-trigger form replaces the device's own boot and network handling.
- To run Ethernet-only with WiFi off entirely, add `wifi_fallback: "false"` under `substitutions:`. Setting `wifi: enable_on_boot: false` alone is not enough, because the firmware turns WiFi back on to recover connectivity. `wifi_fallback` relies on the firmware's `on_boot` entries (keep yours in list form), and safe mode still starts WiFi.
- If you give Ethernet a static IP and your dashboard can't resolve the device by name, set `wifi: use_address:` to that IP. The dashboard uses WiFi's address setting.
- Keep `captive_portal:` and `esp32_improv:`, and put any Improv BLE settings under `esp32_improv:` rather than `improv_ble:`. The firmware refers to both.
- The Ethernet-first `network: priority` is fixed.

## Links

- [Shop](https://apolloautomation.com/products/r-1?utm_source=esphome&utm_medium=social)
- [GitHub](https://github.com/ApolloAutomation/R_PRO-1)
- [Wiki](https://wiki.apolloautomation.com/)
- [Discord](https://dsc.gg/ApolloAutomation)
- [YouTube](https://www.youtube.com/@ApolloAutomation)