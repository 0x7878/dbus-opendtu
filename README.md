# dbus-shelly-3em-smartmeter
Integrate Shelly 3EM smart meter into Victron Energies Venus OS

## Purpose
With the scripts in this repro it should be easy possible to install, uninstall, restart a service that connects the Shelly 3EM to the VenusOS and GX devices from Victron.
Idea is pasend on @RalfZim project linked below

## Inspiration
This project is my first on GitHub and with the Victron Venus OS, so I took some ideas and approaches from the following projects - many thanks for sharing the knowledge:
- https://github.com/RalfZim/venus.dbus-fronius-smartmeter
- https://github.com/victronenergy/dbus-smappee
- https://github.com/Louisvdw/dbus-serialbattery
- https://community.victronenergy.com/questions/85564/eastron-sdm630-modbus-energy-meter-community-editi.html


## How it works
### My setup
- Shelly 3EM with latest firmware (20220209-094824/v1.11.8-g8c7bb8d)
  - 3-Phase installation (normal for Germany)
  - Connected to Wifi netowrk "A"
  - IP 192.168.2.108/24  
- Victron Energy Cerbro GX with Venus OS - Firmware v2.84
  - No other devices from Victron connected (still waiting for shipment of Multiplus-2)
  - Connected to Wifi netowrk "A"
  - IP 192.168.2.110/24

### Details / Process
As mentioned above the script is inspired by @RalfZim fronius smartmeter implementation.
So what is the script doing:
- Running as a service
- connecting to DBus of the Venus OS "com.victronenergy.grid.http_40"
- After successful DBus connection Shelly 3EM is accessed via REST-API - simply the /status is called and a JSON is returned with all details
- Serial/MAC is taken from the response as device serial
- Paths are added to the DBus with default value 0 - including some settings like name, etc
- After that a "loop" is started which pulls Shelly 3EM data every 750ms from the REST-API and updates the values in the DBUS

Thats it 😄

## Install
Just grap a copy of the main branche and copy them to /data/dbus-shelly-3em-smartmeter.
After that call the install.sh script.

The following script should do everything for you:
```
wget https://github.com/fabian-lauer/dbus-shelly-3em-smartmeter/archive/refs/heads/main.zip
unzip main.zip "dbus-shelly-3em-smartmeter-main/*" -d /data
mv /data/dbus-shelly-3em-smartmeter-main /data/dbus-shelly-3em-smartmeter
chmod a+x /data/dbus-shelly-3em-smartmeter/install.sh
/data/dbus-shelly-3em-smartmeter/install.sh
rm main.zip
```
⚠️ Check configuration after that - because service is already installed an running and with wrong connection data (host, username, pwd) you will spam the log-file

## Configuration
Within the project there is a file 'config.ini' - just change the values - most important is the host, username and password in section "ONPREMISE".
The "DEFAULT" section is not used / has no alternatives at the moment.

## Used documentation
- https://github.com/victronenergy/venus/wiki/dbus#grid   DBus paths for Victron namespace
- https://www.victronenergy.com/live/ccgx:root_access   How to get root access on GX device/Venus OS