# voron-2

this repository contains everything you need image, update and operate a voron 2.4r2 3D printer. There are several mods used on my current voron 2.4r2 setup. These mods will be made clear if they require code or system level changes. If you do not have specific mods I will attempt to make it clear what alternative configurations are needed when possible. 

## Roadmap

This repo and doc is still missing some critical items to be fully up and running. some of these items that need to be dome before that milestone is reached are:

- [ ] A few missing components like the A/B and Z motors
- [ ] Add STLs and link to source
- [ ] finish wiring for EBB
- [ ] rewire octopus to use usb or CAN
- [ ] identify the configuration items that need to be adjusted
- [ ] script binary creation. use docker
- [ ] add rpi4 setup
- [ ] more details for the first init setup
- [ ] need to add knomi v2
- [ ] need to add chamber sensor
- [ ] consider adding a heatup (120c) and clean of nozzle before the z calibration
- [ ] tune web camera - framerate is slow


## Component Variants and Mods

| Description | Hardware Variants | Mods | Software Impacts |
| ------------|----------|---------------|------------------|
| Linux PCB | Pi4 2G - piOS | heatsync, mount | Major |
| Main MCU PCB | BTT Octopus Pro | mount | Major |
| Toolhead PCB | BTT EBB 2240 CAN |  motor mount, pcb cover, cable chain arm | Major |
| USB to CAN   | BTT U2C | mount         | Major |
| Extruder Motor | LDO-36STH20 |         | |
| Z Motors | ? | | |
| A/B Motors | ? | | |
| Hotend | Voron Dragon HF | SB Dragon Mount | |
| A/B motor wiring | ? | cable clip | |
| Extrusion support | Titanium Extrusion Backers | | |
| Kinematic Bed | ? | kinematic parts, drill mount | |
| Hotend cleaning | Purge Bucket for Kinematic | parts | Minor |
| Run out sensor | BTT v1.2 Sensor | | Minor |
| Exhaust | ? | ? | Minor |
| Air Filter | Nevermore v2 | ? | Minor |
| X/Y Endstops | Mellow Hall PCB | Voron Hall Pod | Minor |
| Probe / Z endstop | CNC Voron Tap v2 | hall sensor mount | Major |
| Misc. wiring mods |  | rails, mounts, ??? | |
| Misc. panel mods |  | magnets mount top, hinges, bottom plate tabs | |
| power supply mods |  | side mounts, terminal cover | |
| Thermistor | PT100 | | Minor |
| Web Camera | Logitech C920s | Top Mount | Major |

All mods are also stored in the stl folder for version locking. As I upgrade versions these will be upgraded as well based on the original authors revisions.

## Wiring

| Description | Images |
|-------------|--------|
| Overall wiring diagram | ![wiring diagram](/assets/wiring.drawio.png) |
| Wiring for the Octopus. Note that currently the Octopus is using serial to communicate with the pi4. a future iteration I plan to switch back to usb connection or CAN | ![octopus](/assets/octopus.jpg) |
| When shortening the extruder wire, it will also require reordering the pins. Here is the order if its the LDO 20mm motor. If you use a different motor ensure you have the ordering correct | ![Extruder motor wiring](/assets/Emotor_wire.jpg) |
| Wiring for BTT EBB 2240 CAN Toolhead. Using PT100 (set the dip switches!) LDO motor wired above, and a voron TAP | ![Toolhead wiring](/assets/toolhead.jpg) |
| Overall view of underside of voron 2 fully wired with all mods in place | ![underside](/assets/underside.jpg)|



## System Provisioning and Firmware

### Setup RasberryPi 4 and Essential Software
TBD

```
sudo apt-get install gcc-arm-none-eabi dfu-util
```

### Setup Toolhead MCU

To reduce wiring complexity and portability, I have replaced my hardwired toolhead with BTTs EBB 2240 CAN board. If you are using a hard wired setup using something like a Hartk PCB, you can ignore these setup steps since your toolhead will not have a independent MCU. Caution though, your wiring will look very different as a result. refer to a older check-in here if you want to see my legacy Hartk PCB setup.

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

1. Ensure 24V power and CAN lines are unplugged. Plug usb-c into the EBB 2240 and set the usb-c 5v power jumper
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

1. Run the following commands, or jump to the next step if using the prebuilt binary
```
git clone https://github.com/Klipper3d/klipper.git
cd klipper
make -f ../configs/octopus.klipper.config
cd ..
```

More Steps TBD

## Klipper Configuration

TBD

## Inital Setup and Test

Once wiring and software are up and going, run through the tests on https://www.klipper3d.org/Config_checks.html

Anytime something changes that can effect the thermals of the printer, pid calibration is needed. changing sensors, hotends, et would require this. The following command can be run in fluidd terminal and will output new pid values to be added into the printer.cfg. 
```
PID_CALIBRATE HEATER=extruder TARGET=230

or

PID_CALIBRATE HEATER=heater_bed TARGET=110
```

https://all3dp.com/2/klipper-z-offset-simply-explained/

## Slicing

TBD