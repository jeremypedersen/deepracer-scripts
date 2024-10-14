**⚠️Warning: This is just a personal backup of David Smith's repo**. Please [use that repo](https://github.com/davidfsmith/deepracer-scripts) if you want the latest copy of the DeepRacer scripts. 

# DeepRacer Scripts

Please note this scripts are provided as is.... if something breaks through use then a) I'm sorry b) fix it and submit a PR ;-)

## dev-stack-\*

Refactoring of the `dev-build.sh`, which splits it into three distinct scripts.

To ensure that car configuration is correct, please run `tweaks.sh` once after flashing the car.

| -   | File                          | Description                                                                                  |
| --- | ----------------------------- | -------------------------------------------------------------------------------------------- |
| 1   | `tweaks.sh`                   | Update the car settings.                                                                     |
| 2   | `dev-stack-dependencies.sh`   | Installs the dependencies for a custom DR stack. Script is only required to be run one time. |
| 3   | `dev-stack-build.sh`          | Downloads the packages defined in `ws/.rosinstall` and builds them into the `ws` folder.     |
| 4   | `dev-stack-install.sh`        | Installs the stack built in `ws/install` into `/opt/aws/deepracer/lib.custom`, and changes `/opt/aws/deepracer/start_ros.sh` to use it. |
| 5   | `dev-stack-install-remote.sh` | Pushes an install onto a remote machine. Add as parameter the SSH information e.g. `deepracer@amss-hm12` |


The custom stack exposes the following arguments which can be changed through changing `/opt/aws/deepracer/start_ros.sh`.

| Argument | Default | Description | 
| -------- | ------- | ----------- |
| `camera_fps` | `30` | Number of camera frames per second, directly impacting how frequently the car takes new actions. |
| `camera_resize` | `True` | Does the camera resize from 640x480 to 160x120 at source. | 
| `inference_engine` | `CPU` | What device will perform inference. Can be `CPU`, `GPU` or `MYRIAD`; Myriad is the intel compute stick. |
| `logging_enable` | `False` | Shall the output of inference be written to a ROS Bag? |

Example custom `/opt/aws/deepracer/start_ros.sh`:
    
    source /opt/ros/foxy/setup.bash
    source /opt/aws/deepracer/lib.custom/setup.bash
    source /opt/intel/openvino_2021/bin/setupvars.sh
    ros2 launch deepracer_launcher deepracer_launcher.py camera_resize:=False camera_fps:=15 logging_enable:=True

## usb-build.*

**Recommendations**:

- Try using USB 3.0 usb stick, the process will take about 30 minutes per stick compared to 1.5hrs with USB 2.0 ones.

**Requirements**:

- The custom AWS DeepRacer Ubuntu ISO image `ubuntu-20.04.1-20.11.13_V1-desktop-amd64.iso` in the same directory (will be downloaded if missing)
- The latest AWS DeepRacer software update package `factory_reset.zip` **unzipped** in the same directory (will be downloaded and unzipped if missing)

Both files can be downloaded from here https://docs.aws.amazon.com/deepracer/latest/developerguide/deepracer-ubuntu-update.html (under the "Prerequisites" heading).

### OSX version

**Requirements**:

- https://unetbootin.github.io/ installed

**Command**:

```
./usb-build.sh -d disk2 -s <WIFI_SSID> -w <WIFI_PASSWORD>
```

**Note:**

- The wifi credentials are used to create `wifi-creds.txt` on the `DEEPRACER` partition, upon rebooting after flashing the car will use this file to connect to wifi
- Tested and working on Intel and Apple based Macs
- Unetbootin currently doesn't work on OSX Ventura [#Issue 337](https://github.com/unetbootin/unetbootin/issues/337)

### Ubuntu version

**Requirements**:

- Requires sudo privileges
- Will add current user to sudoer automatically (with the "no password" option enabled)

**Command**:

```
./usb-build.ubuntu.sh -d sdX -s <WIFI_SSID> -W <WIFI_PASSWORD>
```

**Note:**

- The wifi credentials are used to create `wifi-creds.txt` on the `DEEPRACER` partition, upon rebooting after flashing the car will use this file to connect to wifi
- Tested and working on Ubuntu 20.0.4 (like a DeepRacer car)

### Windows PowerShell version

**Requirements**:

- Run in PowerShell command window (**NOT** run as Administrator, but you will be prompted to elevate with Administrator at some point in the script=

**Command**:

```
start powershell {.\usb-build.ps1 -DiskId <disk number>}
```

Additional switches:

Description                                                    | Switch
---------------------------------------------------------------|---------------------------------------------------
Provide Wifi Credentials                                       | `-SSID <WIFI_SSID> -SSIDPassword <WIFI_PASSWORD>`
Create partitions (default value is True)                      | `-CreatePartition <True/False>`
Ignore lock files (default value is False)                     | `-IgnoreLock <True/False>`
Ignore Factory Reset content creation (default value is False) | `-IgnoreFactoryReset <True/False>`
Ignore Boot Drive creation (default value is False)            | `-IgnoreBootDrive <True/False>`

**Note:**

- The wifi credentials are used to create `wifi-creds.txt` on the `DEEPRACER` partition, upon rebooting after flashing the car will use this file to connect to wifi
- Tested and working on Windows 11 Pro


## tweaks.sh

A script to change a couple of things on the car that I've found useful at events

- Add `deepracer` user to `sudoers`
- Change the hostname
- Change the car console password (**Note:** Update the default password in the script)
- Update Ubuntu
- Update the car software
- Disable IPV6 on network interfaces
- Disable the video stream on the car console by default
- Disable system suspend
- Disable network power saving
- Disable the software update check
- Enable SSH (You've probably already done this)
- Allow multiple logins to the car console
- Increase the car console cookie duration
- Disable Gnome, Bluetooth & CUPS

Command:

```
sudo ./tweaks.sh -h newhostname -p magicpassword
```

## reset-usb.sh

Needs to be run on the car to reset USB in the event of a failure of the front USB hub, faster than a full reboot of the car - works ~70% of the time YMMV

## VS.Code Dev Container

Files have been added to the repository (`.devcontainer` and `.vscode`) that allows the normal DR packages to be built on another machine, isolated from what is installed on the OS. Tested on Ubuntu 20.04.

Container includes most relevant ROS2 Foxy packages, as well as the OpenVINO release that is used on the car.

Before opening the container run `docker volume create deepracer-ros-bashhistory` to get your commandline history enabled inside the container.
