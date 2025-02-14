# voron-2

this repository contains everything you need image, update and operate a voron 2.4r2 3D printer. There are several mods used on my current voron 2.4r2 setup. These mods will be made clear if they require code or system level changes. If you do not have specific mods I will attempt to make it clear what alternative configurations are needed when possible. 

## Roadmap

This repo and doc is still missing some critical items to be fully up and running. some of these items that need to be dome before that milestone is reached are:

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
| Linux PCB | Pi4 2G - piOS | heatsync | Major |
| Main MCU PCB | BTT Octopus Pro | | Major |
| Toolhead PCB | BTT EBB 2240 CAN | [motor mount, pcb cover, cable chain arm](https://github.com/bigtreetech/EBB/tree/master/EBB%20SB2240_2209%20CAN/STL) | Major |
| USB to CAN   | BTT U2C | [mount](https://mods.vorondesign.com/detail/guUSuCXHsOqPH1Xn5cS1Ag) | Major |
| Extruder Motor | LDO-36STH20 |         | |
| Z Motors | LDO-42STH48 | | Minor |
| A/B Motors | LDO-42STH40 | [plug mounts](https://github.com/Ramalama2/Voron-2-Mods/tree/main/AB_Plugs), [cable clips](https://github.com/Ramalama2/Voron-2-Mods/tree/main/Misumi_Cable_Clip) | Minor |
| Hotend | Voron Dragon HF | | |
| Extrusion support | Titanium Extrusion Backers | | |
| Kinematic Bed |  | [kinematic parts](https://github.com/tanaes/whopping_Voron_mods/tree/main/kinematic_bed) | |
| Hotend cleaning | nozzle scrubber | [Parts](https://github.com/VoronDesign/VoronUsers/tree/master/orphaned_mods/printer_mods/edwardyeeks/Decontaminator_Purge_Bucket_%26_Nozzle_Scrubber), [Purge Bucket for Kinematic](https://github.com/Dfdye/Voron_Mods/tree/main/Purge_Bucket_for_WP_Kinetic_Mount) | Minor |
| Run out sensor | BTT SFS v2.0 Sensor | [mount](https://www.printables.com/model/713155-bigtreetech-smart-filament-sensor-sfs-v20-2020-ext) | Minor |
| Exhaust | ? | ? | Minor |
| Air Filter | Nevermore 5 v2 | [parts](https://github.com/nevermore3d/Nevermore_Micro/tree/master/V5_Duo/V2) | Minor |
| X/Y Endstops | Mellow Hall PCB | | Minor |
| Probe / Z endstop | CNC Voron Tap v2 | [hall sensor mount](https://github.com/Chaoticlab/CNC-Tap-for-Voron/blob/master/STL/CNC_VORON_TAP_V2/V2%20Hall%20Sensor%20Adapter.STL) | Major |
| Misc. wiring mods |  | [rails](https://mods.vorondesign.com/detail/ozerX2agbmmnjUP4EdDyQ), [underbed wagos](https://mods.vorondesign.com/detail/Zdq86RuSkhr4bx3xrauw) | |
| Misc. case mods |  | [hinges](https://mods.vorondesign.com/detail/eeRCH8EJiLrIF5q5l3AQg), [lean supports](https://mods.vorondesign.com/detail/tiIhFDTh9tHJY0JNJK9A), [handles](https://mods.vorondesign.com/detail/EAM1ZiQJCUzXznvOA767w), [magnets mount top](https://www.teamfdm.com/files/file/324-magnetic-panels/), [bottom panel tabs](https://www.teamfdm.com/files/file/134-deck-panel-support-clips/) | |
| Lights | | [mount segment](https://mods.vorondesign.com/detail/8S6KN4daUCgRhEJEX1stQ), [mount ends](https://github.com/Ramalama2/Voron-2-Mods/tree/main/Deprecated/Misumi_Led_Corners/STL), [idler corners](https://mods.vorondesign.com/detail/zBJTvWc9vSnnQoDDP0hyYw), [z corner covers](https://www.printables.com/model/84736-z-belt-cover-a-for-voron-24) | |
| power supply mods |  | [double din mount](https://www.teamfdm.com/files/file/457-double-din-lrs200-24-mounts-for-voron-24/), [terminal cover](https://mods.vorondesign.com/detail/jyO8bHoPTy3XUaYlXscB7w) | |
| Thermistor | PT100 | | Minor |
| Web Camera | Logitech C920s | [Top Mount](https://mods.vorondesign.com/detail/XgriEYTt4Xl5LeDx5DnPRg) | Major |

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

### Setup RaspberryPi 4 and Essential Software

Download the raspberry pi ![imaging tool](https://www.raspberrypi.com/software/) and follow the steps to edit settings for an appropriate hostname, username, password, timezone, and ssh to format a micro sd card. Then install into the raspberry pi. Follow the rest of the steps over SSH.

TBD - work in progress

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

Once wiring and software are up and going, run through the tests and setup on:
- https://www.klipper3d.org/Rotation_Distance.html
- https://www.klipper3d.org/Config_checks.html

Anytime something changes that can effect the thermals of the printer, pid calibration is needed. changing sensors, hotends, et would require this. The following command can be run in fluidd terminal and will output new pid values to be added into the printer.cfg. 
```
PID_CALIBRATE HEATER=extruder TARGET=230

or

PID_CALIBRATE HEATER=heater_bed TARGET=110
```

https://all3dp.com/2/klipper-z-offset-simply-explained/

## Slicing

TBD