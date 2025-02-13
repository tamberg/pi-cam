# Pi Cam
A Raspberry Pi based time lapse camera, work in progress.

> Note: We assume your computer runs MacOS.

## Take pictures
- Plug the power cable into the Pi and a socket
- Wait for the Pi to start up (might take 5 minutes)
- Wait for the camera to take pictures (every 15 minutes)

## Browse pictures
On your computer

> Note: Your computer has to be in the same Wi-Fi network.

- Open http://raspberrypi.local:8080/
- Press F5 or refresh the page
- Browse the directories

## Log into the Pi
On your computer

> Note: Your computer has to be in the same Wi-Fi network.

- Open the _Terminal_ app
- Connect to the Pi with _ssh_
    ```bash
    $ ssh pi@raspberrypi.local
    ```

## Download pictures
On your computer

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

## Hardware
- [Raspberry Pi 3 B+](https://www.pi-shop.ch/raspberry-pi-3-model-b) (CHF 34)
- [Kingston SD card 16 GB](https://www.pi-shop.ch/kingston-microsdhc-karte-industrial-uhs-i-16-gb) (CHF 23)
- [ArduCam 64 MP Autofocus](https://www.pi-shop.ch/arducam-1-1-32-64mp-auto-focus-camera-module-for-raspberry-pi) (CHF )
- [Samsung SSD Disk 500 GB](https://www.digitec.ch/en/s1/product/samsung-portable-t7-red-1000-gb-external-ssd-13199901) (CHF )
- [Micro USB power adapter](https://www.pi-shop.ch/raspberry-pi-12-5w-micro-usb-power-supply-2255) (CHF )

## Set up the SD card
On your computer

- Install _Pi Imager_ from https://www.raspberrypi.com/software/
- Plug in the SD card
- Choose device _Pi 3_
- Choose _Pi OS (other)_ > _Pi OS Lite (64-bit)_
- Choose SD card storage (compare size)
- Next > Edit > Set Wi-Fi credentials, computer name _raspberrypi_, user _pi_, password, enable SSH, etc.
- Wait for the SD card to be written
- Plug the SD card into the Pi

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
## Set up the camera
TODO

## Set up services
TODO
