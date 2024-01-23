# voron-2

this repository contains everything you need image, update and operate a voron 2.4r2 3D printer. There are several mods used on my current voron 2.4r2 setup. These mods will be made clear if they require code or system level changes. If you do not have specific mods I will attemp to make it clear what alternative configurations are needed when possable. 

## Component Variants and Mods
TBD


## Wiring

![wiring diagram](/assets/wiring.drawio.png)

## System Provisioning and Firmware

### Setup RasberryPi 4 and Essential Software
TBD

```
sudo apt-get install gcc-arm-none-eabi dfu-util
```

### Setup Toolhead MCU

| Variants | Mods |
|-----|-----------|
| BTT EBB 2240 CAN | None |
| BTT U2C | |

#### Setup U2C

1. Enter the following command to setup the CAN Network
```
sudo nano /etc/network/interfaces.d/can0
```
2. Enter this content into the file
```
auto can0
iface can0 can static
 bitrate 1000000
 up ifconfig $IFACE txqueuelen 1024
```
3. reboot the device

#### Building and Installing Katapult (Canboot) on EBB

1. Ensure 24V power and CAN lines are unpluged. Plug usb-c into the EBB 2240 and set the usb-c 5v power jumper
2. Run the following commands, or jump to the next step if using the prebuilt binary
```
git clone https://github.com/Arksine/katapult.git
cd katapult
make -f ../configs/toolhead.katapult.config
cd ..
```
3. Enter DFU mode on the toolhead by holding boot and clicking reset
4. Run `lsusb` and use the USB ID in the following command to flash the prebuild binary `/bin/toolhead.katapult.bin` or the one created in `/katapult/out/katapult.bin`.
```
sudo dfu-util -a 0 -d 0483:df11 -s 0x08000000:mass-erase:force -D ~/CanBoot/out/canboot.bin
```
5. remove usb-c and 5v jumper. Hook back up the 24V power and CAN lines.

#### Building and Installing Klipper on EBB

1. Run the following commands, or jump to the next step if using the prebuilt binary
```
git clone https://github.com/Klipper3d/klipper.git
cd klipper
make -f ../configs/toolhead.klipper.config
cd ..
```
2. Run the following commands to get the ID of the CAN toolhead
```
cd katapult/scripts
python3 flash_can.py -i can0 -q
```
3. Flash the device over CAN using the prebilt binary `/bin/toolhead.klipper.bin` or the one created in `/klipper/out/klipper.bin`. Replacing the ID found above here.
```
python3 flash_can.py -i can0 -f ~/klipper/out/klipper.bin -u be69315a613c
```
4. Confirm klipper is installed using the command in step 2. Should now report `Application: klipper`
5. Cycle power

### Setup Octopus MCU

| Variants | Mods |
|-----|-----------|
| Octopus Pro | None |

## Klipper Configuration and Setup
TBD