# Pi Cam
A Raspberry Pi based time lapse camera, work in progress.

> Note: We assume your computer runs MacOS.

## Take pictures
- Plug the power cable into the Pi and a socket
- Wait for the Pi to start up (might take 5 minutes)
- Wait for the camera to take pictures (every 15 minutes)

## Browse pictures
On your computer.

> Note: Your computer has to be in the same Wi-Fi network.

- Open http://raspberrypi.local:8080/
- Press F5 or refresh the page
- Browse the directories

## Log into the Pi
On your computer.

> Note: Your computer has to be in the same Wi-Fi network.

- Open the _Terminal_ app
- Connect to the Pi with _ssh_
    ```bash
    $ ssh pi@raspberrypi.local
    ```
- List files on disk with _tree_
    ```bash
    $ tree /mnt/elements
    ```
- See settings in _photo.sh_
    ```bash
    $ cat photo.sh
    ```  
- Change a file
    ```bash
    $ nano my.txt
    # CTRL-X-Y ENTER
    ```
    
## Download pictures
On your computer.

- Install the _wget_ tool
    ```bash
    $ brew install wget
    ```
- Create a directory
    ```bash
    $ mkdir ~/Desktop/pi-cam
    ```
- Change directory
    ```bash
    $ cd ~/Desktop/pi-cam
    ```
- Sync picture files
    ```bash
    $ wget --mirror http://raspberrypi.local:8080/
    ```
- Open in _Finder_
    ```bash
    $ open ~/Desktop/pi-cam
    ```

# Make your own
## Hardware
- [Raspberry Pi 3 B+](https://www.pi-shop.ch/raspberry-pi-3-model-b) (CHF 34)
- [Kingston SD card 16 GB](https://www.pi-shop.ch/kingston-microsdhc-karte-industrial-uhs-i-16-gb) (CHF 23)
- [ArduCam 64 MP Autofocus](https://www.pi-shop.ch/arducam-1-1-32-64mp-auto-focus-camera-module-for-raspberry-pi) (CHF 30)
- [Samsung SSD Disk 500 GB](https://www.digitec.ch/en/s1/product/samsung-portable-t7-red-1000-gb-external-ssd-13199901) (CHF 137)
- [Micro USB power adapter](https://www.pi-shop.ch/raspberry-pi-12-5w-micro-usb-power-supply-2255) (CHF 12)

## Set up the SD card
On your computer.

- Install _Pi Imager_ from https://www.raspberrypi.com/software/
- Plug in the SD card
- Choose device _Pi 3_
- Choose _Pi OS (other)_ > _Pi OS Lite (64-bit)_
- Choose SD card storage (compare size)
- Next > Edit
    - Wi-Fi credentials, e.g. MY_SSID, MY_PASSWORD
    - Computer name _raspberrypi_
    - User _pi_
    - Password
    - Enable SSH
    - ...
- Wait for the SD card to be written
- Plug the SD card into the Pi
- Try to [log into the Pi](#log-into-the-pi)

## Set up the disk
On the Pi.

> Thanks to [@cspindler](https://github.com/cspindler).

- Plug in the disk via USB
- Install BTRFS file system tools
    ```bash
    $ sudo apt install -y btrfs-progs
    ```

- Format the disk and label it as _capture_
    ```bash
    $ sudo mkfs.btrfs -L capture /dev/sda1
    ```

- Add a mount point to the operating system
    ```bash
    $ echo '/dev/disk/by-label/capture /mnt/elements btrfs  defaults,auto,user  0 0' \
    | sudo tee --append /etc/fstab
    ```

- Mount the disk and change its owner to _pi_
    ```bash
    $ mount /mnt/elements
    $ sudo chown pi /mnt/elements
    ```

- Install the _tree_ command to list files (optional)
    ```bash
    $ sudo apt install tree
    $ tree /mnt/elements
    ```

## Set up the Web UI service
On the Pi.

TODO

## Set up the camera
On the Pi, based on [this guide](https://docs.arducam.com/Raspberry-Pi-Camera/Native-camera/64MP-Hawkeye/#software-guide).

- Connect the camera to the Pi
- Download _install_pivariety_pkgs.sh_
    ```bash
    $ wget -O install_pivariety_pkgs.sh https://github.com/ArduCAM/Arducam-Pivariety-V4L2-Driver/releases/download/install_script/install_pivariety_pkgs.sh
    ```
- Make it executable
    ```bash
    $ chmod +x install_pivariety_pkgs.sh
    ```
- Install _libcamera_dev_
    ```bash
    $ ./install_pivariety_pkgs.sh -p libcamera_dev
    ```
- Install _libcamera_apps_
    ```bash
    $ ./install_pivariety_pkgs.sh -p libcamera_apps
    ```
- List cameras
    ```bash
    $ libcamera-hello --list-cameras
    ```
    e.g.
    ```
    Available cameras
    -----------------
    0 : arducam_64mp [9248x6944 10-bit RGGB] (/base/soc/i2c0mux/i2c@1/arducam_64mp@1a)
        Modes: 'SRGGB10_CSI2P' : 1280x720 [120.09 fps - (2064, 2032)/5120x2880 crop]
            1920x1080 [60.04 fps - (784, 1312)/7680x4320 crop]
            2312x1736 [30.00 fps - (0, 0)/9248x6944 crop]
            3840x2160 [20.00 fps - (784, 1312)/7680x4320 crop]
            4624x3472 [10.00 fps - (0, 0)/9248x6944 crop]
            8000x6000 [3.00 fps - (624, 472)/9248x6944 crop]
            9152x6944 [2.70 fps - (0, 0)/9248x6944 crop]
    ```
- Create _photo.sh_
    ```bash
    $ nano ~/photo.sh
    ```
    paste
    ```
    #!/bin/bash
    libcamera-jpeg --width 4624 --height 3472 -o /mnt/elements/"`date +"%Y-%m-%d"`"/image-"`date +"%Y-%m-%dT%H-%M-%SZ"`".jpg
    ```
    save
    ```bash
    CTRL-X-Y ENTER
    ```
- Try to take a picture
    ```bash
    $ ./photo.sh
    ```
- [Browse pictures](#browse-pictures)

## Set up the photo service
On the Pi.

TODO
