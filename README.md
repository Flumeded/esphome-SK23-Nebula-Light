# ESPHome for Tuya SK23/SK20 (CBU) WiFi Nebula Light

This repository contains ESPHome instructions for flashing and a configuration file to use with MOES Tuya SK23 nebula lights, or newer SK20 light running on [Tuya CBU](https://docs.libretiny.eu/boards/cbu) module. Compared to the older SK20 model, the SK23 cannot be flashed with [cloudcutter](https://docs.libretiny.eu/docs/flashing/tools/cloudcutter/) (please see note below). You will need to open the device and manually flash it using a USB UART/USB-TTL adapter for the initial setup. After the initial flash, the device can be updated through Wi-Fi.

> [!NOTE] 
> Some users reported that it is possible to put CBU Based devices in AP mode by either pressing the main button for 10 seconds, and/or 10 times consequitively.  
>If you managed to put device in AP mode please see if you can flash it using [cloudcutter](https://github.com/tuya-cloudcutter/tuya-cloudcutter) using yml from this repository, and report your results in the issues.

## Reference

This project is heavily based on [SK20 Nebula Light](https://github.com/M4GNV5/esphome-SK20-Nebula-Light). All credit goes to the maintainer of that repository.

---

## Flashing Guide

### 1. Open Your Device

To open the device, you need to remove three screws hidden under the rubber dampeners. These screws require a long-shaft screwdriver with a (likely) PH2 head.

After removing the screws, you can slightly spread the front part of the device (the side with lenses). However, the spine of the device (where the angle feet are located) has two plastic clamps, which require some force to open. Slide a plastic pick from the lens side along the seam as far as possible, then twist the pick to release the clamps.

> [!TIP] 
> This may feel like you're breaking the device, so proceed cautiously, but be prepared to apply some force.

### 2. Preparations

1. Install `ltchiptool` by following the [installation guide](https://docs.libretiny.eu/docs/flashing/tools/ltchiptool/#installation).
2. Install [ESPHome](https://esphome.io/guides/getting_started_command_line.html).
3. Clone this repository and navigate into it:
   
   ```bash
   git clone https://github.com/Flumeded/esphome-SK23-Nebula-Light.git
   cd esphome-SK23-Nebula-Light
   ```
4. Populate `secrets.yml` file.

### 3. Connect Module to UART Programmer

![UART Connection Diagram](https://docs.libretiny.eu/boards/cbu/cbu.svg)

Connect the following pins to the UART Adapter:

> [!CAUTION] 
> TTL Adapter should be set to 3.3V  
> Make sure your device is disconnected from the power plug; the TTL programmer will power the device instead.

| UART | CBU       |
|------|-----------|
| RX   | TX1 (P11) |
| TX   | RX1 (P10) |
| GND  | GND       |

### 4. Create a Backup Dump of the Current Firmware

> [!NOTE] 
> This step is optional, but without proper backup you will not be able to roll back to the stock firmware.

It is advised to create two dumps and then esnure they are the same bit-wise. 
This way you are making sure firmware file was not currupted in the dumping proccess.

```bash
ltchiptool flash read beken-7231n flash.bin
ltchiptool flash read beken-7231n flash-duplicate.bin
cmp flash.bin flash-duplicate.bin
```

If the files are identical, the `cmp` command will output nothing. Once you have your firmware backup ready, you can proceed with flashing.

### 5. Flashing

Now you can compile and flash ESPHome in one go by running:

```bash
esphome run nebula_light_device.yml
```

During flashing, you will be prompted to reset the device. To do this, you need to short `CEN` (P18) and `GND` pin. You can either solder a wire to `CEN` and touch the `GND` pin with it, or use tweezers or another object to short them. You can also toggle 3.3V power, device briefly goes to reset state on boot.

---


## Notes 

There are some bits of non-working code from SK20 project, so some cleanup is needed.  
Also, I could not figure how to make reset button to work.  

I hope this project helps you to kickstart your futher research and any contributions are very welcome.

Also I have managed to kill my laser during investigation, but were able to find a [drop-in replacement](https://aliexpress.com/i/1005002610306340.html).