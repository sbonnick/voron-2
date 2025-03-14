# voron-2

this repository contains everything you need image, update and operate a voron 2.4r2 3D printer. There are several mods used on my current voron 2.4r2 setup. These mods will be made clear if they require code or system level changes. If you do not have specific mods I will attempt to make it clear what alternative configurations are needed when possible. 

## Roadmap

This repo and doc is still missing some critical items to be fully up and running. some of these items that need to be dome before that milestone is reached are:

TODO:
- [ ] rewire octopus to use usb or CAN
- [ ] identify the configuration items that need to be adjusted
- [ ] script binary creation. use docker
- [ ] add rpi4 setup
- [ ] more details for the first init setup
- [ ] need to add knomi v2
- [ ] need to add chamber sensor
- [ ] consider adding a heat up (120c) and clean of nozzle before the z calibration
- [ ] create docker for katapult
- [ ] fully automate upgrading binaries


## Component Variants and Mods

| Description | Hardware Variants | Mods | Software Impacts |
| ------------|----------|---------------|------------------|
| Linux PCB | Pi4 2G - piOS | heat sync | Major |
| Main MCU PCB | BTT Octopus Pro | | Major |
| Toolhead PCB | BTT EBB 2240 CAN | [motor mount, cable chain arm](https://github.com/bigtreetech/EBB/tree/master/EBB%20SB2240_2209%20CAN/STL), [cable cover](https://www.printables.com/model/1216990-stealthburner-cable-cover-for-sb2209-rp2040) | Major |
| USB to CAN   | BTT U2C | [mount](https://mods.vorondesign.com/detail/guUSuCXHsOqPH1Xn5cS1Ag) | Major |
| Extruder Motor | LDO-36STH20 |         | |
| Z Motors | LDO-42STH48 | | Minor |
| A/B Motors | LDO-42STH40 | [plug mounts](https://github.com/Ramalama2/Voron-2-Mods/tree/main/AB_Plugs), [cable clips](https://github.com/Ramalama2/Voron-2-Mods/tree/main/Misumi_Cable_Clip) | Minor |
| Hotend | Voron Dragon HF | | |
| Extrusion support | Titanium Extrusion Backers | | |
| Kinematic Bed |  | [kinematic parts](https://github.com/tanaes/whopping_Voron_mods/tree/main/kinematic_bed) | |
| Hotend cleaning | nozzle scrubber | [brush wiper](https://www.printables.com/model/768112-voron-24-a1-bambu-silicone-brush-wiper), [brush mount](https://www.printables.com/model/659369-voron-24-purge-bucket-silicon-brush-wiper), [Purge Bucket for Kinematic](https://github.com/Dfdye/Voron_Mods/tree/main/Purge_Bucket_for_WP_Kinetic_Mount) | Minor |
| Run out sensor | BTT SFS v2.0 Sensor | [mount](https://www.printables.com/model/713155-bigtreetech-smart-filament-sensor-sfs-v20-2020-ext) | Minor |
| Exhaust | ? | ? | Minor |
| Air Filter | Nevermore 5 v2 | [parts](https://github.com/nevermore3d/Nevermore_Micro/tree/master/V5_Duo/V2) | Minor |
| X/Y End stops | Mellow Hall PCB | | Minor |
| Probe / Z end stop | CNC Voron Tap v2 | [hall sensor mount](https://github.com/Chaoticlab/CNC-Tap-for-Voron/blob/master/STL/CNC_VORON_TAP_V2/V2%20Hall%20Sensor%20Adapter.STL) | Major |
| Misc. wiring mods |  | [rails](https://mods.vorondesign.com/detail/ozerX2agbmmnjUP4EdDyQ), [under bed wagos](https://mods.vorondesign.com/detail/Zdq86RuSkhr4bx3xrauw) | |
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

TODO: Complete this section for a fresh install
1. Install docker (TBD)
2. Install docker-compose (TBD)
3. Other TBD


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
4. Run `lsusb` and use the USB ID in the following command to flash the pre-built binary `/bin/toolhead.katapult.bin` or the one created in `/katapult/out/katapult.bin`.
```
sudo dfu-util -a 0 -d 0483:df11 -s 0x08000000:mass-erase:force -D ~/CanBoot/out/canboot.bin
```
5. remove usb-c and 5v jumper. Hook back up the 24V power and CAN lines.

#### Building and Installing Klipper on EBB (fresh install or upgrade)

Before updating firmware, you need to ensure the same version of klipper is being used. Create a `.env` file and add the tag that is being used by the klipper app in docker. for example `TAG=v0.12.0-439-g1fc6d214f`. this can be retrieved using:
```
docker inspect --format '{{ index .Config.Labels "org.prind.image.version"}}' klipper
```

1. Run the following commands, or jump to the next step if using the prebuilt binary
```
cd ~/core
docker-compose -f docker-compose.toolhead.build.yml run --rm make
cp ./klipper/out/klipper.bin toolhead.klipper.bin
```
2. Run the following commands to get into katapult bootloader
```
python3 ~/temp/katapult/scripts/flashtool.py -i can0 -r -u 8b07d889b7ef
python3 ~/temp/katapult/scripts/flashtool.py -i can0 -q	# should show katapult app
```
If you don't already have your ID, then you need to double click the reset button (back button) on the toolhead to get into this mode. you can then run the query (-q) with same expectations. It will also provide your ID.

3. Flash the binary we create earlier
```
python3 ~/temp/katapult/scripts/flashtool.py -i can0 -f klipper/out/klipper.bin -u 8b07d889b7ef
python3 ~/temp/katapult/scripts/flashtool.py -i can0 -q	# should show klipper app
```
4. Cycle power

### Setup Octopus MCU

Before updating firmware, you need to ensure the same version of klipper is being used. Create a `.env` file and add the tag that is being used by the klipper app in docker. for example `TAG=v0.12.0-439-g1fc6d214f`. this can be retrieved using:
```
docker inspect --format '{{ index .Config.Labels "org.prind.image.version"}}' klipper
```

1. Run the following commands, or jump to the next step if using the prebuilt binary
```
cd ~/core
docker-compose -f docker-compose.octopus.build.yml run --rm make
cp ./klipper/out/klipper.bin octopus.klipper.bin
```
2. Use the samba share to copy the bin to a sd-card on your computer. rename the file `firmware.bin`.
3. Insert the sd-card into your octopus mcu and reboot (cycle power)
4. Wait 5 minutes, power down and remove sd-card. power back on
5. validate there is no longer a `firmware.bin` on the sd-card

## Klipper Configuration

TODO: list here any parameters that need to be adjusted from the `klipper.cfg` included.

## Initial Setup and Test

Once wiring and software are up and going, run through the tests and setup on:

- https://ellis3dp.com/Print-Tuning-Guide
- https://www.klipper3d.org/Config_checks.html

To calibrate dimensions of your printer use the follow pattern:

- [Calistar](https://thangs.com/designer/realdirtdigger/3d-model/Calistar%20%28formerly%20Fleur%20de%20Cali%29%20-%20Open%20source%20printer%20size%20and%20skew%20calibration-1028679)

Anytime something changes that can effect the thermals of the printer, pid calibration is needed. changing sensors, hotends, etc would require this. The following command can be run in fluidd terminal and will output new pid values to be added into the printer.cfg. 
```
PID_CALIBRATE HEATER=extruder TARGET=260

or

PID_CALIBRATE HEATER=heater_bed TARGET=105
```

Anytime mechanical parts are changed, you need to redo the resonance testing:
```
mzv, shaper_freq_y = 33.0
```

## Slicing

TODO: add slicer specific settings and steps to do. OrcaSlicer configuration steps are a good one.

## MISC. help and references

If you run into any CAN related issues, read through the following sites: 
- https://canbus.esoterical.online
- https://github.com/Arksine/katapult

If you run into tuning related issues, read through the Ellis guide, multiple times: 
- https://ellis3dp.com/Print-Tuning-Guide


Much of the docker code is from the following repo with some improvements committed back to the author via issues. Strongly suggest using the docker compose files here if you don't want to use my more opinionated setup. https://github.com/mkuf/prind